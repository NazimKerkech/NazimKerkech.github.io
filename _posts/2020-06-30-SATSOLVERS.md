---
layout: distill
title: "SAT Solvers"
description: 
tags:
giscus_comments: true
date: 2020-06-30
featured: true

authors:
  - name: Nazim Kerkech

use_math: true

bibliography: 2020-06-30-SAT.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: Introduction
  
header-includes:
  - \usepackage{algorithm2e}

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
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.7/katex.min.js"
        integrity="sha512-EKW5YvKU3hpyyOcN6jQnAxO/L8gts+YdYV6Yymtl8pk9YlYFtqJgihORuRoBXK8/cOIlappdU6Ms8KdK6yBCgA=="
        crossorigin="anonymous" referrerpolicy="no-referrer">
</script>

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/pseudocode@2.4.1/build/pseudocode.min.css">
<script src="https://cdn.jsdelivr.net/npm/pseudocode@2.4.1/build/pseudocode.min.js">
</script>

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/pseudocode@latest/build/pseudocode.min.css">
<script src="https://cdn.jsdelivr.net/npm/pseudocode@latest/build/pseudocode.min.js">
</script>



# Introduction 
We discussed in a previous post about SAT and its theoretical context. SAT is a decidable problem in logic and theoretical computer science and it is $\mathsf{NP}$-complete. This means that for every satisfiable instance of the problem (i.e. for which the answer is "yes"), one of the branches of the nondeterministic algorithm solving SAT would lead to a satisfaction of the instance in a polynomial time. Some manipulations like resolution, subsumption, or merging could simplify the instance and sometimes help us conclude the inconsistency of a branch before reaching the leaves. It is then evident that the key to efficiently solve SAT is to know at least one of these branches that leads to satisfaction beforehand. This boils down to "judiciously" traversing the execution tree of the nondeterministic algorithm and thus choosing the propositions to assign whenever the choice presents itself (for every node of the tree).

SAT's difficulty is only practical and not theoretical.Theory just doesn't allow us to know a priori this polynomial complexity branch or even whether it exists (for inconsistent instances). It is because of this theoretical dead-end that we implement in practice an empirical approach to the problem to effectively solve SAT through heuristics.

SAT solvers fall into two categories: incomplete approaches, such as local search or evolutionary genetic algorithms, which do not guarantee finding a solution and consist of, for local search, judiciously navigating the valuation space until finding one that satisfies the formula within a predefined time. Failing that, it cannot conclude. And, for genetic algorithms, generating random valuations and selecting those that appear closest to a formula-satisfying valuation, then applying minimal changes and continuing until finding one; otherwise, if the allotted time is exceeded, it will be impossible to conclude. And the complete approaches. These are based on the principles of enumeration and resolution. We will discuss some of these algorithms in this chapter.

---

# Enumeration
## Semantic Tree
The Semantic Tree is a naive method that constructs a binary tree where each edge represents a valuation of a variable and exhaustively traverses each branch using depth-first search and evaluates the formula once it reaches a leaf until finding one that satisfies the instance.

## Quine Algorithm
The Quine algorithm is a refinement of the semantic trees algorithm. The difference is that it allows to conclude the inconsistency of a branch (and thus of all its leaves) upstream by performing partial evaluations at each node.

<pre id="quine" class="pseudocode">
    % This quicksort algorithm is extracted from Chapter 7, Introduction to Algorithms (3rd edition)
    \begin{algorithm}
    \caption{Quine}
    \begin{algorithmic}
      \INPUT $\mathcal{F}$
      \IF{ $\mathcal{F} = \emptyset \ $ } 
        \RETURN SAT
      \ELIF{$\perp \in \mathcal{F}$}
        \Return INSAT
      \ENDIF
      \STATE $l \ \gets \ BranchingHeuristics (\mathcal{F})$
      \RETURN (QUINE($\mathcal{F}_{|l} $) OR QUINE($\mathcal{F}_{|\neg l} $))
    \end{algorithmic}
    \end{algorithm}
</pre>
<script>
    pseudocode.renderElement(document.getElementById("quine"));
</script>

---
# Resolution
## Resolution Principle

The resolution rule or Robinson's resolution principle consists of iteratively applying resolution derivations to the clauses of a formula. A resolution derivation is a sequence $\Pi [ c_{1},\ ...,\ c_{n} = c]$ such that for every $i \in \{ 1,\ ...,\ k \} $ either $c_{i} \in \mathcal{F}$ or $c_{k}=\eta [x, c_{i}, c_{j}] $ with $i, j \in \{ 1,\ ...,\ k \} $. 

Resolution, subsumption, and merging rules establishes a complete automatic demonstration method. Resolution alone is complete for refutation (i.e. inconsistant formulas)<d-cite key="Konlac2014"></d-cite>. The problem in implementing this kind of procedure is that there is no method to determine which resolution to perform and when. We might end up generating an exponential number of clauses. To address this, a number of orientations and restrictions for building a resolution derivation are proposed, among them the Davis-Putnam algorithm.

## Davis-Putnam Algorithm

The Davis-Putnam algorithm <d-cite key="DBLP:journals/jacm/DavisP60"></d-cite> consists of choosing at each iteration a propositional variable to eliminate from the formula and exhaustively applying resolution between the subsets $\mathcal{F}_{\|x}$ and $ \mathcal{F} _{ \| \neg x} $ of clauses of $ \mathcal{F} $ containing respectively the propositional variable $x$ and its negation. Then, removing them from the formula and replacing them with the set of clauses $\eta [x,\ \mathcal{F} _{ \| x},\ \mathcal{F} _{ \| \neg x} ] $ obtained by applying resolution between each clause of $ \mathcal{F} _{ \| x} $ with each clause of $ \mathcal{F} _{ \| \neg x} $, and simplifying the obtained clause.

The complexity of the algorithm is $O(2^{n})$ where $n$ is the number of variables in the formula.

<pre id="DavisPutnam" class="pseudocode">
    \begin{algorithm}
    \caption{Davis-Putnam}
    \begin{algorithmic}
      \INPUT $\mathcal{F}$
      \WHILE{ $V_\mathcal{F} = \emptyset \ $ } 
        \STATE $V_\mathcal{F}$ being the set of all propositionnal variables of $\mathcal{F}$
        \STATE $x \ \gets \ BranchingHeuristics (\mathcal{F})$
        \STATE $\mathcal{V}_{\mathcal{F}}\ \gets \ \mathcal{V}_{\mathcal{F}} \setminus \{x\}$ 
	      \STATE $\mathcal{F} \ \gets \ \mathcal{F} \ \setminus \ (\mathcal{F}_{x} \ \cup \ \mathcal{F}_{\neg x})$ 
	      \STATE $\mathcal{F} \ \gets \ \mathcal{F} \ \cup \ \eta [x,\ \mathcal{F}_{x}, \  \mathcal{F}_{\neg x}]$       
        \IF{$\perp \in \mathcal{F}$}
          \Return INSAT
        \ENDIF
      \ENDWHILE
      \RETURN SAT
    \end{algorithmic}
    \end{algorithm}
</pre>
<script>
    pseudocode.renderElement(document.getElementById("DavisPutnam"));
</script>
---
# Efficient Solvers

## DPLL Algorithm
To understand the DPLL algorithm <d-cite key="DBLP:journals/cacm/DavisLL62"></d-cite>, we will introduce a set of concepts :

#### Unit Propagation
- If a formula $\mathcal{F}$ contains a unit atom $x$, i.e., an atom that appears in a clause containing only itself, then the formula $\mathcal{F}$ is equivalent to $\mathcal{F}_{|x}$ obtained by assigning $x$ to true, or in other words, by removing the clauses from $\mathcal{F}$ containing $x$ and removing $\neg x$ from clauses containing it as follows:
  $$
  \mathcal{F}_{|x} = \{c | c \in \mathcal{F}, \{x, \neg x \} \cap  c = \emptyset \} \cup \{c \backslash \{\neg x \} | c \in \mathcal{F}, \neg x \in c \}
  $$
- $\mathcal{F}^{*}$ is recursively defined as the formula obtained by applying unit propagation on $\mathcal{F}$ as follows:
  - $ \mathcal{F}^{*}= \mathcal{F} $ if $ \mathcal{F} $ doesn't contains any unit clause
  - $ \mathcal{F}^{*}= \perp $ if $ \mathcal{F} $ contains both unit clauses $x$ and $ \neg x $
  - $ \mathcal{F}^{*}= (\mathcal{F}_{\|x})^{\*}$ if $\mathcal{F}$ contains a unit clause containing the literal $x$.

#### Propagation Sequence
- A propagation sequence $\mathcal{P} = \langle x_{1},\ ...,\ x_{n} \rangle $ is a sequence of literals for a CNF $\mathcal{F}$ such that $\forall i \in [1,\ n] $, the unit clause $ x _{i} \in \mathcal{F} _{ \| x _{1},\ ...,\ x _{i-1}} $

#### Decision-Propagation Sequence
- A decision-propagation sequence $\mathcal{P} = \langle (x),\ x_{1},\ ...,\ x_{n} \rangle $ is a propagation sequence for a CNF $\mathcal{F} \land  (x)$.

#### Propagation Stack
- Considering a sequence of atoms $ [ x _{0} ,\ ...,\ x _{n} ] $ which we will call a decision sequence, a propagation stack $ \mathcal{H}= \{ \mathcal{S} _{0} ,\ ...,\ \mathcal{S} _{n} \} $ is a sequence of decision-propagation sequences $ \mathcal{S} _{i} $ such that $ \forall \ i \ \mathcal{S} _{i} \ is \ a \ DPS \ considering \  ( \mathcal{F} _{ \| x _{0} , \ ..., \ x _{i-1} }) ^{ \* } $

The DPLL algorithm consists of iteratively applying decision-propagation sequences on the instance we want to solve.

<pre id="DPLL" class="pseudocode">
    \begin{algorithm}
    \caption{Davis-Putnam}
    \begin{algorithmic}
      \INPUT $\mathcal{F}$
      \STATE $\mathcal{F} \ \gets \ PropagationUnitaire(\mathcal{F})$ \;
      \IF{ $\mathcal{F} = \emptyset \ $ }
        \Return SAT
      \IF{$\perp \in \mathcal{F}$}
          \Return INSAT
      \STATE $l \ \gets \ BranchingHeuristics (\mathcal{F})$
      \Return (DPLL($\mathcal{F} \land \{l\} $) OR DPLL($\mathcal{F} \land \{\neg l\} $))
    \end{algorithmic}
    \end{algorithm}
</pre>
<script>
    pseudocode.renderElement(document.getElementById("DPLL"));
</script>


\Retour (DPLL($\mathcal{F} \land \{l\} $) ou DPLL($\mathcal{F} \land \{\neg l\} $))
 \caption{Algorithme DPLL}
\end{algorithm}

---
## CDCL Type Algorithms

CDCL (Conflict Driven Clause Learning) type algorithms add on the DPLL algorithm clause learning techniques that are performed at each conflict leading to non-chronological backtracks. CDCL solvers can also implement, in addition to the basic functions of DPLL and clause learning, various components that optimize the algorithm: preprocessing, lazy data structures, heuristics, and restarts.

In this section, we will primarily focus on the components of the Chaff algorithm <d-cite key="DBLP:conf/dac/MoskewiczMZZM01"></d-cite>. The general architecture of CDCL solvers is as follows:


<pre id="CDCL" class="pseudocode">
    \begin{algorithm}
    \caption{CDCL}
    \begin{algorithmic}
    \INPUT $\mathcal{F}$

      \STATE $\Delta \ \gets \ \emptyset$ (Database of learned clauses)
      \STATE $I_{p} \ \gets \ \emptyset$ (Partial interpretation)
      \STATE $dl \ \gets \ 0$ (Decision level)

      \WHILE{True}
          \STATE $\alpha \ \gets \ \text{Unit Propagation}(\mathcal{F} \cup \Delta, \ I_{p} )$ ;
          \IF{$(\alpha = null)$}
              \STATE $x \ \gets \ \text{Branching Heuristic}(\mathcal{F} \cup \Delta, I_{p} )$ ;
              \IF{all variables are assigned}
                  \STATE return SAT 
              \ENDIF
              \STATE $l \ \gets \ \text{Choose Polarity}(x)$ 
              \STATE $dl \gets dl + 1 $ 
              \STATE $I_{p} \gets I_{p} \cup \{l^{dl} \}$ 
              \IF{Perform Reduction()}
                  \STATE $ReductionClausesApprises(\Delta)$ 
              \ENDIF
          \ELSE
              \IF{$dl = 0$}
                  \Return UNSAT
              \ENDIF
              \STATE $\beta, \ bl \ \gets \ \text{Conflict Analysis}(\mathcal{F} \cup \Delta, I_{p} , \alpha)$ ;
              \STATE $\Delta \ \gets \ \Delta \ \cup \beta$ ;
              \STATE $Backtrack(\mathcal{F} \cup \Delta, \ I_{p} , \ bl)$ ;
              \IF{Perform Restart()}
                  \STATE $Restart(I_{p},\ dl)$ ;
              \ENDIF
          \ENDIF
      \ENDWHILE
    \end{algorithmic}
    \end{algorithm}
</pre>
<script>
    pseudocode.renderElement(document.getElementById("CDCL"));
</script>

#### Lazy Data Structures

During unit propagation, the algorithm searches for the possible set of literals involved and conflicts generated by the valuation of the literal that is currently propagated. To achieve this, the naive method would be to exhaustively traverse all the clauses of the formula. However, Zhao, Zhang, and Malik propose in <d-cite key="DBLP:conf/dac/MoskewiczMZZM01"></d-cite> an optimization where a clause needs to be visited only when the number of falsified literals in this clause of size $N$ changes from $N-2$ to $N-1$ (making it a unit clause and thus more critical). They suggest choosing two unassigned literals for each clause. This ensures that until either of these two literals is falsified, the clause is neither unit nor falsified. 

#### Non-Chronological Backtrack

In cases where a conflict arises due to an assignment made several levels upstream, multiple backtracks may be needed to resolve the conflict. It is possible to precisely identify the last assignment causing the conflict by learning a new clause, which, once the non-chronological backtrack is performed, prevents making the same mistake again by propagating the variable that caused the conflict. To understand this, we define a set of concepts:

##### Conflict Reason: 
For a formula $\mathcal{F}$ in CNF and a partial interpretation $I_{p}$ obtained by propagation from $\mathcal{F}$, the conflict reasons of a literal $l \in I_{p}$ is the set of clauses (denoted $\overrightarrow{reason}(l)$) $C$ of $\mathcal{F}$ such that $l \in C$ and for any other $y$ in $C$: $\neg y \in I_{p}$ and $y$ precedes $l$ in the interpretation $I_{p}$.

If there exists $y \in C$ such that $\neg y \notin I_{p}$ (this means that $l$ is not a result of unit propagation), then $\overrightarrow{reason}(l) = \perp$.

We only need to consider a single reason clause for a propagated literal.


##### Explanation: 
For a formula $\mathcal{F}$ in CNF and a partial interpretation $I_{p}$ obtained by propagation from $\mathcal{F}$ using the decision sequence $\delta = [x_{1} ,\ x_{2} ,\ . . . ,\ x_{n}]$, and for a literal $l \in I_{p}$, the explanation of $l$ (denoted $exp(l)$) is $\emptyset$ if $l \in \delta$, or the set of $y \in I_{p}$ such that $\neg y \in I_{p}$ otherwise.

##### Implication Graph: 
For a formula $\mathcal{F}$ in CNF and a partial interpretation $I_{p}$ obtained by propagation from $\mathcal{F}$, the implication graph is a directed graph $ G = (N, A) $ where the set of nodes $N$ is the set of literals of $I_{p}$, and $(a,\ b)\ \in \ A $ if and only if $b\ \in \ exp(a)$.

##### Node Dominating a Level: 
Given $\mathcal{F}$ a formula, $I_{p}$ a partial interpretation, and $G = (N,\ A)$ the implication graph generated from $\mathcal{F}$ and $I_{p}$, a node $x \in N$ dominates a node $y \in N$ if and only if $lvl(x) = lvl(y)$ and $\forall z \in N$, with $lvl(z) = lvl(x)$, all paths covering $z$ and $y$ pass through $x$.

##### Unique Implication Point (UIP): 
Given $\mathcal{F}$ a formula, $I_{p}$ a conflicting partial interpretation, and $ G = (N,\ A)$ the implication graph generated from $\mathcal{F}$ based on $I_{p}$. The node $x$ is a unique implication point if and only if $x$ dominates the conflict.


The authors of <d-cite key="DBLP:conf/dac/MoskewiczMZZM01"></d-cite>  propose considering, during conflict analysis, only the closest UIP to the conflict, called F-UIP. It is the propagation of this unique implication point that led to the conflict. It is itself implied by the propagation of another literal at a previous level. The goal is to highlight this implicit implication by learning a clause from this conflict, and then backtrack to the level where the learned clause became unitary, i.e., the last level where another literal of the clause (other than the F-UIP we found) was propagated.

#### Proof by Conflict-Driven Resolution

Given a formula $\mathcal{F}$ and a conflicting partial interpretation $I_{p}$ obtained by unit propagation, a proof by conflict-driven resolution $R$ is a sequence of clauses $\langle \delta_{1} ,\ \delta_{2} ,\ ... ,\ \delta_{k} \rangle $ that satisfies the following conditions:

- $\delta_{1} = \eta [z, \overrightarrow{reason}(z), \overrightarrow{reason}(\neg z)]$, where $\{z,\ \neg z \}$ is a conflict;
- For every $i \in \{2,\ ...,\ k\}$, $\delta_{i}$ is constructed by selecting a literal $y \in \delta_{i-1}$ for which $\overrightarrow{reason}(\neg y)$ is defined. Thus, $y \in \delta_{i-1}$ and $\neg y \in \overrightarrow{reason}(\neg y)$, allowing two clauses to enter resolution. The clause $\delta_{i}$ is defined as:
  $$ \delta_{i} = \eta [y, \delta_{i-1}, \overrightarrow{reason}(\neg y)] $$
- $\delta_{k}$ is an assertive clause.

It's worth noting that each $\delta_{i}$ is a resolvent of formula $\mathcal{F}$: by induction, $\delta_{1}$ is the resolvent between two clauses of $\mathcal{F}$; for every $i > 1$, $\delta_{i}$ is a resolvent between $\delta_{i-1}$ (which, by the induction hypothesis, is a resolvent) and a clause of $\mathcal{F}$. Each $\delta_{i}$ is also implied by $\mathcal{F}$, i.e., $F \models \delta_{i}$.
