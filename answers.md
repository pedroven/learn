# Answers and grading: `learn` experiment

This page records my answers before and after each bug fix, along with the answer keys and the blind grading totals. Before answers were written from the issue descriptions only. After answers were written from memory eight days after each fix. The full protocol and results are in the [README](README.md).

## Questions

1. Where in the code is it?
2. Why does it happen?
3. Why does the fix work?
4. Which test proves it?
5. What else could break?

## Bug A — opentelemetry-python #5726

[Issue #5726](https://github.com/open-telemetry/opentelemetry-python/issues/5726): `BoundedAttributes` logged a warning for every dropped attribute; the specification allows one warning per record.

### Before — 2026-10-28

1. Probably in the span.set_attribute logic.
2. I don't know exactly, but it seems like every time the attribute limit is reached it starts dropping attributes and sends a warning for each one, but the expected is one per record.
3. Because it will only warn on the first drop, so just one warning, matching the expected.
4. The test from the reproduce section.
5. You could no longer tell from the warnings which attributes were dropped.

### After — 2026-11-16, from memory

1. I dont remember exactly in the code, but is the function that adds the warnings
2. because there is no way of avoiding keep adding warnings when the limit is surpassed
3. because the fix puts a a condition to only print the warning on the first time
4. the test that was added that call the function with a value that before it was expecting more warning calls, but after the fix only one is shown keeping the values of dropped attributes
5. I dont know, the solution was pretty simple so it will not affect too much

## Bug B — prometheus/client_python #1177

[Issue #1177](https://github.com/prometheus/client_python/issues/1177): with OpenMetrics `escaping=underscores`, names were escaped in sample lines but not in `# HELP` / `# TYPE` metadata.

### Before — 2026-10-28

1. The encoder, when using the `Accept: application/openmetrics-text; version=1.0.0` header.
2. I didn't completely get this part.
3. I don't know the fix; it's not mentioned.
4. `curl -i -H "Accept: application/openmetrics-text; version=1.0.0" http://localhost:8000/metrics` and get the expected output, with the metric names following the expected pattern.
5. I don't know.

### After — 2026-11-08, from memory

Questions 1–4 were answered together:

> I dont remember exactly the name of the function but its a function that use to change the name of the metrics, it was not changing in the relator expected way only outside the text of "# HELP" and "# TYPE", for example, at the time of the reported bug the output was "# HELP test:test_metric" and "# TYPE test:test_metric" and "test_test_metric", with a input of this metric name: "test:test_metric", but the ralator was expecting the return "# HELP test_test_metric" and "# TYPE test_test_metric" and "test_test_metric", to keep consistency, it happen that their assumption was wrong the metric name could keep the pattern "test:test_metric" and the problem is that the last part of the output should also change for this pattern, so the fix was change the part in the code that uses a different function to render the name, to use the same function used by the "help" and "type" text, this way keeping the consistency. The test used to this was a test to guarantee different types of metric to keep consistency in all outputs, so if someone change back the function these tests would break

5. what else would break is that if I simply invert and use the same function of the last output in the "help" and "type" outputs, it would generate a wrong pattern of metric name when using a metric of type value, the bug description was testing with a type "gauge"

## Answer keys

These summaries describe the merged fixes and were used as the blind grader's answer keys.

- **Bug A:** `BoundedAttributes` should emit the dropped-attribute warning only on the first drop for a record or span, while continuing to increment the dropped-attribute count for every dropped value. The regression test exceeds the attribute limit and checks that there is at most one warning while the full dropped count is preserved. [Issue #5726](https://github.com/open-telemetry/opentelemetry-python/issues/5726).
- **Bug B:** OpenMetrics exposition should use `escape_metric_name()` for the sample name too, matching the helper already used for `HELP`, `TYPE`, and `UNIT` metadata. The test checks that identifiers match across those lines for every escaping scheme, including `underscores`; metric-name colons are preserved by the helper. [Merged PR #1204](https://github.com/prometheus/client_python/pull/1204).

## Blind grading results

Each of the five answers was scored 0, 0.5, or 1 against its merged-fix answer key. Before and after answers were graded in the same fresh chat, without revealing which method was used.

| Bug | Before | After | Gain |
|---|---:|---:|---:|
| A — delegation | 2.5/5 | 2.5/5 | 0 |
| B — learn mode | 1/5 | 3.5/5 | +2.5 |

The grading result for Bug B's answer 5 is uncertain. The grader awarded 0.5, but the full merged PR does not support the specific regression I proposed: `values` is an escaping scheme, not a metric type, and the change does not alter behavior by metric type. The score above preserves the blind grader's original score rather than revising it after seeing the full PR. Bug A's after-quiz was taken while I was mildly sick; that is context, not a score adjustment.
