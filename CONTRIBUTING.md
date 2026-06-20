# Contributing to send-it

Thanks for helping. Here's how to contribute.

## Quick start

1. Fork the repo
2. Make your changes
3. Open a pull request with a clear description of what you changed and why

No build system, no dependencies to install. Each tool is a single HTML file — open it in a browser and you're developing.

## Guidelines

- **Keep tools self-contained.** Each app should be a single HTML file with no external dependencies beyond CDN-hosted libraries (Chart.js etc.).
- **Local-first.** No data should leave the user's browser. No analytics, no tracking, no external API calls.
- **Test with real FIT files.** If you're fixing a device compatibility issue, include the device model in your PR description.
- **Document edge cases.** If your fix handles a specific FIT file quirk, add a note to the app's README under Known Limitations.

## Reporting bugs

Open an issue with:
- Device make and model (e.g. Coros Pace 3, Garmin Forerunner 965)
- What you expected to happen
- What actually happened
- The FIT file if you're able to share it (scrub any personal data first)

## Feature requests

Open an issue describing the use case, not just the feature. "I want CSV export" is less useful than "After a session I want to paste the quarter breakdown into my training spreadsheet."

## Code style

Vanilla JS, no frameworks. Keep it readable — this codebase should be hackable by someone who knows basic JavaScript, not just React developers.
