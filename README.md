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
- **My gaze.** The crosshair sits where you look; the viewer's button fires. Used
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
- **Keeping the shot on target.** Dropping the thumb nudges the whole hand, so a
  thumb shot goes where the crosshair was 120 ms earlier.
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

Each round is saved in the browser (`skeetVRRounds`): clays hit, shots, thumb and
button shots, the range of thumb readings, how far misses passed from the nearest
clay, flickers that were ignored, the trigger's learned levels, and how often the
hand was lost. **Copy results** on the setup page copies
them, which is what is needed to tune the trigger.

## Status

First version. The game, the trigger logic and the gaze mode have been tested in a
desktop browser with a simulated hand. The thumb trigger has not yet been tried
with a real hand in a viewer, so its thresholds are a starting guess.

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
