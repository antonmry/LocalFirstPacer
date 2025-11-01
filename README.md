# LocalFirst Pacer

LocalFirst Pacer is a hybrid web experience for planning, running, and tracking interval workouts. It is designed to work equally well on desktop and mobile, keeps state locally, and ships as an installable Progressive Web App so you can take it offline to the track or treadmill.

## Features

- **Interval timer** with automatic transitions, per-stage summaries, progress indicator, and quick actions to pause, resume, skip, or jump ahead ten seconds.
- **Web Bluetooth heart-rate monitor** integration that connects to compatible chest straps, plots live BPM history on a lightweight `canvas`, and adapts the UI color based on the target range of the active stage.
- **Visual workout editor** in a full-screen modal where you can add, reorder, duplicate, or delete stages and optional sub-phases with immediate validation and total duration summaries.
- **Session manager** that stores multiple named workouts, supports duplication and safe deletion, and offers a picker directly from the main card title.
- **Local-first persistence** using `localStorage` for both the active configuration and drafts so you always resume where you left off.
- **Automatic internationalization** that detects the browser language (Spanish/English) and applies translations without reloading or duplicating logic.
- **JSON import/export** so workouts can be backed up, shared, or restored instantly.
- **Installable PWA** with a custom manifest, icons, and a service worker that caches critical assets and handles updates seamlessly.

## Project Structure

- `index.html` – Single-page UI with embedded styles and vanilla JavaScript powering timers, validation, audio cues, HR logic, i18n wrappers, and PWA onboarding.
- `hrm.html` – Minimal standalone heart-rate panel useful for isolating Bluetooth tests.
- `manifest.webmanifest` – Web App Manifest with names, colors, description, and icon references for installation prompts.
- `service-worker.js` – Cache-first service worker with background update and version cleanup logic.
- `icons/` – Lightweight `192x192` and `512x512` icons used by the manifest and install banners.
- `TODO.md` – Backlog of potential enhancements and refactors.

## Running Locally

This project uses vanilla HTML, CSS, and JavaScript—no build step required. Any static file server works:

```bash
npx serve
# or
python -m http.server
```

Then open the reported URL (usually `http://localhost:3000` or `http://localhost:8000`). For Bluetooth testing use `https://localhost` or deploy over HTTPS, as Web Bluetooth requires a secure context.

## Heart-Rate Monitoring

1. Ensure your BLE heart-rate strap is advertising and paired with the OS when required.
2. Click the connect button inside the "Heart Rate" panel. The browser will prompt you to pick the device.
3. Once connected, BPM data appears in real time on the inline graph, while the UI highlights whether you are below, inside, or above the desired range for the current stage.
4. Use the disconnect toggle to release the device when you finish your session.

Chrome and Edge currently provide the most reliable Web Bluetooth experience. Mobile browsers may require experimental flags.

## Progressive Web App

- On first load the app registers `service-worker.js`, which pre-caches core routes (`./`, `index.html`, `manifest.webmanifest`, `hrm.html`) and serves them offline.
- When the `beforeinstallprompt` event fires, the UI reveals an "Install app" button. Accepting the prompt installs the PWA; subsequent visits keep the button hidden.
- The `appinstalled` event prevents duplicate prompts and can be used for telemetry or celebratory UI.

## Internationalization

All labels and runtime messages are routed through a small `t()` helper. The browser language is detected once on load, and users can switch between English and Spanish without reloading. To localize new strings, add them to the translation map in `index.html`.

## Data & Persistence

- Active workout plans, drafts, and the last selected session name are stored in `localStorage`.
- JSON export downloads the entire workout list; importing replaces the current list after validation and gives immediate feedback.
- Stage configurations can optionally split a phase into two sub-phases with independent duration and BPM targets, letting you model rest/run splits inside a single stage.

## Development Tips

- Test PWA install flows via Chrome DevTools → Application → Manifest, or force the prompt from the Service Workers panel.
- Use the `hrm.html` page for quick Bluetooth smoke tests without the full workout UI.
- Keep translations in sync after changing labels; missing keys fall back to English by default.

## Roadmap

- Preserve undo history when editing workouts before saving changes.
- Persist detailed heart-rate histories per session (e.g., IndexedDB) for later analysis.
- Export sessions as FIT files for interoperability with training platforms.
- Add podometer or cadence tracking for compatible devices.

## License

This project is licensed under the [Apache License 2.0](LICENSE).
