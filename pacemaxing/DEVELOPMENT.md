# PaceMaxing — Development Notes

Design decisions and planned work, recorded so we don't relitigate them. Newest
entry first.

---

## 2026-06-27 — Interactive piece selection (the next major feature)

**Decision:** Replace the current "set _Number of intervals_ + measure backwards
from each detected piece" flow with an **interactive trace**: show the whole
workout's pace/spm curve first, let the user pick the pieces they want analyzed,
and have the app snap each selection's start/end to the pace inflection.

**Status:** agreed direction, not yet built. This supersedes the parameter-driven
detection described in `README.md` (§How to use, step 3) and directly addresses
the first two _Known limitations_ (threshold-based detection; merged pieces on
short rest).

### Why this is the right move

- It productizes the step that already carries the analysis: scan the full trace →
  anchor each piece on its pace inflection → **confirm before any stats run**. We're
  giving an existing, validated algorithm a UI, not inventing one.
- Showing the whole trace first _is_ the feature. Coached sessions are messy
  (variable lengths, coach-called endings); a trace + draggable selection keeps the
  human in the loop while cutting the work — better than pure-auto or pure-manual.

### The snap rule (already validated in the rowing-session-analysis skill)

Anchor on **GPS pace**, not spm (Coros spm lags actual rate 3–5 s; GPS pace responds
immediately). Show spm as a secondary overlay for context only.

- **Start** = the second pace begins rising sharply — a 20–30 s/500m jump within
  3–5 s (the first hard strokes).
- **End** = the second pace begins falling off — drops 2–5 s/500m over 2–3 s (the
  last hard strokes).

### Failure modes a naïve "snap to the spike" will hit

Each of these was observed in real session data; the design must handle them.

1. **Rolling starts ramp, they don't spike.** A standing start ("from the start")
   spikes cleanly (spm 16→33 in ~2 s). A rolling start ("from the paddle") builds
   over ~25 s with no sharp edge — a spike detector finds nothing. Need two start
   modes: **edge-snap** vs **ramp-onset**.
2. **Mid-piece wobbles look like edges.** A washed/checked stroke mid-build can drop
   pace ~30 s/500m for a few seconds and recover. Snapping must prefer the
   **sustained** inflection, not the first one it sees.
3. **The last recorded second is GPS garbage.** Every piece ends with an
   instantaneous junk spike (e.g. "7.6/500"). Filter it (`speed > 0.5 m/s` AND
   `15 < spm < 50`) or end-snap lands on noise.
4. **Anchor on pace, never spm** (see snap rule above).
5. **Some metadata can't be inferred — ever.** Direction (UP/DN) and start-type
   (standing vs rolling) are not in the data. A click selects the _piece_; the user
   still needs a quick per-piece toggle for **direction** and **start-type**. (Do not
   guess direction from the GPS track silently — surface it as a default the user
   confirms.)

### Recommended interaction design

- **Auto-detect → prune, not blank-slate clicking.** Pre-highlight every candidate
  window using the existing scan heuristic (pace < 2:20, spm > 20, HR rising) and let
  the user reject/adjust. Confirming 2 highlighted pieces beats clicking 2 from
  scratch.
- **Snap as a proposal, not a verdict.** Each selection gets draggable start/end
  handles that magnetically snap to nearby inflections as the user nudges them. This
  dissolves failure modes 1–3 in one drag (fix a rolling-start onset or a cut-short
  end by hand).
- **Live readout while dragging** — show the current selection's distance + avg pace
  so "is this ~1500m?" is answered on the spot.
- **Keep an explicit Confirm gate** before stats compute. The one rule the skill
  never breaks: _no analysis before boundaries are confirmed._

### Free signal worth using: lap markers

FIT files carry **lap messages** (mesg_num 19). Use them as priors:

- If laps look **manual** (few, irregular, aligned to efforts → the rower tapped lap
  per piece), pre-seed the selections from them — boundaries for free.
- If laps look like **auto-splits** (many, evenly spaced — e.g. 18 laps over ~9 km ≈
  500 m auto-laps), they are _not_ piece markers; fall back to inflection detection.
  They can still serve as snap anchors.

### Net shape to build

**auto-detect → draggable snap handles → confirm**, anchored on GPS pace with the
artifact filters above, plus per-piece direction/start-type toggles. This is the
skill's chat workflow (scan → boundary table → confirm) rendered as a canvas, with
the same correctness guarantees at a fraction of the effort.

### Related

- Mirrors the `rowing-session-analysis` Claude Code skill (the reference
  implementation of pace-inflection anchoring and the artifact-flag rules).
- Closes/!reframes README _Good first issue_ "Detect and flag upstream vs downstream
  pieces" — direction becomes a per-piece toggle in the selection UI.
