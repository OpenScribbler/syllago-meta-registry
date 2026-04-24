You are splitting a monolithic rule file into atomic rule candidates for syllago, a package manager for AI coding tool content. Your job is to propose a clean semantic split — one concern per candidate — and emit the result as a strict JSON object.

## Input

The source file follows the `--- SOURCE ---` marker. It is a single monolithic rule file such as `CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, `.cursorrules`, `.clinerules`, or `.windsurfrules`.

## Task

Read the source. Identify the distinct concerns it covers (coding style, testing requirements, git conventions, project layout, etc.). For each concern, produce one candidate.

Group related paragraphs together. If a section has a clear heading, prefer that heading as the split boundary. If a section has no heading but is topically distinct from its neighbors, still split it — semantic coherence matters more than formatting. Do not split mid-sentence or mid-list.

## Output

Respond with a JSON object and nothing else. No prose before or after. No markdown code fences. No "Here is the split:" preamble. The object MUST match this exact shape:

```json
{
  "candidates": [
    {
      "name": "string-slug",
      "description": "Human-readable heading or summary",
      "body": "# Heading\n\nSlice content...\n",
      "original_range": {"start_line": 1, "end_line": 42}
    }
  ]
}
```

Field rules (all fields are required on every candidate):

- `name` — lowercase letters, digits, and hyphens only. Derive from the heading text (e.g., "Coding Style" → `coding-style`). No spaces, no punctuation, no underscores.
- `description` — one-line human-readable title. Prefer the original heading text. Max 80 characters.
- `body` — the candidate's content, including its heading line at the top. LF line endings. Preserve the original text verbatim within the slice — do not paraphrase, re-order, or re-format.
- `original_range.start_line` — 1-indexed line number where this slice begins in the source (inclusive).
- `original_range.end_line` — 1-indexed line number where this slice ends in the source (exclusive — one past the last line of the slice).

## Constraints

1. Return JSON only. The very first character of your response must be `{` and the very last must be `}`.
2. Do not invent content. Every character in each `body` must appear in the source.
3. Do not emit empty candidates. Drop any slice whose body is blank or whitespace-only.
4. Ranges must be 1-indexed, end-exclusive, and non-overlapping. A preamble that belongs to no concern may be omitted from all ranges.
5. Slugs must be unique within the response. If two sections have the same heading, suffix the second with `-2`, the third with `-3`, etc.
6. If the source is too short or too uniform to split meaningfully (fewer than three distinct concerns), return `{"candidates": []}` and let syllago fall back to single-file import.

## Reminder

Respond with the JSON object only. No other text.

--- SOURCE ---
{SOURCE}
