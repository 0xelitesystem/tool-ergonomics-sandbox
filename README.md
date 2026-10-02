# Tool Ergonomics Sandbox

Lint your LLM tool definitions for model-tripping ambiguity, then play the model yourself: build tool calls by hand against live schema validation and export the transcript as an eval case.

**Live demo:** https://0xelitesystem.github.io/tool-ergonomics-sandbox/

## Live demo

https://0xelitesystem.github.io/tool-ergonomics-sandbox/

## Use

1. Paste tool definitions (Anthropic, OpenAI, or a bare array) into the Tool definitions box, or click Load sample definitions.
2. Click Parse and lint, then read the Ergonomics lint findings.
3. Write a task under Your task, then in Play the model pick a tool, fill the generated form, and submit it to see whether the arguments validate.
4. Click Export eval case (JSON) or Copy as Markdown to keep the transcript.

## Why this exists

Models choose tools and fill arguments by reading names, descriptions and schemas, so any ambiguity there turns into wrong calls. This page lets you hit that ambiguity yourself before a model does. It is one HTML file with no dependencies, no tracking and no network calls, released under MIT.

## Features

- **Three input formats, auto-detected.** Paste Anthropic tools (`{name, description, input_schema}`), OpenAI tools (`{type: "function", function: {...}}`), or a bare array of `{name, description, schema}`. The sandbox shows which format it detected and normalizes all three to one internal shape. Parse errors are reported with the offending line and a caret under the failing column.
- **Ergonomics lint.** Runs on load and flags the things that make models fail: missing tool descriptions, missing parameter descriptions, vague parameter names (single letters, `data`, `input`, `value`), string parameters that look like enums but declare none, missing `required` arrays, objects nested three or more levels deep, near-duplicate tool names and descriptions, and oversized tool counts. Every finding includes a plain-language line explaining exactly why the model trips on it.
- **Play mode.** Write yourself a task, then act as the model: pick a tool and the sandbox generates an argument form straight from the schema - text and number inputs, checkboxes for booleans, selects for enums, JSON textareas for objects and arrays. Submitting validates the assembled arguments with a from-scratch validator (type checks, `required`, `enum`, `minimum`/`maximum`, `minLength`/`maxLength`, `pattern`, recursion into nested objects and arrays) and shows either a pass or the exact errors the model would get back.
- **Transcript and export.** Every call is recorded chronologically with its arguments, validation outcome, and a per-step notes field for what confused you. Export the whole run as a JSON eval case (`{task, steps: [{tool, args, valid, notes}]}`) or copy it as Markdown.
- **One-click demo.** The sample button loads three fake tools (weather, calendar, search) with deliberately planted flaws - a vague parameter, a missing description, an enum-shaped string with no enum, a missing `required` array, and a deeply nested filter object - so the lint demonstrates itself immediately.

## How it works

The core thesis comes from published tool-design guidance: if a human cannot complete the task with your tools, the model cannot either. Models pick tools by reading names and descriptions as text, and they fill arguments by mapping intent onto parameter names and descriptions. Every ambiguity you can stumble on by hand - a field you cannot fill without reading backend code, two tools you cannot tell apart, a string you have to guess legal values for - is an ambiguity the model hits blind, at scale, with no way to peek.

This sandbox operationalizes that test. It parses and normalizes your definitions client-side, runs a static ergonomics lint over the schemas, and then puts you in the model's seat: an argument form generated purely from the schema, validated by a small hand-written JSON Schema subset validator, with the failures phrased the way a tool-use API would phrase them. The transcript turns your manual session into a reusable eval case for regression-testing the definitions after you fix them.

It pairs with `json-schema-to-tool-definition` and `chatml-message-builder` in the same portfolio: convert schemas into definitions, sanity-check the message framing, and pressure-test the ergonomics here before a model ever sees them.

## Privacy

Everything runs in your browser. Tool definitions are parsed locally and never uploaded; the page makes no network requests and has no external dependencies, no analytics, and no tracking. Exports are generated client-side and saved by your browser.

The page saves one thing in localStorage: your light or dark theme choice, under the key `tes-theme`. Copy as Markdown writes to your clipboard only when you click it.

## Run locally

```
git clone https://github.com/0xelitesystem/tool-ergonomics-sandbox
cd tool-ergonomics-sandbox
```

Open `index.html` in any browser. Or serve the folder and visit http://localhost:8000:

```
python -m http.server 8000
```

## Build

No build step. The whole tool is one `index.html` file with inline CSS and JavaScript.

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).

## License

MIT
