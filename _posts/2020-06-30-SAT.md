---
layout: distill
title: "The Propositional Satisfiability problem : Theoretical context"
description: An introduction to theoretical computer science and the SAT problem 
tags:
giscus_comments: true
date: 2020-06-30
featured: true

authors:
  - name: Nazim Kerkech


bibliography: 2020-06-30-SAT.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: Introduction
  - name: Theoretical Context
  - name: Propositional Calculus
  - name: Language
  - name: Model Theory
  - name: Proof Theory
  - name: Fundamental Properties
  - name: Results in Logic
  - name: Turing Machines
  - name: Complexity Theory
  - name: Polynomial Reduction
  - name: Citations
  - name: Footnotes


# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }
---

# Introduction
Theoretical computer science is, with mathematics, a subject that holds the key to addressing some of the problems that transcend our existence<d-footnote>This phrasing is to be interpreted either in the weak sense, as mathematics being a tool that perfectly describes the universe and every phenomenon from the scale of the atom to human populations, or in the strongest sense of "transcendence" if we consider <a href="https://physics.mit.edu/faculty/max-tegmark/">Max Tegmark</a>'s proposition of the "<a href="https://arxiv.org/abs/0704.0646">Mathematical Universe</a>" </d-footnote>. $\mathsf{P}$ VS $\mathsf{NP}$ is one of such problems and SAT could be a starting point to answer this problem. 

Given a formula in Propositional Calculus (or a set of clauses), SAT consists of determining whether there exists a valuation of the atoms composing it such as that formula is True. This problem is fundamental due to its simplicity and context. Propositional calculus provides us with a set of concepts allowing us to fully grasp SAT. 

SAT is $\mathsf{NP}$. This means that a non-deterministic Turing machine can solve it in polynomial time. This implies that a deterministic Turing machine can solve it in exponential time (theorem); and thus that a modern computer can solve it (deterministically -by setting heuristics even arbitrary-) in exponential time. Therein lies the problem: $\mathsf{NP}$ problems present an asymmetry between instances for which the answer is "yes" and those for which the answer is "no". For a set of satisfiable clauses (a satisfiable formula), the algorithm will stop at the first branch that would have lead to the satisfaction of the set (and in the best possible case -that is, if the algorithm has never "made a mistake" and has only traversed this path- its complexity will be polynomial<d-footnote>This is purely illustrative ; the 'probability' for a naive algorithm to do this tends exponentially to zero, and even if the heuristic eliminates an exponential number of branches, the algorithme would still have to traverse an exponential number of branches</d-footnote>). But for a set of inconsistent clauses (a non satisfiable formula), the algorithm will have to traverse all paths and thus exhaustively check all possible valuations of the formula's variables ($2^n$) regardless of the heuristics it uses. 

Moreover SAT is $\mathsf{NP}$-Hard which means that every $\mathsf{NP}$ problem can be reduced to it. Therefor, solving SAT efficiently would be equivalent to doing so for every $\mathsf{NP}$ problem (given that the reduction algorithhm is efficient).

We will begin by an introduction to Propositional calculus and formal systems setting the stage for a more formal definition of SAT.

---

# Theoretical Context

## Propositional Calculus
Propositional calculus <d-cite key="alliot2002intelligence"></d-cite> is a formal system describing logic, the study of which consists of two distinct theories: a model theory and a proof theory, respectively studying the semantic and syntactic aspects of the language. Each has its own concepts and methods, which, although, as we will see later, equivalent, should not be confused. 

Understanding propositional calculus amounts to understanding the difficulty and giving ourselves the theoretical means for solving SAT.

This section will be divided into four parts: In the first, we will see the specificities of the language; in the second and third respectively, the model theory and the proof theory; and finally, in the fourth, we will see the fundamental properties of the system.

### Language

The formal language of propositional calculus is a formalization of logic. It considers propositions (or propositional variables) as well as the relationships that exist between them (conjunction, disjunction, implication, equivalence) and the negation of these. The set of propositional variables is denoted as $V_p$. We are going to define each element of the language.

#### Proposition
- A proposition (or a propositional variable) is a closed statement, meaning it does not depend on any external parameter.

#### Atom
- An atom can be a propositional variable, a tautology $\top$, or a contradiction $\bot$. <d-cite key="alliot2002intelligence"></d-cite>

#### Formula
- A formula is defined as follows<d-cite key="alliot2002intelligence"></d-cite>:
  - An atom is a formula;
  - If A and B are formulas, then A$\rightarrow$B, A$\leftrightarrow$B, A$\land$B, A$\lor$B are formulas;
  - If A is a formula, then $\neg$A is a formula. 

### Model Theory

Model theory considers the truth value, i.e., the truth or falsehood of a formula in propositional calculus. It assumes the principle of excluded middle ($\forall A \;\; \;  \models A \lor \neg A$) and the postulate that the truth value of a formula is entirely determined by the truth value of each of its atoms. We will define the concepts that make up model theory.

#### Valuation
- A valuation of a set of propositional variables $V \in V_p$ is a function $v : V \to \{t, f\}$ satisfying the conditions:
  - $v(\neg X) = \neg v(X)$
  - $v(X \land Y) = v(X) \land v(Y)$
  - $v(X \lor Y) =v(X) \lor v(Y)$
  - $v(X \to Y) = v(X) \to v(Y)$

#### Interpretation
- In propositional calculus, an interpretation<d-footnote>Not to be confused with the notion of interpretation for predicate calculus</d-footnote> of a formula $F$ is a valuation of all propositional variables appearing in $F$.

#### Satisfaction
- An interpretation $I$ satisfies a formula $F$ if and only if it leads to a "true" value for $F$ in the truth table (by following the results of the definition table of each connector). We write $I \models F$.

#### Valid Formula
- $F$ is a tautology or valid formula if and only if $F$ is satisfied by all possible interpretations. We write $\models F$.
- If a formula $B$ is true for all interpretations satisfying $A$, then $B$ is a valid consequence of $A$ and we write $A \models B$. If $B \models A$, then $\models (A \to B)$.

### Proof Theory

Proof theory considers the provability of a formula: It admits a number of axioms as well as inference rules. A formula is provable (or is a theorem) if it is possible to derive it from the theorems and inference rules.

#### Theorem
- A formula $A$ is a theorem, denoted $\vdash A$, if $A$ is an axiom or if $A$ is obtained from the application of an inference rule on other theorems. The axioms admitted by propositional calculus are:
  - $A\rightarrow(B\rightarrow A)$
  - $(A\rightarrow(B\rightarrow C))\rightarrow ((A\rightarrow B)\rightarrow(A\rightarrow C))$
  - $(\neg B\rightarrow \neg A)\rightarrow (A\rightarrow B)$
- The axioms are tautologies.

Inference rules are:

- **Modus Ponens**  
<div style="margin-left: 50%">p</div>  
<div style="margin-left: 50%">p → q</div>  
<div style="margin-left: 50%">_________</div>  
<div style="margin-left: 50%">∴ q</div>

- **Modus Tollens**
<div style="margin-left: 50%">¬q</div>  
<div style="margin-left: 50%">p → q</div>  
<div style="margin-left: 50%">_________</div>  
<div style="margin-left: 50%">∴ ¬p</div>

- **Disjunctive Syllogism**
<div style="margin-left: 50%">¬q</div>  
<div style="margin-left: 50%">p ∨ q</div>  
<div style="margin-left: 50%">_________</div>  
<div style="margin-left: 50%">∴ p</div>

- **Hypothetical Syllogism**
<div style="margin-left: 50%">p → q</div>  
<div style="margin-left: 50%">q → r</div>  
<div style="margin-left: 50%">_________</div>  
<div style="margin-left: 50%">∴ p → r</div>


#### Proof
- A proof of a theorem $A$ is a finite sequence ($A_{1}$, $A_{2}$, ..., $A_{i}$, ..., $A$) where each $A_{i}$ is either an axiom or obtained by applying an inference rule to previous theorems $A_{j}$ and $A_{k}$. We write $\vdash A$.

- If we admit that there may be $A_{i}$ that are not theorems, we say that $A$ is deduced from the set of $A_{i}$ that are not theorems, and it is denoted $A_{m}$, ..., $A_{n} \vdash A$. 

#### Deduction Theorem
- If $A \vdash B$, then $\vdash (A \to B)$

#### Inconsistent Formula
- A formula is inconsistent if and only if it is possible to deduce a contradiction $\bot$. A formula is consistent otherwise.

### Fundamental Properties

**Definition: Adequacy**
- A logic is said to be adequate if every theorem is a tautology. Propositional calculus is adequate.

**Definition: Completeness**
- A logic is said to be complete if every tautology is a theorem. Propositional calculus is complete. Furthermore, if for every formula $F$ being a valid consequence of another formula $A$, then $F$ can be deduced from $A$. In this case, propositional calculus is said to be strongly complete.

**Definition: Consistency**
- A logic is said to be consistent if there is no formula $A$ such that both $\vdash A$ and $\vdash \neg A$. Propositional calculus is consistent.

**Definition: Syntactic Completeness**
- A logic is said to be syntactically complete if and only if, for every formula $A$, either $\vdash A$ or $\vdash \neg A$. Propositional calculus is syntactically complete.

**Definition: Decidability**
- A logic is said to be decidable when a mechanical procedure exists that can establish, in a finite time, whether a given formula is a theorem or not. Propositional calculus is decidable.

## Results in Logic
A set $E$ of formulas is unsatisfiable or semantically inconsistent if and only if there exists no interpretation $M$ such that every formula $A$ in $E$ is satisfied by $M$. An antilogy logically implies any formula and in particular $\emptyset$.

**Deduction Principle Theorem**

A formula $A$ is a valid consequence of a set $E$ of formulas if and only if $E \cup \{\neg A\}$ is unsatisfiable.

The deduction principle reduces prooving a deduction to prooving the inconsistency of a set of formulas.

**Resolution**

Given two clauses $c_{i} = (x \lor \alpha_{i} )$ and  $c_{j} = ((\neg x) \lor \alpha_{j} )$ containing respectively $x$ and $\neg x$, a new clause $r = \alpha_{i} \lor \alpha_{j}$ can be obtained by removing all occurrences of the literal $x$ and $\neg x$ in $c_{i}$ and $c_{j}$. This operation, denoted as $\eta[x, c_{i} , c_{j} ]$, is called resolution and the resulting clause $r$ is called resolvent. <d-cite key="Konlac2014"></d-cite>

**Subsumption**

A clause $c_{i}$ subsumes another clause $c_{j}$ if and only if $c_{i} \subseteq  c_{j}$. In this case, $c_{i} \models  c_{j}$. <d-cite key="Konlac2014"></d-cite>

**Auto-subsumption**

A clause $c_{i}$ auto-subsumes another clause $c_{j}$ if and only if the resolvent of $c_{i}$ and $c_{j}$ subsumes $c_{j}$. <d-cite key="Konlac2014"></d-cite>

**Fusion**

Let $c_{i} = \{x_{1} ,\ x_{2} ,\ ...,\ x_{n} ,\ l,\ y_{1} ,\ y_{2} ,\ ...,\ y_{m} ,\ l,\ z_{1} ,\ z_{2} ,\ ...,\ z_{k} ,\ l \}$ be a clause in a formula $F$, $c_{i}$ can be replaced by $c_{i} = \{x_{1} ,\ x_{2} ,\ ...,\ x_{n} ,\ y_{1} ,\ y_{2} ,\ ...,\ y_{m} ,\ z_{1} ,\ z_{2} ,\ ...,\ z_{k} ,\ l \}$. This operation is called fusion. <d-cite key="Konlac2014"></d-cite>

---

# Turing Machines

The Turing machine is an abstract concept formalizing that of an algorithm. A Turing machine operates as follows: a head being at each instant in a particular state $z_{i}$ among $Z = \{z_{0},\ ...,\ z_{n} \}$ moves on a tape divided into an infinite number of cells, all of which are empty initially except for a finite number of them that can contain a symbol from the set $\Gamma = \{s_{0},\ ...,\ s_{n}\}$ according to a function $$\delta : ((Z-Z_{h})\times \Gamma) \mapsto (Z\times \Gamma \times \{D,\ L,\ S\})$$ $Z_{h}$ being the set of halting states of the Turing machine.

---

# Complexity Theory

The temporal complexity $T$ of a problem is a measure associated with the Turing machine that can optimally solve it. It is a function of the length $n$ of the input to the Turing machine. For deterministic Turing machines, it represents the number of operations the Turing machine performs for the input of length $n$ that results in the longest calculation. It is the measure of the evolution of the number of operations to be performed depending on the length of the input:

$$
T_{\mathcal{M}}(n)= \underset{x\in{\Sigma}^*,\ \mid x \mid =n}{max} \{k\ \mid k\ is\ the\ length\ of\ the\ computation\ for\ input\ x\} 
$$

The same goes for non-deterministic Turing machines, except here, we consider that we follow the shortest path leading to an acceptance in the resolution tree of the Turing machine:

$$
T_{\mathcal{M}}(n)= \underset{x \in{\Sigma}^*,\ \mid x \mid =n}{max} \{min \{k\ \mid \ k\ is\ the\ length\ of\ an\ accepting\ computation\ for\ input\ x\}\}
$$

We say that a problem is polynomial or that a language $L$ is recognized in polynomial time<d-footnote>Any decision problem can be reduced to the problem of recognizing a word in a (formal) language (this problem is undecidable in the general case for recursively enumerable languages (see the Chomsky hierarchy)), and the Turing machine that solves this problem is the Turing machine that generates this language.</d-footnote> if and only if there exists a deterministic Turing machine $\mathcal{M}$ generating this language and there exists a polynomial function $P$ such that for every $x$ in $L$, $T_{\mathcal{M}}(\|x\|) < P(\|x\|)$. We thus define the set of polynomial problems $\mathsf{P}$:

$$
\mathsf{P}= \{L \mid L\subset{\Sigma}^*,\ \exists \mathcal{M},\ L=L(\mathcal{M}),\ \exists P,\ \forall x\in L,\ T_{\mathcal{M}}(\mid x \mid) < P(\mid x \mid)\}
$$

$$
\mathcal{M}\ being\ a\ deterministic\ Turing\ machine.
$$

$$
L\ being\ the\ language\ generated\ by\ \mathcal{M}\ (we\ write\ L=L(\mathcal{M}))
$$

Similarly, we define the set of non-deterministic polynomial problems $\mathsf{NP}$, being the set of problems that can be solved in polynomial time by a non-deterministic Turing machine:

$$
\mathsf{NP}= \{L \mid L\subset{\Sigma}^*,\ \exists \mathcal{M},\ L=L(\mathcal{M}),\ \exists P,\ \forall x\in L,\ T_{\mathcal{M}}(\mid x \mid) < P(\mid x \mid)\}
$$

$$
\mathcal{M}\ being\ a\ non-deterministic\ Turing\ machine.
$$

$$
L\ being\ the\ language\ generated\ by\ \mathcal{M}\ (we\ write\ L=L(\mathcal{M}))
$$

An interesting property of $\mathsf{NP}$ problems is that the problem of verifying the solution to an $\mathsf{NP}$ problem is itself polynomial. The algorithm that verifies a solution only consists of visiting the execution branch of the non-deterministic Turing Machine described by the solution.

We also define the sets co-$\mathsf{P}$, the set of complements<d-footnote>A problem complementary to another problem being defined by the opposite question of that defining the latter.</d-footnote> of problems in $\mathsf{P}$, and co-$\mathsf{NP}$, also formed by the set of complements of problems in $\mathsf{NP}$. By definition, for problems in co-$\mathsf{NP}$, it is the verification of what is not a solution to the problem that can be done in polynomial time. We have $\mathsf{P}$=co-$\mathsf{P}$, we can prove it only by interchanging the two states $z_y$ and $z_n$ in the Turing machine that solves the corresponding $\mathsf{P}$ problem for each problem in co-$\mathsf{P}$. 

As for co-$\mathsf{NP}$ we know that there is an asymmetry between instances whose answer is "yes" and those whose answer is "no" for the same problem. Take, for example, the satisfiability problem; there is a polynomial-length calculation that can determine that the starting assumption is true ("yes") (and it is the shortest path to a halt state), whereas for instances where the answer is "no", we must go through all the paths of the Turing machine's execution tree, and thus the complexity is exponential even in the best case. 

We don't known whether $\mathsf{NP}$=co-$\mathsf{NP}$. The problem $\mathsf{P}$=$\mathsf{NP}$ is also still open. In fact, the conjecture $\mathsf{NP}$ $\neq$ co-$\mathsf{NP}$ is even stronger than the conjecture $\mathsf{NP}$$\neq $ $\mathsf{P}$, because if $\mathsf{P}$=$\mathsf{NP}$, then $\mathsf{P}$=co-$\mathsf{P}$=co-$\mathsf{NP}$=$\mathsf{NP}$ (indeed, if $\mathsf{P}$=$\mathsf{NP}$, then $\mathsf{P}$=co-$\mathsf{P}$=co-$\mathsf{NP}$=$\mathsf{NP}$)<d-cite key="alliot2002intelligence"></d-cite>.

---

# Polynomial Reduction

We say that a language $L_{1} \subset \Sigma_{1}^{\*} $ is polynomially reducible to another language $L_{2} \subset \Sigma_{2}^{\*}$ if and only if there exists a function $f : \Sigma_{1}^{\*}\to \Sigma_{2}^{\*}$ such that $f$ can be computed in polynomial time by a deterministic Turing machine and for every $x\in \Sigma_{1}^{\*}$, $x\in L_{1}$ if and only if $f(x)\in L_{2}$. We then denote $L_1 \propto_p L_2$. $\propto_p$ is a preorder (transitive and reflexive relation). A language $L$ is said to be $\mathsf{NP}$-complete if and only if $L$ is in $\mathsf{NP}$ and $\forall L\'\in \mathsf{NP}$, $L\' \propto_p L$. From this definition, we know that if we prove for only one $\mathsf{NP}$-complete language $L_0$ that $L_0\in \mathsf{N}$, then we'd have proven $\mathsf{P}=\mathsf{NP}$.

To illustrate a polynomial reduction from an $\mathsf{NP}$ problem to SAT, we choose to briefly describe the encoding of a CSP into SAT according to <d-cite key="DBLP:conf/cp/Walsh00"></d-cite>.

A Constraint Satisfaction Problem (CSP) consists of a set of variables $X_{i}$ each taking its value in a domain $D_{i}$ as well as a set of constraints between the values that each variable can take.

The direct encoding associates a clause $C_{i}$ with each variable $X_{i}$ of the CSP and a propositional variable $x_{i,\ j}$ with each value $j$ in the domain $D_{i}$ of $X_{i}$. The clauses $C_{i}$ ensure that each variable $X_{i}$ has at least one assigned value. We represent the constraints between the variable values with binary clauses so, for example, if $X_{1} = 2$ and $X_{3} = 1$ is not allowed, then we add the clause $( \neg x_{1,\ 2} \lor \neg x_{3,\ 1} )$.

