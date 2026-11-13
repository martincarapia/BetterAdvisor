# Contributing to BetterAdvisor

Thanks for helping. The goal is simple: any student should be able to work out their registration before the advisor meeting, whatever system their school uses.

## Most wanted

- **Other registration systems:** Workday Student, PeopleSoft Campus Solutions, Ellucian Colleague, Jenzabar, and custom portals. Where are the section search, the degree audit and the register button, and are there JSON endpoints behind them?
- **Other degree-audit tools:** uAchieve, Stellic, and audits built into Workday Student.
- **New gotchas:** portal quirks, hold types, linked-section rules, and calendar edge cases (time zones, DST, terms split into sessions).
- **Examples:** anonymized options write-ups or calendar layouts in [`examples/`](examples/).

## Rules

1. **No personal data.** No names, student IDs, CRNs from your real schedule, emails, or screenshots that show any of them. Use made-up schools, courses and people in examples.
2. **Stay generic.** Write for any school on a given system. If a step only applies to one school, label it as an example.
3. **Keep the safety rules.** Claude never enters passwords. Registration Submit and sending email happen only with the student's explicit approval. The degree audit is the source of truth. Transfer credit and substitutions are always "confirm with your advisor." Pull requests that weaken these won't be merged.
4. **The advisor stays in the loop.** BetterAdvisor helps students prepare. It doesn't replace advising.

## Adding support for another system

Extend the existing skill when the workflow is the same and only the data-pulling steps differ. Add a section like `## Step 3 (Workday Student): Pull every section` next to the Banner one. Create a new skill folder only when the workflow itself is different.

Each skill is a folder under `skills/` with a `SKILL.md` that starts with frontmatter:

```yaml
---
name: "skill-name"
description: "Use when ... (one sentence that tells Claude when to load the skill)"
---
```

## Testing your change

Run the skill with Claude against your own portal, end to end, up to (but not including) Submit unless you actually want to register. In the pull request, describe:

- the system and version you tested on (no school name needed)
- what worked, what you changed, and anything still untested

## Pull request checklist

- [ ] No personal data in the diff, examples, or screenshots
- [ ] Safety rules unchanged
- [ ] Tested end to end, or untested parts marked as such
- [ ] README updated if you added a skill or a supported system
