---
name: split-rules-llm
description: Use when syllago's Add wizard or `syllago add --split=llm` asks for an LLM-driven split of a monolithic rule file (CLAUDE.md, AGENTS.md, GEMINI.md, .cursorrules, .clinerules, .windsurfrules) into atomic rule candidates. Reads the source file on stdin, proposes a semantic split, and emits a structured JSON object the wizard's Review step can consume.
---

# split-rules-llm

LLM-driven splitter for monolithic rule files. This is the opt-in alternative to syllago's deterministic H2/H3/H4/marker splitter (see D9 of the V1 rules-splitter design doc). Users who prefer a semantic split over a heading-based one install this skill and pass `--split=llm` to `syllago add`.

## Contract

| Aspect | Value |
|---|---|
| Input (stdin) | UTF-8 bytes of one monolithic rule file. No flags, no path — the harness pipes the file contents in. |
| Output (stdout) | A single JSON object matching the schema in [`schema.json`](./schema.json). Nothing else — no prose, no code fences. |
| Exit code | `0` on success. Any non-zero code signals failure; syllago will surface the stderr to the user. |

## Output schema

The skill emits exactly this shape:

```json
{
  "candidates": [
    {
      "name": "coding-style",
      "description": "Indent rules and naming conventions",
      "body": "# Coding Style\n\nUse 2-space indentation...\n",
      "original_range": {"start_line": 5, "end_line": 42}
    }
  ]
}
```

Field semantics (all required, all strings unless noted):

- `name` — slug suitable for a library directory name. Lowercase letters, digits, hyphens; no spaces or punctuation.
- `description` — the original heading text (pre-slugify), one line, human-readable.
- `body` — the candidate body bytes. Include the slice's heading at the top. Line endings are LF. syllago re-normalizes canonically at write time (see design doc D12), so trailing whitespace does not matter here.
- `original_range.start_line` — 1-indexed line number in the source where this slice begins (inclusive).
- `original_range.end_line` — 1-indexed line number in the source where this slice ends (exclusive — one past the last line of the slice).

A fully-covered split has `original_range` values that tile the source without overlap or gap (except leading preamble and trailing blank lines, which may be omitted — same rule as the deterministic splitter).

## How the skill runs

The skill is a prompt-template + instruction bundle. When the user's harness invokes it:

1. The harness reads `prompt.md` from this directory.
2. It substitutes the literal token `{SOURCE}` with the stdin bytes.
3. It sends the substituted prompt to the user's LLM.
4. It writes the LLM's JSON object response verbatim to stdout.

The skill deliberately delegates execution to the user's LLM — syllago does not ship an LLM dependency (see D9 for rationale). Any harness capable of text-completion can host this skill.

## When to use

- Input is a monolithic rule file with mixed concerns, no H2 headings, or non-standard structure where the deterministic splitter returns a `SkipSplitSignal`.
- User has opted into AI splitting via `syllago add --split=llm` or selected the "AI split" path in the Add wizard.
- Source file is under ~50KB. Larger files should be split in chunks by the harness before invoking the skill.

## When NOT to use

- Input has clean H2 headings — the deterministic splitter is cheaper and perfectly accurate.
- Input is already atomic (a single `rule.md` for one concept) — pass `--split=single` to syllago instead.
- Harness cannot make an LLM call — use the deterministic splitter.

## Files in this skill

| File | Purpose |
|---|---|
| `SKILL.md` | This file — the skill contract, read by the harness at invocation time. |
| `prompt.md` | Prompt template sent to the LLM. Contains the `{SOURCE}` placeholder. |
| `schema.json` | JSON Schema for the output object. Use to validate or regenerate the LLM response if it fails to parse. |
| `.syllago.yaml` | syllago metadata (name, description, tags, version). |

## Gotchas

- **Strict JSON only.** The LLM must emit the JSON object and nothing else. No leading "Here is the split:", no trailing commentary, no markdown code fences. The prompt repeats this three times; trust it or extend it if your LLM still wraps output.
- **Line ranges are 1-indexed, end-exclusive.** A slice from line 5 through line 41 has `start_line: 5, end_line: 42`. This matches the deterministic splitter's `OriginalRange` semantics so the Review step can treat both producers identically.
- **Slug stability.** The LLM should derive slugs from heading text, not invent them. Stable slugs let users re-split the same file and get the same library entries (no ghost duplicates).
- **No empty candidates.** Drop empty-body slices — don't emit a candidate with `body: ""`.
