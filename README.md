# MIT OpenCourseWare Zoomable Sunburst

An interactive D3.js sunburst chart visualization of [MIT OpenCourseWare](https://ocw.mit.edu) courses.

## Quick Start

1. Open `index.html` in a browser, or
2. Serve locally: `python3 -m http.server 8000` then visit `http://localhost:8000`

## Features

- **Zoomable:** Click segments to zoom in, click center to zoom out
- **Tooltips:** Hover to see course details
- **Navigate:** Click a class to go to its OCW course page
- **3-level hierarchy:** Subject → Century Range → Class

## Data

- 2,573 courses across 42+ subjects
- Course data in `mit-ocw-data.json`
- Raw TSV in `courses.tsv`

## Tech

- D3.js v7
- Vanilla JavaScript (no build step)

## Credits

Data sourced from [MIT OpenCourseWare](https://ocw.mit.edu).

---

See [implementation_plan.md](implementation_plan.md) for details.
