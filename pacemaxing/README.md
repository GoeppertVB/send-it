# PaceMaxing

**Rowing interval analyzer.** Drop in a FIT file, set your workout parameters, get a full breakdown of every piece — pace, stroke rate, DPS, and HR by quarter, plus first and last 30-stroke sprint segments.

## What it does

- Parses FIT files directly in the browser (Coros, Garmin, SpeedCoach)
- Auto-detects intervals from GPS speed and stroke rate data
- Splits each piece into equal quarters (by distance or time)
- Extracts first ~30 strokes (start sequence) and last ~30 strokes (finish sprint)
- Calculates rest intervals between pieces
- Charts pace, HR, and DPS across all pieces side by side

## How to use

1. Open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari)
2. Drop your `.fit` file onto the upload zone
3. Set your workout parameters:
   - **Number of intervals** — how many pieces you did
   - **Interval type** — distance (metres) or time (seconds)
   - **Interval length** — e.g. 1000 for 1000m pieces, 300 for 5-minute pieces
   - **Number of quarters** — 2, 4, or 5 equal segments per piece
4. Click **Analyze Session**

> ⚓ **Intervals are measured backwards from the end of each detected piece.** Set the interval length to match the distance or time you actually rowed, not including warmup or build-in strokes.

## Supported devices

| Device | Notes |
|--------|-------|
| Coros (all models) | Export FIT from Coros app → Activity → ··· → Export |
| Garmin | Export FIT from Garmin Connect → Activity → gear icon → Export Original |
| NK SpeedCoach | Export FIT from SpeedCoach app |

## Key metrics

| Metric | Description |
|--------|-------------|
| **Pace** | Seconds per 500m, derived from GPS speed |
| **spm** | Stroke rate (strokes per minute) |
| **DPS** | Distance per stroke (m) — speed × 60 / spm. Primary efficiency metric |
| **HR** | Heart rate average and peak per segment |

## Known limitations

- **Piece detection** uses a speed + stroke rate threshold. Sessions with long slow paddles between pieces work best. Very short rest intervals (<60s) may cause two pieces to merge.
- **Coros SPM underread** — Coros watches undercount stroke rate by 0.5–2 spm at higher rates. Use a SpeedCoach or Garmin for authoritative spm data; Coros readings are approximate.
- **GPS accuracy** — DPS and pace are GPS-derived. Accuracy degrades in narrow rivers with tall banks, sharp turns, or choppy conditions.
- **Standing starts** — the first 30-stroke window includes the hull acceleration phase, so pace will appear slower than the settled piece pace.

## Contributing

Bug reports, device compatibility fixes, and feature PRs are all welcome. See the repo-level [CONTRIBUTING.md](../CONTRIBUTING.md).

**Good first issues:**
- Test with Garmin FIT files and report field mapping differences
- Add CSV export for the piece breakdown table
- Side-by-side athlete comparison (two FIT files, same session)
- Detect and flag upstream vs downstream pieces from GPS track direction

## License

MIT
