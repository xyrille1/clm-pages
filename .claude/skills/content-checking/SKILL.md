---
name: content-checking
description: Verify and correct a vehicle/dealer HTML page's body copy and JSON-LD schema against its approved source content, section by section, fixing any mismatches found. Use when the user gives a target HTML file plus separate approved content (a .docx in an Approved-Contents folder, or pasted text) and asks to check, verify, audit, or fix content accuracy.
---

# Content Checking

Verify that a vehicle/dealer HTML page (the **target**) accurately follows its approved source copy (the **content**), then fix any mismatch found. This is a fact-accuracy check against a source of truth — not a rewrite, restyle, or restructure. Never touch template structure, layout, or styling; only correct the specific text/value that's wrong.

## 1. Identify inputs

- **Target**: the HTML file the user pointed at (open/selected file, or an explicit path).
- **Content**: look for a matching file in that vehicle's `Approved-Contents/` folder (same dealer directory as the target). Match by vehicle name + model year.
  - Filenames in this project are inconsistent (`_Revised`, `-REVISED (1)`, plain). When more than one plausible candidate exists, pick the most recently modified/most clearly "final" revision. If it's still ambiguous, **ask the user** which file to use — do not guess.
  - If no matching file exists, the user may paste the approved content directly in chat — treat that as the source instead.
- **Extract docx text**: unzip the `.docx` and read `word/document.xml` for the plain text (this project already does this — see any existing `extracted/` + `content_extracted.txt` pattern in a dealer folder for reference). Don't invent a different extraction method.

## 2. Map the HTML into the canonical section order

Walk the target HTML in this fixed order (do not use whatever order the markup happens to appear in):

1. Hero
2. Key Highlights (bullet list)
3. Powertrain
4. Drivetrain / Configuration
5. Interior
6. Technology
7. Capability / Towing
8. Expert Review
9. Owner Review
10. Dealer / Closing
11. FAQ

Also collect every JSON-LD `<script type="application/ld+json">` block (AutoDealer/LocalBusiness, Organization, Car/Product, BreadcrumbList, ImageObject, FAQPage) — schema is in scope for this check alongside body copy.

## 3. Cross-check every item against the source, verbatim

For each section and each schema block, compare against the approved content:

- Numeric specs — horsepower, torque, towing/cargo capacity, dimensions, pricing/VIN if present
- Feature/trim/equipment names and lists
- Expert/owner review quotes and attributions — must match source word-for-word
- Dealer NAP (name, address, phone), hours, areaServed cities
- FAQ question/answer text — must agree across body copy, FAQPage schema, **and** source

Track four outcomes, not just pass/fail:
- **PASS** — matches source exactly
- **FAIL** — present in both but differs (wrong number, altered wording, wrong attribution, etc.)
- **MISSING** — source has it, HTML doesn't
- **EXTRA** — HTML has it but it doesn't trace back to the source (possible fabrication — flag it, don't wave it through)

## 4. Fix mismatches

For every FAIL/MISSING/EXTRA where the correct value is clear from the source, correct the HTML so it matches the source exactly. Edits must be surgical:
- Only touch the specific flagged text/value.
- Never alter surrounding template structure, styling, section order, or copy that already matches.
- If the correct fix is ambiguous (source doesn't clearly resolve it, or a genuinely new candidate content file is ambiguous), leave it as **NEEDS INPUT** — do not guess or invent a value.

## 5. Report

Print a findings table in chat (do not save a report file):

`Section/Block | Item | Verdict (PASS / FAIL→FIXED / MISSING / EXTRA / NEEDS INPUT) | Detail`

Follow with a short summary count (checked / passed / fixed / needs input) and a explicit list of any NEEDS INPUT items still requiring the user's decision.
