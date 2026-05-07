# SKILL.md template

Substitute `{{variables}}` with intake answers. Strip any `<!-- IF: ... -->` ... `<!-- ENDIF -->` block whose flag is false. Do not leave the block markers in the output.

---

```markdown
---
name: {{skill-name}}
description: {{trigger-description}}
---

# {{skill-display-name}}

{{one-line-purpose}}

## When to use this skill

{{when-to-use-paragraph-derived-from-trigger-description}}

Common triggers:

- "{{example-trigger-1}}"
- "{{example-trigger-2}}"
- "{{example-trigger-3}}"

## How it works

1. {{step-1-derived-from-purpose}}
2. {{step-2}}
3. {{step-3}}

## Input

{{expected-input}}

## Output

{{expected-output}}

## Example

See `examples/input.md` for a sample input and `examples/output.md` for the expected output.

<!-- IF: needs-scripts -->
## Scripts

This skill uses helper scripts in `scripts/`. See `scripts/README.md` for what each script does and how it is invoked.
<!-- ENDIF -->

<!-- IF: needs-references -->
## References

Domain-specific knowledge for this skill lives in `references/`. Claude reads only the relevant reference file based on the user's situation, keeping the SKILL.md body small.
<!-- ENDIF -->

<!-- IF: needs-assets -->
## Assets

Templates, letterheads, and other assets used in output live in `assets/`. See `assets/README.md` for the inventory.
<!-- ENDIF -->

## Iteration

To improve this skill's triggering accuracy or output quality, run it through Anthropic's [skill-creator](https://github.com/anthropics/claude-skills) evaluation loop.
```

---

## Variable derivation rules

| Variable | How to derive |
|---|---|
| `{{skill-name}}` | Direct from intake (kebab-case, validated) |
| `{{skill-display-name}}` | Title Case from `{{skill-name}}` (replace hyphens with spaces, capitalize each word) |
| `{{trigger-description}}` | Direct from intake (validated 100–400 chars) |
| `{{one-line-purpose}}` | Direct from intake (validated 10–120 chars) |
| `{{when-to-use-paragraph-derived-from-trigger-description}}` | One paragraph paraphrasing the trigger description in narrative form. 2–4 sentences. |
| `{{example-trigger-1..3}}` | Extract three concrete trigger phrases from the trigger description. If fewer than three appear, ask the user. |
| `{{step-1..3}}` | Decomposition of the workflow implied by the purpose + I/O. Ask the user if not obvious. |
| `{{expected-input}}` | Direct from intake |
| `{{expected-output}}` | Direct from intake |

## Conditional block handling

The template uses HTML-style flag comments so non-applicable sections vanish cleanly:

```markdown
<!-- IF: needs-scripts -->
## Scripts
...
<!-- ENDIF -->
```

Process order:

1. Read the template
2. For each `<!-- IF: flag -->...<!-- ENDIF -->` block: if `flag` is true, remove only the markers; if false, remove the entire block including markers
3. Substitute `{{variables}}`
4. Verify no `{{...}}` placeholders remain — if any do, that's a generation bug, halt and ask the user
5. Verify no `<!-- IF:` markers remain
6. Write the file
