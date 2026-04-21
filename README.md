# Atmos — Live Weather

A single-page weather experience built with **React**, **TypeScript**, **Vite**, and **Tailwind CSS v4**. Search any city, see current conditions in a glass-style layout, and let the app quietly refresh data in the background. All weather and place data come from **[Open-Meteo](https://open-meteo.com/)** public APIs — no API keys required.

---

## GitHub repository description (copy-paste)

**Short (fits GitHub “About” description):**

> React + TypeScript weather app: city search (Open-Meteo geocoding), live current weather, glassmorphism UI, light/dark theme with persistence, and 5s auto-refresh. Vite + Tailwind v4.

**One-line tagline:**

> Live weather, beautifully visualized — search cities, see conditions, toggle themes.

---

## Features

| Feature | What it does |
|--------|----------------|
| **City search** | Type at least three characters; matching places load in a dropdown. |
| **Geocoding** | Uses Open-Meteo’s geocoding API to resolve a city name to coordinates and metadata (country, population). |
| **Current weather** | Fetches `current_weather` from Open-Meteo’s forecast API for the selected coordinates. |
| **Auto-refresh** | After a city is selected, weather data is refetched every **5 seconds** so the card stays “live.” |
| **Condition mapping** | WMO weather codes are mapped to **sunny**, **cloudy**, **rainy**, **snowy**, **stormy**, or **unknown** for labels and icons. |
| **Empty state** | Before a search, a compass-themed placeholder invites the user to search. |
| **Theme toggle** | **Light** and **dark** modes switch the entire palette; preference is stored in **`localStorage`** under `weatherMode`. |
| **Responsive layout** | Main column and grids adapt from mobile widths to wider breakpoints (`md:`). |

---

## Tech stack

- **React 19** — UI and state
- **TypeScript** — Typed models for API responses
- **Vite 8** — Dev server and production build
- **Tailwind CSS v4** — Utility styling via `@tailwindcss/vite`
- **react-icons** — Weather, UI, and brand icons

---

## Project structure

```
Weather-App/
├── index.html
├── vite.config.ts          # React + Tailwind plugins; base path for GitHub Pages
├── public/
│   ├── favicon.svg
│   └── icons.svg
├── src/
│   ├── main.tsx            # App entry, mounts <App />
│   ├── App.tsx             # Layout shell and shared city state
│   ├── index.css           # Design tokens, light/dark themes, global body styles
│   ├── components/
│   │   ├── Bar.tsx         # Top bar: “Atmos” branding + theme toggle
│   │   ├── Header.tsx      # LIVE badge, hero title, subtitle
│   │   ├── SearchBar.tsx   # Input, debounced-style search (3+ chars), suggestions
│   │   └── WeatherCards.tsx# Fetch weather, empty state, main weather card + stats
│   └── types/
│       ├── CityData.ts     # City + geocoding API response types
│       └── WeatherData.ts  # Open-Meteo current weather payload shape
```

---

## How it works

1. **`App.tsx`** holds one piece of state: the object returned from geocoding (`CityData` list wrapper) or `{}` before any selection. It renders the top bar, header, search, and weather card column in order.

2. **`SearchBar.tsx`**
   - On input change, if the string length is **≥ 3**, it calls  
     `https://geocoding-api.open-meteo.com/v1/search?name=...`
   - Results populate a suggestion list; clicking a row runs another geocode fetch for that name and passes the full JSON to `App` via `setCityPropFromSearchBar`, then clears suggestions and the input.

3. **`WeatherCards.tsx`**
   - If `cityPropToWeatherCard` has no `results`, it shows the **“Search a city to begin”** empty state.
   - When `results[0]` exists, it reads **latitude** and **longitude** and requests  
     `https://api.open-meteo.com/v1/forecast?latitude=...&longitude=...&current_weather=true`
   - **`useEffect`** runs the fetch when the city prop changes; a second effect sets a **5s `setInterval`** to refetch while that city is selected.
   - The main card shows **country**, **city name**, **temperature**, a **vibe label** from `getWeatherCondition`, a large **weather icon**, and a grid of **wind speed**, **observation time**, **wind direction**, and **population** (from geocoding).

4. **`Bar.tsx`**
   - Reads initial theme from `localStorage` (`weatherMode`) or defaults to `light`.
   - Sets `document.documentElement.classList` to `light` or `dark` so **`index.css`** theme blocks apply.

5. **`Header.tsx`**
   - Static marketing copy: pulsing **LIVE** pill, gradient-styled headline span (`#griid`), and supporting text.

---

## Design system

The UI is built around **soft gradients**, **frosted / glass surfaces**, and **CSS custom properties** defined in `src/index.css`.

- **Light theme (`:root`)** — Sky blues and lavender in a diagonal background gradient; navy primary text; violet secondary accents; warm **sunny** and cool **rainy** accents for icons.
- **Dark theme (`.dark` on `<html>`)** — Deep blues and violet for an atmospheric night look; adjusted glass borders and input translucency.
- **Headline accent** — The phrase *“Beautifully Visualized”* uses `background-clip: text` with `--headline-gradient`.
- **Components** — Cards and inputs use semi-transparent backgrounds (`--input-bg`, `--glass-bg`) and subtle borders (`--glass-border`) for a consistent **glassmorphism** feel.
- **Tailwind** — Utilities reference tokens like `bg-(--background-gradient)`, `text-(--text-secondary)`, and `border-(--glass-border)` so the same markup works in both themes.

---

## Scripts

| Command | Purpose |
|--------|---------|
| `npm install` | Install dependencies |
| `npm run dev` | Start Vite dev server |
| `npm run build` | Typecheck (`tsc -b`) and production build |
| `npm run preview` | Serve the production build locally |
| `npm run lint` | Run ESLint |

---

## Local development

```bash
npm install
npm run dev
```

Open the URL Vite prints (usually `http://localhost:5173`).

---

## Deployment (GitHub Pages)

`vite.config.ts` sets `base: "/Weather-App/"` so assets resolve correctly when the site is hosted at  
`https://<username>.github.io/Weather-App/`.

- Build: `npm run build`
- Publish the **`dist`** folder to that path (for example with GitHub Actions **“Deploy static content”** or any static host that mirrors this subpath).

If you deploy at the **root** of a domain instead, change `base` to `"/"` in `vite.config.ts` before building.

---

## APIs (reference)

- **Geocoding:** [Open-Meteo Geocoding API](https://open-meteo.com/en/docs/geocoding-api)
- **Forecast (current only):** [Open-Meteo Forecast API](https://open-meteo.com/en/docs) — `current_weather=true`

Terms and attribution for Open-Meteo apply; see their documentation for production use.

---

## License

This project is provided as-is for learning and portfolio use. Add a `LICENSE` file if you plan to open-source formally.
