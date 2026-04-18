# Implementation Plan: Zoomable Sunburst Chart for MIT OpenCourseWare Classes

This document outlines the steps to create a zoomable sunburst chart visualizing the hierarchy of MIT OpenCourseWare classes using the D3.js library.

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

4.  **Structure Data for D3 Sunburst:**
    *   Transform the extracted and mapped class data into a hierarchical JSON format.
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
                  "name": "012",
                  "title": "Introduction To Civil Engineering Design",
                  "semester": "Spring",
                  "year": "2002",
                  "url": "https://ocw.mit.edu/courses/..."
                }
              ]
            }
          ]
        }
        ```
    *   **Stats:** 42 subject areas, 2,573 total courses

## Phase 2: Implementation and Visualization

1.  **D3 Integration and HTML Setup:**
    *   Create an `index.html` file to host the visualization.
    *   Include the D3.js library (via CDN: `https://d3js.org/d3.v7.min.js`).
    *   Load hierarchical JSON data dynamically via `d3.json("mit-ocw-data.json")`.
    *   **Status:** Completed - Data loaded from separate JSON file for scalability.

2.  **Enhance User Interaction:**
    *   **Hover Functionality:** Implemented mouseover events to display the full subject title when hovering over top-level subject course segments and the full class title when hovering over individual class segments.
    *   **Click Functionality (Zoom In/Out):**
        *   Clicking on a Subject Course Number segment zooms in to reveal the classes within that subject.
        *   Clicking on an individual class segment navigates the user to the corresponding course URL on `ocw.mit.edu`.
        *   Clicking the center zooms out to the top level.
    *   **Color Coding:** Different colors for each subject area using D3's category color scale.

## Phase 3: Self-Verification and Testing

*   **Data Integrity Check:** Verified courses.tsv contains 2,573 courses with all 5 columns populated.
*   **JSON Structure Validation:** Verified mit-ocw-data.json structure is compatible with D3 hierarchy and partition layouts.
*   **Local Visualization Testing:** 
    *   Server started on localhost:9999
    *   Verified correct rendering of sunburst chart with 42 subject segments
    *   Verified hovering shows tooltip with subject/class details
    *   Verified clicking segments triggers zoom or navigation
    *   **Status:** All tests passed - 0 JavaScript console errors

## Files Created

| File | Description |
|------|-------------|
| `courses.tsv` | TSV with 2,573 courses (5 columns) |
| `mit-ocw-data.json` | Hierarchical JSON for D3 sunburst |
| `index.html` | Main visualization (loads JSON dynamically) |
| `implementation_plan.md` | This document |

## Key Design Decisions

1.  **Separate Data File:** Data is stored in `mit-ocw-data.json` instead of embedded in HTML for scalability and maintainability.
2.  **TSV for Intermediate Work:** `courses.tsv` preserves the raw extracted data for potential future reprocessing.
3.  **Dynamic Loading:** `index.html` uses `d3.json()` to load data at runtime.
4.  **Full Course Coverage:** Processed all 2,573 courses from the sitemap (exceeds original ~2,500 target).