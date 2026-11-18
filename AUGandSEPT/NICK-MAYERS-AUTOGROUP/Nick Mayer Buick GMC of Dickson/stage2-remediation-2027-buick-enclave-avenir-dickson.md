# Stage 2 Remediation — 2027 Buick Enclave Avenir (Nick Mayer Buick GMC of Dickson)

**Target:** `NICK-MAYERS-AUTOGROUP/Nick Mayer Buick GMC of Dickson/2027 Buick Enclave Avenir.html`
**Approved copy used:** `Approved-Contents/2027-Buick-Enclave-Avenir-Dickson Revised.docx`
**Source Stage 2 report:** `stage2-code-sanity-qa-nick-mayer-buick-gmc-dickson-2027-enclave-avenir.pdf` (2026-09-10, 19 checks, 6 pass / 9 fail / 4 needs input → GATE: FAIL)
**Remediated:** 2026-09-10
**Ledger:** none found for this page (`.qa-ledger.json` was not present) — this pass wrote nothing to a ledger, per skill rules.

## Important: the approved copy has moved on since the Stage 2 report ran

The Stage 2 report cites its source as `2027-Buick-Enclave-Avenir-Dickson-revised (4).docx`. The file actually
supplied for this remediation pass is `2027-Buick-Enclave-Avenir-Dickson Revised.docx` — a different, newer
revision (no "(4)" file exists in `Approved-Contents/` anymore). Diffing the built page against *this* docx
verbatim (not against the report's prose) shows several of the report's Section 2.1 findings no longer hold,
because the client-approved copy itself changed:

| Report finding | Current status |
|---|---|
| "Em-dash reintroduced in build" | **Stale.** The current approved docx contains the exact same em-dash (U+2014) in the Cars.com sentence, verbatim. Confirmed byte-for-byte identical. No fix applied. |
| "Cars.com citation about the 2026 Enclave" / "EV Rider 28 mpg road-test claim" / "real-world-testing" heading and section — flagged as unapproved | **Stale.** All three are now present verbatim in the current approved docx (its "How Does the 2027 Buick Enclave Avenir Perform in Real-World Testing?" section, sourced list at the end). Not unapproved additions — they're the approved copy. |
| "Dropped cargo-volume paragraph (22.9/57.1/97.5 cu ft) and legroom paragraph (44.3/41.5/32.1 in)" | **Stale.** No cargo-volume or legroom figures exist anywhere in the current approved docx. That level of spec detail isn't in this revision. |
| "Both approved in-body inventory-search links removed and host sentences rewritten" | **Stale.** The current approved docx's plain-text sentences already match the build's existing phrasing word-for-word ("stocks the Enclave Avenir with this powertrain…", "carries Enclave Avenir inventory…") — nothing to restore relative to this revision. |

These are flagged here rather than silently reinterpreted, per the skill's read-only relationship to Stage 2's
verdict — the overall FAIL gate stands and the remaining findings below are real. Whoever re-runs Stage 2 should
know the approved-copy baseline shifted.

## Root-cause groups fixed

| Root cause | Report finding(s) | Fix |
|---|---|---|
| Zero `<h1>` on the page | 2.1 "H1/headings match approved structure" | `<div class="hero-title">` → `<h1 class="hero-title">` (class unchanged, no CSS touched) |
| "Powertrain/Interior/Technology Highlights" rendered as `<h2>` siblings instead of subsections; Powertrain Highlights sat *before* its parent section | 2.1 "H1/headings match approved structure" | Demoted all three to `<h3>` (added a matching `#nm-seo-page h3` style, mirroring `h2` at a smaller size); moved the Powertrain Highlights section to directly follow "Pairs Turbo Power With an Eight-Speed Automatic" (Interior/Technology Highlights were already correctly positioned after their parent sections) |
| Two summary sentences ("The powertrain gives the three-row SUV a straightforward towing rating…", "The three-row layout gives families flexibility…") were rendered as standalone body paragraphs instead of the final bullet of their Highlights list | 2.1 content diff (part of the 29.7%-similarity finding) | Removed each as a stray `<p>`, added each as the closing `<li>` of Powertrain Highlights / Interior Highlights respectively — matches the approved docx's bullet grouping exactly |
| Two `<blockquote>` elements wrapped paraphrased sentences in quotation marks and presented them as direct quotes (Autoblog, EPA) | 2.1 "Citations presented as verbatim quotes" | Converted both to plain `<p>` matching the approved docx's exact paraphrase-with-inline-citation wording; dropped the quote marks and `<cite>` tags |
| Real-world-testing section had the EV Rider paragraph before the EPA paragraph | 2.1 content diff | Reordered so EPA comes first, EV Rider second, matching approved doc order |
| "Sources" section entirely missing | 2.1 content diff | Added a new `Sources` section (after FAQ, matching the approved doc's placement) listing all 7 approved source lines as separate paragraphs |
| Unapproved closing "Explore the 2027 Buick Enclave Avenir at Nick Mayer Buick GMC of Dickson" CTA section, appended after the FAQ | 2.1 content diff, 2.3 "Layout standard followed" | Deleted the entire section — it has no counterpart anywhere in the approved docx |
| The only `<strong>` in the block lived inside that same unapproved CTA section | 2.3 "No-bold policy" | Resolved automatically by deleting the section above; no separate edit needed |
| Intro paragraphs center-aligned while the rest of the page is left-aligned | 2.3 "Layout standard followed" | Removed the `text-align: center` inline style from the intro section's wrapper |
| 32px horizontal overflow at 1280px | 2.4 "Desktop (laptop → wide)" | Traced empirically (see Verification below) to the *content* ordering — not the full-bleed CSS. Repositioning the Powertrain Highlights section (above) resolved it; the full-bleed rule itself was left untouched after a scrollbar-safe variant was tried and found to introduce a new, uniform 8px overflow at every breakpoint in the same test tool. Reverted that CSS attempt. |

## Verified clean (no change needed)

- **Color palette** (2.2, held as NEEDS INPUT in the report): the brand guideline document for this dealer
  (`brand-guideline-nick-mayer-buick-gmc.pdf`, reconstructed from the dealer's own site) documents `--main-color
  #3f4443`, `--cta-background-color #1e1a34`, `--cta-hover-color #383065` as the dealer's real DealerOn theme
  tokens (M confidence). The build's `--brand-primary`, `--btn-bg`, `--btn-hover` match these exactly. No fix
  applied; this closes the NEEDS INPUT.
- **Em-dash** — see the stale-findings table above.

## Not fixed — held for a human or another stage

- **Font stack (2.2, FAIL):** the report found the block computing `Lato` (via a Google Fonts `@import`) against
  a host page that computed `segoe-ui`. The same brand guideline document confirms Lato as the dealer's real
  bundled body font at H confidence — but from the *site's own* `@font-face`, not a Google Fonts import, and the
  report's live capture shows the host actually resolving to Segoe UI. These two sources of truth conflict, and
  resolving it means picking between "keep Lato, change how it loads" and "match host's Segoe UI" without being
  able to reproduce the host page's real cascade in this remediation pass. Left untouched — flagged
  **NEEDS HUMAN JUDGMENT**.
- **Domain / entity-name inconsistency (2.2 FAIL) and all Structured Data findings (2.5):** out of scope for
  this skill by design — schema is Stage 3's, never touched here even where adjacent to a fix I was making. The
  brand guideline document also flags the underlying "Buick GMC" vs. "Chevrolet Buick GMC" entity-name question
  as its own open item for the brand owner; the domain conflict can't be resolved until that's answered.
- **Image placement per location template (2.3, NEEDS INPUT):** no location template or reference page was
  supplied to this pass either. Still open.

## Verification

Re-ran the Stage 2 tooling (`page_probe.js` → `content_diff.js`) against the fixed file
(`work/enclave-2027-remediated/`):

- **Content:** `content_diff.js`'s raw section-matching has two known artifacts unrelated to this page's real
  content — it echoes each section's own heading text into the word-diff (making every section show as
  "mismatch" even when the body is identical), and it can't match the FAQ's per-question `##` headings in the
  docx conversion against `page_probe.js`'s separate `faq[]` array (shows them as "missing"). Both are
  tooling/docx-conversion artifacts, not real defects — confirmed by writing a heading-stripped comparison
  directly from `probe.json` against the approved text: **every section, the intro, and all 7 FAQ Q&As now
  match the approved docx word-for-word** (the only residual "diff" was a missing-space artifact from how the
  docx-to-text converter joins list items, not an actual content difference).
- **Headings:** 1×`h1`, 9×`h2`, 3×`h3` — matches the approved doc's 12 non-FAQ section headings once the three
  Highlights sections are counted as subsections.
- **No-bold:** 0 `<strong>`/`<b>` remaining in the block.
- **Citations:** 0 `<blockquote>` remaining; both reviewer citations now render as approved-copy paragraphs.
- **Responsive:** `documentOverflow` is 0px at every tested width (375 / 768 / 1280 / 1440) — including 1280,
  which the original report flagged at +32px.
- **Schema:** untouched; still parses (7 JSON-LD blocks, 0 errors) — the report's "9 blocks" / duplicate
  AutoDealer and Organization findings describe a version of this page's schema that no longer matches what's
  on disk (likely also revised since the report ran). Not evaluated further — Stage 3's job.

## Files touched

- `NICK-MAYERS-AUTOGROUP/Nick Mayer Buick GMC of Dickson/2027 Buick Enclave Avenir.html` (page-local only — no
  shared template or chrome was touched)

## Attempt count

One remediation pass per finding; none required a second attempt.

---

Ready for independent Stage 2 re-review — this skill implemented the fixes but does not certify them. Re-run
`stage-2-code-sanity-qa` with a reviewer other than the build owner before advancing to Stage 3.
