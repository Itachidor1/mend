# mend

One patch tool for every AI edit format. `mend` applies an edit an AI model wrote to a tree of text files, in any of four formats: unified diff, SEARCH/REPLACE blocks, the V4A patch format, and text-editor tool calls as JSON. Every file is changed or nothing is.

## Usage

```
node main.ts [--format=NAME] [--check] [--strict] [PATCH]
```

- `PATCH` is the patch file, relative to the working directory; when absent or `-`, the patch is read from standard input.
- `--format=NAME` reads the patch in one format: `unified`, `search-replace`, `v4a`, `json`, or `auto` (the default, which detects it).
- `--check` does everything but write: the same output and exit code, and no file is created, changed or deleted.
- `--strict` matches lines exactly (tier 0 only).

## Behaviour

- Exit 0: the tree is written; one line per changed path (`A`, `D`, `M`, or `R old -> new` for a move), then `ok: <n> files`.
- Exit 1: nothing is written; one `error: <code> <path>` line per failed edit, then `failed: <k> of <n> edits`.
- Exit 2: nothing is written; exactly one line — `error: usage`, `error: no-patch`, `error: format`, or `error: parse <N>`.

Lines an edit names are found at the first of three matching tiers: exact, ignoring trailing whitespace, or ignoring leading and trailing whitespace (with re-indentation of the new lines). A unified hunk takes the occurrence nearest its line number; a SEARCH/REPLACE block requires exactly one occurrence; a V4A chunk takes the first at or after its cursor. See `SPEC.md` in the kit for the whole contract; the implementation is `main.ts`, Node 22 standard library only.

## Practice

The kit's practice set (100 checks) passes 100/100:

```
node check.ts program/generator/practice.json
```
