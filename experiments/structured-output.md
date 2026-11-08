# Experiment 3: Structured output (JSON extraction)

Run in Google AI Studio with Gemini 3.8 Flash (gemini-3.8-flash), thinking level Medium, no tools except where noted. This model has no temperature setting.

I sent all the inputs for a prompt in one request and ran each prompt once, in a fresh chat. Expected answers were fixed before running. The full report is in [../report/PROMPT_ENGINEERING_REPORT.md](../report/PROMPT_ENGINEERING_REPORT.md).


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

## Inputs and expected values

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

## Strategy A: basic prompt

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

## Strategy B: explicit schema and rules

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

## Strategy C: field definitions, examples and rules

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

## Strategy D: native structured output

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

## Comparison

| Strategy | Valid JSON | Pure JSON (no extra text) | Records matching schema | Values correct | Full successes | Success rate |
|---|---|---|---:|---:|---:|---:|
| A. Basic | yes | no (wrapped in a code fence) | 0 / 10 | names 10 / 10, dates 1 / 10, amounts 0 / 10 | 0 / 10 | 0% |
| B. Explicit schema and rules | yes | yes | 10 / 10 | 10 / 10 | 10 / 10 | 100% |
| C. Definitions, examples, rules | yes | yes | 10 / 10 | 10 / 10 | 10 / 10 | 100% |
| D. Native structured output | yes | yes (object with items array) | 10 / 10 | 10 / 10 | 10 / 10 | 100% |

## What I noticed

The big change is from A to B, 0% to 100%. A already got every name right, so the model could find the information. It just copied it in its original form because the prompt never asked for YYYY-MM-DD dates or numeric amounts. So most of the gain came from telling the model what format to produce, not from the model getting better at extraction. Note also that A did not get the "interpret as DD/MM/YYYY" note for input 3 that B, C and D got.

C and D scored the same as B. The two examples and the native option made no measurable difference here. B was already perfect, so this data cannot show whether examples or native output would help on harder inputs. What native output gives you is a guaranteed shape (keys, types, valid JSON) that does not depend on the model following instructions. In D the date format still came from the prompt, not the schema.

What helped here was stating the exact output format. Examples and native structured output did not add anything on top of that.