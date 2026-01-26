# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Quiz Didattici is a static web application for educational quizzes, focused on mathematics. It's a pure client-side application with no build process, backend, or dependencies - just HTML, CSS, and vanilla JavaScript.

**Live demo**: https://manzolo.github.io/quiz-didattici/

**Documentation**: Available in Italian (README.md) and English (README.en.md)

## Architecture

### File Structure
```
/                           # Root serves as homepage
├── index.html             # Main landing page with subject categories
├── matematica/
│   ├── index.html         # Math section hub
│   ├── equivalenze.html   # Unit conversion quiz (interactive)
│   └── calcolatore.html   # Unit converter calculator (tool)
```

### Key Design Patterns

**1. Self-Contained Pages**
- Each HTML file is completely standalone with embedded CSS and JavaScript
- No external dependencies, libraries, or build tools
- All files work offline once loaded

**2. Internationalization System**
The entire app supports Italian (default) and English through a consistent pattern:

```javascript
const translations = {
    it: { 'key': 'Testo italiano' },
    en: { 'key': 'English text' }
};
```

- Uses `data-i18n="key"` attributes on HTML elements
- Language preference stored in `localStorage.getItem('language')`
- Special handling for units: `data-i18n-unit="categoria.unità"` maps to nested objects
- Translation function: `t(key)` returns translated string based on `currentLanguage`

**3. Unit Conversion Data Model**
All conversion logic is based on a shared data structure:

```javascript
const conversions = {
    massa: {
        kg: { label: { it: 'Chilogrammi', en: 'Kilograms' }, toBase: 1000 },
        g: { label: { it: 'Grammi', en: 'Grams' }, toBase: 1 },
        // ...
    },
    lunghezza: { /* similar */ },
    volume: { /* similar */ }
};
```

- `toBase`: conversion factor to base unit (g, m, l)
- All conversions go through base unit: value * fromUnit.toBase / toUnit.toBase
- Rounding applied to avoid floating-point precision errors: `Math.round(answer * 100000) / 100000`

**4. State Management via localStorage**
- `language`: Current UI language (it/en)
- `quizHistory`: Array of last 10 quiz grades (equivalenze.html only)

## Development Workflow

### Testing Locally
Simply open any HTML file in a modern browser:
```bash
firefox index.html
# or
google-chrome matematica/equivalenze.html
```

No local server needed, but if you prefer one:
```bash
python -m http.server 8000
# Then visit http://localhost:8000
```

### Adding New Translations
When adding new UI text:
1. Add Italian text directly in HTML
2. Add `data-i18n="unique-key"` attribute
3. Add translations to `translations` object in JavaScript:
   ```javascript
   it: { 'unique-key': 'Testo italiano' },
   en: { 'unique-key': 'English text' }
   ```
4. Test language switching works in browser

### Adding New Unit Categories
To add a new measurement category (e.g., temperature):
1. Add to `conversions` object with `toBase` factors
2. Add to `categoryNames` object with translations
3. Add tab button in HTML with `onclick="changeCategory('newcategory')"`
4. Add translations for category name

### Quiz Question Generation Algorithm
The quiz uses a sophisticated system to ensure variety:
- Picks random `fromUnit` and `toUnit` from available units
- Prevents same conversion appearing consecutively
- Prevents reverse conversion appearing consecutively (e.g., kg→g then g→kg)
- Uses `previousQuestions` array (last 3) to track and avoid repetition
- Values are randomly selected from predefined array for realistic numbers

## Common Modifications

### Styling
All CSS is inline in `<style>` tags. The design system uses:
- Primary gradient: `linear-gradient(135deg, #667eea 0%, #764ba2 100%)`
- Success color: `#28a745`
- Error color: `#dc3545`
- White cards on gradient background

### Adding New Quiz Types
Follow this structure when adding to `matematica/`:
1. Create new HTML file (e.g., `frazioni.html`)
2. Copy language selector and back button from existing quiz
3. Implement same `translations` pattern
4. Add card link in `matematica/index.html` with translations
5. Keep consistent visual design (gradients, card styles, buttons)

### Navigation Structure
- All pages have back buttons linking up the hierarchy
- Language selector (🇮🇹/🇬🇧) in top-right of every page
- Maintains breadcrumb-like navigation: Home → Subject → Quiz

## GitHub Pages Deployment

The site is deployed to GitHub Pages from the `main` branch root folder. Changes pushed to main are automatically deployed.

No build step is required - GitHub Pages serves the static files directly.

## Important Constraints

- **No dependencies**: Don't add npm, webpack, or any external libraries
- **No build process**: Keep everything static and directly runnable
- **Offline-first**: Everything must work without internet after initial load
- **Browser compatibility**: Support Chrome/Edge 90+, Firefox 88+, Safari 14+
- **Mobile responsive**: All layouts must work on small screens

## Code Style Conventions

- Italian variable names in some legacy code, English preferred for new code
- camelCase for JavaScript variables and functions
- kebab-case for HTML attributes and CSS classes
- Inline comments in Italian or English
- Git commit messages in English with conventional commits format
