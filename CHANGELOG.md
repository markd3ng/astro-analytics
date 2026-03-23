# Changelog

## Unreleased

### Added
- Added `CHANGELOG.md` to track analytics component fixes, compatibility notes, and documentation updates.

### Changed
- Updated `Plausible` to use the current official default script URL: `https://plausible.io/js/plausible.js`.
- Updated `Umami` to support the recommended `id` + `src` API while keeping compatibility with the legacy `dataId` + `site` + `host` props.
- Updated `Amplitude` to load Browser SDK 2 asynchronously and initialize it after the script is available.
- Updated `Clarity` to use the official bootstrap snippet with correct Astro variable injection.
- Updated `Fathom` typings so `src` is explicitly optional.
- Reworked `README.md` around Astro-first integration patterns, component usage, migration notes, performance guidance, and validation steps.

### Deprecated
- Marked `MinimalAnalytics` as deprecated because it depends on the deprecated Universal Analytics `collect` endpoint.
- Kept `MinimalAnalytics` available for backward compatibility only; it should not be used in new projects.

### Notes
- No component exports were removed.
- Recommended Astro usage is now: inject analytics once in a shared layout and gate it behind `import.meta.env.PROD`.
- More aggressive lazy-loading remains optional because it can reduce tracking accuracy for the first pageview or early events.
