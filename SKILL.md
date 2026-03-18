---
name: business-card-batch-extractor
description: extract structured contact data from one or many business card images and prepare a review-friendly csv. use when a user uploads multiple business card photos, wants batch processing across a folder of images, needs field normalization, or asks for a csv that humans can verify and correct before import.
---

# Overview
Batch-process business card images and return a CSV that is optimized for manual review.

Prefer a flat, deterministic extraction workflow over prose. The primary deliverable is a CSV. A short preview table or summary is optional.

## Default workflow
1. Inspect all uploaded images and treat them as a batch.
2. For each image, mentally correct orientation before reading.
3. Extract the card contents without translating the source text.
4. Normalize into the schema below.
5. Output one CSV row per image.
6. Flag uncertainty in review columns instead of guessing.

## Output schema
Always keep this column order:

```csv
source_file,record_id,name,company,title,mobile,phone,phone_ext,fax,email,address,website,tax_id,country,language_detected,confidence,review_status,notes
```

### Field rules
- `source_file`: original filename if available; otherwise use a stable label such as `card_001`.
- `record_id`: deterministic row id such as `BC-0001`, `BC-0002`.
- `name`: person name only. Do not include honorifics unless printed as part of the name.
- `company`: company or organization.
- `title`: job title / department.
- `mobile`: mobile/cell number only.
- `phone`: main office line only.
- `phone_ext`: extension only, without labels like `ext` or `分機`.
- `fax`: fax number only.
- `email`: lowercase when safe to normalize.
- `address`: full mailing address in original language.
- `website`: website/domain as printed.
- `tax_id`: business registration / tax id / 統編 if present.
- `country`: fill only if explicit or strongly implied by the address/phone format; otherwise leave blank.
- `language_detected`: `zh-Hant`, `zh-Hans`, `en`, `ja`, `ko`, `mixed`, or another short label that best matches the card.
- `confidence`: `high`, `medium`, or `low` for the overall row.
- `review_status`: default to `needs_review` for every row.
- `notes`: record uncertainty, ambiguity, duplicate values, partially visible text, or OCR-like issues.

## Extraction rules
- Never hallucinate missing values.
- Preserve original wording for names, company names, titles, and addresses.
- Separate `phone`, `phone_ext`, `mobile`, and `fax`.
- If multiple phone numbers appear and the label is ambiguous, put the most likely office line in `phone`, additional ambiguity in `notes`, and lower `confidence`.
- If both sides of a card are present, merge them into one row when they clearly belong to the same person/card.
- If one image contains multiple cards, create one row per card and reflect that in `source_file` or `notes`.
- If a value is unclear, leave the field empty and explain in `notes` rather than guessing.

## Batch handling
- Process every uploaded image before emitting the final CSV.
- Keep row formatting consistent across the batch.
- When duplicate cards appear, keep separate rows unless the user explicitly asks for de-duplication.
- If filenames are unavailable, number the rows in image order.

## Review-friendly behavior
The CSV is intended for human validation and correction.
- Default `review_status` to `needs_review`.
- Use `notes` to explain exactly what needs checking, for example:
  - `name unclear`
  - `possible mobile/phone swap`
  - `email partially obscured`
  - `card rotated and low contrast`
- Use `confidence=low` whenever key identity fields are uncertain.

## Final answer pattern
- Provide a brief sentence stating how many cards were processed.
- Then provide the CSV in a fenced `csv` block.
- If helpful, add 1-3 short bullets summarizing common issues found in the batch.
- Do not add extra commentary inside the CSV block.

## Example notes patterns
- `uncertain company suffix`
- `website inferred from printed domain spacing`
- `fax label present but number partially cut off`
- `bilingual card; english title chosen as printed`
