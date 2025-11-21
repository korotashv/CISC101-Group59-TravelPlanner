# Module 01 — Intake & Setup  
*(Collect essentials; normalize/store)*

## Change Log (2025-11-21)
- Added explicit normalization rules for dates, trip length, and seasons (hemisphere-aware)
- Standardized enums for budget style and preferred pace
- Standardized interest list
- Added structured constraint tags (mobility, weather, diet)
- Defined a minimum JSON intake format with example
- Added validation and recovery prompts for common input problems

---

## Purpose

Collect essential trip details, normalize them into a consistent JSON format, and validate inputs to ensure reliable downstream behavior for later modules.

---

## Required Inputs

- **Destination(s)**  
  - City and country  
  - Freeform accepted, but unresolved locations are flagged for clarification

- **Dates or Trip Length**  
  - Either:
    - Specific dates in ISO 8601 format: `YYYY-MM-DD`, or  
    - An integer representing total trip length in days

- **Number of Travelers**  
  - Adults  
  - Children  
  - Child ages (if applicable)  
  - Accessibility needs (e.g., wheelchair access, limited mobility)

- **Budget Style** — standardized enum:  
  - `affordable` | `mid_range` | `luxury`

- **Interests** — standardized list (one or more):  
  - `food`, `culture`, `nature`, `history`, `art`, `nightlife`, `beach`, `wildlife`, `architecture`, `shopping`, `wellness`, `adventure`

- **Preferred Pace** — standardized enum:  
  - `relaxed` | `balanced` | `fast`

- **Constraints** — structured tags:

  - **Mobility:**  
    - `wheelchair_access`, `limited_mobility`

  - **Weather:**  
    - `rain_averse`, `climate_sensitive_hot`, `climate_sensitive_cold`

  - **Diet:**  
    - `diet_vegan`, `diet_vegetarian`, `diet_halal`, `diet_kosher`, `allergy_nuts`, `allergy_shellfish`, `allergy_gluten`

---

## Normalization Rules

### Dates

- If **start_date** and **end_date** are both provided:
  - Store them in ISO 8601 format: `YYYY-MM-DD`
  - Compute and store `trip_length_days` as an integer
- If only **trip length** (in days) is provided:
  - Store `trip_length_days`
  - Set `start_date` and `end_date` to `null` (or equivalent)
- Reject or flag:
  - End date before start date
  - Negative or zero trip length

### Season (Hemisphere-Aware)

- Determine hemisphere for each destination (e.g., northern vs southern).
- Compute the season based on the destination’s hemisphere. Example:
  - June = summer in the northern hemisphere, winter in the southern hemisphere.
- Store both:
  - A **season label** (e.g., `summer`, `winter`)  
  - The **months** included (e.g., `["April", "May"]`)

### Budget

- Normalize `budget_style` to one of: `affordable`, `mid_range`, `luxury`.
- Optionally map style to default per-day budget ranges.
- Allow optional fields:
  - `currency` (e.g., `EUR`, `USD`)
  - Explicit `target_per_day`
  - A numeric range such as `{ "min": 200, "max": 300 }`

### Travelers

- Normalize traveler information into a structured format:
  - `total`, `adults`, `children`, `child_ages`
  - `accessibility` as a list of tags, e.g. `["wheelchair_access"]`

---

## Minimum JSON Intake Format

```json
{
  "destinations": [
    {
      "name": "Paris",
      "country": "France",
      "hemisphere": "northern",
      "season": "spring",
      "months": ["April", "May"]
    }
  ],
  "dates": {
    "start_date": "2026-04-10",
    "end_date": "2026-04-18",
    "trip_length_days": 8
  },
  "travelers": {
    "total": 2,
    "adults": 2,
    "children": 0,
    "child_ages": [],
    "accessibility": ["wheelchair_access"]
  },
  "budget": {
    "style": "mid_range",
    "currency": "EUR",
    "target_per_day": 250,
    "range_per_day": { "min": 200, "max": 300 }
  },
  "preferences": {
    "interests": ["food", "culture", "nature"],
    "pace": "balanced"
  },
  "constraints": {
    "mobility": ["wheelchair_access"],
    "weather": ["rain_averse"],
    "diet": ["diet_vegetarian"]
  },
  "notes": "Prefer boutique hotels; avoid red-eye flights."
}
