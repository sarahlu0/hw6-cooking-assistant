# Cooking Buddy: a voice-guided cooking app

Cooking Buddy talks someone through making turkey chili, one short step at a time. It is designed for
Marcus, a 24-year-old with Down syndrome who wants to cook dinner by himself. He speaks softly, pauses
to think, gets overwhelmed by long instructions, and sometimes forgets whether he finished a step,
especially turning off the stove.

The app works by voice. After pressing **Start**, Marcus can cook the whole recipe without looking at
or touching the screen.

The whole app is one file, `index.html` (HTML, CSS, and plain JavaScript). It has no frameworks, no
build step, and no API keys. It uses the browser's Web Speech API: `SpeechRecognition` to listen and
`speechSynthesis` to talk.

## How to run it

You need **Google Chrome on a computer**, a microphone, and an internet connection. Chrome sends speech
to Google's speech service to turn it into text.

**Option A (recommended): a local web server.** Chrome remembers the microphone permission this way.

```bash
cd hw6-cooking-assistant
python3 -m http.server 8000
```

Then open <http://localhost:8000> in Chrome.

**Option B: open the file directly.** Double-click `index.html` or drag it into Chrome. This works, but
Chrome may ask for microphone permission every time.

Then:

1. Click **Start** and allow the microphone when Chrome asks.
2. The app asks, "What would you like to cook?" Say "turkey chili", then "yes".
3. Follow the steps. Say **done** after each one.

## How it behaves

- **One step at a time.** It says one short instruction and waits. It never moves on by itself, and it
  never moves on because of speech it isn't sure about. (The one exception is the 10-minute simmer
  timer, which moves on when the time is up.)
- **Patient listening.** It waits for 2.5 seconds of silence before acting, so "I want to make...
  turkey chili" counts as one sentence. Listening restarts by itself and never times out. The
  microphone is paused while the app talks, so it doesn't hear itself.
- **Slow, short speech** (rate 0.85). Every step also has a simpler version.
- **Checks instead of guessing.** If it only partly understands, it asks a yes/no question ("Did you
  want me to repeat the oil step?"). If it has no good guess, it says what he can say.
- **Remembers progress.** The current step and confirmed steps are saved in `localStorage` after every
  confirmation, so reloading the page picks up where he left off. The burner-off confirmation is saved
  with the date and time.
- **Temperature safety.** It reads numbers like "165" and "one sixty-five", repeats them back to check
  ("I heard 165 degrees. Is that right?"), and only continues at 165°F or more, on two pieces of turkey.

## Things Marcus can say

He doesn't need exact words. These are examples:

| He wants to... | He can say |
| --- | --- |
| Finish a step | "done", "finished", "okay", "got it", "it's in" |
| Hear it again | "repeat", "say that again", "what?", "huh?", "I didn't hear" |
| Hear it a simpler way | "I don't understand", "explain", "I'm lost", "what does that mean" |
| Hear the last step | "go back", "last step", "previous" (it does not undo anything) |
| Find his place | "where was I", "where am I", "what was I doing" |
| Hear the next step | "what's next" (it does not move forward) |
| Get more time | "wait", "hold on", "not ready" |
| Check the stove (any time) | "did I turn off the stove", "is the burner off" |

The temperature-check step waits for "ready" instead of "done". After dinner, it waits for "I'm back".

**About the stove question:** the app can't see the stove. It only reports what Marcus told it, for
example: "You confirmed the burner was off today at 6:42 PM. I can't check the stove myself." When he
turns the burner on during the recipe, any older "burner off" record is cleared so it can't be
misleading.

## Screen and testing controls

The screen is secondary. Nothing on it is needed to cook.

- Big current instruction, step number, and the yes/no question the app is waiting on.
- "I heard" caption and a log of the conversation.
- **Demo mode:** shrinks the 10-minute simmer timer to 20 seconds, with stir reminders every 6 seconds.
- **Jump to step:** goes straight to any step. Earlier steps count as confirmed.
- **Reset:** clears saved progress and the burner record, and starts over.
- **Stop mic / Resume mic:** a small button that turns listening off and on. The app still talks, and
  typed commands still work while the mic is off.
- **Type a command:** type anything you would say (for example "done" or "165") if the microphone
  isn't working.

## Error handling

- **Microphone blocked:** a message explains how to allow it, and a "Try the microphone again" button
  appears. Typed commands still work.
- **Browser without speech recognition** (Safari, Firefox): a message says to use Chrome. You can still
  press Start and type commands.
- **Recognition errors** (silence, network hiccups): listening restarts quietly.

## Limitations

- Chrome desktop only, and it needs internet for speech recognition.
- Saved progress doesn't expire. If a recipe is left half done, the next Start resumes it. Use Reset to
  start over.
- Reset also clears the burner-off record, because it is meant for demos.
