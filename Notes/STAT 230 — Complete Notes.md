---
tags:
  - stat230
  - probability
  - uwaterloo
course: STAT 230
term: Fall 2026
---

# STAT 230 — Probability — Complete Notes

*UWaterloo, Fall 2026. Instructor: Cecilia A. Cotton. All lectures in one file, following the slide decks with in-class intuition folded in from transcripts. A per-chapter **Glossary** is at the end.*

> [!info] How to read these notes
> **✅ success** = the key formula/rule to remember · **💡 tip** = instructor intuition from lecture · **⚠️ warning / 🚫 danger** = common traps & misconceptions · **📝 note** = definitions · **📋 example** = concept-check questions · **🧾 abstract** = goals & summaries.

## 📖 Index

1. **[[#Chapter 1 — Introduction to Probability|Chapter 1 — Introduction to Probability]]** — definitions of probability, mathematical models (sample space, events, axioms), counting (permutations, combinations, binomial/multinomial), odds
2. **[[#Chapter 2 — Probability Rules and Conditional Probability|Chapter 2 — Probability Rules & Conditional Probability]]** — sets & the base rules, addition rules & inclusion–exclusion, independence, conditional probability, product rule, Law of Total Probability, Bayes' Rule, Monty Hall
3. **[[#Chapter 3 — Univariate Discrete Probability Distributions|Chapter 3 — Discrete Distributions]]** — discrete random variables, probability mass functions *(in progress)*
4. **[[#📖 Glossary (by chapter)|Glossary]]** — key terms, grouped by chapter

## 🗓️ Quiz / exam tracker

- **Quiz 1** — Chapter 1 material (counting-heavy). Show your reasoning, not just numbers.
- **Midterm** — higher communication expectations than Quiz 1: explain your steps, define events/notation before computing.

## 📎 Source materials

- Slide decks: `STAT_230_003_weekNN_...pdf`
- Lecture transcripts (`.txt`) — first 2–3 topics were slides-only; later ones have transcript.

> [!tip] Navigation
> In Obsidian, use the **Outline** panel (or the index links above) to jump between chapters; in a terminal, search (`/`) for "# Chapter N" or "# 📖 Glossary".

---

# Chapter 1 — Introduction to Probability

> [!abstract] Goal
> Build the mathematical foundation of probability: define it rigorously via a sample space + distribution, then learn to compute probabilities by **counting** in equally-likely models.

**Roadmap:** 1.1 Definitions of Probability · 1.2 Mathematical Probability Models · 1.3 Counting in Uniform Probability Models

> [!tip] Big-picture framing (from lecture)
> `Statistics ⟺ Data ⟺ Uncertainty ⟺ Probability`. STAT 231 lives on the *Statistics / Data* side; **STAT 230 lives on the *Uncertainty / Probability* side.** Probability is the mathematics of describing and modelling uncertain events.

## 1.1 Definitions of Probability

Three classical ways to define "the probability of an event." Each is useful, and each has a fatal flaw — which is why we later abandon all three for a rigorous mathematical model.

**1. Classical Probability**

$$P(\text{event}) = \frac{\text{number of ways the event can occur}}{\text{total number of possible outcomes}}$$

Requires every outcome to be **equally likely**.

**2. Relative Frequency.** The probability of an event is the *limiting* proportion of times it occurs in a very long series of repetitions of an experiment.

**3. Subjective Probability.** The probability is a measure of how sure the person making the statement is that the event will happen.

### Example — Rolling a die

- **Classical:** $P(\text{roll a 2}) = 1/6$, because there are 6 equally likely outcomes.
- **Relative frequency:** rolled 1000 times, got a 2 on 200 → $P \approx 0.2$.
- **Subjective (Gambler's Fallacy):** "I've rolled ten times with no six, so a six *feels* more likely now." This reasoning is wrong — a fair die has no memory.

### Example — Two teams

- **Classical:** each team wins with probability 0.5 (only two outcomes).
- **Relative frequency:** Team A won 9 of the last 10 → $P(A) = 0.9$.
- **Subjective:** a broadcaster says $P(A) = 0.25$ because A's star is injured.

> [!warning] The three issues (why we need something better)
> - **Classical** — unusable when outcomes are *not* equally likely.
> - **Relative frequency** — we can never actually repeat an experiment infinitely.
> - **Subjective** — no way to resolve disagreements about the "true" probability.

## 1.2 Mathematical Probability Models

To overcome those difficulties, we treat probability as a **mathematical system** — a *probability model* — built from three pieces.

> [!note] A mathematical probability model consists of
> 1. A **sample space** $S$ — the set of *all distinct outcomes* of an experiment, such that in a single trial **one and only one** outcome occurs. $S = \{a_1, a_2, a_3, \dots\}$
> 2. A **probability distribution** on $S$ — an assignment of a number $P(a_i) \in [0,1]$ to each outcome.
> 3. An **event** is a subset $A \subseteq S$.

### Definition — Sample Space

$S$ is the set of all distinct outcomes, exactly one of which occurs per trial.

- **Rolling a die:** $a_i = \#\text{ dots facing up}$, so $S = \{1,2,3,4,5,6\}$.
- **Runtime of a program:** let $t$ be the runtime, $S = \{t \in \mathbb{R} : t > 0\}$.

**Discrete vs. non-discrete:** a **discrete sample space** consists of a *finite or countably infinite* set of outcomes. Otherwise it is non-discrete (e.g. the runtime example, a continuous interval).

### Definition — Event

An **event** is a subset $A \subseteq S$.

- A **simple event** contains only one outcome, e.g. $A_1 = \{a_1\}$.
- A **compound event** is made up of two or more simple events.

Examples (die, $S=\{1,\dots,6\}$): simple $A=\{1\}$ = "roll a one"; compound $A=\{1,3,5\}$ = "roll an odd." Runtime: $A = \{t : t > 30\}$ = "runtime longer than 30 seconds."

### There is more than one valid sample space

For flipping a fair coin **twice**:

- $S = \{HH, HT, TH, TT\}$ — records all outcomes. ✅
- $S = \{a_0, a_1, a_2\}$ where $a_i =$ "observe $i$ tails." ✅
- $S = \{a_{\text{yes}}, a_{\text{no}}\}$ (e.g. "at least one head?"). ✅
- $S = \{a_1, a_{\text{yes}}, a_{\text{no}}\}$ — **not** a sample space (overlapping outcomes). ❌
- $S = \{a_0\}$ — **not** a sample space (not all outcomes covered). ❌

> [!success] Key requirement for a valid sample space
> Outcomes must be *mutually exclusive* (no overlap) **and** *exhaustive* (cover everything).

### Set notation (events are sets)

| Concept | Notation | Meaning |
|---|---|---|
| Element of | $x \in A$ | outcome $x$ is in event $A$ |
| Union | $A \cup B = \{x : x\in A \text{ or } x\in B\}$ | in $A$, in $B$, or both |
| Intersection | $A \cap B = AB = \{x : x\in A \text{ and } x\in B\}$ | in both |
| Complement | $\bar A = \{x : x\in S,\ x\notin A\}$ | not in $A$ |
| Empty set | $\varnothing$ | contains nothing |
| Disjoint / mutually exclusive | $A \cap B = \varnothing$ | no common outcomes |

Infinite versions: $\bigcup_{i=1}^{\infty} A_i$ and $\bigcap_{i=1}^{\infty} A_i$.

### Probabilities — the Axioms

Let $\mathcal{S}$ be the set of all events. A **probability function** $P : \mathcal{S} \to \mathbb{R}$ takes an event and returns a nonnegative real number, satisfying:

> [!success] The three axioms
> 1. **Scale:** $0 \le P(A) \le 1$ for any event $A$.
> 2. **Something happens:** $P(S) = 1$.
> 3. **Additivity:** if $A$ and $B$ are disjoint, $P(A \cup B) = P(A) + P(B)$.
> 3b. **(Infinite additivity):** if $A_1, A_2, \dots$ are disjoint, then $P\!\left(\bigcup_{i=1}^{\infty} A_i\right) = \sum_{i=1}^{\infty} P(A_i)$.

### Definition — A Probability Distribution

For discrete $S = \{a_1, a_2, \dots\}$, assign $P(a_i)$ so that:

$$\text{(1)}\quad 0 \le P(a_i) \le 1 \qquad\qquad \text{(2)}\quad \sum_{\text{all } i} P(a_i) = 1$$

The probability of an event $A$ is the sum of the probabilities of its simple events:

$$P(A) = \sum_{a \in A} P(a)$$

### Example — Coin-flipping probabilities

With $S_1 = \{HH, HT, TH, TT\}$ and a fair coin, each outcome has probability $1/4$. "At least one head" $A = \{HH, HT, TH\}$:

$$P(A) = P(HH)+P(HT)+P(TH) = \tfrac34$$

Using the alternative space $S_2 = \{a_0,a_1,a_2\}$ ($a_i = \#$ tails) with $P(a_0)=1/4,\ P(a_1)=1/2,\ P(a_2)=1/4$:

$$P(A) = P(a_0)+P(a_1) = \tfrac14 + \tfrac12 = \tfrac34 \quad\text{— same answer.}$$

> [!abstract] Summary — Mathematical Probability Model
> A sample space $S$ is a set of distinct outcomes; a distribution assigns each outcome a probability with $0\le P(a_i)\le 1$ and $\sum P(a_i)=1$; an event $A\subseteq S$ has $P(A) = \sum_{a\in A} P(a)$. Outcomes are tied to the sample space, there is more than one possible sample space, and a distribution + sample space form a pair $(P, S)$ on which events depend.

## 1.3 Counting in Uniform Probability Models

A finite sample space is **equally likely** (a **uniform** distribution) if every outcome has the same probability. Writing $|A|$ for the number of simple outcomes in $A$:

$$1 = P(S) = \sum_{i=1}^{|S|} P(a_i) = P(a_i)\,|S| \;\Rightarrow\; P(a_i) = \frac{1}{|S|}$$

> [!success] Uniform probability model — the master formula
> $$P(A) = \frac{|A|}{|S|} = \frac{\#\text{ outcomes in } A}{\#\text{ outcomes in } S}$$
> So probability reduces to a **counting** problem: count the desired outcomes, count the total.

### Examples of equally likely spaces

1. Fair die: $S=\{1,\dots,6\}$, $P(a_i)=1/6$.
2. Fair coin twice: $S=\{HH,HT,TH,TT\}$, $P(a_i)=1/4$.
3. Two digits from $\{0,1,2\}$ **with** replacement: $|S|=9$, $P(a_i)=1/9$.
4. Two digits from $\{0,1,2\}$ **without** replacement: $S=\{01,02,10,12,20,21\}$, $P(a_i)=1/6$.

### Example — Two Fair Dice ($|S| = 36$)

$S$ is the $6\times 6$ grid of ordered pairs $(i,j)$.

- (a) Dots match: $P = 6/36 = 1/6$.
- (b) Sum to 7: $P = 6/36 = 1/6$.
- (c) Match **and** sum to 7: impossible, $C = \varnothing$, $P = 0$.
- (d) Sum exceeds 8 (i.e. 9, 10, 11, 12): $4+3+2+1 = 10$ outcomes → $P = 10/36$.
- (e) Even on first roll **and** prime on second: $3 \times 3 = 9$ outcomes.

### Two fundamental counting rules

> [!success] Addition & Multiplication rules
> **The Addition Rule (OR):** if job 1 can be done in $p$ ways and job 2 in $q$ ways, then doing *either* job 1 **or** job 2 (but not both) can be done in $p + q$ ways.
>
> **The Multiplication Rule (AND):** if job 1 can be done in $p$ ways and, for each, job 2 in $q$ ways, then doing *both* job 1 **and** job 2 can be done in $p \times q$ ways.

Example (d) used OR ($4+3+2+1$); example (e) used AND ($3\times3$).

### Ordered $k$-tuples

An **ordered $k$-tuple** is $(a_1, \dots, a_k)$. If there are $n_1$ choices for $a_1$, $n_2$ for $a_2$, …, then $|A| = n_1 n_2 \cdots n_k = \prod_{i=1}^{k} n_i$.

### Permutations vs. Combinations — the central distinction

> [!note] Permutation vs. Combination
> **Permutation (ordered):** arrangements where order *matters*, $(a,b) \ne (b,a)$.
> **Combination (unordered):** sets where order does *not* matter, $\{a,b\} = \{b,a\}$.

> [!tip] In-class heuristic — spotting the type
> - "in a row," "arranged," assigning distinct *roles/positions* (President, Shortstop, first digit) → **order matters → permutation.**
> - "a committee," "a team," "select/pick" with no roles, dealing cards you'll sort → **order doesn't matter → combination.**
> - Getting matched to co-op jobs → permutation; testing products for defects → combination.

Baseball intuition: a 9-position lineup from 15 players is a *permutation* ($15\cdot14\cdots7 = 15^{(9)} = 1{,}816{,}214{,}400$); choosing 9 of 15 with no positions is a *combination* ($\binom{15}{9}=5005$).

### Counting Permutations

Given $n$ distinct objects, a **permutation of size $k$** is an ordered subset of $k$ individuals, denoted $n^{(k)}$ ("$n$ to $k$ factors"):

> [!success] Permutations
> $$n^{(k)} = n(n-1)\cdots(n-k+1) = \frac{n!}{(n-k)!}$$
> $n^{(0)} = 1$;  $n^{(n)} = n!$ and $0^{(0)} = 0! = 1$.  **Warning:** $n^{(k)} \ne n^k$.

Motivation: 5 club members select a President and Vice-President (order matters) → $5 \times 4 = 20 = 5^{(2)}$.

### Example — Selecting Numbers (permutation, without replacement)

Select three numbers **without replacement** from $\{1,\dots,9\}$ to form a 3-digit number. Denominator $|S| = 9^{(3)} = 9\cdot8\cdot7$.

1. **Larger than 500?** 1st digit $\in \{5,6,7,8,9\}$ (5 choices), then 8, then 7: $P(A) = \frac{5\cdot8\cdot7}{9\cdot8\cdot7} = \frac59$.
2. **Even?** last digit $\in \{2,4,6,8\}$ (4 choices), then 8, then 7: $P(B) = \frac{4\cdot8\cdot7}{9\cdot8\cdot7} = \frac49$.
3. **Larger than 700 and even?** Split into disjoint cases by first digit (OR rule): starts 7 or 9 → 4 even last-digit choices; starts 8 → 3 choices; middle digit always 7 choices.

$$P(C) = \frac{1\cdot7\cdot4 + 1\cdot7\cdot3 + 1\cdot7\cdot4}{9\cdot8\cdot7} = \frac{4+3+4}{9\cdot8} = \frac{11}{72}$$

### Counting Combinations (the Binomial Coefficient)

A **combination of size $k$** is an unordered subset of $k$ from $n$. Intuition: *combinations = permutations ÷ (permutations per combination).*

> [!success] Combinations / Binomial Coefficient
> $$\binom{n}{k} = \frac{n^{(k)}}{k!} = \frac{n!}{(n-k)!\,k!}$$
> Read "$n$ choose $k$." $\binom{n}{0} = 1$, and $\binom{n}{k} = 0$ if $k<0$. Useful symmetry: $\binom{n}{k} = \binom{n}{n-k}$.

Motivation: 5 members choose 2 for a committee: $\frac{5^{(2)}}{2} = \frac{5\cdot4}{2} = 10$ (AB and BA are the same combination).

### Example — Distinct Books (20 books, 7 by George R.R. Martin)

1. Arrange 12 on a shelf, order matters → permutation: $20^{(12)} = \frac{20!}{8!}$.
2. Pick 12, order doesn't matter → combination: $\binom{20}{12} = \frac{20!}{12!\,8!}$.
3. Pick 12 with **exactly 3** Martin books: choose 3 of 7 Martin **and** 9 of 13 others: $\dfrac{\binom{7}{3}\binom{13}{9}}{\binom{20}{12}}$.
4. A monkey shelves 12 random books — probability **at least 3** are Martin's? Use the **complement**:

$$P(\ge 3) = 1 - \left[\frac{\binom{7}{0}\binom{13}{12}}{\binom{20}{12}} + \frac{\binom{7}{1}\binom{13}{11}}{\binom{20}{12}} + \frac{\binom{7}{2}\binom{13}{10}}{\binom{20}{12}}\right]$$

> [!tip] Lecture tip
> On a quiz you may *stop at the choose-terms* — no need to crunch a single number. And notice the "hockey-stick" pattern: numerators in each pair match, e.g. $\binom{7}{k}\binom{13}{12-k}$.

### Arranging Indistinguishable Objects (Multinomial Coefficient)

When objects repeat: treat identical items as distinguishable to count, then **divide out** the arrangements you can't tell apart.

- **BIRD** (all unique): $4!$ arrangements.
- **BRRR** (three R's): $\frac{4!}{3!} = 4$ (only "where's the B?" matters).
- **DODO** (two D's, two O's): $\frac{4!}{2!\,2!} = 6$.
- **PEPPER** (three P's, two E's, one R): $\frac{6!}{3!\,2!\,1!} = 60$.
- **ABBOZZO** (1 A, 2 B, 2 O, 2 Z): $\frac{7!}{2!\,2!\,2!\,1!} = 630$.

> [!success] Multinomial Coefficient
> For $n$ objects of $k \ge 2$ types with $n_i$ of type $i$ (so $n_1 + \cdots + n_k = n$):
> $$\binom{n}{n_1\, n_2 \cdots n_k} = \frac{n!}{n_1!\, n_2! \cdots n_k!}$$
> **Relationship to the binomial:** when $k=2$ with $n = n_1 + n_2$,
> $$\binom{n}{n_1\, n_2} = \frac{n!}{n_1!\,(n-n_1)!} = \binom{n}{n_1} = \binom{n}{n_2}.$$
> The binomial is the two-class special case. (Reappears later as the **multinomial distribution**.)

### Example — Rearranging "DODO"

1. Number of arrangements: $\frac{4!}{2!\,2!} = \binom{4}{2\,2} = 6$.
2. $P(\text{letters in alphabetic order})$: only **DDOO** works → $P(A) = 1/6$.
3. $P(\text{word ends in "O"})$: fix an O at the end, arrange D, D, O → $\frac{3!}{2!\,1!} = 3$; $P(B) = 3/6 = 1/2$. *Alternate:* 2 of 4 letters are O, so $P = 2/4 = 1/2$.

> [!tip] Lecture emphasis
> There are usually *multiple valid paths* to the same answer (complement, fixing letters, listing the whole sample space). On a quiz you just need to **show your reasoning.**

### Example — Lottery (~ Problem 1.3.10)

Melissa picks 7 numbers from 1–50; a computer picks 7 from 1–50, **without replacement** (order doesn't matter → combinations). She wins if she matches **5 or more**.

- Denominator (all computer picks): $\binom{50}{7} \approx 99$ million.
- Match 7: $\binom{7}{7}\binom{43}{0} = 1$;  Match 6: $\binom{7}{6}\binom{43}{1}$;  Match 5: $\binom{7}{5}\binom{43}{2}$.

$$P(\text{win}) = \frac{\binom{7}{7}\binom{43}{0} + \binom{7}{6}\binom{43}{1} + \binom{7}{5}\binom{43}{2}}{\binom{50}{7}} \approx 0.0001929$$

### Example — Elevator (~ Problems 1.3.6, 1.3.7)

4 passengers, 5 floors, each equally likely to exit anywhere. **Model as passengers picking floors, with replacement.** Denominator $= 5^4$.

1. **All on different floors** — numerator is sampling *without* replacement: $\frac{5^{(4)}}{5^4} = \frac{5\cdot4\cdot3\cdot2}{5^4} = \frac{24}{125} \approx 0.192$.
2. **2 on floor 2, 2 on floor 3:** $\frac{\binom{4}{2}\cdot 1}{5^4}$.
3. **2 on one floor, 2 on another (unspecified):** $\frac{\binom{4}{2}\binom{5}{2}}{5^4}$.
4. **Exactly one on floor 1:** $\frac{\binom{4}{1}4^3}{5^4} \approx 0.4096$.
5. **Exactly one on floor 1 and exactly one on floor 2:** $\frac{\binom{4}{2}\,2!\,3^2}{5^4} \approx 0.1728$.

### The Birthday Problem (~ Problem 1.3.11)

$n$ people, each equally likely to have any of 365 birthdays. Let $A_n$ = "at least two share," $\bar A_n$ = "nobody shares":

$$P(\bar A_n) = \frac{365^{(n)}}{365^n} \qquad\Rightarrow\qquad P(A_n) = 1 - \frac{365^{(n)}}{365^n}$$

Results: $P(A_{23}) = 0.5073$, $P(A_{35}) = 0.8144$, $P(A_{100}) = 0.9999997$.

> [!tip] Takeaway
> A room of just **23** people is more likely than not to contain a shared birthday.

### Summary — Counting

| | With replacement | Without replacement |
|---|---|---|
| **Permutations (ordered)** | $n^k$ | $n^{(k)} = \frac{n!}{(n-k)!}$ |
| **Combinations (unordered)** | — | $\binom{n}{k} = \frac{n!}{k!(n-k)!}$ |

Distinct classes of indistinguishable objects → multinomial $\binom{n}{n_1\cdots n_k} = \frac{n!}{n_1!\cdots n_k!}$. **And when a problem feels like a huge pile of cases — try the complement.**

### Odds (Week 2 slides)

> [!note] Odds
> **Odds in favour of $A$:** $\dfrac{P(A)}{1-P(A)} = \dfrac{P(A)}{P(\bar A)}$.  **Odds against $A$:** $\dfrac{P(\bar A)}{P(A)}$ = odds in favour of $\bar A$.
> Recover probability: $P(A) = \dfrac{\text{odds in favour}}{\text{odds in favour} + 1} = \dfrac{1}{\text{odds against} + 1}$.
> Reading: "4 to 1 in favour" means for every 4 times $A$ occurs it fails once → $P(A) = 0.8$.

---

# Chapter 2 — Probability Rules and Conditional Probability

> [!abstract] Goal
> Prove a reusable toolkit of probability rules from the axioms, then extend to **conditional probability** — the machinery for updating probabilities given new information (product rule, Law of Total Probability, Bayes' Rule).

**Roadmap:** 2.1 Use of Sets · 2.2 Addition Rules for Unions of Events · 2.3 Dependent and Independent Events · 2.4 Conditional Probability and Product Rules.

> [!tip] Method (from lecture)
> Start from the raw definition of a probability model, *prove* each rule once, then reuse the rules freely without going back to first principles.

## 2.1 Use of Sets

An event *is a set*, so set theory is central. Recall the model: $S = \{a_1, a_2, \dots\}$, distribution with $0\le P(a_i)\le 1$ and $\sum P(a) = 1$, and $P(A) = \sum_{a\in A} P(a)$.

### The first three probability rules (proved from the axioms)

> [!success] Rule 1 — Normalization
> $P(S) = 1$. *(Follows directly: $P(S) = \sum_{\text{all } a_i} P(a_i) = 1$ by the definition of the distribution.)*

> [!success] Rule 2 — Boundedness
> For any event $A$, $0 \le P(A) \le 1$. *(Since $A \subseteq S$, $P(A) = \sum_{a\in A}P(a) \le \sum_{a\in S}P(a) = 1$; and $\ge 0$ since no term is negative.)*

> [!success] Rule 3 — Monotonicity
> If $A \subseteq B$, then $P(A) \le P(B)$. *(All outcomes of $A$ are in $B$, which can only add nonnegative probability. E.g. $P(\text{roll a 2}) \le P(\text{roll even})$.)*

> [!tip] Lecture note
> These feel "obvious," but probability would collapse if they failed — so we honestly establish that they follow from the foundations.

### Set notation and terminology (Venn diagrams)

For events $A, B$ in $S$: **Element of** $x\in A$; **Union** $A\cup B$; **Intersection** $A\cap B = AB$; **Complement** $\bar A$; **Empty set** $\varnothing = \bar S$.

> [!tip] Visualizing operations (proof by picture)
> Colouring intersections is easy — yellow ($A$) over blue ($B$) makes green ($A\cap B$). For nested expressions, work from the **innermost brackets outward**. Three sets is about the practical limit for a hand-drawn diagram.

### Example — Two Fair Dice with sets

$S$ = 36 ordered pairs. $A$ = "first die even," $B$ = "sum is 6." Find $C = \bar A \cap B$.

- $\bar A$ = "first die **odd**" = rows starting 1, 3, 5.
- $B$ = "sum 6" = the diagonal $\{(1,5),(2,4),(3,3),(4,2),(5,1)\}$.
- Intersection (first die odd **and** sum 6): $C = \{(1,5),(3,3),(5,1)\}$.

> [!tip] Lesson
> Once we have an actual sample space, work with the outcomes directly (visually or by argument) rather than only shading Venn diagrams.

### De Morgan's Laws

> [!success] De Morgan's Laws
> $$\overline{A \cup B} = \bar A \cap \bar B \qquad\qquad \overline{A \cap B} = \bar A \cup \bar B$$
> "The complement of a union is the intersection of the complements," and vice versa.

Proved **by picture**: shade the left and right side of each identity and observe the regions match. **Why we care:** in probability, *intersections* are often what we can compute, and De Morgan's laws convert a stubborn complement-of-a-union into an intersection (and back).

## 2.2 Addition Rules for Unions of Events

### Rule 4a — Union of Two Events

> [!success] Rule 4a
> $$P(A \cup B) = P(A) + P(B) - P(A \cap B)$$

**Visual proof:** adding $P(A)$ and $P(B)$ counts the overlap $A\cap B$ **twice**, so subtract it once.

**Formal proof (from lecture):** split each event into disjoint pieces — $A = (A\cap B) \cup (A\cap \bar B)$ and $B = (A\cap B) \cup (\bar A \cap B)$. Then $P(A) + P(B)$, after regrouping the disjoint pieces of $A\cup B = (A\cap \bar B)\cup(A\cap B)\cup(\bar A\cap B)$, equals $P(A\cup B) + P(A\cap B)$. Rearrange to get Rule 4a.

### Example — Two Fair Dice, "at least one 6"

Let $R_1$ = "6 on first roll," $R_2$ = "6 on second." "At least one" → union.

$$P(R_1 \cup R_2) = P(R_1) + P(R_2) - P(R_1 \cap R_2) = \tfrac16 + \tfrac16 - \tfrac{1}{36} = \tfrac{11}{36}$$

### Example — Tim Hortons / Canadian Tire

$P(TH) = 0.80$, $P(CT) = 0.63$, $P(TH \cap CT) = 0.51$.

- Shops at **CT or TH**: $P(CT\cup TH) = 0.80 + 0.63 - 0.51 = 0.92$.
- Shops at **CT but not TH**: $P(CT) - P(CT\cap TH) = 0.63 - 0.51 = 0.12$ (uses $P(A\bar B) = P(A) - P(A\cap B)$).

### Rule 4b — Union of Three Events

> [!success] Rule 4b
> $$P(A\cup B\cup C) = P(A)+P(B)+P(C) - P(AB) - P(AC) - P(BC) + P(A\cap B\cap C)$$

> [!tip] Intuition (from lecture)
> Add the three singles; subtract all three pairwise intersections (each double-counted); the triple intersection is now added 3 times and removed 3 times, so add it back once.

### Inclusion–Exclusion Principle (general Rule 4)

> [!success] Inclusion–Exclusion
> For arbitrary events $A_1, \dots, A_n$:
> $$P\!\left(\bigcup_{i=1}^{n} A_i\right) = \sum_i P(A_i) - \sum_{i<j} P(A_iA_j) + \sum_{i<j<k} P(A_iA_jA_k) - \cdots + (-1)^{n-1} P\!\left(\bigcap_{i=1}^{n} A_i\right)$$
> **Pattern:** add singles, subtract pairs, add triples, subtract quads, … Odd-size intersections are *added*, even-size *subtracted* (sign $(-1)^{n-1}$). Rule 4a is $n=2$; Rule 4b is $n=3$.

### Definition — Mutually Exclusive

Events $A, B$ are **mutually exclusive** (disjoint) if $A \cap B = \varnothing$. A family $A_1,\dots,A_n$ is mutually exclusive if **every** pairwise intersection $A_iA_j = \varnothing$.

> [!tip] Lecture intuition
> If events are mutually exclusive, *be happy* — the Venn diagram "pulls apart" into separate islands and probabilities just **add**, no subtraction. Since $P(A\cap B) = P(\varnothing) = 0$, Rule 4a collapses to $P(A\cup B) = P(A) + P(B)$.

### Rule 5 — Union of $n$ Mutually Exclusive Events

> [!success] Rule 5
> If $A_1, \dots, A_n$ are mutually exclusive, then $P\!\left(\bigcup_{i=1}^{n} A_i\right) = \sum_{i=1}^{n} P(A_i)$. A direct consequence of inclusion–exclusion (all multi-way intersections vanish). Extends to countably infinite collections.

### Rule 6 — Complement of an Event

> [!success] Rule 6
> $$P(A) = 1 - P(\bar A) \qquad\Longleftrightarrow\qquad P(\bar A) = 1 - P(A)$$

**Proof (using "mutually exclusive"):** $A$ and $\bar A$ are mutually exclusive (no outcome in both), and $A \cup \bar A = S$. By Normalization, $1 = P(S) = P(A \cup \bar A)$; since disjoint, $= P(A) + P(\bar A)$. Rearrange.

> [!tip] Proof tip
> Always state *why* each equality holds — e.g. "because they are mutually exclusive."

**Secondary useful result (splitting an event):** $A = (A\cap B) \cup (A\cap \bar B)$ (two disjoint pieces), so $P(A) = P(A\cap B) + P(A\cap \bar B)$, giving

$$P(A \cap B) = P(A) - P(A \cap \bar B)$$

Which manipulation you use depends on which probabilities are *given* and which are *easy to compute*.

> [!note] Notation aside
> Complement is written $\bar A$, $A^c$, or $A'$. The instructor prefers the **bar** — unambiguous for De Morgan's law (e.g. $\overline{A\cup B}$), whereas primes need extra brackets.

### Example — Rearranging "FOOD"

Rearrange F, O, O, D at random. Total arrangements $= \frac{4!}{2!} = 12$.

1. $A$ = "the two O's appear together": glue OO into one block → arrange {F, [OO], D} = $3! = 6$; $P(A) = 6/12 = 1/2$.
2. $B$ = "word starts with F": fix F, arrange O, O, D → $\frac{3!}{2!} = 3$; $P(B) = 3/12 = 1/4$.
3. "does **not** start with F **and** the O's do **not** appear together": combine complement/union reasoning with the counts above.

> [!example] Concept check — Mutually Exclusive
> $A, B$ mutually exclusive with $P(\bar A) = 0.8$, $P(\bar B) = 0.7$. So $P(A) = 0.2$, $P(B) = 0.3$. Which is **false**?
> - **A** — "$A$ and $B$ each non-empty": true (both positive probability).
> - **B** — $P(A\cap B) = 0$: true (mutually exclusive).
> - **C** — $P(A\cup B) = 0.4$: **FALSE** — since disjoint, $P(A\cup B) = 0.2 + 0.3 = 0.5$.
> - **D** — $P(A) \le P(B)$: true ($0.2 \le 0.3$).

> [!abstract] Summary — Sets & Addition Rules
> Use Venn diagrams for union/intersection/complement; De Morgan's laws swap unions and intersections under complement; union probabilities come from inclusion–exclusion, simplifying to plain addition when events are mutually exclusive; and there's a deep link between *adding probabilities* and *unions of events*.

## 2.3 Dependent and Independent Events

So far we compute $P(A)$ in isolation. But knowing another event $B$ occurred may change $A$'s probability.

- $P(\text{rain today})$ vs. $P(\text{rain today} \mid \text{cloudy})$.
- If the sum of two dice is 6, what's $P(\text{rolled two 3's})$?

> [!note] Independent vs. dependent
> If knowing $B$ **does not** change $P(A)$ → **independent**. If it **does** → **dependent** (and we quantify the change with *conditional probability*, §2.4).

### Definition — Independent Events

> [!success] Independence (original definition)
> Events $A$ and $B$ are **independent** if and only if
> $$P(A \cap B) = P(A)\,P(B)$$
> If this fails, they are **dependent**.

**Two question types you'll face:**

- **Q1 — Are they independent?** Compute $P(A)$, $P(B)$, $P(A\cap B)$ and check whether $P(A\cap B) = P(A)P(B)$.
- **Q2 — Assume independence to find a probability.** e.g. given $P(A)$, $P(B)$ and independence, find $P(A\cap B) = P(A)P(B)$; or given $P(\bar A)$, $P(A\cap B)$ and independence, back out $P(B)$.

### Example — Two dice: $A=\{\text{sum }10\}$, $B=\{\text{first die }6\}$, $C=\{\text{sum }7\}$

1. $P(A) = 3/36 = 1/12$ (from $(4,6),(5,5),(6,4)$); $P(B) = 6/36 = 1/6$; $P(C) = 6/36 = 1/6$.
2. **$A, B$ independent?** $A\cap B = \{(6,4)\}$, $P = 1/36$. Check $P(A)P(B) = \frac{1}{12}\cdot\frac16 = \frac{1}{72} \ne \frac{1}{36}$ → **dependent.**
3. **$B, C$ independent?** $B\cap C = \{(6,1)\}$, $P = 1/36$. Check $P(B)P(C) = \frac16\cdot\frac16 = \frac{1}{36}$ → **independent.**
4. **$A, C$ independent?** $A\cap C = \varnothing$ (a sum can't be both 10 and 7), $P = 0 \ne \frac{1}{72}$ → **dependent** (in fact mutually exclusive).

### Mutually Exclusive ≠ Independent (common misconception)

> [!danger] Mutually exclusive ≠ independent
> - Mutually exclusive events $A,B$ with $P(A)>0$ and $P(B)>0$ are **never** independent, because $P(A\cap B) = P(\varnothing) = 0 \ne P(A)P(B)$.
> - If $A, B$ are **both** independent **and** mutually exclusive, then $P(A) = 0$ or $P(B) = 0$.

Intuitively: mutual exclusivity is a *strong dependence* — if one happens, the other cannot.

### Independence of the Complement

> [!success] Complement independence
> If $A$ and $B$ are independent, then so are $A$ & $\bar B$, $\bar A$ & $B$, and $\bar A$ & $\bar B$.

### Definition — Mutually Independent (more than two events)

> [!note] Mutually Independent
> Events $A_1, \dots, A_n$ ($n \ge 2$) are **mutually independent** iff for **every** subcollection of distinct indices $i_1, \dots, i_k$:
> $$P(A_{i_1} A_{i_2} \cdots A_{i_k}) = P(A_{i_1})P(A_{i_2})\cdots P(A_{i_k})$$
> For three events $A,B,C$ this requires **all** of $P(AB)=P(A)P(B)$, $P(AC)=P(A)P(C)$, $P(BC)=P(B)P(C)$, **and** $P(ABC)=P(A)P(B)P(C)$. Pairwise independence alone is **not** enough. If mutually independent, so are their complements.

### Example — Passing the Driver's Test

Friends A, B, C pass with probabilities 0.9, 0.8, 0.7, mutually independent.

1. **Exactly one passes** — sum the three disjoint "one passes, two fail" cases:

$$(0.9)(0.2)(0.3) + (0.1)(0.8)(0.3) + (0.1)(0.2)(0.7) = 0.054 + 0.024 + 0.014 = 0.092$$

2. **At least one passes** — complement of "all three fail":

$$1 - (0.1)(0.2)(0.3) = 1 - 0.006 = 0.994$$

### Example — Pairwise but not mutually independent

Two fair dice. $A$ = first die odd, $B$ = second die odd, $C$ = sum odd. Each has probability $1/2$. Every *pair* satisfies $P(\cdot\cap\cdot) = 1/4 = \frac12\cdot\frac12$ (pairwise independent), yet $P(A\cap B\cap C) = 0 \ne 1/8$ (if the first two are odd, the sum is even, so $C$ can't occur). Hence **pairwise independent but not mutually independent.**

> [!abstract] Summary — Dependent & Independent Events
> Two events are **independent** iff $P(A\cap B) = P(A)P(B)$ — either verify it, or assume it to compute. Independence and mutual exclusivity are **different** (disjoint, positive-probability events are dependent). Independence of many events requires every subcollection to factor — hence **mutually independent**.

## 2.4 Conditional Probability and Product Rules

Our estimate of a probability can **change when we learn some information**. Examples:

- $P(\text{rain today})$ vs. $P(\text{rain today} \mid \text{cloudy outside})$.
- $P(\text{a customer makes an insurance claim})$ vs. $P(\text{claim} \mid \text{under age 25})$.
- $P(\text{world ends Friday})$ vs. $P(\text{world ends Friday} \mid \text{I ate cheese})$.

This is captured by **conditional probability**.

### Definition — Conditional Probability

> [!success] Conditional Probability
> The **conditional probability of event $A$, given event $B$** is
> $$P(A\mid B) = \frac{P(A \cap B)}{P(B)}, \qquad \text{provided } P(B) > 0.$$

### Changing perspective ($B$ becomes the new sample space)

> [!tip] Key intuition (from lecture)
> Once we're told $B$ occurred, we treat $B$ as the **new sample space** and "zoom in" on it. $P(A\mid B)$ asks: of the region $B$, what fraction is also in $A$? That's why we divide by $P(B)$ instead of $P(S)$ — we renormalize so that $B$ has total probability 1. Because $B$ is just a new sample space, **all the usual probability rules still hold** for conditional probability; we just carry the "$\mid B$" through.

### Conditional probability obeys the same rules

> [!success] Rules for conditional probability
> For a fixed conditioning event $B$ with $P(B) > 0$:
> - **Normalization:** $P(S\mid B) = 1$.  *(Since $S\cap B = B$, $P(S\mid B) = \frac{P(B)}{P(B)} = 1$.)*
> - **Boundedness:** $0 \le P(A\mid B) \le 1$.  *(From $A\cap B \subseteq B$ via monotonicity.)*
> - **Union:** $P(A_1 \cup A_2 \mid B) = P(A_1\mid B) + P(A_2\mid B) - P(A_1 \cap A_2 \mid B)$.
> - **Complement:** $P(A\mid B) = 1 - P(\bar A \mid B)$.  *(Complement the $A$ part, **not** the $B$.)*

### Example — Rolling two fair dice

Let $A=\{\text{sum }10\}$, $B=\{\text{first die }6\}$, $C=\{\text{sum }7\}$. Recall $P(A)=3/36$, $P(B)=6/36$, $P(C)=6/36$.

1. $P(A\mid B) = \frac{P(A\cap B)}{P(B)} = \frac{1/36}{6/36} = \frac16$  (only $(6,4)$ is in $A\cap B$).
2. $P(B\mid A) = \frac{P(A\cap B)}{P(A)} = \frac{1/36}{3/36} = \frac13$.
3. $P(A\mid C) = \frac{P(A\cap C)}{P(C)} = \frac{0}{6/36} = 0$  (a sum can't be both 10 and 7).
4. $P(C\mid B) = \frac{P(C\cap B)}{P(B)} = \frac{1/36}{6/36} = \frac16$  (only $(6,1)$ is in $C\cap B$).

### Equivalent Definition of Independence

> [!success] Independence (conditional form)
> Two events $A, B$ with $P(A) > 0$ and $P(B) > 0$ are **independent** if and only if **either** holds:
> $$P(A\mid B) = P(A) \qquad\text{or}\qquad P(B\mid A) = P(B).$$
> **Meaning:** knowing $B$ occurred gives *no information* about $A$ — it doesn't change $A$'s probability.

**Proof sketch (from lecture, if-and-only-if).** ( $\Rightarrow$ ) If independent, $P(A\mid B) = \frac{P(A\cap B)}{P(B)} = \frac{P(A)P(B)}{P(B)} = P(A)$. ( $\Leftarrow$ ) If $P(A\mid B) = P(A)$, then $\frac{P(A\cap B)}{P(B)} = P(A)$, so $P(A\cap B) = P(A)P(B)$ — the original definition. Valid only when neither probability is 0.

> [!tip] Why this is useful
> If $P(A\cap B)$ is awkward to compute but you can get $P(A\mid B)$ and $P(A)$ directly, comparing them is a faster independence check. (State up front that neither probability is 0.)

### Example — Rearranging "RACECAR"

7 letters: 1 E, 2 A's, 2 C's, 2 R's → total arrangements $\frac{7!}{2!\,2!\,2!} = 630$. The word **given** in a question signals a **conditional probability**.

**1. $P(\text{word ends in "R"} \mid \text{word starts with "ACE"})$.** Fix ACE at the front; the remaining letters are A, C, R, R (4 letters, two R's). Directly:
$$P(R\mid A) = \frac{3!/2!}{4!/2!} = \frac{3}{12} = \frac12.$$
*Quick argument:* 2 of the 4 remaining letters are R, so $P = 2/4 = 1/2$.

**2. Is "starts with ACE" independent of "ends with R"?** Compute the unconditional $P(\text{ends in R})$: 2 of the 7 letters are R, so $P(R) = 2/7$. Since
$$P(R\mid A) = \tfrac12 \ne \tfrac27 = P(R),$$
the events are **not independent**. This makes sense: sending A, C, E to the front leaves fewer letters, changing R's chance of landing last. (Doing it via conditional probability needed **two** probabilities instead of three.)

### Rule 7a — Product Rule (Intersection of Two Events)

> [!success] Product Rule
> For events $A, B$:
> $$P(A\cap B) = P(A\mid B)\,P(B) \quad (\text{if } P(B)>0) \qquad = \qquad P(B\mid A)\,P(A) \quad (\text{if } P(A)>0).$$
> Just a rearrangement of the conditional-probability definition. **Ideal for sequential events.**

### Example — Red and Blue Balls (12 red, 7 blue; 19 total; without replacement)

Let $R_i$ = "the $i$-th ball drawn is red."

**Part 1 — $P(\text{both red})$.**

*Counting method:* $\dfrac{\binom{12}{2}\binom{7}{0}}{\binom{19}{2}} = \dfrac{66}{171} = \dfrac{12\cdot 11}{19\cdot 18} \approx 0.386$.

*Product-rule method (sequential):*
$$P(R_1 \cap R_2) = P(R_2\mid R_1)\,P(R_1) = \frac{12}{19}\cdot\frac{11}{18} = \frac{132}{342} \approx 0.386.$$
The second draw's probability *depends* on the first — after pulling a red, 11 reds remain out of 18. This justifies the "instinctive" sequential arguments from Chapter 1.

### Definition — Partition of the Sample Space

> [!note] Partition
> Sets $B_1, B_2, \dots, B_k$ **partition** $S$ if they cover it with no overlap:
> $$\bigcup_{j=1}^{k} B_j = S \qquad\text{and}\qquad B_i \cap B_j = \varnothing \text{ for all } i \ne j.$$
> The simplest partition is $B$ and $\bar B$.

### Rule 8 — Law of Total Probability

> [!success] Law of Total Probability
> If $B_1, \dots, B_k$ partition $S$ with each $P(B_i) > 0$, then for any event $A$:
> $$P(A) = \sum_{i=1}^{k} P(A\mid B_i)\,P(B_i).$$
> **Common two-piece form** (partition $B, \bar B$):
> $$P(A) = P(A\mid B)P(B) + P(A\mid \bar B)P(\bar B).$$

**Example — Red and Blue Balls, Part 2: $P(\text{second ball is red})$.** Partition on the first draw:
$$P(R_2) = P(R_2\mid R_1)P(R_1) + P(R_2\mid \bar R_1)P(\bar R_1) = \frac{11}{18}\cdot\frac{12}{19} + \frac{12}{18}\cdot\frac{7}{19} = \frac{216}{342} = \frac{12}{19} \approx 0.632.$$
Notice $P(R_2) = 12/19 = P(R_1)$ — the second ball is red with the same probability as the first (by symmetry, before we look).

> [!tip] LTP as a weighted average (from lecture)
> Think of $P(A) = \sum_i P(A\mid B_i)P(B_i)$ as a **weighted sum of the conditional probabilities**, where the weights are the partition probabilities $P(B_i)$. You're averaging "the chance of $A$ in each group" weighted by "how big each group is."

**Example — Residence & bicycles.** 40% of students live East, 60% West ($P(E)=0.4$, $P(W)=0.6$). A student owns a bike with probability 0.7 if East, 0.4 if West. Probability a random student owns a bike:
$$P(\text{bike}) = P(\text{bike}\mid E)P(E) + P(\text{bike}\mid W)P(W) = 0.7(0.4) + 0.4(0.6) = 0.28 + 0.24 = 0.52.$$

### Rule 8 — Bayes' Rule / Bayes' Theorem

> [!success] Bayes' Rule
> If $B_1, \dots, B_k$ partition $S$, then for any event $A$:
> $$P(B_i\mid A) = \frac{P(A\mid B_i)\,P(B_i)}{\sum_{j=1}^{k} P(A\mid B_j)\,P(B_j)}.$$
> **Simpler form** (flipping the direction of conditioning):
> $$P(A\mid B) = \frac{P(B\mid A)\,P(A)}{P(B)}.$$
> Use it when you know one direction of conditioning but want the other. (The denominator is usually found via the Law of Total Probability.)

> [!tip] When to reach for Bayes (from lecture)
> If a question feels like **the conditioning is "slipping" or flipping** — you're *given* $P(\text{evidence}\mid \text{cause})$ but *asked* for $P(\text{cause}\mid \text{evidence})$ — that's the signal to use Bayes' Rule.

**Example — Residence, flipped (Bayes).** Using the residence setup ($P(E)=0.4$, $P(\text{bike}\mid E)=0.7$, and $P(\text{bike})=0.52$ from the LTP example above): given a student owns a bike, what's the probability they live East?
$$P(E\mid \text{bike}) = \frac{P(\text{bike}\mid E)P(E)}{P(\text{bike})} = \frac{0.7(0.4)}{0.52} = \frac{0.28}{0.52} \approx 0.538.$$
We only knew the *other* direction ($P(\text{bike}\mid E)$), so we flipped it with Bayes, expanding the denominator via the Law of Total Probability.

### Example — Testing for a Rare Disease

A disease affects **0.3%** of a population. Test **sensitivity** $P(+\mid D) = 0.96$; **false-positive rate** $P(+\mid \bar D) = 0.05$. So $P(D)=0.003$, $P(\bar D)=0.997$.

> [!note] Diagnostic-testing terminology (worth knowing, not memorizing)
> The same setup appears in manufacturing (D = "defective part"). There are **two ways to be right** (two disease states):
> - **Prevalence** = $P(D)$ = base rate of the disease (here 0.003).
> - **Sensitivity** = $P(+\mid D)$ = P(test catches a true case) = 0.96.
> - **Specificity** = $P(-\mid \bar D)$ = P(test clears a healthy person) = $1 - 0.05 = 0.95$.
> - **Positive predictive value (PPV)** = $P(D\mid +)$ (Part 2 below).
> - **Negative predictive value (NPV)** = $P(\bar D\mid -)$ (very high here).
>
> Complements move between conditional probabilities: $P(-\mid \bar D) = 1 - P(+\mid \bar D)$.

**Part 1 — $P(\text{test is positive})$** (Law of Total Probability):
$$P(+) = P(+\mid D)P(D) + P(+\mid \bar D)P(\bar D) = 0.96(0.003) + 0.05(0.997) = 0.00288 + 0.04985 \approx 0.0527.$$

**Part 2 — $P(\text{has disease} \mid \text{positive})$** (Bayes):
$$P(D\mid +) = \frac{P(+\mid D)P(D)}{P(+)} = \frac{0.00288}{0.0527} \approx 0.0546.$$

> [!danger] The surprise (base-rate fallacy)
> Even after a positive result on a 96%-accurate test, there's only about a **5.5%** chance the person is actually sick. Because the disease is so rare, the *false positives from the huge healthy majority* swamp the true positives.

**Part 3 — $P(\text{has disease} \mid \text{negative})$.** With $P(-\mid D) = 0.04$ and $P(-) = 1 - 0.0527 = 0.9473$:
$$P(D\mid -) = \frac{P(-\mid D)P(D)}{P(-)} = \frac{0.04(0.003)}{0.9473} \approx 0.000127.$$
A negative test drives the disease probability far below the 0.3% base rate — very reassuring. (This is the **negative predictive value**'s complement: $P(\bar D\mid -) \approx 0.99987$ — an excellent NPV.)

### Tree-diagram method (same disease problem)

A visual alternative for conditional-probability problems. **Split on what you're told first** (here, disease status — you don't have $P(+)$ yet, so you can't split on the test first). After the first split, every branch probability is **conditional** on the branches above it. Multiply along a path to get the intersection at each leaf.

| Path | Branch probs | Leaf = intersection |
|---|---|---|
| $D \to +$ | $0.003 \times 0.96$ | $P(D\cap +) = 0.00288$ |
| $D \to -$ | $0.003 \times 0.04$ | $P(D\cap -) = 0.00012$ |
| $\bar D \to +$ | $0.997 \times 0.05$ | $P(\bar D\cap +) = 0.04985$ |
| $\bar D \to -$ | $0.997 \times 0.95$ | $P(\bar D\cap -) = 0.94715$ |

> [!tip] Tree-diagram sanity check
> The **leaf probabilities must sum to 1** (everyone follows exactly one path) — here $0.00288 + 0.00012 + 0.04985 + 0.94715 = 1$. ✅ Recover $P(+)$ by adding the two "+" leaves: $0.00288 + 0.04985 = 0.05273$, matching Part 1.

### The Monty Hall Problem

Three doors: two hide goats, one a car. You pick a door; the host (who knows) opens a *different* door revealing a goat; you may **stay** or **switch**. What should you do?

| Door 1 (your pick) | Door 2 | Door 3 | Stay wins? | Switch wins? |
|---|---|---|---|---|
| Goat | Goat | Car | No | Yes |
| Goat | Car | Goat | No | Yes |
| Car | Goat | Goat | Yes | No |

> [!success] Answer: switch
> Staying wins only when your first pick was the car — probability $1/3$. Switching wins in the other $2/3$ of cases. The host's action leaks information because they never open the car door.

**Formal proof (Law of Total Probability).** WLOG you pick door 1. Let $D_i$ = "car is behind door $i$"; $\{D_1, D_2, D_3\}$ **partitions** $S$, each with $P(D_i) = 1/3$. Under a fixed strategy, $P(\text{win}\mid D_i)$ is just 0 or 1 (you either win in that arrangement or you don't):

$$P(\text{win} \mid \text{stay}) = \sum_{i=1}^{3} P(\text{win}\mid D_i)P(D_i) = \underbrace{1}_{D_1}\cdot\tfrac13 + \underbrace{0}_{D_2}\cdot\tfrac13 + \underbrace{0}_{D_3}\cdot\tfrac13 = \tfrac13.$$
$$P(\text{win} \mid \text{switch}) = 0\cdot\tfrac13 + 1\cdot\tfrac13 + 1\cdot\tfrac13 = \tfrac23.$$

> [!tip] Exam framing (from lecture)
> You wouldn't be handed the table — **building the table yourself** (enumerate the arrangements, mark win/lose under each strategy) is what earns marks. Once the table exists, the LTP calculation is straightforward.

> [!abstract] Summary — §2.4
> Conditional probability $P(A\mid B) = \frac{P(A\cap B)}{P(B)}$ renormalizes to $B$ as the new sample space and obeys all the usual rules. **Product rule:** $P(A\cap B) = P(A\mid B)P(B) = P(B\mid A)P(A)$. **Independence** $\iff P(A\mid B) = P(A)$. A **partition** splits $S$; the **Law of Total Probability** builds $P(A)$ from the pieces; **Bayes' Rule** flips the conditioning. These problems "usually involve a long story."

---

# Chapter 3 — Univariate Discrete Probability Distributions

> [!abstract] Goal
> Move from events to **random variables**: functions that turn outcomes into numbers. Describe a discrete RV with its **pmf** and **CDF**, transform one RV into another ($Y = g(X)$), and summarize a distribution with its **expectation**.

**Roadmap:** 3.1 Discrete Random Variables · 3.2 Functions of Random Variables · 3.3 Expectation · 3.4 Moment Generating Functions · 3.5 Special Discrete Distributions.

## 3.1 Discrete Random Variables

> [!tip] Why we're doing this (from lecture)
> So far every probability came from a **uniform model** — count outcomes, assume each is equally likely. Statisticians rarely count from first principles. Instead we bucket recurring counting patterns into **named standard distributions** (binomial, Poisson, hypergeometric, and later the normal "bell curve"). A distribution is really just an *organized counting argument we reuse over and over* — far more powerful once set up. Chapter 3 builds the notation; §3.5 reaches the named distributions.

### Useful mathematical results (keep these handy)

> [!success] Series & theorems you'll reuse
> **Geometric series:** $\sum_{i=0}^{\infty} r^i = \dfrac{1}{1-r}$ if $|r| < 1$.
> **Partial geometric series:** $\sum_{i=0}^{k} r^i = \dfrac{1 - r^{k+1}}{1-r}$.
> **Binomial theorem:** $(a+b)^n = \sum_{i=0}^{n} \binom{n}{i} a^i b^{n-i}$.
> **Taylor series for the exponential:** $e^x = \sum_{i=0}^{\infty} \dfrac{x^i}{i!}$.

These appear constantly when summing pmf's to 1 or deriving means/variances.

> [!example] Worked geometric series — watch the starting index
> Evaluate $\sum_{i=1}^{\infty} \left(\tfrac12\right)^i$. The formula starts at $i=0$, but this sum starts at $i=1$, so subtract the $i=0$ term:
> $$\sum_{i=1}^{\infty} \left(\tfrac12\right)^i = \sum_{i=0}^{\infty} \left(\tfrac12\right)^i - \left(\tfrac12\right)^0 = \frac{1}{1 - \tfrac12} - 1 = 2 - 1 = 1.$$
> **Check the index** before applying the formula — forgetting the $i=0$ term is the classic slip.

### Definition — Random Variable

> [!note] Random Variable
> A **random variable** $X$ is a function assigning a real number to each point in a sample space $S$:
> $$X : S \to \mathbb{R}.$$
> - Abbreviated **RV** or **rv**.
> - The **range** of $X$ (the values it can take) is denoted $X(S)$ or $A$.
> - A **realization** is written lowercase, e.g. $x$.

### Example — number of tails in two coin flips

$S = \{HH, HT, TH, TT\}$; let $X$ = number of tails. Then $X(HH)=0$, $X(HT)=X(TH)=1$, $X(TT)=2$, so the **range** is $X(S) = \{0,1,2\}$. To get $P(X=x)$, add the probabilities of all outcomes mapping to $x$: e.g. $P(X=1) = P(\{HT, TH\}) = \tfrac14 + \tfrac14 = \tfrac12$.

### Discrete vs. Continuous Random Variables

Classified by their range:

> [!note] Discrete vs. Continuous
> - **Discrete:** range is a *discrete* subset of $\mathbb{R}$ (finite or countably infinite).
> - **Continuous:** range is an *interval* of $\mathbb{R}$ (e.g. $[0,1]$, $(0,\infty)$, $\mathbb{R}$).

| Experiment | $X$ | Range | Type |
|---|---|---|---|
| Flip a coin | # of heads | $\{0, 1\}$ | Discrete |
| Roll two 6-sided dice | sum of rolls | $\{2, 3, \dots, 12\}$ | Discrete |
| Roll a die until a six appears | # rolls | $\{1, 2, 3, \dots\}$ | Discrete (countably infinite) |
| Fill a water bottle | amount of water | $[0, c]$ interval | Continuous |
| Emergency-room wait time | time measured | $[0, \infty)$ | Continuous |

> [!note] Mixed / singular RVs (aside — out of scope)
> A **mixed** RV has both a discrete point mass and a continuous part. Example: $X$ = time waiting to turn left at a light. Arrive on **green** → $X = 0$ (a point mass, discrete). Arrive on **red** → $X \in (0, \infty)$ (continuous). We won't handle these — just make sure when you *define* a random variable it comes out **fully discrete or fully continuous**. Continuous RVs come in Chapter 5.

### Definition — Probability Mass Function (pmf)

> [!note] Probability Mass Function
> The **probability mass function** of a discrete RV $X$ is
> $$f_X(x) = P(X = x), \qquad \text{defined for all } x \in A \text{ (the range)}.$$
> - Shorthand: $P(X=x)$ means $P(\{\omega \in S : X(\omega) = x\})$.
> - Defined for all $x$, but nonzero only on the range: if $x \notin A$ then $f(x) = 0$.
> - The set of pairs $\{(x, f(x)) : x \in A\}$ is the **probability distribution** of $X$.

> [!success] Two conditions every pmf must satisfy
> (Inherited from the probability axioms.)
> $$f_X(x) \ge 0 \text{ for all } x \qquad\text{and}\qquad \sum_{x \in A} f_X(x) = 1.$$

### Example — pmf of "number of tails" (fair coin, two flips)

$$f_X(0) = \tfrac14, \qquad f_X(1) = \tfrac12, \qquad f_X(2) = \tfrac14.$$

| $x$ | 0 | 1 | 2 |
|---|---|---|---|
| $f_X(x)$ | $1/4$ | $1/2$ | $1/4$ |

Check: $\tfrac14 + \tfrac12 + \tfrac14 = 1$. ✅ The pmf is symmetric, peaking at $x=1$ (the most likely outcome).

### Example — The Geometric Distribution (roll until the first six)

Let $X$ = number of tosses until the first six on a fair die. The **range** is $X(S) = \{1, 2, 3, \dots\}$ — you can't get a six in 0 tosses, and in theory it could take arbitrarily many (countably infinite range). Build the pmf by noticing the tosses are **independent**, so probabilities multiply:

- $f(1) = P(X=1) = P(\text{six on toss 1}) = \tfrac16$.
- $f(2) = P(\text{not six, then six}) = \tfrac56\cdot\tfrac16$.
- $f(3) = P(\text{not six, not six, then six}) = \left(\tfrac56\right)^2\tfrac16$.

The pattern:

> [!success] Geometric pmf
> $$f(x) = P(X=x) = \left(\tfrac56\right)^{x-1}\left(\tfrac16\right), \qquad x = 1, 2, 3, \dots$$
> In words: the first $x-1$ tosses are **not** six, then the $x$-th **is** six.

Plotted, it **decays geometrically**: $f(1)=\tfrac16$, $f(2)=\tfrac{5}{36}$, $f(3)=\tfrac{25}{216}$, … smaller and smaller as $x$ grows.

> [!tip] A recurring "wait for the first success" pattern
> Any experiment that **repeats an independent trial until the first success** has this shape — flip a coin until the first head, draw until the first defective, etc. This is the **geometric distribution**, one of the named distributions in §3.5.

### Why a valid pmf sums to 1 (proof sketch)

The events $\{\omega : X(\omega)=x\}$ for different $x$ are **disjoint** — each outcome maps to **exactly one** value of $X$ (that's what makes $X$ a function). Summing the pmf over all $x$ therefore sums the probabilities of disjoint events that together cover $S$:

$$\sum_{x} f(x) = \sum_{x} P(X=x) = P\!\left(\bigcup_{x}\{X=x\}\right) = P(S) = 1.$$

> [!note] Many-to-one is fine; one-to-many is not
> *Multiple* outcomes mapping to the same $x$ is expected and desirable — collapsing a huge sample space into a few values is the whole point of a random variable. What's forbidden is one outcome mapping to two different $x$ (then it wouldn't be a function).

### Example — verifying the geometric pmf sums to 1

$$\sum_{x=1}^{\infty} \left(\tfrac56\right)^{x-1}\tfrac16 \;\overset{z=x-1}{=}\; \sum_{z=0}^{\infty} \left(\tfrac56\right)^{z}\tfrac16 = \tfrac16\cdot\frac{1}{1-\tfrac56} = \tfrac16\cdot 6 = 1. \;✅$$

> [!tip] Re-index with a new letter
> To make a sum start at 0, substitute a fresh letter ($z = x-1$) rather than reusing $x$ — it keeps the bookkeeping clear. (You *can* instead add-and-subtract the missing term — "the only two moves in math are add 0 and multiply by 1" — but a new letter is cleaner.)

### Finding an unknown constant in a pmf

Often you're given a pmf's *shape* up to a constant and must pin the constant down so the pmf is valid.

**Example (toy).** $X$ takes values $-4, 0, 7$ with $f(-4) = \tfrac{c^2}{6}$, $f(0) = \tfrac{3c}{6}$, $f(7) = \tfrac{2c}{6}$. Find $c$.

**Step 1 — force the sum to 1:**
$$\tfrac{c^2}{6} + \tfrac{3c}{6} + \tfrac{2c}{6} = 1 \;\Rightarrow\; c^2 + 5c = 6 \;\Rightarrow\; c^2 + 5c - 6 = 0 \;\Rightarrow\; (c-1)(c+6) = 0 \;\Rightarrow\; c = 1 \text{ or } c = -6.$$

**Step 2 — force $0 \le f(x) \le 1$ for every $x$:** $c = -6$ gives $f(0) = \tfrac{3(-6)}{6} = -3 < 0$, impossible. So $\boxed{c = 1}$.

> [!warning] Summing to 1 is necessary but not sufficient
> A quadratic can hand you a spurious root that makes some probability negative (or $>1$). **Always do Step 2** and discard any $c$ that breaks $0 \le f(x) \le 1$.

> [!example] Practice — find $k$
> $f(x) = kx$ for $x = 1, 2, \dots, 10$. Sum to 1: $k(1+2+\cdots+10) = k\cdot 55 = 1 \Rightarrow k = \tfrac{1}{55}$. (Uses the arithmetic-series sum $\sum_{i=1}^{n} i = \tfrac{n(n+1)}{2} = 55$.)

> [!note] Validity doesn't depend on the $x$-values
> Whether a pmf is valid depends only on the **probabilities** (nonnegative, sum to 1) — the actual values $-4, 0, 7$ are irrelevant here. *Later* summaries (expectation, variance) **will** depend on the $x$-values.

### Definition — Cumulative Distribution Function (CDF)

A second way to describe a distribution.

> [!note] Cumulative Distribution Function
> The **CDF** of a random variable $X$ is
> $$F(x) = P(X \le x) = \sum_{u \le x} f(u), \qquad \text{defined for all real } x.$$
> - **Notation:** lowercase $f$ = pmf, **UPPERCASE $F$** = CDF. Keep them straight.
> - It's the **cumulative sum** of the pmf up to $x$. For a natural-number range: $F(x) = \sum_{u=1}^{x} f(u)$.

> [!success] PMF and CDF come in pairs
> Know one, get the other — both **uniquely** describe the distribution. Use whichever is easier for the problem at hand.

### Example — CDF of the toy pmf

With $c = 1$ ($f(-4) = \tfrac16$, $f(0) = \tfrac36$, $f(7) = \tfrac26$), break into cases — closed vs. open brackets **matter**:

$$F(x) = \begin{cases} 0 & x < -4 \\[2pt] \tfrac16 & -4 \le x < 0 \\[2pt] \tfrac46 & 0 \le x < 7 \quad(\tfrac16 + \tfrac36) \\[2pt] 1 & x \ge 7 \end{cases}$$

A discrete CDF is a **step function**: it **jumps** at each value where the pmf is positive ($-4, 0, 7$), by the size of the pmf there, and is **flat** in between. It is **right-continuous** — the solid dot sits at the **top** of each step, so e.g. $F(-4) = \tfrac16$ *includes* the jump. It starts at 0 and climbs to 1.

### Example — CDF of the geometric distribution

From $f(j) = \left(\tfrac56\right)^{j-1}\tfrac16$:

$$F(x) = \sum_{j=1}^{x} \left(\tfrac56\right)^{j-1}\tfrac16 \;\overset{i=j-1}{=}\; \tfrac16\sum_{i=0}^{x-1}\left(\tfrac56\right)^{i} = \tfrac16\cdot\frac{1 - \left(\tfrac56\right)^{x}}{1 - \tfrac56} = 1 - \left(\tfrac56\right)^{x},$$

for $x = 1, 2, 3, \dots$ (using the **partial** geometric series $\sum_{i=0}^{n} r^i = \tfrac{1-r^{n+1}}{1-r}$). To define $F$ over **all reals**, use the **floor** function:

> [!success] Geometric CDF
> $$F(x) = 1 - \left(\tfrac56\right)^{\lfloor x \rfloor}, \quad x \ge 1 \qquad (F(x) = 0 \text{ for } x < 1).$$
> $\lfloor x \rfloor$ = the largest integer $\le x$ (e.g. $\lfloor 1.9 \rfloor = 1$). It rises toward 1 but **never reaches it** — a very long wait is improbable but possible.

> [!tip] Why the floor?
> A CDF must be defined for **all real $x$**, not just the range. Between jump points the value is constant, which is exactly what $\lfloor x \rfloor$ produces.

### CDF properties

> [!success] Properties of any CDF $F(x) = P(X \le x)$
> 1. **Bounded:** $0 \le F(x) \le 1$ for all real $x$.
> 2. **Non-decreasing:** $x < y \Rightarrow F(x) \le F(y)$.
> 3. **Right-continuous:** $\lim_{h\to 0^+} F(x+h) = F(x)$ (the solid dot is at the top of each step).
> 4. **Limits:** $\lim_{x\to-\infty} F(x) = 0$ and $\lim_{x\to+\infty} F(x) = 1$.

### Recovering the pmf from the CDF (and interval probabilities)

A discrete CDF jumps exactly at the values with positive probability, and **the jump size is the pmf**. Order the range $A = \{a_1 < a_2 < a_3 < \cdots\}$:

> [!success] pmf ⇄ CDF
> $$f(a_1) = F(a_1), \qquad f(a_i) = F(a_i) - F(a_{i-1}) \ \ (i \ge 2),$$
> the size of the "jump" in $F$ at $a_i$. And the interval probability
> $$P(a < X \le b) = F(b) - F(a).$$

So the two representations are interchangeable: **sum** the pmf to build $F$; **difference** $F$ to recover the pmf.

> [!warning] Mind the brackets
> $P(a < X \le b) = F(b) - F(a)$ is **strict on the left, inclusive on the right** — matching right-continuity. Changing a $<$ to $\le$ can add or drop a jump's worth of probability.

### Example — Minimum of Five Dice (CDF-first)

Roll five fair dice; let $X$ = the **minimum** of the five rolls, range $\{1,\dots,6\}$. The **CDF is easier than the pmf** here, via the complement — the minimum exceeds $x$ iff *every* die exceeds $x$:

$$F(x) = P(X \le x) = 1 - P(X > x) = 1 - P(\text{all five} > x) = 1 - \left(\tfrac{6-x}{6}\right)^{5}, \qquad x = 1,\dots,6,$$

since each die independently exceeds $x$ with probability $\tfrac{6-x}{6}$. Recover the pmf by differencing:

$$f(x) = F(x) - F(x-1) = \left(\tfrac{7-x}{6}\right)^{5} - \left(\tfrac{6-x}{6}\right)^{5}.$$

Interval probability:
$$P(2 < X \le 4) = F(4) - F(2) = \left[1 - \left(\tfrac{2}{6}\right)^5\right] - \left[1 - \left(\tfrac{4}{6}\right)^5\right] = \left(\tfrac23\right)^5 - \left(\tfrac13\right)^5 = \tfrac{32}{243} - \tfrac{1}{243} = \tfrac{31}{243} \approx 0.128.$$

> [!tip] When to go CDF-first
> For the **min or max of several independent trials**, the CDF is the natural object: $\min > x \iff \text{all} > x$, and $\max \le x \iff \text{all} \le x$. Get $F$ by the complement, then difference it to the pmf.

### (Equivalent) Distributions

> [!note] Distribution & "same distribution"
> The **distribution** of $X$ is the probability measure $P_X(B) = P(X \in B)$ on $\mathbb{R}$ — described **entirely** by the CDF $F_X(x)$.
> Two random variables $X, Y$ **have the same distribution**, written $X \sim Y$, if $F_X(x) = F_Y(x)$ for all real $x$.

**Example.** $X$ = # heads in one fair coin flip, and $Y = 1$ if a die roll is even, $0$ if odd. Both have $f(0)=f(1)=\tfrac12$, so $X \sim Y$ — same distribution, even though the experiments differ.

> [!abstract] Summary — §3.1
> A random variable $X : S \to \mathbb{R}$ turns outcomes into numbers; its **range** is the values it takes (discrete if finite/countable). Its distribution is captured by the **pmf** $f(x) = P(X=x)$ (nonnegative, sums to 1) or the **CDF** $F(x) = P(X \le x)$ — a **non-decreasing, right-continuous** step function from 0 to 1 — and each determines the other: $F$ sums the pmf; the pmf is the jump $f(a_i) = F(a_i) - F(a_{i-1})$, with $P(a < X \le b) = F(b) - F(a)$. **Validate** a pmf with $\sum f = 1$ **and** $0 \le f \le 1$. Two RVs with equal CDFs have the **same distribution** ($X \sim Y$). Running examples: the **geometric** $f(x) = (5/6)^{x-1}(1/6)$, $F(x) = 1-(5/6)^{\lfloor x\rfloor}$; and the **minimum of five dice**.

## 3.2 Functions of Random Variables

Often the process is defined via one RV $X$, but we care about a related $Y = g(X)$.

- $X$ = temperature in °C, $Y$ = °F: the linear transformation $Y = 1.8X + 32$.
- $X$ = radius of a growing tumour, $Y$ = its volume: $Y = \tfrac43 \pi X^3$ (nonlinear).

If $X$ is discrete with range $A$, then $Y = g(X)$ is also discrete, and we get its pmf from $X$'s.

> [!success] pmf of a transformed RV
> $$f_Y(y) = P(Y = y) = P(x \in A : g(x) = y) = \sum_{x \in A\, :\, g(x)=y} f_X(x).$$
> Add up the probabilities of **all** $x$ that $g$ sends to $y$.

### Example — Transformation of a toy example

$X \in \{-4, 0, 7\}$ with $f_X = \tfrac16, \tfrac36, \tfrac26$. Let $Y = |X + 2|$. Map each value: $g(-4)=|-2|=2$, $g(0)=|2|=2$, $g(7)=|9|=9$. So $y=2$ comes from **two** $x$'s and $y=9$ from one:

$$f_Y(2) = f_X(-4) + f_X(0) = \tfrac16 + \tfrac36 = \tfrac46, \qquad f_Y(9) = f_X(7) = \tfrac26.$$

(The two $x$'s collapsing onto $y=2$ is exactly the "sum over the preimage" rule.)

### Injective (one-to-one) transformations

If $g$ is **injective**, each $y$ comes from exactly one $x = g^{-1}(y)$, so the sum has a single term:

> [!success] Injective transformation
> $$f_Y(y) = f_X\!\big(g^{-1}(y)\big).$$
> **Linear case** $Y = aX + b$ (with $a \ne 0$): $f_Y(y) = f_X\!\left(\dfrac{y - b}{a}\right)$.

### Example — Linear transformation (geometric)

Roll until the first six: $f_X(x) = \tfrac16\left(\tfrac56\right)^{x-1}$, $x = 1,2,\dots$. Let $Y$ = number of non-sixes **before** the six $= X - 1$ (injective, $a=1$, $b=-1$, so $g^{-1}(y) = y+1$):

$$f_Y(y) = f_X(y+1) = \tfrac16\left(\tfrac56\right)^{y}, \qquad y = 0, 1, 2, \dots$$

the "number of failures before the first success" form of the geometric distribution.

> [!abstract] Summary — §3.2
> For $Y = g(X)$: $f_Y(y) = \sum_{x:\,g(x)=y} f_X(x)$. If $g$ is injective, $f_Y(y) = f_X(g^{-1}(y))$; for linear $Y = aX+b$, $f_Y(y) = f_X((y-b)/a)$. (And for an *expectation* of $g(X)$ we won't even need $f_Y$ — see §3.3.)

## 3.3 Expectation of a Random Variable

Usually we want a single **summary measure**, not the whole distribution — a class average, a lottery's expected return, a country's life expectancy.

### Definition — Expected Value

> [!success] Expected value (mean / first moment)
> For a discrete RV $X$ with range $A$ and pmf $f(x)$:
> $$E(X) = \sum_{x \in A} x\, f(x).$$
> - Denoted $\mu$ or $\mu_X$; also called the **mean** or the **first moment** of $X$.
> - A **weighted average** of $X$'s values, weighted by their probabilities.
> - $E(X)$ need **not** be a value in the range $A$.

> [!tip] Interpretations
> - **Balancing point** (centre of mass) of the pmf.
> - **Law of large numbers:** the average of many independent realizations of $X$ approaches $E(X)$.

### Example — expected value of a die

$X$ = one fair die roll: $E(X) = \sum x\cdot\tfrac16 = \tfrac{1+2+3+4+5+6}{6} = \tfrac{21}{6} = 3.5$. (Not an achievable face — expectations needn't lie in the range.)

### Example — toy expectation

$X \in \{-4, 0, 7\}$, $f = \tfrac16, \tfrac36, \tfrac26$: $E(X) = -4\cdot\tfrac16 + 0\cdot\tfrac36 + 7\cdot\tfrac26 = \tfrac{-4 + 0 + 14}{6} = \tfrac{10}{6} = \tfrac53 \approx 1.67$.

### Example — Lottery expected return

Match all 7 of 7 numbers drawn without replacement from 1–50: $P(\text{win}) = 1/\binom{50}{7}$ with $\binom{50}{7} = 99{,}884{,}400$. A ticket costs \$1 and the jackpot is \$5,000,000. Net return $R$ is $+\$4{,}999{,}999$ on a win, $-\$1$ on a loss:

$$E(R) = 4{,}999{,}999\cdot\tfrac{1}{99{,}884{,}400} + (-1)\cdot\left(1 - \tfrac{1}{99{,}884{,}400}\right) = \tfrac{5{,}000{,}000}{99{,}884{,}400} - 1 \approx 0.0501 - 1 \approx -\$0.95.$$

On average you lose about **95¢ per ticket** — the expected gross payout is only ~5¢ on a \$1 bet.

> [!warning] A distribution without an expectation
> Expectation doesn't always exist. If $f(x) = \tfrac{6}{\pi^2}\cdot\tfrac{1}{x^2}$ for $x = 1,2,3,\dots$ (a valid pmf, since $\sum \tfrac{1}{x^2} = \tfrac{\pi^2}{6}$), then
> $$E(X) = \sum_{x=1}^\infty x\cdot\tfrac{6}{\pi^2}\tfrac{1}{x^2} = \tfrac{6}{\pi^2}\sum_{x=1}^\infty \tfrac1x = +\infty$$
> (the harmonic series diverges). So $E(X) = +\infty$ — no finite mean.

### Expectation of a function: $E[g(X)]$

> [!success] Law of the unconscious statistician (LOTUS)
> $$E[g(X)] = \sum_{x \in A} g(x)\, f_X(x).$$
> You do **not** need the pmf of $g(X)$ — just weight $g(x)$ by $f_X(x)$.

### Example — $E|X|$ (toy)

$E|X| = |-4|\cdot\tfrac16 + |0|\cdot\tfrac36 + |7|\cdot\tfrac26 = \tfrac46 + 0 + \tfrac{14}{6} = \tfrac{18}{6} = 3.$

### Example — $E[X^2]$ for a die ($g(x) = x^2$)

$E[X^2] = \sum x^2\cdot\tfrac16 = \tfrac{1+4+9+16+25+36}{6} = \tfrac{91}{6} \approx 15.17.$ Note $E[X^2] = \tfrac{91}{6} \ne (E X)^2 = 3.5^2 = 12.25$.

### Linearity of expectation

> [!success] Linearity
> For constants $c_1,\dots,c_n$ and functions $g_1,\dots,g_n$:
> $$E\!\left[\sum_{i=1}^n c_i\, g_i(X)\right] = \sum_{i=1}^n c_i\, E[g_i(X)].$$
> **Linear-function corollary:** $E(aX + b) = a\,E(X) + b$.

> [!danger] Nonlinear $g$: $E[g(X)] \ne g(E[X])$
> Linearity only passes **constants** through. For nonlinear $g$, in general $g(E[X]) \ne E[g(X)]$ — e.g. for the die $E[X^2] = \tfrac{91}{6} \approx 15.17$ but $(E X)^2 = 12.25$.

> [!abstract] Summary — §3.3
> The **expectation** $\mu = E(X) = \sum x\,f(x)$ is the probability-weighted average (balancing point; the long-run average by the law of large numbers), need not lie in the range, and can even be infinite. For a function, **$E[g(X)] = \sum g(x)\,f(x)$** (no need for $g(X)$'s pmf). Expectation is **linear**: $E[\sum c_i g_i(X)] = \sum c_i E[g_i(X)]$ and $E(aX+b) = aE(X)+b$ — but $g(E[X]) \ne E[g(X)]$ for nonlinear $g$.

---

# 📖 Glossary (by chapter)

Key terms, grouped by chapter. Symbols: $S$ sample space · $\bar A$ complement · $\cup$ union · $\cap$ intersection · $\varnothing$ empty set · $P(A\mid B)$ conditional probability.

## Glossary — Chapter 1 (Introduction & Counting)

- **Classical probability** — (# favourable outcomes)/(total outcomes); needs equally likely outcomes.
- **Relative frequency** — limiting proportion over many repetitions.
- **Subjective probability** — a personal degree of belief.
- **Sample space $S$** — set of all distinct outcomes; exactly one occurs per trial. **Discrete** if finite or countably infinite.
- **Event** — a subset of $S$. **Simple** = one outcome; **compound** = two or more.
- **Mutually exclusive / exhaustive** — no overlap / covers everything (both needed for a valid $S$).
- **Union / intersection / complement / empty set** — $\cup$ / $\cap$ (=$AB$) / $\bar A$ / $\varnothing$.
- **Probability axioms** — scale $0\le P(A)\le1$, $P(S)=1$, additivity for disjoint events.
- **Probability distribution** — $P(a_i)$ with $0\le P(a_i)\le1$ and $\sum P(a_i)=1$.
- **Uniform / equally likely model** — every outcome same probability; $P(A)=|A|/|S|$.
- **Addition rule (OR)** / **Multiplication rule (AND)** — $p+q$ / $p\times q$.
- **Ordered $k$-tuple** — $|A| = n_1 n_2\cdots n_k$.
- **Permutation** (ordered) — $n^{(k)} = n!/(n-k)!$; note $n^{(k)}\ne n^k$.
- **Combination / binomial coefficient** (unordered) — $\binom{n}{k} = n!/(k!(n-k)!)$; symmetry $\binom{n}{k}=\binom{n}{n-k}$.
- **Multinomial coefficient** — $\binom{n}{n_1\cdots n_k} = n!/(n_1!\cdots n_k!)$ (arrangements of repeated items).
- **With / without replacement** — permutations: $n^k$ / $n^{(k)}$.
- **Complement trick** — for "at least" problems, compute $1 - P(\text{none})$.
- **Odds** — in favour $P(A)/P(\bar A)$; against $P(\bar A)/P(A)$.

## Glossary — Chapter 2 (Rules & Conditional Probability)

- **Normalization / Boundedness / Monotonicity** — $P(S)=1$ / $0\le P(A)\le1$ / $A\subseteq B\Rightarrow P(A)\le P(B)$.
- **De Morgan's laws** — $\overline{A\cup B}=\bar A\cap\bar B$; $\overline{A\cap B}=\bar A\cup\bar B$.
- **Rule 4a** — $P(A\cup B)=P(A)+P(B)-P(A\cap B)$. **Rule 4b** — three-event version.
- **Inclusion–Exclusion** — add singles, subtract pairs, add triples, … ($(-1)^{n-1}$ on the $n$-way term).
- **Mutually exclusive** — $A\cap B=\varnothing$. **Rule 5** — disjoint union probabilities just add.
- **Rule 6 — complement** — $P(\bar A)=1-P(A)$. Also $P(A\cap B)=P(A)-P(A\cap\bar B)$.
- **Independent events** — $P(A\cap B)=P(A)P(B)$; equivalently $P(A\mid B)=P(A)$.
- **Mutually independent** — every subcollection factors (stronger than pairwise).
- **Mutually exclusive ≠ independent** — disjoint positive-probability events are dependent.
- **Conditional probability** — $P(A\mid B)=P(A\cap B)/P(B)$; treat $B$ as the new sample space.
- **Product rule** — $P(A\cap B)=P(A\mid B)P(B)=P(B\mid A)P(A)$ (great for sequential draws).
- **Partition** — sets covering $S$ with no overlap (simplest: $B,\bar B$).
- **Law of Total Probability** — $P(A)=\sum_i P(A\mid B_i)P(B_i)$ (a weighted average over the partition).
- **Bayes' Rule** — $P(B_i\mid A)=P(A\mid B_i)P(B_i)/\sum_j P(A\mid B_j)P(B_j)$; flips the conditioning.
- **Tree diagram** — split on what you're told first; branch probs are conditional; leaves sum to 1.
- **Diagnostic terms** — prevalence $P(D)$; sensitivity $P(+\mid D)$; specificity $P(-\mid\bar D)$; PPV $P(D\mid+)$; NPV $P(\bar D\mid-)$.

## Glossary — Chapter 3 (Discrete Distributions)

- **Random variable (RV) $X$** — a function $X:S\to\mathbb{R}$ mapping outcomes to numbers.
- **Range $X(S)$** — the set of values $X$ can take. **Realization $x$** — a specific value (lowercase).
- **Discrete RV** — range finite or countably infinite. **Continuous RV** — range an interval.
- **Mixed / singular RV** — mix of point mass + continuous part (out of scope).
- **Probability mass function (pmf) $f_X(x)$** — $P(X=x)$; nonnegative and sums to 1 over the range (its preimages are disjoint and cover $S$); 0 off the range.
- **Probability distribution** — the set of pairs $\{(x, f_X(x))\}$.
- **Geometric distribution** — "wait for the first success": $f(x) = (5/6)^{x-1}(1/6)$ for $x=1,2,\dots$ (here, tosses until the first six); decays geometrically.
- **Finding/validating a constant** — set $\sum f(x)=1$ to solve for it, **then** reject any root breaking $0\le f(x)\le1$.
- **Cumulative distribution function (CDF) $F_X(x)$** — $P(X\le x)=\sum_{u\le x}f(u)$, defined for all real $x$; a non-decreasing, right-continuous **step function** from 0 to 1 ($0\le F\le1$; limits 0 at $-\infty$, 1 at $+\infty$). Lowercase $f$ = pmf, uppercase $F$ = CDF.
- **pmf ⇄ CDF recovery** — jump sizes give the pmf: $f(a_1)=F(a_1)$, $f(a_i)=F(a_i)-F(a_{i-1})$; interval $P(a<X\le b)=F(b)-F(a)$ (strict left, inclusive right).
- **Floor function $\lfloor x\rfloor$** — largest integer $\le x$; used to define a discrete CDF over all reals (e.g. geometric $F(x)=1-(5/6)^{\lfloor x\rfloor}$).
- **min/max of independent trials** — go CDF-first via complement: $\min>x\iff$ all $>x$ (e.g. min of 5 dice: $F(x)=1-((6-x)/6)^5$), then difference to the pmf.
- **Same distribution ($X\sim Y$)** — equal CDFs $F_X=F_Y$; the distribution $P_X(B)=P(X\in B)$ is described entirely by the CDF.
- **Function of an RV $Y=g(X)$** — pmf $f_Y(y)=\sum_{x:\,g(x)=y}f_X(x)$; if $g$ injective, $f_Y(y)=f_X(g^{-1}(y))$; linear $Y=aX+b$ → $f_X((y-b)/a)$.
- **Expectation / mean / first moment** — $\mu=E(X)=\sum x f(x)$; probability-weighted average / balancing point / long-run average (LLN); need not be in the range; may be $+\infty$.
- **LOTUS** — $E[g(X)]=\sum g(x)f(x)$ (no need for $g(X)$'s pmf).
- **Linearity of expectation** — $E[\sum c_i g_i(X)]=\sum c_i E[g_i(X)]$; $E(aX+b)=aE(X)+b$; but $g(E[X])\ne E[g(X)]$ for nonlinear $g$.
- **Key series** — geometric $\sum r^i = 1/(1-r)$, partial geometric $\sum_{i=0}^{n} r^i = (1-r^{n+1})/(1-r)$, binomial theorem, Taylor series for $e^x$ (used to sum pmf's / find means).

---

> [!note] Origin
> Consolidated from four per-chapter files (Home + Chapters 1–3) into this single document (Oct 2026). Previously migrated from the STAT 230 Notion page (which hit its free-block limit). **Source materials:** slide decks `STAT_230_003_weekNN_...pdf` and lecture transcripts (first 2–3 topics slide-only; later ones integrate transcripts).
