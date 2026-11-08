# Test cases and expected answers

These are the inputs used in the three experiments, with the expected answers I wrote before running anything. They were not changed afterwards. The inputs are exactly as they appear in the prompts in the report.

## Experiment 1: sentiment classification (14 reviews)

| No. | Review | Expected |
|---:|---|---|
| 1 | The product is excellent and works perfectly. | positive |
| 2 | I am extremely disappointed with this purchase. | negative |
| 3 | The package arrived yesterday. | neutral |
| 4 | Amazing quality for the price. | positive |
| 5 | The battery stopped working after two days. | negative |
| 6 | The product is okay, nothing special. | neutral |
| 7 | I absolutely love this product. | positive |
| 8 | Terrible experience. I want a refund. | negative |
| 9 | It arrived in the expected time. | neutral |
| 10 | Much better than I expected. | positive |
| 11 | Not bad, but I expected more. | neutral |
| 12 | The screen is gorgeous, but it stopped charging after a week. | negative |
| 13 | I can't say I'm unhappy with it. | positive |
| 14 | Great, another package that arrived broken. | negative |

Reviews 1 to 10 are easy. Reviews 11 to 14 are harder (mixed, double negative, sarcasm). Review 11 is a judgement call.

The five few-shot examples are not in this list:

- "I absolutely love this phone. The camera is amazing." (positive)
- "The product stopped working after one day." (negative)
- "The box contains a charger and a manual." (neutral)
- "It does the job, nothing more." (neutral)
- "Looks nice, but the zipper broke within a week." (negative)

## Experiment 2: reasoning problems (5 problems)

Problem 1: A shop has 120 notebooks. It sells 30, receives 50 more, and sells 40. Then it sells 20% of the remaining notebooks. How many notebooks are left?
Expected: 80 notebooks (120 - 30 = 90, + 50 = 140, - 40 = 100, 20% of 100 = 20, 100 - 20 = 80)

Problem 2: A jacket costs ₹2,000. It receives a 15% discount, followed by another 10% discount on the reduced price. Then 5% tax is added. What is the final price?
Expected: ₹1,606.50 (2,000 x 0.85 = 1,700, x 0.90 = 1,530, x 1.05 = 1,606.50)

Problem 3: Asha is twice as old as Ben. In 6 years, the sum of their ages will be 48. How old is Asha now?
Expected: 24 years (A = 2B, 3B + 12 = 48, B = 12, A = 24)

Problem 4: A train travels at 60 km/h and leaves at 9:00 AM. A second train travels in the same direction at 90 km/h and leaves at 10:00 AM. At what time will the second train catch the first?
Expected: 12:00 PM (60 km head start, closing speed 30 km/h, 2 hours after 10:00)

Problem 5: Amit, Binu, and Chitra each have a different pet: a cat, a dog, or a parrot. Amit does not have a dog. Binu has neither a cat nor a dog. Who has which pet?
Expected: Amit has the cat, Binu has the parrot, Chitra has the dog

## Experiment 3: structured output (10 transactions)

Schema for each record: name (string), date (string, YYYY-MM-DD), amount (number).

| No. | Input | Name | Date | Amount |
|---:|---|---|---|---:|
| 1 | Rahul purchased a laptop from ABC Store on 15 October 2026 for ₹58,999. | Rahul | 2026-10-15 | 58999 |
| 2 | On September 3, 2026, Priya paid Rs. 12,500 for the conference registration. | Priya | 2026-09-03 | 12500 |
| 3 | Arjun's order was processed on 21/08/2026 and final amount ₹4,999. | Arjun | 2026-08-21 | 4999 |
| 4 | Meera paid 7500 INR for annual subscription on 1 January 2026. | Meera | 2026-01-01 | 7500 |
| 5 | On 12 March 2026, Vikram purchased a monitor costing ₹18,450. | Vikram | 2026-03-12 | 18450 |
| 6 | Sneha's invoice dated 2026-07-09 shows total 2,300 rupees. | Sneha | 2026-07-09 | 2300 |
| 7 | Kabir handed over twelve hundred and fifty rupees for textbook on 4th February 2026. | Kabir | 2026-02-04 | 1250 |
| 8 | Order #88213 shipped to Pune. Divya, premium member, paid ₹3,499.50 on 30 Nov 2025 after coupon SAVE10. | Divya | 2025-11-30 | 3499.5 |
| 9 | Imran transferred ₹1,25,000 to contractor on 8 June 2026. | Imran | 2026-06-08 | 125000 |
| 10 | Amount paid Rs 899, by Tanvi, sometime on 2 April 2026. | Tanvi | 2026-04-02 | 899 |

Input 3 is read as DD/MM/YYYY.