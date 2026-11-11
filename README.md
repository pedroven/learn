# learn

Does pairing with an AI in a "learn" mode make me retain more than plain delegation?

In learn mode the AI writes no non-trivial code until I explain why it has to be shaped that way, I do all my own debugging, and the help fades as I show the reasoning without it. Delegation is the usual way: I describe the task, the AI does it, I review.

This file was committed before I solved either bug.

## Protocol

Two already-fixed bugs in open-source projects, each solved from the commit before the fix. One with learn mode, one by plain delegation. The same 5 questions are answered three times: before (from the issue description only), and a week after each fix, from memory. The merged fix is the answer key: I don't open the fix PR until the last quiz is done.

Measure: gain = after score − before score, per bug.

### Questions (same for both bugs)

1. Where in the code is it?
2. Why does it happen?
3. Why does the fix work?
4. Which test proves it?
5. What else could break?

### Bugs

- **A: [opentelemetry-python #5726](https://github.com/open-telemetry/opentelemetry-python/issues/5726).** BoundedAttributes logs a warning for every dropped attribute; the spec allows one per record.
- **B: [prometheus/client_python #1177](https://github.com/prometheus/client_python/issues/1177).** OpenMetrics `escaping=underscores`: names are escaped in sample lines but not in `# HELP` / `# TYPE`.

Before-answers for both were written on 2026-10-28, from the issue descriptions only, with no code.

### Assignment

Coin flip on 2026-10-28 with `python3 -c "import secrets; print(secrets.choice(['heads', 'tails']))"`. Heads meant A gets learn mode. Result: tails, so **B gets learn mode and A gets delegation.**

### Grading

Blind. A fresh Claude chat gets the answers and the merged fix, not the method. Each answer scores 0, ½ or 1. Before and after answers are graded in the same chat.

## Prediction

Written 2026-10-29, before solving either bug: learn mode (B) scores **4/5** vs delegation (A) **3/5** on the after quiz, and takes **3×** longer. Exact numbers, not "at least".

## Results

| Bug | Method | Date solved | Time | Before /5 | After /5 (quiz date) | Gain |
|---|---|---|---|---|---|---|
| A | delegation | 2026-11-08 | 11m | | | |
| B | learn mode | 2026-10-31 | 1h 26m | | | |
