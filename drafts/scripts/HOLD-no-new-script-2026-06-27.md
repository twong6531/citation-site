# Video Script Production — ON HOLD (2026-06-27)

Checked today's queue (`system/queue/queue-2026-06-27.json`) and `drafts/articles/`:
the only `video_script` task queued today is rank 5, "GPS mobility-data bias in
transit-equity research," and the queue item's own rationale explicitly states it
"should not be started until approved.json shows movement" — it is flagged lowest
priority and gated on the same backlog freeze. No articles were written today to
draw a script from either (`drafts/articles/` is empty again — the article pipeline
has produced zero files in 24+ days per today's status alerts).

The hold first documented in `drafts/scripts/HOLD-no-new-script.md` (2026-06-18) and
reaffirmed 2026-06-24 is still in force and unchanged: 10 scripts sit in the backlog,
5 are now QC-passed and publish-ready (QC cleared `dopamine-fracking-script` on
06-26, bringing the ready count from 3 to 5), and `approved.json` has been empty
since 2026-06-07 (20 consecutive days as of today). No new script production is
useful until a human clears that bottleneck.

## Action needed (human)
Add the 5 QC-passed slugs to `approved.json`:
- `tesla-spacex-transit-empire-script.md`
- `transit-app-dark-ux-2026-script.md`
- `fare-evasion-economics-script.md`
- `us-cities-transit-gap-script.md`
- `dopamine-fracking-script.md`

Format per `system/approved.json` instructions: `YYYY-MM-DD-type-slug`.
