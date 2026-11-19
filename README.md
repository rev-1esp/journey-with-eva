# Journey with Eva

**An interactive trip through the fifth dimension, where you can see time and feel it physically.**

Journey with Eva is a 3D web experience built for students and young space enthusiasts. Instead of reading a document about space history, you fly up a glowing timeline of cubes and visit the machines humans left behind on the Moon and Mars. Each machine sits in its own room, and a small floating robot named **Eva** tells you its story and answers your questions out loud.

**Live demo:** https://rev-1esp.github.io/journey-with-eva/

Built for the NASA Space Apps Challenge, around the idea of machines that are *abandoned but not forgotten*.

---

## The idea

Space history is usually taught as dates and lists, which can feel boring. We wanted something you can *feel*.

The project is inspired by the tesseract scene in *Interstellar*, where a character moves through a structure in which time becomes a physical place you can walk through. In Journey with Eva, **time is a direction you can travel**: the higher you go, the later the year. A timeline bar at the top shows the year and month as you move, and it runs faster when you accelerate.

> **A note on the science:** travelling through time like this is a visual metaphor built on a speculative idea from astrophysics, that extra dimensions might let us treat time as something we can move along. It is a way to make history tangible, not an established physical theory. Eva is also honest about this: if something is only a theory, she says so.

---

## What you can do

- Fly along two timelines, one for the **Moon** and one for **Mars**.
- Stop at nine abandoned machines, ordered from oldest to newest, with empty cubes between them so you can *feel* the years passing.
- Step into a machine's cube, which opens into a realistic surface: a cratered lunar landscape with Earth in the black sky, or a Martian plain with dust storms.
- Talk with **Eva**, who narrates each machine's story and answers your questions, in a spoken voice with captions.
- Follow **Wally**, a small robot guide who rolls with you between machines and tells you where you are.

### The machines

| World | Machine | Year |
|---|---|---|
| Moon | Luna 9 | 1966 |
| Moon | Surveyor 3 | 1967 |
| Moon | Apollo 11 "Eagle" | 1969 |
| Moon | Lunokhod 1 | 1970 |
| Mars | Viking 1 | 1976 |
| Mars | Mars Pathfinder | 1997 |
| Mars | Spirit | 2004 |
| Mars | Opportunity | 2004 |
| Mars | Phoenix | 2008 |

### Controls

| Key | Action |
|---|---|
| Mouse | Aim |
| `W` `A` `S` `D` | Move |
| `Q` / `E` | Down / up |
| `Shift` (hold) | Go faster (the speed keeps building) |
| `M` | Mute sound |

You can only travel along the bold lines of the timeline.

---

## How it works (technical overview)

Everything runs in the browser. There is no build step and no backend: the project is one HTML file plus one small file that holds the API key.

### Front end: HTML, CSS and JavaScript

- **HTML:** a single `index.html` contains the whole experience: the page structure, styles and scripts, including the opening scene and Wally's 3D model.
- **CSS:** custom styling for the heads-up display (HUD), the timeline bar, speech bubbles and the machine rooms. The palette is dark walnut, bronze and brushed brass, matched to the look of the *Interstellar* tesseract. The layout is responsive, with separate rules for phones and tablets, and it respects `prefers-reduced-motion`.
- **JavaScript:** plain ES modules, with no framework. It handles the camera and guided travel, the timeline, the machine rooms, the chat with Eva, and all audio.

### 3D graphics

- **[Three.js](https://threejs.org/) (v0.168.0)**, loaded through an import map from unpkg.
- Custom **GLSL shaders** (`ShaderMaterial`) for the sky, the swirling dust columns and the dust wall on Mars, and the glowing light on each machine box.
- Procedural terrain with noise-based craters for the Moon and dunes for Mars, and 3D models of each machine built from code.

### AI: the LLM that powers Eva

- **[Google Gen AI SDK](https://www.npmjs.com/package/@google/genai)** (`@google/genai`), loaded in the browser.
- **Chat model:** `gemini-3.8-flash`, with automatic fallbacks (`gemini-3.6-flash`, then `gemini-3.5-flash-lite`) that are tried if the main model is busy or unavailable.
- **Thinking:** the model runs with `thinkingLevel: "low"`, so Eva answers quickly instead of making students wait.
- **Generation settings:** temperature `0.8`, up to `2048` output tokens.
- **System prompt:** Eva is written as a gentle teacher for students aged about 9 to 15. Her answers are short and spoken (around 4 to 8 sentences), start from an everyday comparison before the real science, use plain text only, and are honest about uncertainty. She never invents facts.
- **Knowledge base per machine:** each machine has its own set of facts that Eva may use. Every fact is tagged with how confident we are and how to say it (for example, "state as fact" or "say *about*"), and with its source. Sources include NASA NSSDCA, NASA Science, the National Research Council, the Smithsonian National Air and Space Museum, Space.com, Astronomy.com and Wikipedia. This keeps her grounded in real data.
- **Quick questions:** suggested questions with pre-written answers keep Eva useful even when the AI is unavailable.
- **Friendly limit message:** if the free quota is used up, Eva tells the visitor she has reached her request limit and to try again later.

### Text to speech

- Eva's voice comes from **Gemini text-to-speech models** (`gemini-3.8-flash-tts`, with `gemini-3.8-flash-lite-tts` and `gemini-3.1-flash-tts-preview` as fallbacks), using the **Despina** voice.
- Sentences are generated and queued one at a time, and cached so a repeated line plays instantly.
- A **Web Audio API** cleanup pass improves the voice: it removes hiss and static, softens harsh "s/sh" sounds, and trims the small burst of noise the voice model adds at the end of each request.
- The browser's robotic built-in voice is deliberately **not** used as a fallback, so a failed sentence stays silent while its caption still shows.

### Sound design

- Sound effects (the mysterious acceleration hum, Wally's chirps and blips) are generated live with the **Web Audio API**, so no audio files are needed.

---

## Run it yourself

1. Download or clone this repository.
2. Create a file named `API_new.js` in the same folder as `index.html`, with this content:

   ```js
   window.EVA_SECRETS = { API_KEY: "YOUR_GEMINI_API_KEY" };
   ```

   You can get a free key from [Google AI Studio](https://aistudio.google.com/).
3. Open `index.html` in a modern browser (Chrome, Edge, Firefox or Safari) with an internet connection, since the 3D library and AI SDK load from the web.

> **About the API key:** because this is a browser-only project, the key is visible to anyone who opens the site. We use a free key, so the worst case is that the free quota runs out. If you fork this project, use your own free key and consider restricting it to your website's domain in Google AI Studio.

### Files

| File | Purpose |
|---|---|
| `index.html` | The whole experience (HTML, CSS and JavaScript) |
| `API_new.js` | Holds the API key (`window.EVA_SECRETS`) |
| `.gitignore` | Keeps unused files out of the repo |

---

## Team

| Role | Person |
|---|---|
| Front end: all of the HTML, CSS and JavaScript, the 3D world and the experience design | [Your name](https://github.com/rev-1esp) |
| LLM: Eva's AI, prompts and conversation | Maria [last name] |
| Machines: the 3D machine models inside the experience, plus a large part of the data collection | [Friend's name] |
| Data: research and data work | [Friend's name] |

---

## Credits and acknowledgements

- Inspired by the tesseract scene in *Interstellar*.
- Mission facts from NASA, the National Research Council, the Smithsonian and other sources listed in each machine's knowledge base.
- 3D rendering by Three.js; AI and voice by Google Gemini.
