# Implementation Plan: Zoomable Sunburst Chart for MIT OpenCourseWare Classes

This document outlines the steps to create a zoomable sunburst chart visualizing the hierarchy of MIT OpenCourseWare classes using the D3.js library.

## Project Status: Complete ✓

The MIT OCW zoomable sunburst chart is live at: **https://github.com/fuzheado/wikimit-sunburst**

## Phase 1: Data Extraction and Preparation

1.  **Fetch `sitemap.xml`:**
    *   Retrieve the content of `https://ocw.mit.edu/sitemap.xml` using a web fetching tool.
    *   **Status:** Completed - Found ~2,573 course URLs.

2.  **Parse `sitemap.xml` and Extract Class Data:**
    *   Iterate through each `<loc>` tag within the XML to identify course URLs.
    *   For each course URL (e.g., `1-012-introduction-to-civil-engineering-design-spring-2002` from `ocw.mit.edu/courses/...`), extract the following five fields using regular expressions:
        *   Subject course number (e.g., "1", "HST")
        *   Class number (e.g., "012")
        *   Class title (e.g., "introduction-to-civil-engineering-design")
        *   Class semester (e.g., "spring", "fall", "summer")
        *   Class year (e.g., "2002")
    *   **Output:** `courses.tsv` - Tab-separated file with 2,573 courses
    *   **Format:** `subject_number	class_number	title	semester	year	url`

3.  **Map Subject Course Numbers to Titles:**
    *   Create a mapping where subject course numbers (e.g., "1", "HST") are associated with their full descriptive titles (e.g., "Civil and Environmental Engineering", "Health Sciences and Technology").
    *   **Status:** Completed - Used common MIT subject mappings + reasonable guesses for unmapped subjects.

4.  **Structure Data for D3 Sunburst (v2 - with Hundred Ranges):**
    *   Transform the extracted and mapped class data into a hierarchical JSON format with **three levels**.
    *   **Output:** `mit-ocw-data.json` - Hierarchical JSON structure
    *   **Structure:**
        ```json
        {
          "name": "MIT OpenCourseWare",
          "children": [
            {
              "name": "1",
              "title": "Civil and Environmental Engineering",
              "children": [
                {
                  "name": "000s",
                  "title": "Classes 000-099",
                  "children": [
                    {
                      "name": "012",
                      "title": "Introduction To Civil Engineering Design",
                      "fullTitle": "1-012-introduction-to-civil-engineering-design",
                      "semester": "Spring",
                      "year": "2002",
                      "url": "https://ocw.mit.edu/courses/..."
                    }
                  ]
                }
              ]
            }
          ]
        }
        ```
    *   **Stats:** 42 subject areas, thousands of courses across hundred ranges

## Phase 2: Implementation and Visualization

1.  **D3 Integration and HTML Setup:**
    *   Create an `index.html` file to host the visualization.
    *   Include the D3.js library (via CDN: `https://d3js.org/d3.v7.min.js`).
    *   Load hierarchical JSON data dynamically via `d3.json("mit-ocw-data.json")`.
    *   **Status:** Completed - Data loaded from separate JSON file for scalability.

2.  **Enhance User Interaction (v2 Updates):**
    *   **Three-Level Hierarchy:** Subject → Hundred Range (000s, 100s, 200s...) → Class
        *   Solves issue of too many classes in a single slice (e.g., subject "6" had 216 classes)
        *   Classes grouped by hundreds: "000s" (0-99), "100s" (100-199), "200s" (200-299), etc.
    *   **Hover Functionality:**
        *   Depth 1: Subject title + course count
        *   Depth 2: Hundred range name + class count
        *   Depth 3: **fullTitle** (e.g., "1-012-introduction-to-civil-engineering-design") + semester/year
    *   **Click Functionality (Zoom In/Out):**
        *   Click Subject → zooms to Hundred Ranges
        *   Click Hundred Range → zooms to Classes
        *   Click Class → navigates to OCW course page
        *   Click center → zooms out to previous level
    *   **Color Coding:** Different colors for each subject area using D3's category color scale.

## Phase 3: Self-Verification and Testing

*   **Data Integrity Check:** Verified courses.tsv contains 2,573 courses with all 5 columns populated.
*   **JSON Structure Validation:** Verified mit-ocw-data.json structure is compatible with D3 hierarchy and partition layouts.
*   **Visualization Testing:** 
    *   Server started on localhost:9999
    *   Verified correct rendering of sunburst chart with subject segments
    *   Verified hovering shows fullTitle at class level
    *   Verified hundred range grouping reduces slice density
    *   Tested zoom in/out navigation at all levels
    *   **Status:** All tests passed - 0 JavaScript console errors

## Phase 4: Deployment

*   **GitHub Repository:** Created at https://github.com/fuzheado/wikimit-sunburst
*   **Push Status:** Code and tags pushed to GitHub
*   **Version:** v1.0.0 (initial), v1.1.0 (hundred range update)

## Files Created

| File | Description |
|------|-------------|
| `courses.tsv` | TSV with all courses (raw data) |
| `mit-ocw-data.json` | Hierarchical JSON for D3 sunburst |
| `index.html` | Main visualization |
| `implementation_plan.md` | This document |
| `.gitignore` | Git ignore rules |

## Key Design Decisions

1.  **Separate Data File:** Data is stored in `mit-ocw-data.json` instead of embedded in HTML for scalability and maintainability.
2.  **TSV for Intermediate Work:** `courses.tsv` preserves the raw extracted data for potential future reprocessing.
3.  **Dynamic Loading:** `index.html` uses `d3.json()` to load data at runtime.
4.  **Full Course Coverage:** Processed all 2,573 courses from the sitemap (exceeds original ~2,500 target).
5.  **Hundred Range Grouping:** Added intermediate level to prevent overcrowding at depth 2.
6.  **fullTitle Display:** Shows complete course identifier in tooltip for clarity.

## Git History

```
3cef1b7 Add hundred range hierarchy and fullTitle tooltip
e4d80e0 Initial commit: MIT OCW zoomable sunburst chart
v1.0.0 (tag)
```