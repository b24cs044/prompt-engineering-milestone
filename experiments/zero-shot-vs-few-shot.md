# Experiment 1: Zero-shot vs Few-shot (sentiment classification)

Run in Google AI Studio with Gemini 3.8 Flash (gemini-3.8-flash), thinking level Medium, no tools except where noted. This model has no temperature setting.

I sent all the inputs for a prompt in one request and ran each prompt once, in a fresh chat. Expected answers were fixed before running. The full report is in [../report/PROMPT_ENGINEERING_REPORT.md](../report/PROMPT_ENGINEERING_REPORT.md).


Task: label 14 customer reviews as positive, negative or neutral. Reviews 1 to 10 are easy. Reviews 11 to 14 are harder (mixed, double negative, sarcasm). Both methods got the same 14 reviews.

## Zero-shot prompt

```text
Classify the sentiment of each customer review.

The possible labels are:
- positive
- negative
- neutral

Return exactly 14 numbered labels, one per review.
Do not include explanations.

Reviews:
1. The product is excellent and works perfectly.
2. I am extremely disappointed with this purchase.
3. The package arrived yesterday.
4. Amazing quality for the price.
5. The battery stopped working after two days.
6. The product is okay, nothing special.
7. I absolutely love this product.
8. Terrible experience. I want a refund.
9. It arrived in the expected time.
10. Much better than I expected.
11. Not bad, but I expected more.
12. The screen is gorgeous, but it stopped charging after a week.
13. I can't say I'm unhappy with it.
14. Great, another package that arrived broken.
```

## Zero-shot output

```text
1. positive
2. negative
3. neutral
4. positive
5. negative
6. neutral
7. positive
8. negative
9. neutral
10. positive
11. negative
12. negative
13. positive
14. negative
```

## Few-shot prompt

Five examples, none of which is one of the 14 test reviews.

```text
Classify customer reviews as positive, negative, or neutral.

Review: "I absolutely love this phone. The camera is amazing."
Sentiment: positive

Review: "The product stopped working after one day."
Sentiment: negative

Review: "The box contains a charger and a manual."
Sentiment: neutral

Review: "It does the job, nothing more."
Sentiment: neutral

Review: "Looks nice, but the zipper broke within a week."
Sentiment: negative

Now classify these 14 reviews. Return exactly 14 numbered labels, one per review. Do not include explanations.

Reviews:
1. The product is excellent and works perfectly.
2. I am extremely disappointed with this purchase.
3. The package arrived yesterday.
4. Amazing quality for the price.
5. The battery stopped working after two days.
6. The product is okay, nothing special.
7. I absolutely love this product.
8. Terrible experience. I want a refund.
9. It arrived in the expected time.
10. Much better than I expected.
11. Not bad, but I expected more.
12. The screen is gorgeous, but it stopped charging after a week.
13. I can't say I'm unhappy with it.
14. Great, another package that arrived broken.
```

## Few-shot output

```text
1. positive
2. negative
3. neutral
4. positive
5. negative
6. neutral
7. positive
8. negative
9. neutral
10. positive
11. neutral
12. negative
13. positive
14. negative
```

## Results

| No. | Review | Expected | Zero-shot | Few-shot |
|---:|---|---|---|---|
| 1 | The product is excellent and works perfectly. | positive | positive | positive |
| 2 | I am extremely disappointed with this purchase. | negative | negative | negative |
| 3 | The package arrived yesterday. | neutral | neutral | neutral |
| 4 | Amazing quality for the price. | positive | positive | positive |
| 5 | The battery stopped working after two days. | negative | negative | negative |
| 6 | The product is okay, nothing special. | neutral | neutral | neutral |
| 7 | I absolutely love this product. | positive | positive | positive |
| 8 | Terrible experience. I want a refund. | negative | negative | negative |
| 9 | It arrived in the expected time. | neutral | neutral | neutral |
| 10 | Much better than I expected. | positive | positive | positive |
| 11 | Not bad, but I expected more. | neutral | **negative (wrong)** | neutral |
| 12 | The screen is gorgeous, but it stopped charging after a week. | negative | negative | negative |
| 13 | I can't say I'm unhappy with it. | positive | positive | positive |
| 14 | Great, another package that arrived broken. | negative | negative | negative |

Zero-shot: 13 / 14 correct = 92.86%.
Few-shot: 14 / 14 correct = 100%.

## What I noticed

The only difference between the two outputs is review 11, "Not bad, but I expected more." I labelled it neutral before running. Zero-shot said negative and few-shot said neutral.

I would not read much into this. It is one review out of 14, from one run each. Review 11 is also a judgement call, because "I expected more" can fairly be read as negative. The few-shot examples included a neutral one ("It does the job, nothing more.") and a "but" sentence labelled negative, which may have pushed the model one way or the other. The first few-shot example is also close in wording to review 7 ("I absolutely love this product"), but zero-shot already got review 7 right.

Both prompts followed the format exactly, so few-shot did not help with format. The zero-shot prompt was already clear about it. Reviews 12, 13 and 14 (mixed, double negative, sarcasm) were right in both.

So few-shot fixed one borderline review and nothing else changed.