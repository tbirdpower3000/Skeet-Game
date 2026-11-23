# Privacy

**The camera stays on your phone.** With the finger gun or "See my room" on, the page uses the phone's rear camera. A hand-tracking library (Google MediaPipe) runs in your browser to find your hand. The picture is processed on the phone and is never sent, recorded or saved.

**Motion sensors.** The page reads the phone's orientation to follow your head. Those readings are used only to draw the view and are not saved.

**What is saved.** After each round a few numbers are saved in this browser's local storage (`skeetVRRounds`, at most 200 rounds): the time, aim mode, clays thrown and hit, shots, how many were fired by thumb and by button, the range of the thumb readings, how far misses passed from the nearest clay, how often the hand was lost, the frame rate, the screen size and the camera's name as the phone reports it. They stay in that browser. "Clear" deletes them. "Copy results" puts them on your clipboard only when you press it.

**Third parties your browser contacts.** Loading the page fetches three.js from cdnjs, the hand-tracking library from jsDelivr and its model from Google's storage; those services see your IP address like any website you visit. The page itself has no analytics, cookies or accounts.

**Not a medical test, no warranty.** See `DISCLAIMER.md`.
