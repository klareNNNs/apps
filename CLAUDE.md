# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Escultor Digital AI** - A single-page web application for generating AI reference images of Warhammer: The Old World Chaos miniatures for 3D printing. Uses Google's Imagen 4.0 API.

## Tech Stack

- Pure HTML5 + Vanilla JavaScript (ES6+)
- TailwindCSS 3.4.1 via CDN
- Google Generative Language API (Imagen 4.0)
- No build system, no package manager, no backend

## Running the Application

Open `index.html` directly in a browser (drag to browser or double-click). Requires:
- Internet connection (for TailwindCSS CDN and API calls)
- Valid Google API key set in `apiKey` variable (line 170)

Note: Opening via JetBrains IDE uses localhost:63342 which is slower due to the integrated server.

## Architecture

Single `index.html` file with embedded CSS and JavaScript (~680 lines total).

### Key Data Structures

- `UNIT_TYPES`: Unit configurations (base size, mount description, label) for 5 types: infanteriaLigera, infanteriaPesada, caballeriaLigera, caballeriaPesada, monstruo
- `HEAD_OPTIONS`: Head/helmet varieties - bare heads or chaos ritual headpieces (NOT functional helmets)
- `DYNAMIC_POSES` / `CAVALRY_POSES`: Pose arrays for infantry/cavalry
- `WEAPON_OPTIONS`: Weapon + shield combinations

### Key Functions

- `rerollOptions()`: Randomizes pose, head, and weapon selections
- `previewPrompt()`: Shows full prompt without sending to API
- `generateImage()`: Builds optimized prompt and calls Imagen API with retry logic
- `copyPrompt(btn)`: Copies displayed prompt to clipboard
- `setUnitType()`, `setHeadOption()`, `setWeaponOption()`: UI state handlers

### Two Generation Modes

1. **Guided Mode**: Constructs detailed prompts based on unit type with automatic equipment, poses, base sizes, and chaos aesthetic
2. **Free Mode**: Sends user prompt directly to API without modifications

## Prompt Engineering Notes

The prompt system is carefully optimized for Imagen 4.0:

- **Helmets**: Use "ritual headpiece / sealed war mask / head idol" terminology instead of "helmet" to prevent AI from adding functional eye holes
- **Base**: Emphasize "SQUARE" or "RECTANGULAR" with "sharp 90-degree corners" to prevent round bases
- **Cavalry**: Mark as "MOUNT (CRITICAL)" to ensure horse is included
- **Structure**: Use maximum 3 CRITICAL markers to avoid AI confusion
- **Negative prompts**: Keep concise, avoid redundancy

## API Integration

- Endpoint: `https://generativelanguage.googleapis.com/v1beta/models/imagen-4.0-generate-001:predict`
- Retry mechanism: Exponential backoff, up to 5 retries
- Response: Base64-encoded PNG images

## Language

UI is in Spanish, prompts are in English.

---

## Secondary Tool: Army Visualizer & Shopper

Located in `/organizer/index.html` - A companion tool for organizing army lists.

### Purpose
Import and organize Warhammer: The Old World army lists from Old World Builder, add store prices/images, and calculate budget per faction.

### Tech Stack
- Pure HTML5 + Vanilla JavaScript + CSS (no frameworks)
- localStorage for persistence (key: `tow_visualizer_units`)

### Data Structure
```javascript
unit = {
  id: timestamp,
  faction: "Warriors of Chaos",
  name: "Chaos Warriors",
  pts: "180",      // game points
  price: 45.00,    // store price €
  img: "url",      // product image
  link: "url"      // store link
}
```

### Key Functions
- `processList()`: Parses Old World Builder text export using regex
- `renderTabs()`: Generates faction tabs
- `renderUnits()`: Displays unit cards + calculates total budget
- `promptEdit(id)`: Quick edit via browser prompts

### Parser Regex
```javascript
// Extract faction
text.match(/Warhammer: The Old World, (.*?),/)

// Extract units: "Chaos Warriors [180 pts]"
line.match(/^([a-zA-Z0-9\s]+)\[(\d+)\spts\]/)
