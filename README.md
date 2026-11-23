# Skeet VR

Clay-target shooting for a phone in a Google Cardboard viewer. Clays are thrown
from a high house on the left and a low house on the right and cross in front of
you. You aim with a finger gun, seen by the phone's rear camera and tracked in the
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
- A round is 20 clays: singles from each house in turn, and every third throw a
  double. A beep comes just before each throw.
- When a round ends, look at the board for two seconds or fire to play again.

## How the trigger works

- **The reading.** How far the thumb's tip is from the base of the index finger, as
  a share of the hand's length, measured on the tracker's 3D hand so that tilting
  the hand changes it little.
- **Learning "thumb up".** The largest readings lately count as up. The trigger is
  cocked above 75% of that and fires on the way down through 55%, so one drop of
  the thumb is one shot.
- **Keeping the shot on target.** Dropping the thumb nudges the whole hand, so a
  thumb shot goes where the crosshair was 120 ms earlier.
- A shot breaks any clay it passes within 1.6° of. Clays are drawn several times
  the size of real ones so they can be seen and hit 10 m away.

Each round is saved in the browser (`skeetVRRounds`): clays hit, shots, thumb and
button shots, the range of thumb readings, how far misses passed from the nearest
clay, and how often the hand was lost. **Copy results** on the setup page copies
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
