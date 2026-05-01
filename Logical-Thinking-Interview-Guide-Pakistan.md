# Logical Thinking & Problem-Solving Interview Guide (Pakistan Software Companies)

A practical handbook for **fresh graduate, junior MERN, internship, and trainee** screening rounds: **aptitude-style logic, patterns, puzzles, and basic algorithmic thinking**—**not** advanced data structures theory.

---

## Table of Contents

1. [Introduction: What Pakistani Companies Test](#1-introduction-what-pakistani-companies-test)
2. [Core Problem-Solving Techniques](#2-core-problem-solving-techniques)
3. [Time & Work / Speed Problems](#3-time--work--speed-problems)
4. [Number Series & Pattern Recognition](#4-number-series--pattern-recognition)
5. [Logical Puzzles (Very Important)](#5-logical-puzzles-very-important)
6. [Coding Logic Questions (Non-DSA Level)](#6-coding-logic-questions-non-dsa-level)
7. [Basic Mathematics Logic Questions](#7-basic-mathematics-logic-questions)
8. [Input-Output Based Logic Questions](#8-input-output-based-logic-questions)
9. [Common Interview Trick Questions](#9-common-interview-trick-questions)
10. [Step-by-Step Solving Framework](#10-step-by-step-solving-framework)
11. [Practice Set (Very Important)](#11-practice-set-very-important)
12. [Interview Simulation Section](#12-interview-simulation-section)
13. [Common Mistakes Students Make](#13-common-mistakes-students-make)
14. [Speed Strategy for Interviews](#14-speed-strategy-for-interviews)
15. [Final Revision Sheet](#15-final-revision-sheet)

---

## 1. Introduction: What Pakistani Companies Test

### Why companies test logic instead of (only) coding

Many Pakistani **campus drives** and **trainee programs** use **aptitude + logical reasoning** to filter a large pool quickly. It is cheaper than long coding rounds and checks:

- Can you **read carefully**?
- Can you **structure** thinking?
- Will you **panic** under time?

### What “logical thinking” means in interviews

Not genius IQ. It usually means:

1. **Translate** words into a small model (numbers, table, picture).
2. **Apply** one or two rules step by step.
3. **Check** the answer on a simple example.

### What they expect from fresh graduates

- **Clear steps**, even if the final number is wrong.
- **Honest** “let me redraw / recheck” instead of random guessing.
- **Basic** arithmetic comfort and **pattern** spotting.

---

## 2. Core Problem-Solving Techniques

| Technique | How to use it |
|-----------|----------------|
| **Break into parts** | Split story problems: who works when, what changes each hour. |
| **Find patterns** | Write first 5–6 terms; look at **differences**, **ratios**, **squares**. |
| **Dry run** | Walk small input by hand; trace “What is the value after step 3?” |
| **Constraints** | Note “must be positive”, “integer”, “only one true” — eliminates options fast. |
| **Elimination** | In MCQs, kill impossible answers using parity, range, or units. |
| **Brute → refine** | List all small cases mentally, then see the rule. |

**Mindset:** Interviewers often reward **visible reasoning** more than a silent correct tick.

---

## 3. Time & Work / Speed Problems

### Core idea

If someone finishes work in **D** days, one day’s work = **1/D** of the total job.

**Working together:** add **per-day work rates** (like adding speeds).

### Shortcut

- Combined rate = **1/LCM-style thought**: if A does 1/4 and B does 1/6 per day, together **1/4 + 1/6 = 5/12** per day → full job in **12/5** days.
- **Man-days:** (men × days × hours) is constant if work amount is same.

---

### Question 1 — Two workers

**Q.** A completes a job in **12** days. B completes the same job in **18** days. How many days if **both** work together?

**Solution (step-by-step):**

- A’s 1 day = **1/12** of job.  
- B’s 1 day = **1/18** of job.  
- Together per day = **1/12 + 1/18**.  
- LCM(12,18) = 36 → **3/36 + 2/36 = 5/36** per day.  
- Days = **36/5 = 7.2 days** (or 7 days + fraction).

**Trap:** Adding 12+18 (wrong).

---

### Question 2 — Pipe filling

**Q.** Pipe P fills a tank in **4** hours. Pipe Q empties a full tank in **8** hours. Tank is empty. **Both** opened together. How many hours to fill?

**Solution:**

- P: **+1/4** tank per hour.  
- Q: **−1/8** tank per hour.  
- Net: **1/4 − 1/8 = 1/8** per hour.  
- Time = **8 hours**.

**Trap:** Treating emptying as “same rate sign” as filling.

---

### Question 3 — Efficiency

**Q.** 12 men complete work in **10** days. How many days for **8 men** (same hours, same work)?

**Solution:**

- Total **man-days** = 12 × 10 = **120**.  
- Days for 8 men = 120 / 8 = **15 days**.

---

## 4. Number Series & Pattern Recognition

### Common patterns (checklist)

| Pattern | What to look for |
|---------|------------------|
| **Constant difference** | 2,5,8,11 → +3 |
| **Changing difference** | Differences themselves form 1,2,3… |
| **× constant** | Each term ×2, ×3… |
| **Squares / cubes** | 1,4,9,16 or 1,8,27… |
| **Fibonacci-style** | Each term = sum of previous two |
| **Alternate rules** | Odd positions one rule, even another |

---

### Example A — Differences

**Series:** 3, 7, 13, 21, 31, **?**

- Diffs: 4,6,8,10 → next diff **12** → next term 31+12 = **43**.

---

### Example B — Multiplicative

**Series:** 2, 6, 18, 54, **?**

- ×3 each time → **162**.

---

### Example C — Odd one out (concept)

**Set:** 16, 25, 36, 49, 64, **72**

- Most are **perfect squares**; **72** is not → odd one out.

---

### Example D — Missing middle

**Series:** 5, 11, 20, **?**, 47

- Diffs: 6, 9, ?, ? — if second diff +3: 6,9,12,15 → next terms +12, +15 → 20+12=**32**, 32+15=47 ✓.

---

## 5. Logical Puzzles (Very Important)

### A. Classic “boxes and labels” (all wrongly labelled)

**Setup:** Three boxes: labelled **Apples**, **Oranges**, **Mixed**. **Every label is wrong.** You may **draw one fruit** from **one** box. Which box do you pick to identify all?

**Reasoning:**

1. Pick from the box labelled **Mixed** (must be either all apples or all oranges).  
2. Suppose you draw an **apple** → that box is **Apples**.  
3. The box labelled **Oranges** cannot be oranges (label wrong) and cannot be apples (you found apples box) → it must be **Mixed**.  
4. Remaining box is **Oranges**.

**Pattern:** Use the **known lie** to force logic.

---

### B. River crossing (wolf, goat, cabbage style)

**Mini version:** Man, **Goat**, **Wolf** must cross. Boat fits man + **one** other. **Goat cannot stay alone with wolf. Goat cannot stay alone with… (often cabbage in full story).** Here: **Wolf eats goat** if alone; **Goat eats grass** if alone — classic uses cabbage; for interview shorten to **wolf-goat only** sometimes.

**Standard wolf-goat-cabbage solution idea:**

1. Take **goat** across.  
2. Return alone.  
3. Take **wolf** across; **bring goat back**.  
4. Take **cabbage** (or grass) across.  
5. Return alone.  
6. Take **goat** across.

**If only wolf vs goat:** alternate so **goat is never left with wolf** without man.

**Diagram (idea):** Draw two banks; never leave **goat with wolf** or **goat with cabbage** without the farmer.

---

### C. Truth-tellers and liars (simple)

**Q.** Two guards, **one always lies, one always tells truth**. One door safe. One question?

**Classic:** Ask one guard: “If I asked the other guard which door is safe, what would he point to?” Then **choose the opposite** door (depending on formulation).

**Interview tip:** Explain **you need one question that links both behaviors**—memorize idea, not long wording.

---

### D. Seating arrangement (small)

**Q.** A,B,C,D sit in a row. **A** not at ends. **B** left of C. **D** is at an end. Possible order?

**Approach:**

- D at end → positions 1 or 4.  
- Try D=position1: **D _ _ _**; A not 1 or 4 → A in 2 or 3; place B left of C.

**Dry-run** each case; use **elimination**.

---

## 6. Coding Logic Questions (Non-DSA Level)

### Reverse digits (no built-in reverse)

**Idea:** `rev = rev*10 + lastDigit`, `n = n/10` until n=0.

**Pseudocode:**

```
rev = 0
while n > 0:
    rev = rev * 10 + (n % 10)
    n = floor(n / 10)
return rev
```

**Dry run n=1205:** 5021 after loop.

**Trap:** Negative n (ask clarifier in interview).

---

### Palindrome number check

Compare **reverse == original**, or compare first/last moving inward for strings.

---

### Count digits

Keep dividing by 10 until 0; **count++**.

---

### Count vowels in string

Loop; if char in `{a,e,i,o,u}` count++ (case-insensitive).

---

### Swap without temp (two numbers)

```
a = a + b
b = a - b
a = a - b
```

**Caution:** **overflow risk** in real code; interview often accepts **XOR trick** for integers:

```
a = a XOR b
b = a XOR b
a = a XOR b
```

---

## 7. Basic Mathematics Logic Questions

### Percentage

**“What is 15% of 200?”** → 0.15 × 200 = **30**.  
**Shortcut:** 10%=20, 5%=10 → 30.

### Profit / loss

**Cost C, sold at S.**

- Profit = S − C; **Profit%** = (Profit/C)×100 **on cost** (unless problem says on SP).

### Ratio

**Divide a:b in ratio m:n** → parts = m+n; first = (m/(m+n))×total.

### Average

**Average** = sum / count.  
If average of n numbers is A and new number x added: new avg = **(nA + x)/(n+1)**.

---

## 8. Input-Output Based Logic Questions

### Pattern: shift / rotate

| Input | Output | Rule (guess) |
|-------|--------|----------------|
| 1,2,3,4 | 2,3,4,1 | Rotate left by 1 |
| 12 | 21 | Reverse digits |

### Pattern: conditional

**Rule:** If input even → **÷2**; if odd → **×3+1** (Collatz-style step).  
Input **6** → 3 → 10 → 5 → 16 … (trace as asked).

### Simulation template

1. Write **variables** from problem.  
2. For each **step** in the statement, update once.  
3. Stop at given **step number** or until condition.

**Trap:** Off-by-one (“after **3rd** operation” = do exactly 3 updates).

---

## 9. Common Interview Trick Questions

| Trick | How to handle |
|-------|----------------|
| **“Largest number with these digits”** | Usually rearrange order; watch **leading zero** rule. |
| **“Probability 1 or 0?”** | Read “certain / impossible” carefully. |
| **Units** | Mix hours vs minutes; convert first. |
| **Inclusive counting** | “1 to 100” how many integers? **100**, not 99. |
| **“Half the bottle”** | Water+air puzzles—state assumptions aloud. |
| **Misleading average** | Need total, not only averages of subgroups. |

**Edge-case mantra:** “What if n=0? What if all same?”

---

## 10. Step-by-Step Solving Framework

**Universal order:**

1. **Read twice** — underline numbers and constraints.  
2. **Restate** in one line: “We need ___ given ___.”  
3. **Small example** — pick n=2 or 3.  
4. **Brute list** — enumerate if fast (MCQ options often 4).  
5. **Generalize** — find formula or pattern.  
6. **Verify** — plug back; check units.

**What to say:** “I will try n=2 first… this suggests the rule is … now verify n=5.”

---

## 11. Practice Set (Very Important)

---

### EASY (10)

---

#### E1

**Q.** Find next: 4, 7, 10, 13, **?**  
**Hint:** First differences.  
**Solution:** +3 each time → **16**.  
**Why:** Arithmetic progression with d=3.

---

#### E2

**Q.** Odd one out: 9, 16, 25, 36, **44**  
**Hint:** Square numbers.  
**Solution:** **44** is not a perfect square.  
**Why:** Others are 3²,4²,5²,6².

---

#### E3

**Q.** If 5 machines make 100 parts in 4 hours, how many parts do **12** machines make in 4 hours (same rate)?  
**Hint:** Proportion on machines.  
**Solution:** Parts ∝ machines → (12/5)×100 = **240**.  
**Why:** Time unchanged.

---

#### E4

**Q.** 20% of a number is 36. The number is?  
**Hint:** 0.2N = 36.  
**Solution:** N = 36/0.2 = **180**.  

---

#### E5

**Q.** A fair coin is flipped **3** times. What is the probability of **at least one head**?  
**Hint:** Complement: 1 minus “all tails”.  
**Solution:** P(all tails) = (1/2)³ = **1/8** → P(at least one H) = **7/8**.  
**Why:** “At least one” is often easier via **1 − none**.

---

#### E6

**Q.** Binary-like series: 1, 2, 4, 8, **?**  
**Hint:** Multiply.  
**Solution:** **16** (powers of 2).

---

#### E7

**Q.** Average of 5 numbers is 20. After adding a 6th number, average becomes 22. The 6th number is?  
**Hint:** Use totals.  
**Solution:** Old sum = 100; new sum = 132 → sixth = **32**.

---

#### E8

**Q.** From digits **1,2,3** form **largest** two-digit number (each digit once).  
**Hint:** Tens place biggest.  
**Solution:** **32**.

---

#### E9

**Q.** If **CAT** → **DBU** (each letter +1), then **DOG** → **?**  
**Hint:** Shift cipher.  
**Solution:** **EPH**.

---

#### E10

**Q.** You have **3** shirts, **2** pants. How many outfits?  
**Hint:** Multiply choices.  
**Solution:** 3×2 = **6**.

---

### MEDIUM (15)

---

#### M1

**Q.** A,B together finish in **12** days. A alone needs **20** days. How long **B** alone?  
**Hint:** Rates add.  
**Solution:** 1/12 − 1/20 = (5−3)/60 = 2/60 = 1/30 per day → B needs **30** days.

---

#### M2

**Q.** Series: 2, 3, 5, 9, 17, **?**  
**Hint:** Differences double?  
**Solution:** Diffs: 1,2,4,8 → next diff 16 → 17+16 = **33**.

---

#### M3

**Q.** A train 120 m crosses a pole in 8 s. Speed km/h?  
**Hint:** Speed = distance/time.  
**Solution:** 120/8 = 15 m/s → ×18/5 = **54 km/h**.

---

#### M4

**Q.** 40% students passed. If **12 more** passed, pass percentage becomes **50%**. Total students?  
**Hint:** Equation on counts.  
**Solution:** 0.4T + 12 = 0.5T → 0.1T=12 → T=**120**.

---

#### M5

**Q.** Clock angle at **3:15** approximately?  
**Hint:** Minute at 90°, hour moved 1/4 of 30° from 3.  
**Solution:** Hour: 90+7.5 = 97.5°; Minute 90° → diff **7.5°** (approx answer often accepted as 7.5°).

---

#### M6

**Q.** Five people shake hands **pairwise once**. How many handshakes?  
**Hint:** Combination C(5,2).  
**Solution:** **10**.

---

#### M7

**Q.** Find next: Z, Y, X, W, **?**  
**Hint:** Reverse alphabet.  
**Solution:** **V** (if pattern ZYXW… skip none) — confirm: Z(26),Y(25),X(24),W(23) → next letter **V**(22). ✓

---

#### M8

**Q.** Eggs in basket: count by 2s,3s,5s all leave **1** remainder. Smallest positive count?  
**Hint:** N ≡ 1 mod lcm(2,3,5).  
**Solution:** lcm=30 → **31**.

---

#### M9

**Q.** A lies Mon–Tue, truth Wed–Sun. Today says “Tomorrow I lie.” Which day?  
**Hint:** Case on today.  
**Solution approach:** “Tomorrow I lie” interpreted as “tomorrow is a lying day for me”. Enumerate — classic lands on **Wednesday** in standard schedules — **show your table** in interview.  
**Explanation:** These puzzles are **schedule tables**, not magic.

---

#### M10

**Q.** A **full** water tank empties through a drain in **12** hours at a constant rate. How long to drain **half** the tank?  
**Hint:** Half the volume → half the time.  
**Solution:** **6** hours.

---

#### M11

**Q.** Permutation: **DATA** — how many distinct arrangements?  
**Hint:** Repeated A.  
**Solution:** 4!/2! = **12**.

---

#### M12

**Q.** I/O: 1→3, 2→5, 3→7, 4→9 → rule?  
**Hint:** Linear.  
**Solution:** **2n+1** → 10→**21** if extended.

---

#### M13

**Q.** Sum 1+2+…+40?  
**Hint:** n(n+1)/2.  
**Solution:** 40×41/2 = **820**.

---

#### M14

**Q.** **50%** students take tea, **40%** take coffee, **20%** take **both**. **Neither**?  
**Hint:** Inclusion–exclusion.  
**Solution:** Tea∪Coffee = 50+40−20 = **70%** → Neither **30%**.

---

#### M15

**Q.** Start at **0**. Repeat forever: **add 5**, then **subtract 2** (two steps = one round). After how many **single steps** is the value **≥ 20** for the first time?  
**Hint:** Write the sequence until you hit ≥ 20.  
**Solution:** 0 →+5→5 →−2→3 →+5→8 →−2→6 →+5→11 →−2→9 →+5→14 →−2→12 →+5→17 →−2→15 →+5→**20**.  
That is **11** single steps (alternating +5 and −2).  
**Why:** Small rule → write the sequence; avoid guessing.

---

### HARD (10)

---

#### H1

**Q.** 12 coins, **1** fake lighter. **Balance scale**, minimum **weighings** to find fake?  
**Hint:** Ternary search intuition.  
**Solution concept:** Divide into **3 groups of 4**; first weighing reduces to 4; then **1 weighing of 1 vs 1** style — standard answer for 12 balls one heavy/light differs; **lighter fake** classic: **3 weighings** for 12 coins **one lighter** (divide 4,4,4).  
**Explanation:** Each weighing has **3 outcomes** → log₃(12) ~ 3.

---

#### H2

**Q.** Two doors, one guard **truth**, one **lies**. One door prize. **One question**?

**Hint:** Self-reference.  
**Solution idea:** Ask guard A: “If I asked the other guard which is prize door, what would he say?” Pick **opposite** (standard setup).  
**Explain:** Forces lie inside lie or truth about lie — collapses to **safe inversion**.

---

#### H3

**Q.** Series: **1, 3, 4, 7, 11, 18, ?**  
**Hint:** Each term might depend on the **previous two**.  
**Solution:** Check **sum of previous two**: 1+3=4, 3+4=7, 4+7=11, 7+11=18 → next = 11+18 = **29**.  
**Why:** Fibonacci-style recurrence on the series.

---

#### H4

**Q.** **Egg dropping** (2 eggs, 100 floors) — **minimum drops in worst case** (concept only).

**Hint:** Minimize worst case by balancing intervals.

**Interview answer:** “I know the classic trade-off uses **triangular numbers** ~**14** drops idea for 100 floors with 2 eggs — I’d derive from **sum 1+2+…+k ≥ 100**.”  
**Math:** k(k+1)/2 ≥ 100 → k≈**14**.  
**No deep DSA** — **reasoning about worst-case balance**.

---

#### H5

**Q.** **Monty Hall** (3 doors, 1 car). You pick door 1. Host opens **empty** door 3. Should you **switch**?

**Solution:** **Yes**, switch → win prob **2/3** (given standard rules: host always opens **goat**, knows).  
**Explain:** Your first pick wrong with prob 2/3; host reveals goat → switching **wins** if you were initially wrong.

---

#### H6

**Q.** **1024** team knockout. How many **matches** to decide **1** winner?

**Hint:** Each match eliminates **1**.

**Solution:** Need **1023** eliminations → **1023** matches.

---

#### H7

**Q.** **Snail** in 10m well: climbs **+3m** day, slips **−2m** night. First moment top?

**Hint:** Last climb may not slip.

**Solution:** Simulate day by day until height **≥ 10** after daytime climb (before night slip):  
Day 1: 0+3=3 → night →1; … Day 7 ends night at **7**; Day 8: 7+3=**10** (escape, no slip needed). Answer: **8** **days** (8th climb).

---

#### H8

**Q.** Clock **overlapping** hands: how many times in **12** hours?

**Solution:** **11** times (not 12 — ~ every 65+ min).

---

#### H9

**Q.** **River**: 3 couples, husband jealous if wife with **another** man without him **present** — boat 2 people. (Very hard full solve.)  

**Interview stance:** “I state **constraints**, try **small ferry plan**, backtrack if invalid — I don’t memorize full 3-couple optimal.”

**Coaching:** Shows **honesty + systematic trial**.

---

#### H10

**Q.** Digit puzzle: **SEND + MORE = MONEY** (each letter digit, first not zero) — famous.

**Interview:** “I’d fix **M=1** carrying rules, then propagate constraints—known puzzle; I practice **constraint propagation**.”

**Known solution exists** (9567+1085=10652 classic) — **don’t memorize**; explain **method**.

---

## 12. Interview Simulation Section

### Mock Round A — Screening (10 min)

| Interviewer | You (good response) |
|-------------|---------------------|
| “You have **90 seconds**. Next number: 2,5,11,23,47?” | “Diffs **3,6,12,24** doubles → next diff 48 → 47+48=**95**.” |
| “Why that pattern?” | “Each step adds double the previous difference — exponential-ish growth.” |

**Common mistake:** Blurting “95” with **no** difference table.

---

### Mock Round B — Puzzle

| Interviewer | You |
|-------------|-----|
| “3 light switches, 1 bulb in other room; **one** visit to bulb room?” | “Turn switch **1** on **long time** (hot), **2** on **short**, **3** off. Feel bulb: **hot+on** →1, **on cool**→2, **off cool**→3.” |

**Mistake:** Assuming only “on/off” states — use **heat** if allowed (classic variant).

---

### Mock Round C — Pressure

| Interviewer | You |
|-------------|-----|
| “Are you sure?” | “Let me verify once with n=…” |

---

## 13. Common Mistakes Students Make

| Mistake | Fix |
|---------|-----|
| **Jumping** to formula | Write **1 example** first |
| **No dry run** | Trace **2 loops** by hand for coding logic |
| **Ignore “not”, “except”** | Reread negations |
| **Silent work** | **Think aloud** short: “checking differences…” |
| **Unit mess** | Convert **hours→minutes** once |

---

## 14. Speed Strategy for Interviews

- **First 20 seconds:** classify: series / work-rate / puzzle / simulation.  
- **If stuck 60s:** pick **best MCQ** guess using **elimination**; mark revisit.  
- **Breathing:** 4-2-4 exhale before next question.  
- **Aloud script:** “Pattern: arithmetic diff / geometric ratio / …”

---

## 15. Final Revision Sheet

### Must-know formulas

| Topic | Formula |
|-------|---------|
| Work rate | Together: **1/Ta + 1/Tb** per day |
| Avg change | New avg = **(n×old + x)/(n+1)** |
| AP sum | **n/2 × (2a+(n-1)d)** |
| GP sum | **a(r^n−1)/(r−1)** |
| nCr handshake | **n(n−1)/2** |
| % change | **(new−old)/old × 100** |

### Pattern triggers

- **Differences constant** → AP  
- **Ratios constant** → GP  
- **Differences of differences** constant → quadratic  
- **Sum previous two** → Fibonacci style  

### Categories checklist

- [ ] Time–work  
- [ ] Series  
- [ ] Truth/Lie / arrangement  
- [ ] Basic % ratio  
- [ ] Simulation / I/O rule  
- [ ] “One clever insight” puzzles  

---

**End of guide.** Use **Section 11** daily with a timer; pair with your coding portfolio prep for full Pakistani junior hiring pipelines.
