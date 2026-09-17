# Course Register — Course Management System

A single-file CRUD web application for managing a college course catalog: add, view, search, filter, edit, and delete course records.

## Overview

| | |
|---|---|
| **Type** | Course Management System |
| **Category** | Mini Web Application (CRUD-based) |
| **Tech stack** | HTML, CSS, JavaScript (vanilla) |
| **Data storage** | Browser `localStorage` (acts as the persistence layer) |
| **File** | `course-management-system.html` — fully self-contained, no build step or server required |

## Problem Statement

Course catalogs are usually tracked manually or in spreadsheets, making it hard to search, update, or check seat availability at a glance. This application gives a simple interface to manage course records — code, name, department, instructor, credits, semester, and seat count — with all four CRUD operations.

## Objectives

- Implement full CRUD (Create, Read, Update, Delete) for course records
- Provide a usable, responsive interface for entering and browsing data
- Apply client-side validation with clear error feedback
- Support search and filtering (by name/code/instructor, department, semester)
- Structure the code so the storage layer could be swapped for a real backend with minimal change

## Architecture

```
User → HTML/CSS/JS Frontend → courseStore module → localStorage (data store)
```

The app is split into two logical layers inside the single file:

1. **UI layer** — renders the table, stats, search/filter controls, and the add/edit modal; handles form validation and user interaction.
2. **`courseStore` data layer** — exposes four functions that mirror what a REST backend would expose:

| Function | Simulated REST equivalent | Purpose |
|---|---|---|
| `courseStore.list()` | `GET /api/courses/` | Return all course records |
| `courseStore.create(data)` | `POST /api/courses/` | Add a new course record |
| `courseStore.update(id, data)` | `PUT /api/courses/{id}/` | Update an existing record |
| `courseStore.remove(id)` | `DELETE /api/courses/{id}/` | Delete a record |

Because the UI only ever calls these four functions (never touching `localStorage` directly), the data layer could be replaced with real `fetch()` calls to a Django REST Framework or Spring Boot backend without changing any UI code.

## Data Model

Each course record has the following fields:

| Field | Type | Notes |
|---|---|---|
| `id` | string | Generated automatically, primary key |
| `code` | string | e.g. `IT301`; must match `AA00` to `AAAAA0000` pattern; unique |
| `name` | string | Course title |
| `department` | string | e.g. `Information Technology` |
| `instructor` | string | Instructor name |
| `credits` | number | 1–6 |
| `semester` | number | 1–8 |
| `seats` | number | ≥ 0; shows as "Full" at 0 |
| `description` | string | Optional |

## CRUD Implementation Details

- **Create** — "Add course" opens a modal form; on submit, data is validated then passed to `courseStore.create()`.
- **Read** — `courseStore.list()` is called on load and after every change; results are filtered (search/department/semester) and rendered into the table, with live stats (total courses, total credits, departments, open seats) recomputed each time.
- **Update** — "Edit" opens the same modal pre-filled with the record's data; on submit, `courseStore.update(id, data)` is called, keeping the original `id`.
- **Delete** — "Delete" asks for confirmation, then calls `courseStore.remove(id)`.

## Validation Rules

| Field | Rule |
|---|---|
| Course code | Required; must match pattern (2–5 letters + 2–4 digits); must be unique |
| Course name | Required |
| Department | Required |
| Semester | Required, must be selected |
| Instructor | Required |
| Credits | Required, numeric, 1–6 |
| Seats | Required, numeric, ≥ 0 |

Invalid fields are outlined and show an inline error message; the form will not submit until all errors are resolved.

## Installation & Execution

No installation needed.

1. Download `course-management-system.html`.
2. Open it in any modern browser (Chrome, Edge, Firefox).
3. The app loads with four sample courses pre-filled so the table isn't empty on first run.
4. Data you add, edit, or delete persists in that browser via `localStorage` until it's cleared.

## Testing Performed

| Test | Steps | Result |
|---|---|---|
| Create — valid data | Add course with all required fields filled correctly | Record appears in table, stats update |
| Create — missing fields | Submit form with empty required fields | Fields highlighted, submission blocked |
| Create — duplicate code | Add a course using a code already in use | Code field flagged as invalid, submission blocked |
| Create — invalid credits/seats | Enter credits outside 1–6 or a negative seat count | Field flagged, submission blocked |
| Read — search | Type into the search box | Table filters live by name/code/instructor |
| Read — filters | Select a department or semester | Table filters accordingly |
| Read — empty result | Search for a non-existent course | "No courses match yet" message shown |
| Update | Edit an existing course and change values | Table reflects updated values, original `id` preserved |
| Delete | Delete a course, confirm the prompt | Record removed from table and stats |
| Delete — cancel | Delete a course, cancel the prompt | Record remains unchanged |
| Responsiveness | Resize to mobile width | Table collapses into stacked cards |

## Future Enhancements

- Replace `courseStore`'s localStorage calls with real REST API calls to a Django/Spring Boot backend
- Add user authentication and role-based access (admin vs. student view)
- Add pagination for large course catalogs
- Export course list to CSV/PDF
