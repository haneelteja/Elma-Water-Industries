# Certificates

Everything in this folder is deployed publicly (Vercel serves the whole `elma-website/` tree as static files), so only put files here that are safe to publish.

| File                     | Used by                                              |
| ------------------------ | ----------------------------------------------------- |
| `fssai-license-p1.jpeg`  | "FSSAI License" card — page 1 (main license details) |
| `fssai-license-p2.jpeg`  | "FSSAI License" card — page 2 (product annexure)     |

Both are referenced in the `CERTIFICATES` config in `elma-website/index.html`'s `<script>` block (`id: 'fssai'`).

## Water quality lab reports — deliberately NOT here

Elma's Hexiqon Laboratory (ISO 17025:2017-accredited) test reports each carry this restriction printed on them:

> "This report, in full or in part, shall not be published, advertised, used for any legal action, unless prior permission has been secured from the Director General of Bureau of Indian Standards. This Report is only for BIS Certification purpose."

So the actual PDFs and their specific test figures are **not** on the site and must not be added to this folder or committed to git — that would count as publishing them without BIS's permission, license terms aside from just being visible in a public repo/deployment. Instead, the "Water Quality Testing" card on the site (`id: 'water-report'` in `CERTIFICATES`) is an `info`-type entry: a factual, non-numeric statement about the testing programme (lab, accreditation, standard, what's tested), with no file link.

If you get written permission from BIS to publish, switch that entry to `type: 'document'`, add the PDF(s) here, and point `files` at them — same pattern as the FSSAI entry.

## Updating the FSSAI license (e.g. after renewal)

1. Replace `fssai-license-p1.jpeg` / `-p2.jpeg` (same filenames, or update `files` in the `CERTIFICATES` config).
2. Update `issuedOn` / `validUpto` for the `fssai` entry in the same config — the badge on the site automatically shows "Valid until …" and flips to an amber "Renewal in process" state once it's within 45 days of expiry (or past it).
3. Commit and redeploy.

## Adding a new certificate

Add a new entry to the `CERTIFICATES` array in `index.html` — a new card appears automatically, no other HTML edits needed. Use `type: 'document'` with a `files` array (images or a PDF) for anything safe to publish in full, or `type: 'info'` with `infoHtml` for anything you can only describe, not publish.
