# Video Script Production — ON HOLD (2026-07-06)

No new script generated today. Video script production remains paused per explicit
`"hold": true` flag on the queue's rank-3 video_script task and
`"video_scripts": "hold_backlog_day_30_no_new_scripts"` in today's status.

## Hold reason (from queue)

> "Video script production on HOLD since 2026-06-18 pending human review of backlog.
> 5 needs-revision scripts remain unresolved."

## Current backlog state

### PUBLISHED (2026-07-05) — 5 scripts approved and live, `approved.json` needs clearing
- `tesla-spacex-transit-empire-script.md`
- `transit-app-dark-ux-2026-script.md`
- `fare-evasion-economics-script.md`
- `us-cities-transit-gap-script.md`
- `dopamine-fracking-script.md`

### NEEDS REVISION before QC pass — blocks new production
- `apple-gemini-2026-script.md`
- `electric-axial-flux-motor-transit-explainer-script.md`
- `pokemon-go-drones-urban-data-script.md`
- `slime-mold-transit-script.md`
- `transit-desert-pomdp-script.md`

## Timely topic queued but not producible under hold

**real-time-rail-maps-video-script-2026** (queue rank 3) — "Every train in Britain, live
on your screen: the open data revolution in rail." Hook on dual HN viral moment: GB rail
map (249 pts) + France rail map (HN top) both hit simultaneously. Topic window is 24–48h.

## Hard deadline alert

**Gemini Code Assist shutdown: July 17 — 11 days remaining.** No video script on this
topic is producible under current hold, but the deadline is time-sensitive enough to flag
for manual fast-track consideration.

## Action needed (human)

1. **Clear `system/approved.json`** — the 5 published slugs are stale; clear them so the
   next batch of approved scripts can flow through the publishing pipeline.
2. **Decide on 5 needs-revision scripts** — either approve for publishing as-is, assign
   revisions, or reject to remove from backlog. Until resolved, new production is blocked.
3. **Optional: approve `real-time-rail-maps-video-script-2026`** via `approved.json` (add
   slug) to allow this agent to produce the script on next run while the HN signal is warm.

Format for `approved.json` entry: `"2026-07-06-script-real-time-rail-maps-video-script-2026"`
