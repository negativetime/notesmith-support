# Bibliome Redaction Notes

A synthesis of 45 field notes on Bibliome's redaction tools, collected from one person doing real
evidence work on legal / FOIA-style document sets. Nothing here was drawn from the Bibliome source,
which was not available when this was compiled.

**Tally:** 8 defects · 6 workflow gaps · 10 detection items · 5 leak risks · 16 other ideas

---

## The finding that matters most

**Redactions are thrown away at export. There is no way to correct a mistake except to start the
whole file over.**

Everything else is downstream of this. He reports losing four hours failing to redact a set of large
evidence files — not because any single feature was missing, but because each attempt had to begin
from a blank document. A redaction pass is iterative by nature: you redact, you review, you find one
more name. Bibliome currently supports exactly one attempt.

---

## Defects: things that don't work today

These are reported as failures, not wishes. Several look quick, and two make the feature unusable on
whole categories of file.

- **Redactions vanish once the file is exported.** After output, the work no longer exists in the
  app. Changing one box means redoing the entire document.
- **Single-PDF redaction appears broken — no red areas render.** Drawn regions don't show up at all
  in the per-file path.
- **Password-locked PDFs can't be redacted.** The path fails outright. Encrypted PDFs are routine in
  legal production, so this blocks a common input.
- **Selecting text doesn't create a redaction.** Only drawing a box works. He expects selection to be
  a first-class way to mark something — and separately asks that it redact automatically on select,
  to remove a click.
- **Box selection jumps to regions that weren't selected.** The drawn rectangle doesn't land where it
  was drawn.
- **Some text can't be highlighted at all.** Reported in both the redaction view and the exported
  PDF. Text that displays fine on the page won't respond to selection — so it can't be found, marked,
  or verified.
- **The "Advanced" menu the help section describes isn't findable.** Either the documentation is
  stale or the entry point is buried.
- **The save panel hijacks the next open location.** Save a redacted file to a separate folder, and
  the next file picker opens in the destination folder rather than the source folder he's working
  through. Save and open locations should be tracked separately.

## Persistence and reuse

The structural fix. Redaction stops being a one-shot render and becomes an editable layer that
carries across files.

- **A saved redaction layer on each file's info page.** Re-output a redacted version at any time,
  change what is and isn't redacted without touching the original, toggle the redacted view per file.
- **Copy and paste redaction terms between files.** Evidence sets repeat the same handful of names
  and addresses across hundreds of pages.
- **Saveable redaction templates.** The same idea promoted to a named, reusable object — per matter,
  per client, per production run.
- **A review pass before export.** Click through every redaction in a file and confirm each before
  committing. The last checkpoint before a document leaves the building.
- **A redaction dashboard.** Every term redacted, a search for similar terms, a search for other
  categories of information worth redacting, and an exclusions list for terms that must stay visible.
- **Scoped redaction rules.** Define a parameter rather than a term — "anything related to documents
  in xyz folder" — and let it find and mark, or just highlight for review.

## The detection engine

One marked item should propagate to every instance. The notes are unusually specific about the ways
real documents defeat a naive string match.

- **Mark one, find the rest.** Draw a box, and the app identifies what it is and reports "3 others
  found" — an address, a QR code, a phone number. The highest-leverage interaction in the notes.
- **Bulk detection by category.** Names, SSNs, addresses. Scan the file and produce a list of
  everything redactable; click any entry to walk its instances.
- **Name variants.** Mark "John Adam Smith" and also catch John Smith, John A. Smith, J. Smith,
  John S. — and in reverse, mark John Smith and surface the fuller name elsewhere.
- **Typos and near-misses.** Terms that are close but not exact still leak; fuzzy matching belongs in
  the sweep, not a separate tool.
- **Deliberate lookalike spellings.** His example: agencies producing FOIA documents have rendered
  "Comey" as "Corney" — visually near-identical, textually unsearchable. Homoglyph detection catches
  both other people's evasions and your own OCR errors.
- **Barcodes, QR codes and vertical text.** Include them in auto-detection and decode them in place
  so you can judge whether the contents need redacting. If a number is redacted in the text, redact
  the barcodes that encode it too.
- **Faces in photographs.** Route embedded images through Apple's Vision framework and offer detected
  faces as redaction candidates.
- **Detection has to survive bad PDFs.** Mixed fonts, poor printing, and text layers that render on
  screen but resist selection — the same root cause as the highlighting defect above.
- **Reinforce redactions that already exist.** Find black bars already in a document and extend them,
  to guarantee nothing survives at the edges.
- **Detect failed physical redactions.** Forensic recovery of text under bars that didn't fully cover
  it; his example is released UFO files. He flags this himself as possibly more than the app needs.

## Leaks that redaction alone doesn't stop

The most valuable section, because one of these already happened to him.

- **Show-through from phone-scanned pages (confirmed leak).** Scanning thin paper captures the ink of
  the sheet behind it. He redacted a name on one page, uploaded the redacted PDF to an AI model, and
  believes the model read that same name off the show-through on another page. The fix he wants:
  detect see-through and offset ink and white it out as part of the redaction.
- **Invisible text in the PDF layer.** Transparent text, white on white, text matching the
  background. He raises it as a prompt-injection vector — content planted to manipulate an AI reading
  the file. He wants a hidden-text report on each file's overview page, with one action to strip it.
- **Decoy redactions.** Add extra black boxes over empty or unimportant areas so the size, count and
  position of real redactions reveal nothing. A genuine countermeasure — redaction geometry has been
  used to reconstruct redacted text.
- **Keep the original untouched.** Stated as an assumption throughout; worth making explicit and
  visible in the UI.
- **Explain what was redacted.** A per-file report, generated with Apple Intelligence, describing the
  categories of redaction applied — a privilege log that writes itself.

## The redaction canvas

Small individually; together they're the difference between a viewer with a drawing tool and an
editor.

- **Redactions as their own layer.** Toggle the layer on and off; select any box; drag it; resize it.
- **Preview the true output.** Switch boxes from working transparent-red to final solid black without
  exporting.
- **Right-click a redaction for its options.** Find others like it, change its label, exclude it,
  delete it — at the box.
- **Lasso select,** for irregular regions a rectangle can't cover.
- **Selecting text redacts it.** No second click; selection is the gesture.

## Output options

- **Print labels over the bars.** "Person A", "Person B", "[claimant address]" — consistent
  pseudonyms so a reader can still follow who is who. Standard practice in filed documents.
- **Suggest the labels automatically.** Generate a description of what's underneath that gives enough
  context without revealing the detail.
- **Outline instead of fill, to save toner.** Export black bars as white boxes with a black outline.
  Small, easy, and immediately appreciated by anyone printing a thousand-page production.

## Document conditioning

Not really about redaction: repairing bad scans so the rest of Bibliome can work with them. This is
the part he thinks could stand alone as a PDF optimizer.

- **De-warp scanned pages.** Phone-scanned documents are rarely flat or photographed square.
- **Condition faint or blurry text to solid black,** so it reads and so detection can find it.
- **Detect the font and re-render text over the bitmap.** The ambitious version: replace degraded
  scanned type with real, selectable text in a matching face. This is what would make the
  unselectable-text problem disappear for good.
- **Remove show-through and hidden text as a cleanup step,** producing a clean working copy beside
  the untouched original.

## Just tell it what to do

He points out that Bibliome already has an LLM in it, and asks why redaction requires clicking at
all.

- **Describe the redaction in your own words.** "Since it already is working with an LLM, why not
  have the option to tell the app in your own words what you want the redaction engine to do, and
  have it do it on its own." Worth pairing with the review pass — plain-language instructions are
  most useful when a human confirms the result before export.
- **A report explaining the redactions made,** per file, in plain language.
- **Highlight rather than redact.** Let a rule mark candidates for a human to approve.

## Ship it separately?

A business thought he returns to twice — at the start of the notes and again at the end, after
listing everything he'd want.

**The redaction engine as its own app.** Possibly two products: a PDF optimizer that cleans documents
up, and a redactor that includes the optimizer. Sold cheaper than Bibliome, at a wider audience.

The notes make the case for this without arguing it: nothing in the detection, conditioning or output
sections depends on Bibliome's library. The one thing that does is per-file persistence — which is
exactly the piece that's missing.

---

## If you only do five things

Ordered by how much of the rest they unblock, not by effort.

1. **Make the redaction layer persist.** Store it with the file, editable and re-exportable. This
   alone converts the four-hour failure into an ordinary afternoon, and every reuse feature depends
   on it.
2. **Fix the paths that are simply broken.** Single-file redaction rendering nothing, password-locked
   PDFs, the jumping selection box, the save panel stealing the open location, and the missing
   Advanced menu. Five separate reports, all cheap next to the value of the feature working at all.
3. **Make text selection a redaction gesture** — and solve the text that won't highlight, since it's
   the same underlying problem and it silently breaks find-and-redact everywhere else.
4. **Mark one, find the rest.** Detect the type of the thing just redacted, report how many others
   exist, and offer to apply them. Name variants and fuzzy matching are the natural follow-ons.
5. **Close the show-through and hidden-text leaks.** He has a documented case of a correctly redacted
   file giving up its contents anyway. For a tool people trust with evidence, that's the one class of
   bug that costs more than time.

## Worth checking before acting

Points where the notes are ambiguous and this is a reading rather than a determination.

- **"Redact per single PDF seems broken — no red areas."** Read as a per-file mode distinct from a
  bulk mode. If there's only one mode, this may be the same defect as the jumping selection box.
- **The missing Advanced menu.** Could be stale help text, a platform difference, or a menu that only
  appears in a state he wasn't in. Cheapest to reproduce first.
- **The AI show-through leak.** He infers it — the model surfaced information he'd redacted
  elsewhere. Plausible, and worth reproducing directly before designing the fix.
- **Forensic recovery of other people's failed redactions** is the one item he flags as possibly
  excessive, and that seems right — it's a different product from the one these notes describe.
- **Effort is not estimated anywhere here.** The ordering weighs unblocking value only; font
  detection and de-warping in particular are large.
