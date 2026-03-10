# skill-eval

**Do agent skills actually help? We tested 123 to find out.**

A self-evolving evaluation engine that blind-tests [ClawHub](https://clawhub.ai) agent skills against a no-skill baseline. Every skill gets a standardized score card, and the methodology improves with each batch.

## Key Findings

- **123 skills evaluated** using Claude Opus 4
- **76% are genuinely useful** (score 7+, "Recommended")
- **92% show positive quality delta** over baseline
- **Only 4% are actively harmful** (score < 3)
- **The #1 improvement lever is removing content**, not adding it

## Live Leaderboard

**[jensen-srp.github.io/skill-eval](https://jensen-srp.github.io/skill-eval/)**

Browse all 123 skill cards, filter by verdict, sort by score.

## Methodology

Blind A/B testing with two-layer assertions:

1. **Layer 1 (Deterministic):** File checks, keyword presence, format compliance
2. **Layer 2 (Rubric-based):** LLM-as-judge quality scoring with specific rubrics

Each skill runs the same prompts with and without the skill loaded. The only variable is whether the skill is present. Scores combine quality (0-5), value-add delta (0-3), and efficiency (0-2).

**[Read the full methodology](https://jensen-srp.github.io/skill-eval/blog/methodology.html)**

## Scoring

| Score | Verdict | Meaning |
|-------|---------|---------|
| 7-10 | Recommended | Clear value over baseline |
| 5-6.9 | Conditional | Some value with trade-offs |
| 3-4.9 | Marginal | Overhead without proportional improvement |
| 0-2.9 | Not Recommended | Baseline is comparable or better |

## Top 10

| Rank | Skill | Score | Delta |
|------|-------|-------|-------|
| 1 | Explain Code | 10.0 | +50% |
| 2 | BookNotes | 9.6 | +88% |
| 3 | Self-Improving with Reflection | 9.5 | +100% |
| 4 | EasyDoc Parse | 9.5 | +100% |
| 5 | BusAPI Agent Marketplace | 9.5 | +100% |
| 6 | Binance Web3 API | 9.5 | +100% |
| 7 | Article Writer (Chinese Viral) | 9.5 | +100% |
| 8 | KU Portal | 9.5 | +100% |
| 9 | Scan to Skill | 9.5 | +100% |
| 10 | GitVerse | 9.5 | +100% |

## Self-Evolving Engine

The engine improves its own methodology after each evaluation batch:

- **v0.1.0** -- Basic blind A/B, 7 phases
- **v0.2.0** -- Dependency handling, category-specific assertions
- **v0.3.0** -- Anti-pattern detection, skill improvement pipeline
- **v0.4.0** -- Multi-model support, self-evolving improvement engine, 12 phases

## Structure

```
skill-cards/       -- 123 evaluation reports (one per skill)
blog/              -- Methodology blog post
index.html         -- Interactive leaderboard
```

## For Skill Authors

Your skill has been evaluated. Find your report in `skill-cards/<your-skill>.md`.

Tips for higher scores:
1. Be opinionated -- define specific formats, banned words, required sections
2. Don't teach -- instruct. Use MUST/ALWAYS/NEVER, not tutorials
3. Keep it under 100 lines
4. Handle errors gracefully
5. Don't reference tools that don't exist

---

Built with skill-eval v0.4.0 | [ClawHub](https://clawhub.ai)
