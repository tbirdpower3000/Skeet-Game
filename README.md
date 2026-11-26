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

## The practice step

Before a round with the finger gun, a disc hangs still straight ahead, and the game asks
for one thing at a time:

1. **Point your finger at the disc.** The ring must rest on it for half a second.
2. **Stick your thumb straight up.** Held steady for half a second, this becomes your
   own "thumb up".
3. **Drop your thumb onto your finger.** Where it comes to rest becomes your "thumb
   down", and the disc breaks.

Three discs, each in a slightly different place. Each line of the prompt says what the
game is waiting for, so it shows which part is not being read: the hand, the aim or the
thumb. It follows the training page's pinch step (open hand, firm pinch, three times):
the player learns the gesture and the game learns the player. The viewer's button skips
it. **Practice first** on the setup page stays ticked until it has been done once.

## How the trigger and aim work

These carry over what the hand-control training page and Hand Hub learned about
pinches and cursors:

- **The reading is a gap.** How far the thumb's tip is from the base of the index finger
  (from its knuckle to its first joint), as a share of the hand's size. Thumb up, the gap
  is wide; thumb dropped onto the finger, it closes. It is measured on the tracker's 3D
  hand and goes through the One Euro filter (3 Hz, beta 1).
- **Tried and dropped: an angle.** v0.15 read the angle between thumb and finger and
  fired from perpendicular to parallel. On a real hand it read worse. Seen from behind, a
  finger pointing away from the camera is nearly end-on, so its direction leans on the
  tracker's guess at depth; a gap between two points plainly in the picture does not.
- **Frames that make no sense are ignored.** If the thumb's length is wrong for the hand
  (the tracker has mixed up its points), the last good reading is kept, as the training
  page does for the pinch.
- **You can see what the game sees.** Drawn over your hand: a line along the index
  finger, a dot on the fingertip, a dot on the thumb's tip, and a line across the gap
  being measured. The gap line is gold while the trigger is ready, grey once the thumb
  has dropped.
- **Two levels with a gap.** The shot fires on the way down, 35% of the way up from
  thumb-down; the trigger is cocked again 60% of the way up. A reading hovering near
  one level can't fire twice.
- **Learning your thumb.** The practice step measures your own "up" and "down" (see
  above), and they are kept for next time (`skeetVRTrigger3`). After that they move only
  slowly. Without the practice they are learned from where your thumb comes to rest.
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
- **Easy on the arm** (an option, off unless chosen). Your head does the big aiming and your hand the
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

## Versions

The version is shown beside the title on the setup page and saved with every round
(`ver`). It goes up by one with every change to the game. Newest first.

- **v0.16** (26 Nov 2026): The trigger goes back to the first measure, the gap between the thumb's tip and the base of the index finger, which read better on a real hand than v0.15's angle; the practice step now measures your own gap. The view draws your index finger, fingertip, thumb tip and the gap between them, as the game sees them
- **v0.15** (26 Nov 2026): A practice step before the round (point at a still disc, thumb up, drop your thumb to break it, three times), which also measures your own thumb. The trigger now reads the angle between thumb and index finger and fires on the way from perpendicular to parallel
- **v0.14** (26 Nov 2026): Runs on the shared VR engine (the VR-Portal repo, engine 1.0.0): the drawing surface, lens fitting, text signs, filters, camera, hand finder, sound and saved settings are loaded from there and no longer kept in this page. Nothing should look or play differently
- **v0.13** (26 Nov 2026): Easy on the arm is now off unless chosen, so the crosshair follows the fingertip as before; a bolder crosshair; a blue dot on the fingertip when Easy on the arm is on; the Ready prompt says when the hand is found, with a count of sightings
- **v0.12** (26 Nov 2026): Version numbers: shown beside the title, saved with each round
- **v0.11** (26 Nov 2026): Hand finder: look at a part of the picture that follows the hand
- **v0.10** (26 Nov 2026): Hand finder: fall back to the processor, and say what went wrong
- **v0.9** (26 Nov 2026): Aim: rewound hit test, hand kept in the room, head-plus-hand aiming, per-shot rewind
- **v0.8** (26 Nov 2026): Ask the camera for 60 pictures a second
- **v0.7** (26 Nov 2026): Fit the picture to the lenses of a Google Cardboard (2015)
- **v0.6** (23 Nov 2026): Keep the field in front of the player
- **v0.5** (23 Nov 2026): Gaze shots that fire by themselves, and a safer camera switch
- **v0.4** (23 Nov 2026): Say inside the viewer why the hand is not being used
- **v0.3** (23 Nov 2026): Bigger clays, set further back, easier to hit; houses hidden
- **v0.2** (23 Nov 2026): Trigger and aim: carry over the training page's lessons
- **v0.1** (23 Nov 2026): Skeet VR: clay shooting with a finger gun, first version

## Design notes

**The shared engine.** About half of this page used to be the same code as Fruit Slicer
VR's, copied by hand. That half now lives once, in the
[VR-Portal](https://github.com/tbirdpower3000/VR-Portal) repo, and this page loads it
from `https://tbirdpower3000.github.io/VR-Portal/v1/vr-engine.js`. The page keeps what is
Skeet's own: head tracking, the hand-in-the-room aiming, the thumb trigger, the clays
and the scoring. If the engine cannot be loaded, the setup page says so and Start is
switched off.

**Finding a small hand through the widest camera.** The game always opens the widest
rear camera, and through it a hand at arm's length is only about a tenth of the
picture's width. The hand finder first shrinks the picture to a small square, where a
hand that size cannot be made out. So the hand finder is given a part of the picture
half as wide and half as tall, in which the hand looks twice the size. The part follows
the hand; while no hand is found it steps round the picture (middle, below, sides,
above, then the whole picture for a hand held close). The view behind the game still
shows the whole wide picture.

**The hand finder's two ways of running.** It is tried on the phone's graphics chip
first. If it cannot be set up there, or fails on twenty pictures running, it is made on
the ordinary processor instead. After a round, the line under Start says which is in
use and what went wrong, and both are saved with the round.

**Safari's address bar in the viewer.** On an iPhone a web page cannot hide Safari's
address bar or toolbar: iPhone Safari does not offer full screen to pages (Android
browsers and iPads do). So the page does not try. The ways to play without the bar:

- **Add to Home Screen** (Share → Add to Home Screen) and open the game from its icon.
  It then runs with no browser bars. This is the recommended way. Rounds saved in a
  Safari tab are kept separately from rounds saved from the icon.
- **Hide Toolbar** from Safari's page menu (the aA button). The bar returns when the
  top of the screen is tapped, so it is less dependable inside a viewer.
- A wrapper app with no browser bars (the sideloaded Fruit VR app).

The lens fitting measures up from the bottom edge of the picture, which is the edge
the phone rests on in the viewer. A bar across the top therefore costs a strip of
picture but does not move the picture off the lenses.

Not built yet: asking for full screen on Start where the browser allows it (Android,
iPad), and a one-line tip on the setup page when the game is open in an iPhone Safari
tab.

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
