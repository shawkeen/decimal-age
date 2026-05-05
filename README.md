# age

A brutalist terminal-aesthetic, real-time age counter. Enter your birthdate and watch your age tick forward to 24 decimal places — live, in your browser, never leaving your device.

![age counter preview](preview.png)

---

## What it does

- Displays your age as a continuously updating decimal number in years (`25.847392018475920183746251...`)
- Live stats: days, hours, minutes, and seconds alive — all ticking in real time
- Progress bar showing how far through the current year of your life you are
- Countdown to your next birthday (days · hours · minutes · seconds)
- Stores your birthdate in `localStorage` — no re-entry needed on refresh
- Page title mirrors your live age (bookmark it, it updates)
- Zero dependencies, zero backend, zero data collection — single HTML file

---

## How it works

### Core math

```js
const YEAR_SECONDS = 365.2425 * 24 * 60 * 60; // Julian/Gregorian year
const ageSeconds   = (Date.now() - birthdate.getTime()) / 1000;
const age          = ageSeconds / YEAR_SECONDS;
```

`365.2425` is the mean Gregorian calendar year, accounting for leap years (97 leap years per 400 years), making the displayed value astronomically accurate.

### Smooth interpolation

To prevent the displayed number from jumping in discrete steps each frame, a [lerp](https://en.wikipedia.org/wiki/Linear_interpolation) is applied:

```js
smoothAge += (trueAge - smoothAge) * 0.25;
```

This chases the true value each frame. At 60 fps, the displayed number always appears to be flowing forward even though `Date.now()` only has millisecond resolution.

### `requestAnimationFrame` loop

The entire update loop runs inside `requestAnimationFrame`, which:
- Syncs to the display refresh rate (usually 60 fps)
- Automatically pauses when the tab is hidden (saves CPU)
- Provides a high-resolution timestamp

### Persistence

```js
localStorage.setItem('age_birthdate_v1', birth.toISOString());
```

Birthdate is stored as an ISO 8601 string. On page load, if a valid entry exists, the counter launches immediately without showing the input form.

---

## Usage

### Option 1 — Open directly

Download `age.html` and open it in any browser. No server needed.

### Option 2 — Serve locally

```bash
python3 -m http.server 8080
# then visit http://localhost:8080/age.html
```

### Option 3 — Deploy to GitHub Pages

1. Fork this repository
2. Go to **Settings → Pages**
3. Set source to `main` branch, root `/`
4. Your counter will be live at `https://<your-username>.github.io/<repo-name>/age.html`

---

## Features at a glance

| Feature | Details |
|---|---|
| Decimal precision | 24 decimal places |
| Year constant | 365.2425 days (Gregorian mean) |
| Smoothing | Lerp factor `0.25` per frame |
| Persistence | `localStorage` — never expires |
| Next birthday | Live countdown: `d h m s` |
| Progress bar | Fraction of current year elapsed |
| Stats panel | Days / Hours / Minutes / Seconds alive |
| Page title | Mirrors live age (10 decimal places) |
| Network requests | Zero (except Google Fonts) |
| Data sent | None |

---

## Customisation

| Variable | Location | What it changes |
|---|---|---|
| `DECIMALS` | top of `<script>` | Decimal places shown (default `24`) |
| `YEAR_SECONDS` | top of `<script>` | Year length constant |
| Lerp factor `0.25` | `tick()` function | Smoothing speed (0 = instant, 1 = frozen) |
| `--fg` CSS variable | `:root` | Primary phosphor colour |
| `--bg` CSS variable | `:root` | Background colour |

To change the theme colour from phosphor green to amber, update `:root`:

```css
:root {
  --fg: #ffb347;
  --fg-dim: #cc8822;
  --fg-muted: #664411;
  --glow: 0 0 12px rgba(255,179,71,0.35);
  --glow-strong: 0 0 24px rgba(255,179,71,0.6);
}
```

---

## Privacy

All computation happens entirely in the browser. Your birthdate is stored only in your own browser's `localStorage` and is never transmitted anywhere. The only outbound request is loading two fonts from Google Fonts (`Share Tech Mono` and `VT323`). To make it fully offline, host the fonts locally or replace them with a system monospace font.

---

## Browser support

Any modern browser (Chrome, Firefox, Safari, Edge). Requires JavaScript enabled.

---

## License

MIT
