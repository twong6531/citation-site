# Content Production System — Architecture

## Overview

8 autonomous agents running on Claude Code Routines (CCR), coordinated via shared state files in this git repo. GitHub is the database. GitHub Pages is the delivery layer.

## Folder Structure

```
citation-site/
├── docs/                        ← GitHub Pages (live site)
│   ├── index.html               ← archive home (rebuilt daily)
│   ├── research.html            ← research archive index
│   ├── YYYY-MM-DD.html          ← daily news briefings
│   ├── research-YYYY-MM-DD.html ← daily research reports
│   ├── *.html                   ← evergreen explainers
│   ├── infographic-*.svg        ← SVG infographics
│   └── imagen-*.png             ← AI-generated illustrations
│
├── drafts/                      ← pre-publication staging
│   ├── articles/                ← long-form HTML drafts
│   ├── scripts/                 ← video/reel scripts (.md)
│   ├── infographics/            ← draft SVGs before QC
│   └── qc/                      ← QC reports (JSON)
│
└── system/                      ← agent shared state
    ├── memory.json              ← rolling content memory
    ├── approved.json            ← human approval flags
    ├── queue/                   ← daily work queues
    │   └── queue-YYYY-MM-DD.json
    ├── briefings/               ← daily briefing reports
    │   └── briefing-YYYY-MM-DD.md
    └── status/                  ← job completion status
        └── status-YYYY-MM-DD.json
```

## Agent Schedule (PDT)

```
07:00  [1] BRIEFING AGENT       — reviews 24h output, scans trends, generates queue
08:00  [2] RESEARCH AGENT       — deep web research on queued topics
08:30  [3] CONTENT PLAN AGENT   — turns research into article outlines + social hooks
09:00  [4] ARTICLE AGENT        — writes full HTML articles to drafts/
09:30  [5] INFOGRAPHIC AGENT    — generates SVG data layer + Imagen illustration
10:00  [6] VIDEO SCRIPT AGENT   — writes short-form video/reel scripts
10:30  [7] QC AGENT             — reviews all drafts, writes QC reports, flags issues
11:00  [8] PUBLISHING AGENT     — reads approved.json, moves approved drafts → docs/, pushes
```

## Shared State Flow

```
Briefing → queue-YYYY-MM-DD.json
Research → system/status (research: done) + drafts/research-raw.json
Content Plan → drafts/articles/outline-*.json
Article Agent → drafts/articles/SLUG.html
Infographic Agent → drafts/infographics/SLUG.svg + SLUG.png
Video Script Agent → drafts/scripts/SLUG.md
QC Agent → drafts/qc/SLUG-qc.json (pass/fail/revise)
Publishing Agent → reads approved.json + QC pass → docs/
```

## Memory Design

### memory.json — rolling long-term memory
- All published URLs with topic + date
- Topic performance signals
- Covered topics (to avoid repetition)
- Content gaps and opportunities
- Niche focus areas

### queue-YYYY-MM-DD.json — daily work queue
- Priority-ranked list of content jobs
- Each job: type, topic, rationale, inputs, expected output path
- Failed job retries from previous days
- Trend signals from that morning's news scan

### status-YYYY-MM-DD.json — real-time job state
- Per-agent completion status
- Errors + error messages
- Output file paths

### approved.json — human gate
- Slug list of items approved for publication
- Human edits this file directly in GitHub UI
- Publishing Agent reads it at 11am

## Agent Inputs / Outputs / Decision Criteria

### [1] Briefing Agent
- IN: git log (24h), memory.json, live news feeds
- OUT: queue-YYYY-MM-DD.json, briefing-YYYY-MM-DD.md, memory.json (updated)
- DECIDES: topic priority, what to retry, what to skip
- ESCALATE: if 3+ consecutive job failures → writes ALERT to briefing

### [2] Research Agent
- IN: queue-YYYY-MM-DD.json (research tasks), arXiv, Semantic Scholar, news
- OUT: drafts/research-raw-YYYY-MM-DD.json (structured findings per topic)
- DECIDES: source quality, relevance threshold
- ESCALATE: if fewer than 3 quality sources → marks topic as "low-confidence"

### [3] Content Plan Agent
- IN: research-raw JSON, memory.json (avoid repeating covered topics)
- OUT: drafts/articles/outline-SLUG.json (title, hook, sections, key stats)
- DECIDES: angle, framing, target reader
- ESCALATE: if topic already covered in last 14 days → propose fresh angle or skip

### [4] Article Agent
- IN: outline-SLUG.json
- OUT: drafts/articles/SLUG.html (full long-form article, dark theme)
- DECIDES: depth, word count (target 800-1500 words), visual hierarchy
- ESCALATE: if research data is thin → writes stub + flags for human review

### [5] Infographic Agent
- IN: research-raw JSON + outline JSON, Gemini Imagen API
- OUT: drafts/infographics/SLUG.svg + SLUG.png
- DECIDES: visual subject, Imagen prompt specificity
- ESCALATE: if Imagen fails 2x → fall back to SVG only, log failure

### [6] Video Script Agent
- IN: outline-SLUG.json + article draft
- OUT: drafts/scripts/SLUG.md (hook, 3-5 scenes, CTA, captions)
- FORMAT: 60-90 second reel script + longer 5-min YouTube version
- ESCALATE: if topic is too technical for short-form → skip reel, write YouTube only

### [7] QC Agent
- IN: all files in drafts/ created today
- OUT: drafts/qc/SLUG-qc.json per item
- CHECKS: factual consistency, source citations present, no placeholder text,
          HTML valid, image files exist, word count in range, no duplicate topics
- GRADES: PASS / REVISE (with specific notes) / FAIL
- ESCALATE: if FAIL → writes to system/status with error, does NOT add to approved candidates

### [8] Publishing Agent
- IN: approved.json, drafts/qc/*.json (must be PASS), docs/
- ACTIONS: copy approved+QC-passed drafts → docs/, rebuild index.html + research.html,
           update memory.json with new published items, commit + push
- WILL NOT PUBLISH: anything not in approved.json OR with QC status != PASS
- ESCALATE: if nothing approved by 11am → skips publish, logs to status

## Escalation Rules

1. Job failure → write to status-YYYY-MM-DD.json + add to memory.json failed_jobs
2. 3 consecutive daily failures of same job → write ALERT section in next briefing
3. QC FAIL → item goes back to queue for next day with failure note
4. Imagen API failure → fall back SVG, log, continue
5. Git push conflict → pull --rebase, retry once, then log and skip

## Quality Standards (enforced by QC Agent)

- Articles: 800+ words, 3+ cited sources, no "as an AI" language, no filler
- Infographics: SVG renders without errors, PNG exists or SVG fallback noted
- Scripts: hook in first 3 seconds, clear CTA, platform-appropriate length
- All HTML: valid structure, links to index.html, mobile-responsive meta tag
