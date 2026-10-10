# Bharat Vision AI: Website

A region-aware AI image generation website for India, built as a single self-contained HTML file.
It has an animated 3D landing page and a logged-in app with **Create**, **Gallery** and **Profile** pages.

> **Prototype notice:** this file is a front-end prototype. Login is simulated and the image generator
> draws demo patterns in the browser. It can be connected to a real backend (see
> [Connecting your backend](#connecting-your-backend)).

## Features

### Landing page
- Full-screen hero with a rangoli background that tilts in 3D as you move the mouse
- Letter-by-letter animated title, flickering diya glows, floating embers and petals, cursor glow
- Feature cards with 3D tilt, a "three steps" section with an animated progress card, and a regions section
- Log in / Sign up pop-ups

### App (after login)
- **Sidebar navigation**: Create, Gallery, Profile and Sign out (becomes a bottom tab bar on mobile)
- **Indian-themed background**: your rangoli image, rotating mandalas and a temple skyline silhouette
- **Create**
  - 3D carousel of featured photos. Clicking the centre card fills the prompt and region.
  - Prompt box, state dropdown and cultural-touch chips (clothing, festivals, architecture, folk art, food, nature)
  - Live progress ("Step 12 of 25") with a blur-to-sharp reveal, then Download and Copy prompt
- **Gallery**
  - **3D view**: a draggable rotating ring of images with reflections
  - **Grid view**: cards that tilt toward your mouse
  - Region filter, large image pop-up, Download, Copy prompt and Delete (for your own images)
- **Profile**: name, state, city and cultural preferences, plus your image count and top region

## Tech stack

| Area | Used |
|---|---|
| Structure and logic | HTML, CSS, vanilla JavaScript (no framework, no build step) |
| 3D and motion | CSS 3D transforms, `requestAnimationFrame`, CSS `@property` conic-gradient borders |
| Drawing | HTML Canvas (embers, demo images), inline SVG (icons, skyline) |
| Storage | `localStorage` (profile and generated images) |
| Fonts | Google Fonts: Inter and Playfair Display |
| Images | Rangoli background and gallery photos embedded as base64 |

## How it works

### Login and sign up
There is no real authentication.

1. Log in and Sign up open the same pop-up.
2. `auth()` only checks that the email is not empty. The password is never checked or stored.
3. It looks for a saved profile in `localStorage` with the same email. If found, it is restored; otherwise a new profile is created.
4. The landing page is hidden and the app is shown. Sign out hides the app again.

Data stays in the current browser. For real accounts you need a backend (password hashing, sessions or tokens).

### Demo image generation
Images are generated patterns, not AI output.

1. Prompt + state become a text seed for a small seeded random-number generator, so the same input always gives the same image.
2. A 512×512 canvas is drawn with a radial gradient in the state's colour palette, scattered glowing dots, rings of rotated petals and a centre disc.
3. The canvas is exported as a JPEG and shown in the result panel.
4. The 25-step progress bar is simulated with a timer, while the image goes from blurred to sharp inside a growing circle.

The prompt text only changes the seed, so different subjects do not produce different content.

### Gallery storage
- The 8 built-in photos (the `PH` array in the script) are always shown and cannot be deleted.
- Your generated images are saved in `localStorage` under `bv_gal2` (the 12 most recent).
- The profile is saved under `bv_user`.

## Run it

Open `bharat-vision-ai.html` in a modern browser. No install or build is needed.

To serve it locally instead:

```bash
python -m http.server 5500
# then open http://localhost:5500/bharat-vision-ai.html
```

Needs a browser that supports CSS `@property` and `backdrop-filter` (current Chrome, Edge, Safari, Firefox).

## Connecting your backend

Near the top of the script, set the API address:

```js
const API = 'http://localhost:8000';   // empty string = demo mode
```

With `API` set, the page sends:

```
POST {API}/api/generate/stream
Content-Type: application/json

{ "prompt": "<prompt>, <state>, <selected cultural touches>" }
```

and reads Server-Sent Events from the response:

```
data: {"status":"generating","step":12,"total_steps":25,"progress":48}
data: {"status":"completed","progress":100,"image_url":"/generated_images/gen_123.png"}
data: {"status":"error","error":"Pipeline failure details"}
```

- `generating` updates the progress bar and step text.
- `completed` loads `API + image_url` as the result image and adds it to the gallery.
- `error` shows an error message.

Notes:
- Open the file locally (or host it) while your backend is running. A page hosted elsewhere cannot reach `localhost`.
- Your backend must allow the page's origin through CORS.
- The state and cultural touches are currently joined into the prompt string, not sent as separate fields. Edit the `stream()` function to send the fields your API expects.
- Real login would need new endpoints; replace the `auth()` function.

## Customising

| To change | Edit |
|---|---|
| Gallery and carousel photos | the `PH` array (title, state, prompt, base64 image) |
| States and colour palettes | the `ST` list and `PAL` object |
| Cultural-touch chips | the `PF` list |
| Brand colours | the CSS variables at the top (`--gold`, `--sa`, `--bg`) |
| Landing page text | the HTML sections `#features`, `#how`, `#regions` |

## Limitations
- Login is a demo and has no real security.
- Demo images are decorative patterns, not photographs or AI generations.
- The Download button may not work inside a published or embedded page; it works from the downloaded file.
- Images and profile live in one browser's `localStorage` only.

## License
MIT, matching the main Bharat Vision AI project.
