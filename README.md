# JanYatra

A responsive, dependency-light JanYatra web prototype built for simulated transport data.

## Architecture

- `index.html` — semantic application shell, navigation, SOS dialog.
- `styles.css` — centralized JanYatra tokens, responsive layouts, reusable visual primitives, dark mode, and reduced-motion support.
- `app.js` — hash-route views, shared UI render helpers, controlled interactions, and the data-service boundary.

## Live telemetry

The client attempts to connect to a real backend whenever it is served over HTTP(S):

- `GET /api/buses` — initial fleet snapshot. Expected response is a bus array or `{ buses: [...] }`.
- `WS /api/telemetry` — incremental vehicle updates. Each message must include `id` or `busId` and may include `lat`, `lng`, `passengers`, `capacity`, `eta`, `next`, and `accessible`.

Set `window.JANYATRA_CONFIG` before `app.js` for a separate backend or map tile provider:

```js
window.JANYATRA_CONFIG = {
  apiBaseUrl: 'https://api.example.org',
  telemetryWsUrl: 'wss://api.example.org/api/telemetry',
  mapTilesUrl: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png'
};
```

When the initial service cannot be reached, the interface automatically switches to clearly marked **DEMO DATA** with simulated updates. If the initial API response succeeds but the WebSocket is unavailable, it shows **LIVE SNAPSHOT** and does not simulate further movement. It never presents the fallback as a live feed.

## Demo data boundary

All simulated data is held behind services in `app.js`. Replace these with authenticated API clients when the FastAPI service is available:

- `busService`
- `routeService`
- `occupancyService`
- `analyticsService`
- `alertService`

`savedJourneyService` persists saved route preferences and demo alert choices locally in the browser. `journeyInsightService` supplies shareable, explicitly estimated comfort, time-saved, and accessibility summaries. Connect these to authenticated account APIs before using them across devices.

## Map

Live Buses uses Leaflet with OpenStreetMap tiles, vehicle markers, stop markers, popups, and a stop-level route line. It degrades to the fleet list with an explanatory state if the map library is unavailable.

The interface deliberately labels all dynamic values as **DEMO DATA** or **DEMO / SIMULATED AI OUTPUT**. No live locations, AI inference, or emergency-service communication is claimed.

## Run

Open `index.html` in a modern browser, or serve this directory with any static web server.
