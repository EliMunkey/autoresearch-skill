autoresearch-skill

Point an AI agent at your codebase. Wake up to a better one.
An open-source Claude Skill that brings Karpathy's autoresearch pattern to any project with a measurable optimization target. It scans your code, finds what can be improved autonomously, scaffolds the entire experiment loop, and optionally kicks it off.
Works with Claude Code, Claude.ai, and the Claude API.
What it does
Karpathy's autoresearch showed that an AI agent can run hundreds of experiments overnight on a training script, keeping improvements and discarding failures. The results were striking: ~20 real improvements discovered autonomously, 11% faster time-to-GPT-2, and changes that transferred from small to large models.
But the pattern isn't limited to LLM training. It works on anything with a number to optimize and a way to measure it.
This skill generalizes autoresearch to any project:

Scans your codebase and identifies optimization candidates
Ranks them by suitability (metric clarity, experiment speed, file scope, headroom, risk)
Scaffolds a tailored autoresearch setup (program.md, eval harness, results tracking)
Establishes a baseline to beat
Kicks off the autonomous loop if you want it to

What makes a good target
The pattern works when three things are true:
PrerequisiteWhat it meansExampleMeasurable metricA number that can be computed automaticallyNDCG, accuracy, latency, val_bpb, throughputFast experimentsYou can run and evaluate in minutes, not hoursA training script, a benchmark suite, an eval harnessClear keep/discard"Higher is better" or "lower is better" fully determines successNo human judgment needed per experiment
Works well on:

ML training scripts (loss, accuracy, F1)
Search/ranking algorithms (NDCG, MRR, precision)
Data processing pipelines (speed, compression, accuracy)
System prompts and LLM prompts (eval score, pass rate)
Algorithm implementations with benchmarks (execution time, throughput)

Doesn't work on:

UI code (subjective quality)
Code with no benchmarks (no metric)
Multi-file changes needed (too much blast radius)
Anything requiring human judgment per experiment

System prompts: a first-class target
This skill treats prompt optimization as a core use case, not an afterthought. A system prompt is the perfect autoresearch target: it's a single file, experiments are fast (one API call + eval), and with a held-out eval set you get a clean score.
The skill scaffolds prompt-specific guardrails:

Eval set isolation — agent never sees the test cases, only the aggregate score
Stochastic output handling — temperature=0 or multi-run averaging
Prompt-specific experiment ideas — few-shot examples, instruction restructuring, edge case handling

This works for chatbot prompts, agent instructions, RAG prompts, extraction prompts, classification prompts, tool-use prompts, voice agent prompts — anything where you can automatically score the output.
The generated scaffold
When you pick a target, the skill creates:
autoresearch/
└── <target-name>/
    ├── program.md      # Tailored agent instructions for the experiment loop
    ├── results.tsv     # Experiment log with baseline
    ├── eval.sh         # Locked evaluation script
    └── README.md       # What this target is about
The program.md is the brain. It's not a generic template — the skill researches the domain and writes specific experiment suggestions informed by the state of the art. A program.md for a BM25 search engine will suggest BM25+ variants and term proximity scoring. A program.md for a classifier will suggest architecture changes and regularization strategies.
Install
Claude.ai (Pro, Max, Team, Enterprise)

Download autoresearch.skill from Releases
Go to Settings → Customize → Skills
Upload the .skill file
Done — say "find things to optimize" in any chat

Claude Code
Option A: Plugin marketplace
bash/plugin marketplace add EliMunkey/autoresearch-skill
Option B: Manual install
bashgit clone https://github.com/EliMunkey/autoresearch-skill.git
cp -r autoresearch-skill ~/.claude/skills/autoresearch
```

### Claude API

See the [Skills API Quickstart](https://docs.anthropic.com/en/docs/agents-and-tools/skills) for how to use skills programmatically.

## Usage

Just describe what you want. The skill triggers automatically:
```
"Find things to optimize in this project"
"Run autoresearch on this codebase"
"Can an agent improve my search algorithm overnight?"
"Set up autonomous optimization for my training script"
"Optimize my system prompt with autoresearch"
```

The skill will scan, rank, scaffold, and offer to start. You stay in control of what gets optimized and when the loop begins.

## How the loop works

Once scaffolded, the experiment loop follows Karpathy's pattern:
```
┌─────────────────────────────────────────┐
│  1. THINK                               │
│     Review past results, propose idea   │
├─────────────────────────────────────────┤
│  2. IMPLEMENT                           │
│     Modify target file, git commit      │
├─────────────────────────────────────────┤
│  3. RUN                                 │
│     Execute experiment, capture output  │
├─────────────────────────────────────────┤
│  4. EVALUATE                            │
│     Extract metric from output          │
├─────────────────────────────────────────┤
│  5. DECIDE                              │
│     Better? → keep commit               │
│     Worse?  → git reset, discard        │
│     Crash?  → git reset, log it         │
├─────────────────────────────────────────┤
│  6. REPEAT                              │
│     Next experiment. Review every 10.   │
└─────────────────────────────────────────┘
Everything happens on an isolated git branch. Your main branch stays clean.
Design principles
Single file, single metric. The agent modifies exactly one file. This is non-negotiable. The constraint is what makes it work — it keeps diffs reviewable, experiments isolated, and reverts clean.
The eval is sacred. The evaluation harness is locked. The agent cannot modify it. If the agent could touch the eval, it would game the metric instead of improving the code.
Simplicity over completeness. Karpathy's original is three files. This skill generates four. A focused 50-line program.md beats a 500-line instruction novel.
Git branch isolation. All experiments happen on a dedicated branch. If the whole thing is a bust, delete the branch.
Inspired by

karpathy/autoresearch — the original. AI agents running research on single-GPU nanochat training automatically.
Tobi Lütke's QMD experiment — adapted autoresearch for a query-expansion model overnight. 19% improvement, smaller model outperformed a larger one.
Phil Schmid's analysis — on how autoresearch will change small language model adoption.

Contributing
PRs welcome. If you've used the skill on a project and found improvements to the scanning heuristics, candidate ranking, or program.md templates, open an issue or PR.
License
MIT
