# Do Agent Skills Actually Help? We Tested 123 to Find Out.

*Published 2026-03-10 | skill-eval v0.4.0*

---

Everyone's building agent skills. Prompt templates, workflow scaffolds, CLI wrappers -- the ClawHub marketplace has hundreds. But here's the question nobody's answering: **do they actually make the agent better?**

We built a system to find out. Not with vibes. Not with self-reported metrics. With blind A/B testing at scale.

## The Setup

**skill-eval** is a self-evolving evaluation engine. For each skill, it runs the same task twice: once with the skill loaded, once without (bare model). Same prompts, same assertions, same grading. The only variable is whether the skill is present.

We tested **123 skills** from ClawHub using Claude Opus 4 as the execution model. Every skill got:
- 2-3 realistic test prompts
- Deterministic assertions (file checks, keyword presence, format compliance)
- Rubric-based quality grading
- Timing and token usage measurement

The result: a score from 0-10 combining quality (0-5), value-add delta (0-3), and efficiency (0-2).

## The Results

**76% of skills are genuinely useful.** That's 94 out of 123 scoring 7+ ("Recommended").

The full breakdown:

- **Recommended (7-10):** 94 skills (76.4%)
- **Conditional (5-6.9):** 25 skills (20.3%)
- **Marginal (3-4.9):** 3 skills (2.4%)
- **Not Recommended (0-2.9):** 5 skills (4.1%)

92% of skills showed a positive quality delta over baseline. The model is measurably better with most skills loaded.

But not all skills are created equal.

## What Makes a Great Skill

The top scorer -- **Explain Code** at 10.0/10 -- teaches the model to use analogies, ASCII diagrams, and structured walkthroughs when explaining code. The model CAN do these things without the skill, but it doesn't by default. The skill makes it consistent.

This is the pattern: the best skills don't teach the model new facts. They encode **preferences and processes** the model wouldn't follow on its own.

**Article Writer** (9.5/10) defines a banned-word list, numbered module structure, and "golden sentence" convention. The baseline model uses filler words freely; the skill eliminates them. 100% delta -- our first perfect discriminator.

**BookNotes** (9.6/10) produces structured reading notes with a specific template. Baseline pass rate: 12%. With skill: 100%. The model needs to be told what format to use.

The common thread: **opinionated skills that define specific, testable conventions score highest.** Vague "be better at X" instructions produce minimal delta.

## What Makes a Terrible Skill

The bottom of the leaderboard tells an equally clear story.

**bio-generator-pay** and **hashtag-generator-pay** both scored 2.0/10 with 0% pass rate. Why? They require paid API credentials that weren't configured. The baseline model completed the same tasks perfectly. These aren't bad skills -- they're broken deployment stories.

**finance-lite** (2.8/10) failed because its error handling bypasses its own output formatting. When the market data API is down, it drops the source citations and freshness timestamps that its template requires. The template is good; the fallback path is not.

## The 8 Failure Modes

We cataloged every way skills fail:

1. **Dependency gate hard-fail** -- Paid skills crash without API keys while baseline works fine
2. **Phantom tooling** -- SKILL.md references scripts that don't exist in the package
3. **Reference manual anti-pattern** -- 200+ lines of tutorial content the model already knows
4. **Library-as-skill** -- Contains Python class definitions instead of instructions
5. **Baseline already strong** -- Technical domains where the base model already excels
6. **Template compliance drift** -- Error paths bypass required formatting
7. **Marketing claims without evidence** -- "7.8x faster" with no data
8. **Assertion-skill mismatch** -- Output improves but score doesn't because assertions test the wrong thing

The most common? **Reference manual anti-pattern.** Skill authors paste SQL templates, framework guides, and code patterns into SKILL.md thinking more context helps. It doesn't. The model already knows this stuff. You're just burning tokens.

## The Surprising Finding: Less Is More

We built an automated skill improvement pipeline. For skills scoring below 7, we rewrite the SKILL.md and re-evaluate.

The results:

| Skill | Before | After | What Changed |
|-------|--------|-------|-------------|
| Data Analyst | 5.0 | 7.0 | Removed 80% of content |
| Data Model Designer | 5.5 | 7.0 | Replaced Python classes with instructions |
| Test Runner | 5.5 | 7.0 | Removed framework tables, added mandates |

The primary improvement lever in all three cases: **deleting content**. Not adding instructions. Not making the skill smarter. Just making it smaller.

Data Analyst's original SKILL.md was full of SQL templates, pandas patterns, and statistics references. The model already knows all of this. After removing 80% of the content and adding 10 lines of behavioral mandates (MUST include methodology section, NEVER use SELECT *, ALWAYS start with executive summary), the overhead dropped from +155% to +45% and the score jumped 2 points.

**Skills should be behavioral contracts, not textbooks.**

## The Self-Evolving Engine

The evaluation system itself improves over time. It's not a static benchmark.

After each batch of evaluations, the engine:
1. Updates its lessons learned (what assertion patterns work for which skill categories)
2. Catalogs new failure modes
3. Absorbs proven patterns back into its own methodology document
4. Bumps its version when the methodology meaningfully changes

We went from v0.1.0 (basic blind A/B, 7 phases) to v0.4.0 (12 phases, multi-model support, self-evolving improvement engine) in 4 days. Each batch produced sharper assertions that caught problems the previous batch missed.

The improvement engine has its own knowledge base too -- learned patterns like "for reference-manual skills, delete 70% and add MUST/ALWAYS/NEVER mandates, expected gain: +1.5 to +2.0 points." It tracks which strategies work, which fail, and gets better at fixing skills over time.

## What This Means for Skill Authors

If you're writing agent skills:

1. **Be opinionated.** Vague skills score low. Define specific output formats, banned words, required sections.
2. **Don't teach -- instruct.** The model already knows SQL, Python, and design patterns. Tell it WHAT TO DO, not HOW THINGS WORK.
3. **Keep it under 100 lines.** Every line of SKILL.md costs tokens. If your skill is over 100 lines, question every paragraph.
4. **Use MUST/ALWAYS/NEVER.** Behavioral mandates change model behavior. Descriptions don't.
5. **Handle errors gracefully.** If your skill depends on external data, the error path still needs to follow your output format.
6. **Don't reference tools that don't exist.** If your SKILL.md mentions scripts that aren't in the package, that's phantom tooling.

## What's Next

- **Multi-model evaluation**: Testing skills across Claude, GPT-4.1, and Gemini to see which skills are model-agnostic vs model-specific
- **Skill author feedback pipeline**: Auto-filing PRs with improved SKILL.md files for low-scoring skills
- **Regression tracking**: Re-evaluating skills when they update to catch quality regressions
- **Skill composition testing**: What happens when you stack multiple skills?

The leaderboard is live. The methodology is open. The engine keeps getting better.

Check the [full leaderboard](./index.html) to see how your favorite skills rank.

---

*Built with skill-eval v0.4.0. Methodology: [SKILL-EVAL.md](https://github.com/jensen-srp/skill-eval). Questions? Open an issue.*
