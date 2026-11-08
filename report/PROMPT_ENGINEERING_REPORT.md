# Prompt Engineering Milestone Report

Platform: Google AI Studio
Model: Gemini 3.8 Flash (gemini-3.8-flash)
Thinking level: Medium
Temperature: this model has no temperature setting, so I could not set it

## 1. What I did

I tested three prompting techniques on one model: zero-shot vs few-shot, a direct prompt vs a step-by-step prompt, and four ways of getting JSON that matches a schema. Each experiment uses a fixed set of test inputs. All prompts and model outputs below are pasted exactly as they were used and returned, and where something did not help I have said so.

## 2. Setup

Platform: Google AI Studio chat, no code.
Model: Gemini 3.8 Flash (gemini-3.8-flash).
Thinking level: Medium.
Tools: all off. Structured outputs was switched on only for Strategy D in Experiment 3.

Every prompt was run in a fresh chat. To save time I sent all the inputs for a prompt together in one request, for example all 14 reviews at once and all 10 transactions at once. That makes only a few runs per experiment: 2 for Experiment 1, 2 for Experiment 2 and 4 for Experiment 3. Each was run once and none were repeated.

I wrote the expected answers before running anything and did not change them afterwards.

How I scored:

- Experiment 1: number of correct labels out of 14. I also checked whether the format was followed (14 numbered one-word labels, no explanations).
- Experiment 2: each problem is right or wrong by its final answer. I also noted whether working was shown.
- Experiment 3: a record is a full success only if the JSON is valid, the keys and types match the schema (name as a string, date as a string in YYYY-MM-DD, amount as a number) and the values are correct. Success rate = full successes / 10 x 100.

## 3. Experiment 1: zero-shot vs few-shot

Task: label 14 customer reviews as positive, negative or neutral. Reviews 1 to 10 are easy. Reviews 11 to 14 are harder (mixed, double negative, sarcasm). Both methods got the same 14 reviews.

### Zero-shot prompt

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

### Zero-shot output

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

### Few-shot prompt

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

### Few-shot output

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

### Results

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

### What I noticed

The only difference between the two outputs is review 11, "Not bad, but I expected more." I labelled it neutral before running. Zero-shot said negative and few-shot said neutral.

I would not read much into this. It is one review out of 14, from one run each. Review 11 is also a judgement call, because "I expected more" can fairly be read as negative. The few-shot examples included a neutral one ("It does the job, nothing more.") and a "but" sentence labelled negative, which may have pushed the model one way or the other. The first few-shot example is also close in wording to review 7 ("I absolutely love this product"), but zero-shot already got review 7 right.

Both prompts followed the format exactly, so few-shot did not help with format. The zero-shot prompt was already clear about it. Reviews 12, 13 and 14 (mixed, double negative, sarcasm) were right in both.

So few-shot fixed one borderline review and nothing else changed.

## 4. Experiment 2: direct prompt vs step-by-step prompt

Task: five multi-step problems (arithmetic, percentages, an age problem, relative speed, a logic puzzle). Both prompts got the same five problems.

| Problem | Topic | Expected answer |
|---:|---|---|
| 1 | Notebook stock | 80 notebooks |
| 2 | Jacket price with two discounts and tax | 1,606.50 rupees |
| 3 | Asha's age | 24 years |
| 4 | Train catch-up time | 12:00 PM |
| 5 | Pet logic puzzle | Amit: cat, Binu: parrot, Chitra: dog |

### Direct prompt

```text
Solve each of the following problems and provide the final answer for each.

Problem 1:
A shop has 120 notebooks. It sells 30, receives 50 more, and sells 40. Then it sells 20% of the remaining notebooks. How many notebooks are left?

Problem 2:
A jacket costs ₹2,000. It receives a 15% discount, followed by another 10% discount on the reduced price. Then 5% tax is added. What is the final price?

Problem 3:
Asha is twice as old as Ben. In 6 years, the sum of their ages will be 48. How old is Asha now?

Problem 4:
A train travels at 60 km/h and leaves at 9:00 AM. A second train travels in the same direction at 90 km/h and leaves at 10:00 AM. At what time will the second train catch the first?

Problem 5:
Amit, Binu, and Chitra each have a different pet: a cat, a dog, or a parrot. Amit does not have a dog. Binu has neither a cat nor a dog. Who has which pet?
```

### Direct output

This is the raw output, including the Markdown and LaTeX markup the model produced.

```text
Here are the solutions to each of the problems:

---

### **Problem 1**
1. **Initial count:** $120$
2. **After selling 30:** $120 - 30 = 90$
3. **After receiving 50:** $90 + 50 = 140$
4. **After selling 40:** $140 - 40 = 100$
5. **After selling 20% of the remaining:**  
   $20\% \text{ of } 100 = 20$  
   $100 - 20 = 80$

**Final Answer:** **80 notebooks**

---

### **Problem 2**
1. **Initial price:** ₹$2,000$
2. **After 15% discount:**  
   $2,000 \times (1 - 0.15) = 2,000 \times 0.85 = \text{₹}1,700$
3. **After 10% discount on the reduced price:**  
   $1,700 \times (1 - 0.10) = 1,700 \times 0.90 = \text{₹}1,530$
4. **After 5% tax is added:**  
   $1,530 \times (1 + 0.05) = 1,530 \times 1.05 = \text{₹}1,606.50$

**Final Answer:** **₹1,606.50**

---

### **Problem 3**
1. Let Ben's current age be $B$. Then Asha's current age is $A = 2B$.
2. In 6 years, their ages will be $(A + 6)$ and $(B + 6)$.
3. Sum of their ages in 6 years:
   $$(2B + 6) + (B + 6) = 48$$
   $$3B + 12 = 48$$
   $$3B = 36 \implies B = 12$$
4. Asha's current age:  
   $$A = 2 \times 12 = 24$$

**Final Answer:** **24 years old**

---

### **Problem 4**
1. By 10:00 AM (1 hour after departure), the first train has traveled:  
   $$60 \text{ km/h} \times 1 \text{ h} = 60 \text{ km}$$
2. The relative speed between the two trains is:  
   $$90 \text{ km/h} - 60 \text{ km/h} = 30 \text{ km/h}$$
3. Time required for the second train to close the 60 km gap:  
   $$\frac{60 \text{ km}}{30 \text{ km/h}} = 2 \text{ hours}$$
4. Catch-up time:  
   $$\text{10:00 AM} + 2 \text{ hours} = \text{12:00 PM}$$

**Final Answer:** **12:00 PM (Noon)**

---

### **Problem 5**
1. **Binu** has neither a cat nor a dog, so Binu must have the **parrot**.
2. **Amit** does not have a dog and the parrot is already taken by Binu, so Amit must have the **cat**.
3. Therefore, **Chitra** must have the **dog**.

**Final Answer:**  
* **Amit:** Cat  
* **Binu:** Parrot  
* **Chitra:** Dog
```

### Step-by-step prompt

```text
Solve the following problems carefully.

Work through each problem step by step before giving the final answer.

Problem 1:
A shop has 120 notebooks. It sells 30, receives 50 more, and sells 40. Then it sells 20% of the remaining notebooks. How many notebooks are left?

Problem 2:
A jacket costs ₹2,000. It receives a 15% discount, followed by another 10% discount on the reduced price. Then 5% tax is added. What is the final price?

Problem 3:
Asha is twice as old as Ben. In 6 years, the sum of their ages will be 48. How old is Asha now?

Problem 4:
A train travels at 60 km/h and leaves at 9:00 AM. A second train travels in the same direction at 90 km/h and leaves at 10:00 AM. At what time will the second train catch the first?

Problem 5:
Amit, Binu, and Chitra each have a different pet: a cat, a dog, or a parrot. Amit does not have a dog. Binu has neither a cat nor a dog. Who has which pet?

Give the final answer clearly for each problem.
```

### Step-by-step output

```text
Here are the step-by-step solutions for each problem:

---

### **Problem 1**

1. **Initial count:** 120 notebooks
2. **After selling 30:** 
   $$120 - 30 = 90$$
3. **After receiving 50 more:** 
   $$90 + 50 = 140$$
4. **After selling 40:** 
   $$140 - 40 = 100$$
5. **Selling 20% of the remaining:** 
   $$20\% \text{ of } 100 = 0.20 \times 100 = 20$$
   $$100 - 20 = 80$$

**Final Answer:** **80 notebooks** are left.

---

### **Problem 2**

1. **Original price:** ₹2,000
2. **Apply 15% discount:**
   $$\text{Discount} = 15\% \text{ of } 2,000 = ₹300$$
   $$\text{Price} = 2,000 - 300 = ₹1,700$$
3. **Apply 10% discount on the reduced price:**
   $$\text{Discount} = 10\% \text{ of } 1,700 = ₹170$$
   $$\text{Price} = 1,700 - 170 = ₹1,530$$
4. **Add 5% tax:**
   $$\text{Tax} = 5\% \text{ of } 1,530 = ₹76.50$$
   $$\text{Final Price} = 1,530 + 76.50 = ₹1,606.50$$

**Final Answer:** The final price is **₹1,606.50**.

---

### **Problem 3**

1. Let Ben's current age be $B$.
2. Asha's current age is $A = 2B$.
3. In 6 years:
   - Ben's age will be $B + 6$
   - Asha's age will be $2B + 6$
4. Set up the equation for the sum of their ages:
   $$(B + 6) + (2B + 6) = 48$$
   $$3B + 12 = 48$$
   $$3B = 36$$
   $$B = 12$$
5. Asha's age now:
   $$A = 2 \times 12 = 24$$

**Final Answer:** Asha is **24 years old** now.

---

### **Problem 4**

1. By 10:00 AM (1 hour later), the first train has traveled:
   $$\text{Distance} = 60 \text{ km/h} \times 1 \text{ hour} = 60 \text{ km}$$
2. The relative speed between the two trains:
   $$\text{Relative Speed} = 90 \text{ km/h} - 60 \text{ km/h} = 30 \text{ km/h}$$
3. Time taken for the second train to catch up:
   $$\text{Time} = \frac{60 \text{ km}}{30 \text{ km/h}} = 2 \text{ hours}$$
4. Catch-up time:
   $$\text{10:00 AM} + 2 \text{ hours} = \text{12:00 PM (noon)}$$

**Final Answer:** The second train will catch the first at **12:00 PM**.

---

### **Problem 5**

1. **Binu:** Since Binu has neither a cat nor a dog, Binu must have the **parrot**.
2. **Amit:** The remaining pets are the cat and the dog. Since Amit does not have a dog, Amit must have the **cat**.
3. **Chitra:** The only remaining pet is the **dog**, so Chitra has the dog.

**Final Answer:**
- **Amit has the cat**
- **Binu has the parrot**
- **Chitra has the dog**
```

### Results

Both prompts got all 5 problems right (5 / 5 each). Every final answer matches the expected answer in the table above.

### What I noticed

Asking for step-by-step working did not change accuracy. The direct prompt did not ask for working at all, but the model still gave numbered steps for every problem.

The outputs differ a little in presentation. In Problem 2 the step-by-step output works out the discounts and tax as rupee amounts (300, 170, 76.50), while the direct output multiplies by 0.85, 0.90 and 1.05. In Problem 5 the step-by-step output says which pets are left at each stage.

Two reasons this test is weak. The thinking level was Medium, so the model may have reasoned internally before answering in both cases. And the problems are easy for this model. So step-by-step prompting did not help here, and I can't say whether it would on harder problems.

## 5. Experiment 3: structured output (JSON)

Task: pull a name, a date and an amount out of 10 messy transaction sentences. Required format for each record:

```json
{
  "name": "string",
  "date": "YYYY-MM-DD",
  "amount": 0
}
```

Four strategies, same 10 inputs:

- A: basic prompt that asks for JSON with the three keys.
- B: explicit schema and rules.
- C: field definitions, two examples and rules.
- D: AI Studio's Structured outputs option.

### Inputs and expected values

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

### Strategy A: basic prompt

```text
Extract the person's name, date, and amount from each text.

Return the results as JSON.

1. Rahul purchased a laptop from ABC Store on 15 October 2026 for ₹58,999.
2. On September 3, 2026, Priya paid Rs. 12,500 for the conference registration.
3. Arjun's order was processed on 21/08/2026 and final amount ₹4,999.
4. Meera paid 7500 INR for annual subscription on 1 January 2026.
5. On 12 March 2026, Vikram purchased a monitor costing ₹18,450.
6. Sneha's invoice dated 2026-07-09 shows total 2,300 rupees.
7. Kabir handed over twelve hundred and fifty rupees for textbook on 4th February 2026.
8. Order #88213 shipped to Pune. Divya, premium member, paid ₹3,499.50 on 30 Nov 2025 after coupon SAVE10.
9. Imran transferred ₹1,25,000 to contractor on 8 June 2026.
10. Amount paid Rs 899, by Tanvi, sometime on 2 April 2026.

Return a JSON array containing exactly 10 objects. Each object should have these keys: name, date, amount.
```

Raw output, pasted as it came back. It includes the Markdown code fence around the JSON.

````text
```json
[
  {
    "name": "Rahul",
    "date": "15 October 2026",
    "amount": "₹58,999"
  },
  {
    "name": "Priya",
    "date": "September 3, 2026",
    "amount": "Rs. 12,500"
  },
  {
    "name": "Arjun",
    "date": "21/08/2026",
    "amount": "₹4,999"
  },
  {
    "name": "Meera",
    "date": "1 January 2026",
    "amount": "7500 INR"
  },
  {
    "name": "Vikram",
    "date": "12 March 2026",
    "amount": "₹18,450"
  },
  {
    "name": "Sneha",
    "date": "2026-07-09",
    "amount": "2,300 rupees"
  },
  {
    "name": "Kabir",
    "date": "4th February 2026",
    "amount": "twelve hundred and fifty rupees"
  },
  {
    "name": "Divya",
    "date": "30 Nov 2025",
    "amount": "₹3,499.50"
  },
  {
    "name": "Imran",
    "date": "8 June 2026",
    "amount": "₹1,25,000"
  },
  {
    "name": "Tanvi",
    "date": "2 April 2026",
    "amount": "Rs 899"
  }
]
```
````

What went wrong in A:

| No. | Name | Date as returned | Date in YYYY-MM-DD | Amount as returned | Amount is a number | Full success |
|---:|---|---|---|---|---|---|
| 1 | correct | 15 October 2026 | no | "₹58,999" | no | no |
| 2 | correct | September 3, 2026 | no | "Rs. 12,500" | no | no |
| 3 | correct | 21/08/2026 | no | "₹4,999" | no | no |
| 4 | correct | 1 January 2026 | no | "7500 INR" | no | no |
| 5 | correct | 12 March 2026 | no | "₹18,450" | no | no |
| 6 | correct | 2026-07-09 | yes | "2,300 rupees" | no | no |
| 7 | correct | 4th February 2026 | no | "twelve hundred and fifty rupees" | no | no |
| 8 | correct | 30 Nov 2025 | no | "₹3,499.50" | no | no |
| 9 | correct | 8 June 2026 | no | "₹1,25,000" | no | no |
| 10 | correct | 2 April 2026 | no | "Rs 899" | no | no |

All 10 names were right and the keys were right. Only 1 of 10 dates was in YYYY-MM-DD (the one that was already written that way) and 0 of 10 amounts were numbers. That is 0 / 10 full successes. Because of the code fence, the response as pasted is also not pure JSON.

### Strategy B: explicit schema and rules

```text
You are a structured information extraction system.

Extract exactly three fields from each transaction:
- name: string containing the person's name.
- date: string formatted strictly as YYYY-MM-DD.
- amount: numeric value without currency symbols, currency names, or commas.

Required JSON schema:
{
  "name": "string",
  "date": "YYYY-MM-DD",
  "amount": 0
}

Rules:
1. Convert every date into YYYY-MM-DD format.
2. Convert every amount into a JSON number.
3. Remove currency symbols, currency names, and thousands separators.
4. Preserve decimal amounts where present.
5. Do not add extra fields.
6. Return exactly 10 objects in one JSON array.
7. Do not include explanations, Markdown, or code fences.
8. Return only valid JSON.

Transactions:

1. Rahul purchased a laptop from ABC Store on 15 October 2026 for ₹58,999.
2. On September 3, 2026, Priya paid Rs. 12,500 for the conference registration.
3. Arjun's order was processed on 21/08/2026 and final amount ₹4,999. (Interpret this date as DD/MM/YYYY.)
4. Meera paid 7500 INR for annual subscription on 1 January 2026.
5. On 12 March 2026, Vikram purchased a monitor costing ₹18,450.
6. Sneha's invoice dated 2026-07-09 shows total 2,300 rupees.
7. Kabir handed over twelve hundred and fifty rupees for textbook on 4th February 2026.
8. Order #88213 shipped to Pune. Divya, premium member, paid ₹3,499.50 on 30 Nov 2025 after coupon SAVE10.
9. Imran transferred ₹1,25,000 to contractor on 8 June 2026.
10. Amount paid Rs 899, by Tanvi, sometime on 2 April 2026.
```

Raw output:

```text
[
  {
    "name": "Rahul",
    "date": "2026-10-15",
    "amount": 58999
  },
  {
    "name": "Priya",
    "date": "2026-09-03",
    "amount": 12500
  },
  {
    "name": "Arjun",
    "date": "2026-08-21",
    "amount": 4999
  },
  {
    "name": "Meera",
    "date": "2026-01-01",
    "amount": 7500
  },
  {
    "name": "Vikram",
    "date": "2026-03-12",
    "amount": 18450
  },
  {
    "name": "Sneha",
    "date": "2026-07-09",
    "amount": 2300
  },
  {
    "name": "Kabir",
    "date": "2026-02-04",
    "amount": 1250
  },
  {
    "name": "Divya",
    "date": "2025-11-30",
    "amount": 3499.5
  },
  {
    "name": "Imran",
    "date": "2026-06-08",
    "amount": 125000
  },
  {
    "name": "Tanvi",
    "date": "2026-04-02",
    "amount": 899
  }
]
```

10 / 10 full successes. Pure JSON, no extra text.

### Strategy C: field definitions, examples and rules

This prompt does not simply add examples to B. I replaced B's schema block with the field definitions and added two examples (neither is one of the 10 test inputs).

```text
You are a structured information extraction system.

Extract exactly three fields from each transaction:
- name: string containing the person's name.
- date: string formatted as YYYY-MM-DD.
- amount: numeric value without currency symbols, currency names, or commas.

Follow these examples:

Example 1:
Input: Nisha bought a phone on 5 May 2026 for ₹21,990.
Output:
{"name":"Nisha","date":"2026-05-05","amount":21990}

Example 2:
Input: On 17 December 2025, Omar paid Rs. 640.75 for a train ticket.
Output:
{"name":"Omar","date":"2025-12-17","amount":640.75}

Rules:
1. Convert every date into YYYY-MM-DD format.
2. Convert every amount into a JSON number.
3. Remove currency symbols, currency names, and thousands separators.
4. Preserve decimal amounts where present.
5. Do not add extra fields.
6. Return exactly 10 objects in one JSON array.
7. Do not include explanations, Markdown, or code fences.
8. Return only valid JSON.

Transactions:

1. Rahul purchased a laptop from ABC Store on 15 October 2026 for ₹58,999.
2. On September 3, 2026, Priya paid Rs. 12,500 for the conference registration.
3. Arjun's order was processed on 21/08/2026 and final amount ₹4,999. (Interpret this date as DD/MM/YYYY.)
4. Meera paid 7500 INR for annual subscription on 1 January 2026.
5. On 12 March 2026, Vikram purchased a monitor costing ₹18,450.
6. Sneha's invoice dated 2026-07-09 shows total 2,300 rupees.
7. Kabir handed over twelve hundred and fifty rupees for textbook on 4th February 2026.
8. Order #88213 shipped to Pune. Divya, premium member, paid ₹3,499.50 on 30 Nov 2025 after coupon SAVE10.
9. Imran transferred ₹1,25,000 to contractor on 8 June 2026.
10. Amount paid Rs 899, by Tanvi, sometime on 2 April 2026.
```

Raw output:

```text
[{"name":"Rahul","date":"2026-10-15","amount":58999},{"name":"Priya","date":"2026-09-03","amount":12500},{"name":"Arjun","date":"2026-08-21","amount":4999},{"name":"Meera","date":"2026-01-01","amount":7500},{"name":"Vikram","date":"2026-03-12","amount":18450},{"name":"Sneha","date":"2026-07-09","amount":2300},{"name":"Kabir","date":"2026-02-04","amount":1250},{"name":"Divya","date":"2025-11-30","amount":3499.5},{"name":"Imran","date":"2026-06-08","amount":125000},{"name":"Tanvi","date":"2026-04-02","amount":899}]
```

10 / 10 full successes. Pure JSON, no extra text.

### Strategy D: native structured output

I switched on Structured outputs in AI Studio and set the schema as an array of objects with name (STRING), date (STRING) and amount (NUMBER). The response came back as an object with one key, items, holding the array. The prompt is shorter than B and C but still gives the date format and says amounts should be numbers, because a STRING field in the schema cannot force the YYYY-MM-DD pattern.

```text
Extract the person's name, transaction date, and amount from each of the following 10 transactions.

Use YYYY-MM-DD for dates and numeric values for amounts. Interpret 21/08/2026 as DD/MM/YYYY.

Transactions:

1. Rahul purchased a laptop from ABC Store on 15 October 2026 for ₹58,999.
2. On September 3, 2026, Priya paid Rs. 12,500 for the conference registration.
3. Arjun's order was processed on 21/08/2026 and final amount ₹4,999.
4. Meera paid 7500 INR for annual subscription on 1 January 2026.
5. On 12 March 2026, Vikram purchased a monitor costing ₹18,450.
6. Sneha's invoice dated 2026-07-09 shows total 2,300 rupees.
7. Kabir handed over twelve hundred and fifty rupees for textbook on 4th February 2026.
8. Order #88213 shipped to Pune. Divya, premium member, paid ₹3,499.50 on 30 Nov 2025 after coupon SAVE10.
9. Imran transferred ₹1,25,000 to contractor on 8 June 2026.
10. Amount paid Rs 899, by Tanvi, sometime on 2 April 2026.
```

Raw output:

```text
{"items":[{"name":"Rahul","date":"2026-10-15","amount":58999},{"name":"Priya","date":"2026-09-03","amount":12500},{"name":"Arjun","date":"2026-08-21","amount":4999},{"name":"Meera","date":"2026-01-01","amount":7500},{"name":"Vikram","date":"2026-03-12","amount":18450},{"name":"Sneha","date":"2026-07-09","amount":2300},{"name":"Kabir","date":"2026-02-04","amount":1250},{"name":"Divya","date":"2025-11-30","amount":3499.5},{"name":"Imran","date":"2026-06-08","amount":125000},{"name":"Tanvi","date":"2026-04-02","amount":899}]}
```

10 / 10 full successes. Pure JSON, but wrapped in an items object instead of a bare array.

### Comparison

| Strategy | Valid JSON | Pure JSON (no extra text) | Records matching schema | Values correct | Full successes | Success rate |
|---|---|---|---:|---:|---:|---:|
| A. Basic | yes | no (wrapped in a code fence) | 0 / 10 | names 10 / 10, dates 1 / 10, amounts 0 / 10 | 0 / 10 | 0% |
| B. Explicit schema and rules | yes | yes | 10 / 10 | 10 / 10 | 10 / 10 | 100% |
| C. Definitions, examples, rules | yes | yes | 10 / 10 | 10 / 10 | 10 / 10 | 100% |
| D. Native structured output | yes | yes (object with items array) | 10 / 10 | 10 / 10 | 10 / 10 | 100% |

### What I noticed

The big change is from A to B, 0% to 100%. A already got every name right, so the model could find the information. It just copied it in its original form because the prompt never asked for YYYY-MM-DD dates or numeric amounts. So most of the gain came from telling the model what format to produce, not from the model getting better at extraction. Note also that A did not get the "interpret as DD/MM/YYYY" note for input 3 that B, C and D got.

C and D scored the same as B. The two examples and the native option made no measurable difference here. B was already perfect, so this data cannot show whether examples or native output would help on harder inputs. What native output gives you is a guaranteed shape (keys, types, valid JSON) that does not depend on the model following instructions. In D the date format still came from the prompt, not the schema.

What helped here was stating the exact output format. Examples and native structured output did not add anything on top of that.

## 6. Overall results

| Experiment | Technique | Result |
|---|---|---|
| 1 | Zero-shot | 13 / 14 (92.86%) |
| 1 | Few-shot (5 examples) | 14 / 14 (100%) |
| 2 | Direct prompt | 5 / 5 (100%) |
| 2 | Step-by-step prompt | 5 / 5 (100%) |
| 3 | A. Basic JSON prompt | 0 / 10 full successes (0%) |
| 3 | B. Explicit schema and rules | 10 / 10 (100%) |
| 3 | C. Definitions, examples, rules | 10 / 10 (100%) |
| 3 | D. Native structured output | 10 / 10 (100%) |

What worked:

- Few-shot examples fixed the one borderline review that zero-shot got wrong.
- Spelling out the output format, types and rules took the JSON task from 0% to 100%.

What did not help, or what the data could not show:

- Step-by-step working made no difference to accuracy, and the direct prompt already showed its working.
- Examples in the JSON prompt and native structured output did not improve on the explicit schema prompt, which already scored 100%.
- Few-shot did not change the output format in Experiment 1.

## 7. Limitations

1. The datasets are small: 14 reviews, 5 problems and 10 transactions. In Experiment 1 the whole difference was one review.
2. Each prompt was run once. There were no repeats, and since the model has no temperature setting I could not control randomness, so a rerun might give different results.
3. All inputs for a method were sent in one request instead of one request each. Items in the same request could in principle affect each other.
4. The thinking level was Medium, which probably narrows the gap between the direct and step-by-step prompts.
5. The expected label for review 11 is a judgement call, and one few-shot example is close in wording to review 7.
6. The reasoning problems were easy for this model.
7. The four JSON strategies are not a perfectly controlled comparison. A never asked for the format and did not get the DD/MM/YYYY note, C is not B plus examples, and D returned an items wrapper and took the date format from the prompt.
8. Only one model was tested.

## 8. Conclusion

Few-shot prompting took sentiment accuracy from 92.86% to 100%, but that was one review on a small set. Step-by-step prompting made no difference to accuracy because the model already showed its working when asked directly. For JSON output, the biggest effect came from stating the schema, formats and rules clearly: 0% with a basic prompt and 100% with an explicit one. Examples and native structured output matched that but did not beat it. On easy tasks a technique may show no benefit at all, so it is worth checking against real outputs.

## 9. Evidence

evidence/settings-zero-shot.png is a screenshot of the run settings (model gemini-3.8-flash, thinking level Medium, all tools off) taken in the zero-shot chat. It also shows part of the model's thinking summary, where it goes back and forth on review 11 before settling on negative, which matches the zero-shot output above.