# Prompt Engineering Milestone

I tested three prompting techniques on one model and kept the real prompts and raw outputs. Expected answers were written before running anything.

Platform: Google AI Studio
Model: Gemini 3.8 Flash (gemini-3.8-flash)
Thinking level: Medium
Temperature: not available for this model

## Files

- report/PROMPT_ENGINEERING_REPORT.md: the full report
- experiments/zero-shot-vs-few-shot.md: Experiment 1, sentiment classification
- experiments/chain-of-thought.md: Experiment 2, direct vs step-by-step
- experiments/structured-output.md: Experiment 3, JSON extraction
- data/test-cases.md: all test inputs and the expected answers
- evidence/settings-zero-shot.png: screenshot of the run settings

## Results

| Experiment | Technique | Result |
|---|---|---|
| 1 | Zero-shot | 13 / 14 correct (92.86%) |
| 1 | Few-shot, 5 examples | 14 / 14 correct (100%) |
| 2 | Direct prompt | 5 / 5 correct |
| 2 | Step-by-step prompt | 5 / 5 correct |
| 3 | A. Basic JSON prompt | 0 / 10 full successes |
| 3 | B. Explicit schema and rules | 10 / 10 |
| 3 | C. Field definitions, examples, rules | 10 / 10 |
| 3 | D. Native structured output | 10 / 10 |

A JSON record counts as a full success only if the JSON is valid, the keys and types match the schema, and the values are right.

## Summary

- Few-shot fixed the one borderline review zero-shot got wrong. That is one review out of 14, so it is not strong evidence.
- Asking for step-by-step working did not change accuracy. The direct prompt already showed its working.
- Spelling out the output format took JSON extraction from 0% to 100%. Examples and native structured output matched that but did not improve on it.

Each prompt was run once, with all inputs sent in one request. The datasets are small, so see section 7 of the report for the limits.
