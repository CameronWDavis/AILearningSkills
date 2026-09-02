# AI Tutor Skills/Prompts

A collection of Claude Skills built around a simple idea: **AI is at its best as a personal tutor, not as a brain you outsource to.**

The skills here encode real software engineering practices — the kind of habits that make you a better developer over time — and shape how Claude behaves so it teaches those practices instead of doing the work for you.

## Why this exists

AI can write your code. That's not in question anymore. The question is whether you come out of the interaction stronger or weaker for it.

Problem-solving, reading errors, debugging by hypothesis, understanding how systems fit together — these are durable skills that outlast any specific framework, language, or model. They're what makes someone a real engineer. And they only develop through practice.

The skills in this repo tilt Claude toward being a tutor: explaining concepts, pointing at what to investigate, letting you struggle productively when the struggle is where the learning happens. Not because AI-written code is bad, but because someone who *only* accepts AI-written code isn't learning anything transferable.

Use these when you want to grow. Turn them off when you just want to ship.

## What's in the collection

| Skill | What it does |
|-------|--------------|
| [`ai-learner`](./skills/ai-learner.md/) | Guides you through building software as a teacher rather than a code-writer. Withholds unsolicited code, explains concepts, uses a graduated debugging progression to help you find bugs yourself. |
| [`latex-writing`](./skills/latexwriting.skill) | Skilled with a standardized format to produce Latex documents, use cases could be classes, work or hobbies | 

| prompt | What it does |
|-------|--------------|
| [`4hourgrill.json`](./prompts/4hourgrill.json/) | focuses on rapid teaching of a subject to get you to a semi functional conversational level along with guiding user for new skills |

More skills coming as the collection grows.

## Installing a skill

Each skill lives in its own folder under `skills/`. To install one into your Claude profile:

1. Open the skill's folder and locate `SKILL.md`
2. Package it (or share the `SKILL.md` file directly)
3. In Claude, install it via the skill card that appears when the file is shared

Once installed, the skill's description determines when it triggers — you don't need to manually invoke it. For the learning assistant, phrases like "help me learn X" or "walk me through" will load it automatically.

## Design principles for skills in this repo

Skills added here should share the same posture:

- **Teach, don't type.** Withhold code unless the user explicitly asks. Explain the concept, sketch the shape, let them write it.
- **Match depth to the question.** Short answers to short questions. Expand on request.
- **Point, don't fix.** When debugging, guide the user toward the bug rather than paste back the corrected code.
- **Respect override signals.** When the user asks for code directly, deliver it cleanly without relitigating the rules.
- **Flag patterns kindly.** If the user is skimming or pattern-matching without understanding, say so — a direct callout is more respectful than silent repetition.

A skill that doesn't hold this posture doesn't belong in this collection.

## Contributing

If you want to add a skill:

1. Create a new folder under `skills/` with a descriptive name
2. Write a `SKILL.md` with clear frontmatter (name, description) and a body that captures the teaching posture
3. The description in the frontmatter is the trigger — make it specific about *when* to load, not just what it does
4. Keep the skill focused on one thing; multi-purpose skills trigger unreliably

## License

MIT — use, modify, share.
