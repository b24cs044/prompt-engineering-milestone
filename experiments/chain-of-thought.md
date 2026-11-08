# Experiment 2: Direct prompt vs Step-by-step prompt (multi-step reasoning)

Run in Google AI Studio with Gemini 3.8 Flash (gemini-3.8-flash), thinking level Medium, no tools except where noted. This model has no temperature setting.

I sent all the inputs for a prompt in one request and ran each prompt once, in a fresh chat. Expected answers were fixed before running. The full report is in [../report/PROMPT_ENGINEERING_REPORT.md](../report/PROMPT_ENGINEERING_REPORT.md).


Task: five multi-step problems (arithmetic, percentages, an age problem, relative speed, a logic puzzle). Both prompts got the same five problems.

| Problem | Topic | Expected answer |
|---:|---|---|
| 1 | Notebook stock | 80 notebooks |
| 2 | Jacket price with two discounts and tax | 1,606.50 rupees |
| 3 | Asha's age | 24 years |
| 4 | Train catch-up time | 12:00 PM |
| 5 | Pet logic puzzle | Amit: cat, Binu: parrot, Chitra: dog |

## Direct prompt

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

## Direct output

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

## Step-by-step prompt

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

## Step-by-step output

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

## Results

Both prompts got all 5 problems right (5 / 5 each). Every final answer matches the expected answer in the table above.

## What I noticed

Asking for step-by-step working did not change accuracy. The direct prompt did not ask for working at all, but the model still gave numbered steps for every problem.

The outputs differ a little in presentation. In Problem 2 the step-by-step output works out the discounts and tax as rupee amounts (300, 170, 76.50), while the direct output multiplies by 0.85, 0.90 and 1.05. In Problem 5 the step-by-step output says which pets are left at each stage.

Two reasons this test is weak. The thinking level was Medium, so the model may have reasoned internally before answering in both cases. And the problems are easy for this model. So step-by-step prompting did not help here, and I can't say whether it would on harder problems.