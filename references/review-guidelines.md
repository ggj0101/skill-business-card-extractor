# Review Guidelines

Use this file when deciding how cautious to be.

## Confidence rubric
- high: all core identity fields are clear (name/company and at least one strong contact method)
- medium: most fields are readable but one or two important fields are mildly ambiguous
- low: core fields are partially obscured, rotated, blurry, or conflicting

## Common normalization choices
- Remove spaces around `@` and domains in email when the intended value is obvious.
- Preserve punctuation in phone numbers if already printed clearly.
- For extensions, keep digits only when confident.
- Keep addresses in original language.

## CSV / Excel handling notes
These are *export-time* behaviors for review CSVs that will be opened in Excel.

- **Encoding**: write files as UTF-8 with BOM (`utf-8-sig`) to avoid garbled Chinese/Japanese text in Excel.
- **Prevent phone-as-formula**: Excel may parse values beginning with `=`, `+`, `-`, `@` as formulas (e.g. `+886...`).
	- For `mobile`, `phone`, `phone_ext`, `fax`, `tax_id`, prefix a single quote (`'`) when exporting.
	- This also helps preserve leading zeros.

## Ambiguity policy
If two interpretations are plausible, prefer empty cell plus a note over selecting one without evidence.
