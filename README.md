# Poly Guide

A mobile-first campus guide for Eldoret National Polytechnic. It is a static React/Vite app: there is no login, database, backend, or required environment configuration.

## Run locally

```sh
npm install
npm run dev
```

Build the static site with `npm run build`; Vite writes the deployable site to `dist/`.

## Campus data

The app reads `/public/data/places.json` and `/public/data/paths.json` at startup. Replace these with the files exported from Mapping mode, then run a new build and deploy. The current 20 places and walkway network are approximate placeholders. They demonstrate the data format and routing, but are not surveyed campus data; replace them before relying on route accuracy.

`places.json` is an array of objects with `id`, `name`, `category`, `lat`, `lng`, `description`, and `entrance_note`. Categories are `gate`, `lecture_hall`, `lab`, `workshop`, `library`, `admin`, `hostel`, `canteen`, `sports`, `toilets`, and `other`.

`paths.json` is a GeoJSON `FeatureCollection` of `LineString` features. Coordinates follow GeoJSON order: `[longitude, latitude]`. Each line can have an optional `name` property.

## Mapping mode

Open `/mapping` directly on the deployed or local site; the route is intentionally not linked from the student map. Tap **Place**, then tap the map to enter a place name, category, description, and entrance note. Tap **Draw walkway**, add line points on the map, and choose **Save walkway**. Undo removes the last point in the current line. GPS displays your current position when the browser grants location access. Mapping edits are stored in this browser's `localStorage` until cleared or exported.

Use **Export places** and **Export paths** to download the two JSON files. Copy both into `public/data/` (overwriting the placeholders), then redeploy. Mapping edits are local to the device/browser where they were collected; export them before switching devices or clearing browser storage.

## Routing and map

The routing helper in `src/main.jsx` turns each walkway vertex into a graph node, weights edges by walking distance, snaps both endpoints to their nearest mapped vertex, and runs Dijkstra's algorithm. If the graph cannot connect the endpoints, the app draws a straight dashed approximate route. The campus map is constrained to the bounds declared near the top of `src/main.jsx`. OSM street tiles and Esri satellite imagery need an internet connection. The service worker caches the app shell and campus JSON after the first visit, so the interface and route data can reopen offline; map tiles may depend on the browser's existing tile cache.

## Deploy to Vercel

Import this project into Vercel and use the detected Vite defaults (`npm run build`, output directory `dist`). No environment variables or server functions are needed. Vercel's Vite static deployment serves `/mapping` through the SPA fallback.

## Language and styling

English interface text is centralized in the `COPY` object in `src/main.jsx`. CSS theme colors are defined as variables at the top of `src/style.css`.
