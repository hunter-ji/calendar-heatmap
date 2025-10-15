# GitHub Copilot Instructions - Calendar Heatmap

This file provides guidelines for GitHub Copilot when working on the calendar-heatmap project.

## Project Overview

Calendar Heatmap is a lightweight JavaScript library for rendering GitHub-style calendar heatmaps. It's a **dependency-free**, **vanilla JavaScript** library that works in browsers and supports multiple module systems (UMD).

## Code Style and Structure

### Module System
- Use **UMD (Universal Module Definition)** pattern for compatibility with CommonJS, AMD, and global scope
- Support both `module.exports` and global `window.CalendarHeatmap` assignment
- Check for `typeof module === 'object'`, `define.amd`, and fallback to global scope

### JavaScript Style
- Use **ES5-compatible JavaScript** for maximum browser compatibility
- Use `const` and `let` for variable declarations (transpiled for older browsers if needed)
- Use arrow functions sparingly; prefer traditional function expressions for public APIs
- Use template literals for string interpolation
- Keep code compact and readable with minimal dependencies

### Naming Conventions
- Use **camelCase** for variable and function names
- Use **SCREAMING_SNAKE_CASE** for constants (e.g., `DEFAULT_OPTIONS`, `LANGUAGE_CONFIG`)
- Use descriptive names that clearly indicate purpose
- Prefix internal/private functions with descriptive context (e.g., `dateKey`, `normalizeData`)

### Code Organization
- Define all constants at the top of the module
- Group related utility functions together
- Keep the main `CalendarHeatmap` class after helper functions
- Export static properties like `CalendarHeatmap.defaults` and `CalendarHeatmap.languages`

## API and Configuration

### Options Pattern
- Always provide sensible defaults in `DEFAULT_OPTIONS`
- Support both CSS selectors (strings) and DOM nodes for container
- Allow flexible data input formats: `{ date, value }`, `{ day, count }`, etc.
- Use type checking and validation for user inputs

### Color Scale
- Support both array-based color scales and function-based color mappings
- Default to GitHub-style green palette: `['#ebedf0', '#9be9a8', '#40c463', '#30a14e', '#216e39']`
- Allow functions that return either a color string or `{ color, level }` object

### Views and Layouts
- Support `view` options: `'year'`, `'month'`, `'week'`, `'recent'`
- Each view has specific required/optional parameters
- Year view: `year` parameter
- Month view: `year` and `month` parameters
- Week view: `startDate` parameter
- Recent view: `recentDays` parameter

## Internationalization (i18n)

### Language Packs
- Store all language configurations in `LANGUAGE_CONFIG` object
- Each language pack must include:
  - `locale`: ICU locale string (e.g., `'en-US'`, `'zh-CN'`)
  - `legend`: Object with `less` and `more` properties
  - `weekdays`: Array of 7 weekday labels (Sunday first)
  - `tooltip`: Function that takes `(value, date)` and returns formatted string

### Language Aliases
- Maintain `LANGUAGE_ALIASES` map for common locale variations
- Support lowercase variations (e.g., `'en-us'`, `'zh-cn'`)
- Support region codes (e.g., `'zh-Hans'`, `'zh-Hant'`)
- Support language name aliases (e.g., `'english'`, `'japanese'`)

### Adding New Languages
- Follow existing language pack structure exactly
- Use native locale conventions for date formatting
- Ensure weekday labels are appropriately short (3-4 characters)
- Test tooltip formatting with various date formats

## DOM and Styling

### CSS Classes
- Use `ch-` prefix for all CSS classes to avoid conflicts
- Main classes: `ch-container`, `ch-day`, `ch-labels`, `ch-legend`, `ch-tooltip`
- Inject styles dynamically via `ensureStyles()` function
- Use CSS custom properties for dynamic values (e.g., `--ch-size`, `--ch-gap`)

### Inline Styles
- Set CSS custom properties on the container element
- Use inline styles for dynamic positioning (tooltips)
- Minimize inline styles; prefer CSS classes

### SVG and HTML
- Do not use SVG for the heatmap grid; use HTML divs for better flexibility
- Use CSS Grid for layout when appropriate
- Support both fixed and responsive layouts

## Data Handling

### Data Normalization
- Accept flexible input formats: `date`, `day`, `dateString`, `value`, `count`
- Convert all dates to `YYYY-MM-DD` string keys internally
- Handle Date objects, ISO strings, and custom date formats
- Validate data and handle edge cases gracefully

### Date Utilities
- Use native `Date` objects for calculations
- Implement helper functions: `dateKey()`, `parseKey()`, `addDays()`, `startOfWeek()`, `endOfWeek()`
- Keep timezone handling simple and consistent
- Always normalize dates to midnight local time

## Testing and Development

### Manual Testing
- Primary test file: `tests/index.html`
- Test all views: year, month, week, recent
- Test all language packs
- Test with different data densities and edge cases
- Open `tests/index.html` directly in browser for testing

### Test Data
- Generate synthetic random data for demonstrations
- Cover edge cases: empty data, sparse data, dense data, future dates
- Test with various date ranges and view configurations

### No Build Step
- Library should work directly without transpilation/bundling
- Keep dependencies at zero
- ES5+ compatible code that runs in modern browsers
- No npm build scripts required (current `npm test` returns error is acceptable)

## Documentation

### Code Comments
- Use JSDoc-style comments for public methods and classes
- Keep comments concise and informative
- Comment complex algorithms or non-obvious logic
- Avoid obvious comments that restate the code

### README Updates
- Keep API documentation in sync with code
- Update both English (`README.md`) and Chinese (`README.zh-CN.md`) versions
- Include code examples for new features
- Document breaking changes clearly

### TypeScript Definitions
- Maintain `index.d.ts` for TypeScript users
- Export all public interfaces and types
- Keep type definitions in sync with implementation
- Use union types for flexible parameters

## Security and Best Practices

### Input Validation
- Validate container parameter (string selector or DOM node)
- Check for required parameters before processing
- Handle invalid dates gracefully
- Sanitize any user-provided HTML content

### Performance
- Minimize DOM operations; batch updates when possible
- Use event delegation for tooltips
- Clean up event listeners in `destroy()` method
- Avoid memory leaks by removing references on cleanup

### Browser Compatibility
- Support modern evergreen browsers (Chrome, Firefox, Safari, Edge)
- Use feature detection, not browser detection
- Provide graceful degradation for missing features
- Test in different browsers when adding new features

## Common Patterns

### Error Handling
```javascript
if (!container) {
    throw new Error('CalendarHeatmap: container is required.');
}
```

### Language Resolution
```javascript
const key = String(language || 'en').toLowerCase();
const matched = LANGUAGE_ALIASES[key] || key;
return LANGUAGE_CONFIG[matched] || LANGUAGE_CONFIG.en;
```

### Option Merging
```javascript
this.options = Object.assign({}, DEFAULT_OPTIONS, options);
```

### Date Key Generation
```javascript
function dateKey(date) {
    const d = date instanceof Date ? date : new Date(date);
    return d.toISOString().split('T')[0];
}
```

## Contributions

### Pull Request Guidelines
- Keep changes minimal and focused
- Test thoroughly before submitting
- Update documentation as needed
- Follow existing code style and patterns
- Add examples to `tests/index.html` for new features

### Issue Guidelines
- Provide minimal reproducible examples
- Include browser and version information
- Check existing issues before creating new ones
- Be respectful and constructive

## Additional Notes

- This is a **presentation library**, not a data analytics library
- Prioritize simplicity and ease of use over advanced features
- Keep bundle size small (currently ~541 lines of code)
- Maintain zero dependencies philosophy
- Support both direct `<script>` tag usage and module imports
