# Skeet VR

Clay-target shooting for a phone in a Google Cardboard viewer. Clays are thrown
from high on the left and low on the right and cross about 17 m in front of you. You aim with a finger gun, seen by the phone's rear camera and tracked in the
browser by [MediaPipe](https://developers.google.com/mediapipe), and fire by
dropping your thumb.

It is built on the same engine as
[Fruit Slicer VR](https://github.com/tbirdpower3000/VR-FruitSlicer): its own
side-by-side stereo view, head tracking from the phone's motion sensors, and the
rear-camera hand tracker.

## Play

Open <https://tbirdpower3000.github.io/Skeet-Game/> on the phone (or scan the QR
code on the page from a computer), press **Start**, allow motion and the camera,
turn the phone sideways and put it in the viewer. Fire once to begin.

- **Finger gun.** Index finger pointing, thumb up. The crosshair follows your
  fingertip. Drop your thumb onto your hand to fire; lift it again for the next
  shot. The crosshair is gold when a shot is ready and grey while the thumb is down.
- **My gaze.** The crosshair sits where you look. Hold it on a clay for about a third
  of a second and it fires by itself; the viewer's button fires too. Used
  automatically if the camera or the hand tracker isn't available.
- The viewer's button (a tap on the screen) always fires too.
- A round is 20 clays: singles from each side in turn, and every third throw a
  double (one from each side). A beep comes just before each throw.
- When a round ends, look at the board for two seconds or fire to play again.

## How the trigger and aim work

These carry over what the hand-control training page and Hand Hub learned about
pinches and cursors:

- **The reading.** How far the thumb's tip is from the base of the index finger, as
  a share of the hand's size, measured on the tracker's 3D hand so that tilting the
  hand changes it little. Hand size is the average of two bones, which is steadier
  than one. The reading goes through the One Euro filter (3 Hz, beta 1).
- **Two levels with a gap.** The shot fires on the way down, 35% of the way up from
  thumb-down; the trigger is cocked again 60% of the way up. A reading hovering near
  one level can't fire twice.
- **Learning your thumb.** "Up" and "down" are learned from where your thumb comes to
  rest, and kept for next time (`skeetVRTrigger`), so a thumb that only drops part of
  the way still gets a trigger that fits it.
- **Deliberate shots.** A shot counts only if the thumb had been up for 150 ms first.
- **Keeping the shot on target.** Dropping the thumb nudges the whole hand, so a thumb
  shot is aimed from just before the thumb began to drop. That moment is found for each
  shot from the last half second of thumb readings (30 ms before the thumb left its
  "up" level, between 60 and 250 ms back), so a slow, careful thumb gets a longer look
  back than a snappy one.
- **Judged against where the clay was.** The shot is tested against where each clay was
  at the moment it is aimed from, not where the clay has flown on to since. Before
  this, a hand following a clay perfectly could still miss.
- **The hand is kept in the room.** A camera picture is old by the time the page has it,
  and the camera is on your head. Each sighting of the hand is turned by where the head
  was pointing when the picture was taken (the browser's own time for the picture where
  it gives one, else 70 ms ago), so turning your head no longer drags the crosshair.
- **Easy on the arm** (on by default). Your head does the big aiming and your hand the
  fine: the crosshair is where you look, moved by half of how far your hand is from
  where it rests. The resting place follows the hand slowly between throws and holds
  still during one, so the arm can stay low and bent. Off: the crosshair sits on your
  fingertip.
- **Forgiving shots.** If that aim misses, the shot may count from anywhere the
  crosshair was in the 150 ms before it.
- **Steady crosshair.** The fingertip goes through a median of three and the One Euro
  filter (0.8 Hz, beta 3), with more smoothing near the edge of the camera's view,
  where the crosshair also fades to warn that the hand is about to be lost.
- A shot breaks any clay it passes within 2.5° of. Clays are drawn far bigger than
  real ones (1.6 m across) so they are easy to see and hit 17 m away.

Not carried over: the training page's "virtual fingertip" (a fingertip rebuilt from
the palm). It was made for a hand facing a webcam; here the camera looks along the
pointing finger from behind, where the palm's direction in the picture is too short
to rebuild a fingertip from.

These four came from the hand-control research bot's list (lag-compensated hit test,
head-pose pairing, head-plus-hand aiming, per-shot rewind). They pass with a simulated
head and hand; none has been tried on a phone yet.

Each round is saved in the browser (`skeetVRRounds`): clays hit, shots, thumb and
button shots, the range of thumb readings, how far misses passed from the nearest
clay, flickers that were ignored, how far back each thumb shot was aimed from, how far the
hand sat from its resting place, the camera's rate and delay, the trigger's learned levels, and how often the
hand was lost. **Copy results** on the setup page copies
them, which is what is needed to tune the trigger.

## Status

First version. The game, the trigger logic and the gaze mode have been tested in a
desktop browser with a simulated hand. The thumb trigger has not yet been tried
with a real hand in a viewer, so its thresholds are a starting guess.

## Fitting the picture to the lenses

With **Fit the picture to the lenses** on (the default), each eye's picture is made for
a Google Cardboard of 2015 (I/O 2015, "version 2"), using the figures in Google's own
viewer profile: lenses 63.9 mm apart and 39.3 mm from the screen, their centres 35 mm
above the edge the phone rests on, and distortion coefficients k1 = 0.33582564,
k2 = 0.55348791.

- **Centred on each lens.** Each eye's view is drawn about its own lens's axis, not
  about the middle of its half of the screen.
- **True size.** Angles on screen match angles in the scene, from the screen's real
  size (taken as an iPhone's, 460 pixels to the inch).
- **Lens stretch taken out.** The lenses stretch the picture more towards the edges.
  Each eye is first drawn flat, off screen, then put on the screen squeezed the
  opposite way, so through the lens straight lines look straight.
- **Field of view.** Google designed this lens for a round field 80° across, so the
  picture is drawn out to 45° from the lens's axis and fades to black from 41°.

**Picture size** (80% to 125%) is for phones whose screens are not an iPhone's size per
pixel. Both settings are kept in the browser (`vrLens`) and shared by the games on
this site. Checked in a desktop browser; not yet looked at through a viewer.

## Test switches

- `?stereo=0` / `?stereo=1`: one view or the split view. `?n=6`: clays in a round.
- `window.__skeet` in the console exposes the state, `look(yaw, pitch)`,
  `hand(u, v)`, `feedTrigger(reading)` and `fire('tap')`.

## What's inside

A single `index.html` (no build step) using three.js r128 from cdnjs and MediaPipe
Tasks Vision 0.10.14 from jsDelivr, plus `vendor/qrcode-generator.js` (Kazuhiko
Arase, MIT). All sounds are made in the page. The camera picture never leaves the
phone; see `PRIVACY.md`. Not a medical or diagnostic device, and nothing to do with
real firearms training; see `DISCLAIMER.md`.

## Credits

Built with Claude by tbirdpower3000 and contributors. Hand tracking by Google
MediaPipe Tasks (Apache-2.0); 3D by three.js (MIT). Code released under the
Apache-2.0 licence (see `LICENSE`).
