# NP-Complete Problems

## Problems and Intractability 
Not all computing problems are born equal. <span style="color:#f88146">Some problems are **more difficult** than others</span>. The difficulty of a problem is part of its description. Following, Garey & Johnson's *Computers and Intractability* to describe properly a problem we need: 
- A general description of all its <span style="color:#f88146">parameters</span>, and 
- A <span style="color:#f88146">statement </span> of what properties the answer, or solution, is required to satisfy. 

An <span style="color:#f88146">instance of a problem</span> is obtained by specifying particular values for all
the problem parameters.

**Graphs**. Since almost all combinatorial problems can be mapped to a graph $G=(V,E)$, the *parameters* of the problem are typically the *number of nodes* $n=|V|$ and the *statement* is the *desired property* satisfied by the nodes, paths, cycles and subgraphs of $G$ in the solution. <span style="color:#f88146">It is the fulfillment of this property what makes them **tractable** or **intractable**!</span> 

## The SAT problem 
**Satisfiability**. Consider the following problem that is not (apparently) is not related to graphs: *Given well-formed formula (wff) in first-order logic, determine whether it is satisfiable*.  
- We have a set of $n$ Boolean variables $X=\{x_1,x_2,\ldots,x_n\}$, where $x_i\in\{0,1\}\;\forall i$. 
- In the wff, we may have variables $x_i$ and their negations $\neg x_i$. 
- The wff is in Conjunctive Normal Form (CNF) i.e. a collection of disjunctions $\lor$ joined by $\land$ connectors. 

For instance, the wff 

$$
(x_1\lor x_2\lor \neg x_3)\land (\neg x_1\lor x_3\lor x_4)\land (x_2\lor x_3\lor \neg x_4)
$$

is in CNF: it has 3 *clauses* with three *literals* each. Then, determining if it is <span style="color:#f88146">satisfiable</span> means determining whether there is a Boolean assignment for the variables so that the wff is true (evaluated to $1$). 
- Since the wff is in CNF, **all** the clauses must be evaluated to $1$. 
- Inside each clause, **at least one** literal must be evaluated to $1$.

A **brute force** algorithm should evaluate all the $2^n$ possible Boolean assignments $\{0,1\}$ for the $n$ variables. Is there any way of solving the so called 
<span style="color:#f88146">SAT problem</span> in *polynomial time* (not exponential)? Unfortunately, only if each clause has less than $3$ literals! 

**SAT2**. In the SAT2 problem, all clauses have 2 literals. Consider, for instance, the wff 

$$
(x_1\lor x_5)\land 
(x_2\lor x_3)\land
(x_1\lor x_6)\land
(x_4\lor x_4)\land
(\neg x_6\lor \neg x_3)\land
(\neg x_2\lor x_3)\land 
(\neg x_4\lor x_2)\;.
$$

where all the clauses have two literals and $x_4\equiv(x_4\lor x_4)$ so that clauses with one literal end up having two of them. Then, 
- We map the logic variables in $X$ to $2n$ nodes in $V$: one node for the non-negated variable $x_i$ and another one for $\neg x_i$. 
- Exploiting here the equivalences $(\neg a\lor b)\equiv (a\Rightarrow b)\equiv (\neg b\Rightarrow \neg a)$, we have two edges per clause, except for the ones with $(x_i\lor x_i)$ where we have only one edge. 

This result in the digraph (directed graph) $G=(V,E)$ as the one in {numref}`SAT2`:

```{figure} ./images/Topic1/SAT2-Photoroom.png
---
name: SAT2
width: 600px
align: center
height: 500px
---
An instance of SAT2 for $n=5$ variables. 
```
Then, the SAT2 problem is **reformulated as follows** [(Aspvall, Plass and Tarjan, 1979)](https://www.sciencedirect.com/sdfe/pdf/download/eid/1-s2.0-0020019079900024/first-page-pdf): 

*Given a CNF-digraph $G=(V,E)$, the CNF encoded by it is satisfiable if and only if there is no strongly-connected component $C_k$ where $x_i\in C_k$ and  $\neg x_i\in C_k$*.

Firstly, two nodes $i,j\in V$ are <span style="color:#f88146">strongly connected</span> if there exists a directed path $\Gamma_{ij}$ connecting them. For instance, in {numref}`SAT2`, $i=x_2$ and $j=x_1$ are strongly connected since we have $\Gamma_{ij}=\{i=x_2\rightarrow x_3\rightarrow \neg x_6\rightarrow x_1=j\}
$. Actually all the nodes in dark orange belong to the same **strongly-connected component** $C_8=\{x_1,x_2,x_3, 
x_4,\neg x_6\}$. In short, we can visit all the nodes in $C_8$ starting from any of them. 

In addition, the connected component $C_8$ does not include simultaneously an $x_i$ and its negation $\neg x_i$. If this also happens in the remaining strongly-connected components, then the wff is satifiable. Actually this is the case since the remaining components of the above graph are 

$$
C_1=\{\neg x_1\}, C_2=\{\neg x_2\}, C_3=\{\neg x_3\}, C_4=\{\neg x_4\}, C_5=\{x_5\}, C_6=\{x_6\}, C_7=\{\neg x_5\}
$$

which all are *atomic* and there is no possibility of containing both a variable and its negation. Therefore, the above wff is satisfiable. Why? Basically because starting at any node is it impossible to reach a contradiction, i.e. to have $x_i$ and $\neg x_i$ in the same component. This is so simple that we do not need to make the two-directional proof (necessary and sufficient condition). 

**Is SAT2 polynomial?** Well, the question reduces now to determine whether the strongly connected components of a digraph can be found in polynomial time. Actually, each component can be found by launching paths and annotating they return, if it happens, or declaring an atomic component otherwise. This is the strategy of the [Tarjan's Algorithm](https://en.wikipedia.org/wiki/Tarjan%27s_strongly_connected_components_algorithm) which takes $O(|V| + |E|)$. 
Herein, we use the [Big-O notation](https://web.mit.edu/16.070/www/lecture/big_o.pdfwhere) 

$$
f(n):=O(g(n))\;\text{means}\;\exists c: |f(n)
|\le c \cdot |g(n)|\; \forall n\ge 0. 
$$

Therefore, **SAT2 is polynomial** and therefore it is **not intractable**. We express that, saying <span style="color:#f88146">$\text{SAT2}\not\in\text{NP}$</span>. 

## The Clique problem 
Although we could reduce SAT2 to a polynomial problem (find strongly-connected components), this is not the case for the *general Satisfiability problem* where clauses have *any* $n>2$ number of literals . [Cook and Levin](https://en.wikipedia.org/wiki/Cook%E2%80%93Levin_theorem) proved that in a theorem described in the second chapter of Garey & Johnson. The theorem is beautiful and it relies on Turing Machines to show that the CNF solving the SAT problem may have an exponential number of terms! However, this proof is out of the scope of this subject. 

Herein, however, we are interested in showing *why* SAT is intractable (we say <span style="color:#f88146">$\text{SAT}\in\text{NP}$</span>) by *reducing other problems to it*. The first of this prolems is the <span style="color:#f88146">Clique problem</span>. 

### From SAT3 to Clique 
The link between SAT and Clique is given by first transforming a SAT problem to a SAT3 problem where all clauses have $3$ literals. This is done as follows: 

Given a clause $(x_1\lor x_2\lor\ldots\lor x_m)$, we transform it into $m-2$ clauses with $3$ literals: 

$$
(x_1\lor x_2\lor \alpha_2)\land (\neg \alpha_2\lor x_3\lor \alpha_3)\land (\neg \alpha_3\lor x_4\lor \alpha_4)\land \ldots\land (\neg \alpha_{m-2}\lor x_{m-1}\lor x_m)\;,
$$

where $\alpha_2,\ldots,\alpha_{n-2}$ are *fresh variables* which do not modify the satisfiability of the original clause. Why? Note that if $\alpha_i$ appears in one sub-clause $i-1$, its negation $\neg\alpha_i$ appears in the sub-clause $i$. As a result this variable does not interfere in the satisfiability of the clause. 

Now, if we have originally $c$ clauses, each one with $n\ge m>3$ literals, then we have $O(m\cdot c)$ clauses which is linear in the number of terms. 

**Maximal cliques**. The purpose of the above transformation is to build a graph $G=(V,E)$ where we have $3\cdot c$ nodes ($3$ nodes per clause, because each clause has $3$ literals). The graph $G$ is non-directed and there is an edge $(i,j)\in E$ if: 
- $i$ and $j$ belong to different clauses: $c(i)\neq c(j)$. 
- $l_i$ and $l_j$, the respective literals, do not induce a contradiction: $l_i\neq \neg l_j$. 


For example, in {numref}`SAT2-clique` we show the nodes for the wff

$$
(x_1\lor x_2\lor \neg x_3)\land (\neg x_1\lor x_3\lor x_4)\land (x_2\lor x_3\lor \neg x_4)
$$

which does not need any transformations since all the clauses have $3$ literals yet. The 
nodes corresponding to the same clause are have the same color. Note that the literal $x_3$ leads to 
$2$ diferent nodes (orange and yellow clauses) and it appears negated in the red clause. Overall, <span style="color:#f88146">the wff is satisfiable if and only if there is a group of $c=3$ nodes mutually inter-connected</span>, such as $\neg x_3$, $x_4$ and $x_2$. Such a group of nodes forms a complete subgraph $K_c$ or <span style="color:#f88146">clique</span> whose *order* is $c$. 

Actually, all the cliques of order $c$ lead to a valid interpretation of the wff: $\neg x_3=1$, $x_4=1$ and $x_2=1$, i.e. $x_3=0$, $x_4=1$ and $x_2=1$, makes the above wff True. Can you find other cliques (groups of $c$ nodes completely inter-connected) in the graph? Of course they are. For instance: $\neg x_3$, $\neg x_1$ and $\neg x_4$ lead to $x_3=0$, $x_1=0$ and $x_4=0$ which makes the wff True as well. 


```{figure} ./images/Topic1/SAT2-clique-Photoroom.png
---
name: SAT2-clique
width: 600px
align: center
height: 500px
---
Finding maximal cliques in SAT3 for $c=3$ clauses. 
```

**Not-maximal cliques**. Given the above graph $G=(V,E)$, its construction ensures that <span style="color:#f88146">the maximum size any of its cliques is $c$</span> and this occurs, at least for one clique, only when the wff encoded by the graph is satisfiable. For instance, it is well known that a wff is not satisfiable *if and only if (iff) the wff is a contradiction*.

The most basic contradiction in CNF is 

$$
x\land \neg x\;.
$$

However, this is not in SAT3 form. To do so we proceed as follows:

$$
x \equiv (x\lor \alpha_1\lor \alpha_2)\land (x\lor \alpha_1\lor \neg\alpha_2)\land (x\lor \neg\alpha_1\lor \alpha_2)\land (x\lor \neg\alpha_1\lor\neg\alpha_2)
$$

and 

$$
\neg x \equiv (\neg x\lor \beta_1\lor \beta_2)\land (\neg x\lor \beta_1\lor \neg\beta_2)\land (\neg x\lor \beta_1\lor \neg\beta_2)\land (\neg x\lor \neg\beta_1\lor\neg\beta_2)\;,
$$

i.e. each atomic clause is replaced by the conjunction of $4$ clauses and this conjunction is satisfiable iff the atomic clause is satisfiable. 

Well, all we have to do is to build now the graph $G=(V,E)$ with $c=8$ clauses with $3$ nodes each ($24$ nodes overall). The new formula equivalent to $x\land\neg x$ will be satisfiable if we can find any clique of size $8$ in this graph. Do we really need to build the graph to verify this point? In other words, can we have a complete subgraph $K_8$ inside the graph of $24$ nodes if:
- The variables in the same clause cannot be connected.
- No variable can be connected with its negation?

In order to build a clique of size $c=8$ we need $8$ literals where we cannot include both a literal and its negation. One option is to get the set $C=\{x^1,x^2,x^3,x^4,\alpha^1_1,\alpha^3_2,\beta^1_1,\beta^3_2\}$ where the super-indexes refer to the clause's number. 
- It is clear that we can form a $4-$clique with the $x$s. 
- No problem to extend it to a $6$-clique by including the $\beta$s. 
- We can go further and form a $7$-clique by including $\alpha^3_2$.

However, to extend the $7$-clique we need to include $\alpha^1_1$ (there is no other possibility). But this cannot be done since $\alpha^1_1$ and $x^1$ (yet included) belong to the same clause and there is no edge between them! As a result, the wff is not satisfiable. 

<span style="color:#f88146"> **An important lesson of this proof** is that: cliques are full-connected subsets. If you can create them it means that their members form an equivalence class</span>. 

In addition, adding clauses only leads to make things more complicated since the order needed for satisfiability gets larger. 
<br></br>
<span style="color:#d94f0b"> 
**Exercise**. Consider now the following clause: $x\lor \neg x$
which is a tautology, i.e. it is *fully satisfiable* (all assignments lead to True). 
<br></br>
In this case, the equivalent SAT3 problem has two clauses, i.e. $c=2$ (see Garey & Johnson, page $48$): 
<br></br>
</span>
<span style="color:#d94f0b"> 
$
(x\lor \neg x)\equiv (x\lor \neg x\lor \alpha_1)\land (x\lor \neg x\lor \neg\alpha_1)
$
</span>
<br></br>
<span style="color:#d94f0b"> 
Build the associated graph and determine the maximal clique.
</spah>
<br></br>
<span style="color:#d94f0b"> 
Answer: $G=(V,E)$ will have $V=\{x^1,\neg x^1,\alpha_1, x^2,\neg x^2,\neg\alpha_1\}$, i.e. $n=6$ nodes. 
The (undirected) edges are 
</span>
<br></br>
<span style="color:#d94f0b"> 
$
\begin{align}
E &=\{(x^1,x^2),(x^1,\neg x^2),(x^1,\neg\alpha_1)\}\cup \{(\neg x^1,x^2),(\neg x^1,\neg x^2),(\neg x^1,\neg\alpha_1)\} \cup \{(\alpha_1,x^2),(\alpha_1,\neg x^2)\}\\
\end{align}
$
</span>
<br></br>
<span style="color:#d94f0b">
All the possible maximal subsets of $V$ have size $3$: $\{x^1,x^2,\alpha_1\}$, $(x^1,\neg x^2,\alpha_1)$ etc. However, it is impossible to form any clique of size $3$. Why? Because in all of these subsets we must include either two literals of the same clause or two contradictory literals, which does not match the edge set $E$. As a result, the largest cliques have size $c=2$ (actually, each edge in an undirected graph is a $2$-clique) and the formula is satisfiable as expected (see {numref}`SAT2-clique-ex`).
</span>

```{figure} ./images/Topic1/SAT2-clique-ex-Photoroom.png
---
name: SAT2-clique-ex
width: 500px
align: center
height: 400px
---
Finding trivial maximal cliques in SAT3 for $c=2$ clauses. 
```

### From Clique to SAT3
So far, we know that SAT3 can be always reduced to an equivalent (maximal) Clique problem. As we know that <span style="color:#f88146">$\text{SAT3}\in\text{NP}$</span>, then <span style="color:#f88146">$\text{Clique}\in\text{NP}$</span>. Actually, this is all we have to do to prove whether a problem <span style="color:#f88146">$\Pi\in \text{NP}$</span>:

1) Select a know problem <span style="color:#f88146">$\Pi'\in\text{NP}$</span>.
2) Reduce <span style="color:#f88146">$\Pi$</span> to <span style="color:#f88146">$\Pi'$</span>.
3) Test whether the reduction is polynomial. 


In the previous section, we have worked on the *if* part of the reduction, i.e. a SAT3 can be formulated as a clique problem. Now, we are working in the *iff* part: any clique problem can be mapped to an equivalent SAT3 one. Let's go!

In Garey & Johnson, the Clique problem is **formulated as follows** (page $47$): 

*Given a (non-directed) graph $G=(V,E)$ and a positive integer $J\le|V|$, does the graph contains a 
clique of size of size $J$ or more, that is a subset $V'\subseteq V$ such that $|V'|\ge J$ 
and every two nodes in $V'$ are joined by an edge in $E$*?

Obviuously, the concept of a clique only makes sense in undirected graphs. Moving on, in Garey & Johnson  the formulation of this problem results from reducing SAT3 to Vertex Cover (VC), another NP problem, and then this to clique. Herein, we go straight from SAT3 to clique as follows: 

[//]: https://www.youtube.com/watch?v=5PJDPIjIpYA

1) Given an integer constant $c>0$, suppose that $G=(V,E)$ has $|V|=3c$ and it has a clique of size $J\ge c$. Since we have $3c$ nodes, we denote each group $C_r$ of $3$ nodes as $C_r=\{l_1^r,l_2^r, l^3_r\}$. Without loss of generality (wlog) we may assume that $(l^{r}_i,l^{r}_j)\not\in E\;\forall i,j$. 

2) Then, if a $J$-clique $V'$ exists *only a node per group* $C_r$ with $r=1\ldots c$ can be in it since we need full-connection of range $J$ and the nodes inside a group are not connected. If we denote this node as $v_i^r\in V'$, we set $l_i^r=1$ indicating that the literal associated with this node is True. 

3) Actually $c$ denotes the number of clauses. But remember that $J\le c$. 

4) If $J=c$ then as there is one True literal per clause, then the wff associated with the and of all clauses is satisfiable. 

5) If $J<c$ then, having a $J$-clique means that only $J$ clauses can be *simultaneously* true. This it all we can guarantee, so the wff associated to the graph, whatever it is, it not satisfiable!

Note that the size of the size of the maximal clique is critical to the satisfiability. The more clauses we have the larger must be the clique. 

In addition, the $J-$clique may not be unique it it exist. All the $J$-cliques you find correspond to a form of satisfying the wff (what it is called in Logic as an *interpretation*).

## Clique-related problems
### Recognizing NP problems

The clique problem is a paradigmatic NP problem. Its **nature is non-polynomial** because, <span style="color:#f88146">in the worst case, we are forced to enumerate all the subsets of $V\subseteq 'V$</span>. Remember that if $n=|V|$ there are $O(2^n)$ subsets to verify whether they form a clique or not. 

This enlights our interpretation of SAT3: safisfying a wff in CNF (all wff can be transformed to this form) means finding a subset of literals (at least one per clause) simultaneously True. 

This <span style="color:#f88146">**subsetness flavor**</span> is shared by many NP problems. Let's review three of them that are closely related to the Clique problem. 

### Independent sets 
An <span style="color:#f88146">independent set</span> in a graph G=(V,E) is a subset $V'\subseteq V$ such that for all pairs $i,j$ in $V'$ the edge $(i,j)\not\in V'$, i.e.*the nodes of $V'$ are not mutually adjacent*. 

The independent set problem is: 

*Given a (non-directed) graph $G=(V,E)$ and a positive integer $J\le|V|$, does the graph contains an independent set having $|V'|\ge J$*?

This problem is automatically reduced to the clique problem by defining  the *complement  graph* $\bar{G}=(V,\bar{E})$ of $G$ with $\bar{E}=\{(i,j)\in E\times E, (i,j)\not\in E\}$: 

*An independent set $V'\subset V$ of $G$ is a clique in $\bar{G}$*. Actually an indendent set is called an <span style="color:#f88146">anti-clique</span>. In the previous SAT3-exercise illustrated in {numref}`SAT2-clique-ex`, independent sets create cliques between the literals of the same clause and links between contradictory literals of different clauses. See {numref}`SAT3-independent-ex`

```{figure} ./images/Topic1/SAT3-independent-ex-Photoroom.png
---
name: SAT3-independent-ex
width: 500px
align: center
height: 400px
---
Maximal independent set for a trivial SAT3. 
```

Applied to SAT3, maximal independent sets report groups of literals coming from the same clause. The linkage of negated literals is binary since every other linkage is allowed in SAT3. 

However, independent sets are very interesting *per se*. They are key to characterize the <span style="color:#f88146">graph coloring problem</span>. This problem consists in *assigning the maximum number of different colors to non-adjacent vertices*. See the following article in [Wikipedia](https://en.wikipedia.org/wiki/Graph_coloring). 

 
See {numref}`Color-ex` where we color the SAT3 graph in{numref}`SAT2-clique-ex`. This coloring comes from finding the independent sets in {numref}`SAT3-independent-ex` and then linking only nodes with different colors!

```{figure} ./images/Topic1/Color-ex-Photoroom.png
---
name: Color-ex
width: 500px
align: center
height: 400px
---
Coloring a trivial SAT3. 
```

The problem of $k-$coloring (decide whether a graph accepts $k$ colors) is NP except for $k\in\{0,1,2\}$. Artificial Intelligence (AI) applications of this problem include the <span style="color:#f88146">*Sudoku* game</span> where the colors are the $1-9$ digits (see {numref}`Sudoku`).

```{figure} ./images/Topic1/Sudoku-Photoroom.png
---
name: Sudoku
width: 500px
align: center
height: 400px
---
A solution to the $3\times 3$ Sudoku game [Source](https://networkx.org/nx-guides/content/generators/sudoku.html). 
```

Note that in a Sudoku of order $n=3$ we have:
- $n=3\times 3$ colors.
- $n^2\times n^2 = n^4 = 81$ nodes arranged in a grid.  
- $n$ rows of size $n$ in which each color appears exactly once.
- $n$ columns of size $n$ in which each color appears exactly once.
- $n\times n$ *sub-grids* where each color must apperar exactly once. 

Two distinct vertices will be adjacent *iff 
corresponding cells in the grid are either in the same row, or same column, or the same sub-grid*. This is important since some edges are hidden in {numref}`Sudoku`. 

Therefore, If we label the vertices as $(i,j)$ with $1\le i,j\le n^2$, there is an edge between $(i,j)$ and $(i',j')$ if 

$$
(i=i'\lor j=j')\lor (\lceil i/n\rceil = \lceil i'/n\rceil \land \lceil j/n\rceil = \lceil j'/n\rceil)\;,
$$

where $\lceil x \rceil$ denotes the *ceil* operation, i.e. the largest integer to which $x$ is rounded. For instance $\lceil 1/2 \rceil = \lceil 3/4 \rceil = 1$. Actually, this second part of the formula is to determine whether $(i,i)$ and $(i',j')$ belong to the same sub-grid. 

The above formula ensures that each row, column and subgrid has a clique of size $n^2$, i.e we have $3n^2$ complete subgraphs $K_{n^2}$. 

In other words, two nodes belong to the same clique if they are in the same row, or in the same column or in the same sub-grid. 

Does this means that all the maximal independent sets found are valid solutions of the Sudoku game? 

Let's explore this question in the following exercise. 
<br></br>
<span style="color:#d94f0b"> 
**Exercise**. Some questions on the $2\times 2$ Sudoku ({numref}`Sudoku-4`): **a)** Caracterize the problem : number of colors, nodes, node degree, edges and cliques. **b)** Characterize the complement graph and **c)** Map the coloring solution to finding independent sets through the maximal cliques of the complement  graph ({numref}`Sudoku-4c`).  Do all the maximal independent sets found are valid solutions of the Sudoku game?\
</span>
```{figure} ./images/Topic1/Sudoku-4-ex-Photoroom.png
---
name: Sudoku-4
width: 400px
align: center
height: 300px
---
A solution to the $2\times 2$ Sudoku game, i.e. with $4$ colors. 
```
```{figure} ./images/Topic1/Sudoku-4c-ex-Photoroom.png
---
name: Sudoku-4c
width: 400px
align: center
height: 300px
---
Complement graph of the $2\times 2$ Sudoku. 
```
<br></br>
<span style="color:#d94f0b">
Answer:\
**a)** The $2\times 2$ Sudoku corresponds to $4$ colors $1-4$ (e.g. orange, red, green and blue). This means that $V=\{(i,j),1\le i,j\le 4\}$ (see the labels of the nodes in (col,row) coordinates used by NetworkX in {numref}`Sudoku-4`). Then, we have $16$ nodes arranged in a $4\times 4$ grid.
<br></br>
There is a clique per row, column and sub-grid. Some of these cliques (the ones corresponding to rows and columns) are not visible in {numref}`Sudoku-4` but you must consider them. For instance nodes $(1,4),(2,4),(3,4)$ and $(4,4)$ form a clique. Also do nodes $(1,4),(1,2),(1,3)$ and $(1,4)$. The $4$ cliques in the sub-grids are obvious: one of them is $\{(1,4),(2,4),(1,3),(2,3)\}$. Overall, since $n=2$ we have $3n^2=12$ cliques, all of them of size $4$.
<br></br>
Number of edges: There is an edge between $(i,j)$ and $(i',j')$ when $
(i=i'\lor j=j')$ or $(\lceil i/2\rceil = \lceil i'/2\rceil \land \lceil j/2\rceil = \lceil j'/2\rceil)$.
However, instead of counting the edges manually it its better to compute the degree of each node. Consider for instance the top-left vertex $(1,4)$. It **participates in $3$ cliques** (row, col and sub-grid), each one with $3$ other different nodes. Then it has degree $d=(3n + 1)(n - 1)=3\cdot 2 + 1=7$. Why? Well, if a node participates in $3$ cliques of $n^2-1$ nodes we have $3(n^2-1)$. However, note that some edges in its corresponding row and column are shared with the sub-grid. Actually, after discounting them, the edges in the sub-grid become $(n-1)^2=n^2 + 1 - 2n$ instead of $n^2 - 1$. Then we count the edges of this clique separately: 
</span>
<span style="color:#d94f0b">
$
2(n^2-1) + (n-1)^2 = 2n^2 - 2 + n^2 + 1 - 2n = 3n^2 - 2n - 1 = (3n + 1)(n-1).
$
</span>
<br></br>
<span style="color:#d94f0b">
**b)** The complement graph has the same nodes as the original, and the edges are those which are not in the original (and they are not loops). Now, we connect nodes which are not in the same row, same column and sub-grid. How many cliques do we have here and what is their size? Looking at {numref}`Sudoku-4c` is helpful. Take node $(1,4)$ again. It is connected with $8$ nodes:
</span>
<span style="color:#d94f0b">
$4$ of them correspond to the sub-grid not sharing any row or column with it.
The other $4$ come from the $2$ sub-grids sharing row or col. 
</span>
<br></br>
<span style="color:#d94f0b">
We have $n^2$ sub-grids: $1$ shares row and column with a given node, $(n-1)^2$ do not share neither row or col with that node. Then 
$n^2 - (n-1)^2 - 1=n^2 - [n^2+1-2n] - 1 = 2n -2$ share either a row or column with that vertex. 
</span>
<br></br>
<span style="color:#d94f0b">
 Since each sub-grid has $n^2$ nodes, a given node in the complementary graph is linked with $[(n^2-n)\cdot (2n -2)]=4$ nodes concerning the some-sharing sub-grids and with $[n^2\cdot (n-1)^2]=4$ nodes concerning no-sharing sub-grids. 
</span>
<br></br>
<span style="color:#d94f0b">
**c)** A node in this example can have $8$ neighbors in the complement graph, but note that the purpose of Sudoku is to obtain $n^2$ independent set of size $n^2$ each. Looking again at the complement graph in {numref}`Sudoku-4c`, all the nodes with the same color are in the same cliques. However, there are additional edges leading to form cliques with nodes of different colors. Actually, we have $n^4$ cliques on $n^2$ nodes each in the complement graph. Why?
<br></br>
Note that when we link a node with other sub-grids *only one node in this sub-grids may have the same color, so the remaining ones have different colors*. Therefore, **only half the cliques in the complement graph are independent sets compatible with Sudoku solutions**. Just filter the cliques whose colors are not equal!
</span>

### Vertex covers 
A <span style="color:#f88146">vertex cover</span> $V'\subseteq V$  in $G=(V,E)$ is a subset of nodes so that $(i,j)\in E\Leftrightarrow (i\in V'\lor j\in V')$. Then, we have the following NP problem: 

*Given a graph $G=(V,E)$ and a positive integer $J\le|V|$, does the graph contains a 
vertex cover of size $J$ or less, that is a subset $V'\subseteq V$ such that $|V'|\ge J$ 
and for each edge $(i,j)\in E$, at least one of $i$ and $j$ belongs to $V'$*?

It is obvious that now, the *trivial cover* is $V$ itself. Therefore, we are usually interested in <span style="color:#f88146">minimal covers</span>, since a minimal cover *captures the essence or the core of the graph*. In this regard, the vertex cover problem is somewhat compementary to indepentent set and clique. More precisely: 

*A graph $G=(V,E)$ has a vertex cover of size $J$ iff the complement graph $\bar{G}=(V,\bar{E})$ a clique of size $|V|-J$* which is equivalent to

*A graph $G=(V,E)$ has a vertex cover of size $J$ iff it has an independent set of size $|V|-J$*. 

See for instance the example in {numref}`Cover`. The orange nodes are a vertex cover of size $J=3$. The yellow nodes are connected at least to one node in the cover and they form a clique in the complement graph. 

```{figure} ./images/Topic1/Cover-Photoroom.png
---
name: Cover
width: 400px
align: center
height: 300px
---
Minimal cover of a graph (in orange) and independent set (in yellow). 
```

Note that the *cover problem partitions the graph* into the nodes in the core and the remaining ones, which form an independent set. Note also that $J=3$ is the minimum cover size for this graph, but it may be not unique (see for instance $\{2,3,4\}$ where the independet set would be $\{1,5,6\}$). 

Given the above relationship between vertex covers and independent sets, their size characterizes de <span style="color:#f88146">colorability of a graph</span> (how many different colors can we allocate). In {numref}`Cover-color` we show that the mininal number of colors, or chromatic number $\alpha(G)$, for the above graph $G$ is $3$. However, it is well known that *if the cover size is $J=k$, then the graph admits $k+1$ colors (it is $k+1$ colorable)*. Proving that is simple: assign a *different* color to each of the $k$ nodes in the cover and then asign the *same* color to the nodes in the independent set. In the above example we could assign ($1=\text{red}$,$3=\text{green}$,$4=\text{blue}$) and assing $\text{orange}$ to $2$, $5$ and $6$ (i.e $4$ colors). We do that in {numref}`Maxcolor`.

```{figure} ./images/Topic1/Cover-color-Photoroom.png
---
name: Cover-color
width: 400px
align: center
height: 300px
---
Minimal coloring of a graph. 
```

```{figure} ./images/Topic1/Colorability-Photoroom.png
---
name: Maxcolor
width: 400px
align: center
height: 300px
---
Maximal coloring of a graph. 
```

### SAT3 reduction 
Finding independent sets (IS) and vertex covers (VC) is equivalent because of their relationship with the clique problem. However, this does not ensure <span style="color:#f88146">$\text{IS}\in \text{NP}$</span> and <span style="color:#f88146">$\text{VC}\in \text{NP}$</span>. To prove that we have to prove than any of them is in NP. Following Garey & Johnson, we prove that **VC can be reduced to SAT3**.

The proof consists of **two steps**: 
1) <span style="color:#f88146">Create a graph</span> $G=(V,E)$ encoding the SAT3 from a vertex cover perspective. This graph has to be created in polynomial time. 
2) <span style="color:#f88146">Prove</span> that a vertex cover of size $J\le |V|$ (minimal cover) exists in $G$ iff the clauses encoded in it are satisfiable. 

<span style="color:#f88146">**Create a graph**</span>. Given $m$ clauses $C_1,\ldots,C_m$ and $n$ variables $x_1,\ldots,x_n$ the graph construction $G=(V,E)$ consists of: 
- A 3-nodes clique per clause $C_i$ linking its literals. 
- A 2-nodes clique (dipole) $D_i$ per variable $x_i$ where we have $x_i$ and its negation $\neg x_i$. 

Then we proceed to add additional edges between the clauses-cliques and the dipoles by linking the homonimous literals as we show {numref}`CoverSAT1` where we have only $m=2$ clauses and $n=3$ variables: 

$$
(x_1\lor\neg x_2\lor\neg x_3)\land(\neg x_1\lor x_2\lor\neg x_3)
$$

and we have colored compatible literals in the dipoles and cliques with the same color. 

```{figure} ./images/Topic1/Cover-SAT1-Photoroom.png
---
name: CoverSAT1
width: 500px
align: center
height: 400px
---
SAT3 as Vertex Cover. Example of construction. 
```

The "magic" of this construction relies on assigning a truth value $1$ to *some of the vertices* included in a vertex cover of $G$ and $0$ otherwise. 

Remember that a cover $V'\subseteq V$ is *a subset of $V$ where all the edges $E$ of $G$ are reachable (covered) from the vertices  of $V'$*. Then 
- We have 3 edges per clause clique, 2 edges per dipole and no more than $3mn$ edges between cliques and dipoles. Thus, the graph is created in polynomial time. 
- To cover $G$ we meed to take **at least** two nodes per clause clique and also **at least** one node per dipole. 

For instance, 
- To cover the first clause, if we put only the top node $\neg x_3$ in the cover, the edge between the other two are not covered, and similarly for the second clause with $x_2$.
- Once we know that we need two nodes to cover $3$ edges in a 3-clique, its obvious that we only need a node to cover a dipole.
- The nodes in the dipoles $D_i$ either cover in addition to their counterparts' edges the edges of the clause cliques or the edges  between the nodes in the dipole and the clause cliques. 
- Therefore, a possible cover will be: 

$$
V' = \underbrace{\{x_1,\neg x_2\}}_{C_1}\cup \underbrace{\{\neg x_1,\neg x_3\}}_{C_2}\cup\underbrace{\{\neg x_1\}}_{D_1}
\cup\underbrace{\{x_2\}}_{D_2}
\cup\underbrace{\{\neg x_3\}}_{D_3}\;.
$$

Note that assigning true values $1$ to the nodes in the cover $V'$ coming from the dipoles,i.e. $\neg x_1=1$, $x2=1$ and $\neg x_3=1$, we satisfy all the clauses. Therefore, the *logic interpretation* $x_3=x_1=0,x_2=1$ makes the CNF satisfiable. We encode that by coloring in orange the nodes belonging to $V'$ in {numref}`CoverSAT2`. 

**Vertex covers and Indepentent Sets**. Remember that saying that $V'\subseteq V$ is a cover of size $J\le |V|$ is equivalent to say that $V - V'$ is an independent set. In {numref}`CoverSAT2` the nodes in the intependent set corresponding to $V'$ are colored in yellow. Note that the size of the indepedent set is $|V|-J=5$ whereas the size of the cover is $J=2m + n = 7$. We will prove later on that this is the size we need to make the CNF satisfiable. But at this point finding independent set of size $|V|-(2m + n)$ is a good way of finding covers that make the CNF satisfiable. If we can only find smaller independent sets then CNF is not satisfiable.   

```{figure} ./images/Topic1/Cover-SAT2-Photoroom.png
---
name: CoverSAT2
width: 500px
align: center
height: 400px
---
Yellow nodes do not belong to the cover. 
```
<br></br>
<span style="color:#d94f0b"> 
**Exercise**. Given the SAT3 in {numref}`CoverSAT1`, identify the covers where the two clauses **are NOT** satisfied. Compare with the satisfiable cover in {numref}`CoverSAT2`.
<br></br>
Answer. We have $2^{n=3}$ truth assignments. When $(\neg x_3 = 1) \equiv (x_3=0)$ the CNF is satified and this happens $2^2$ times (in bold in the table below). Otherwise, the CNF is satisfied when either $x_1=x_2=1$ or $x_1=x_2=0$. 
<br></br>
Looking to the above thruth table
</span> 
<span style="color:#d94f0b">
<br></br>
$
\begin{aligned}
&\begin{array}{ccc|c|c|}
\hline \hline x_1 &  x_2 &  x_3  & (x_1\lor\neg x_2\lor\neg x_3) & (\neg x_1\lor x_2\lor\neg x_3) & \text{CNF}  \\
\hline 
0 & 0 & \mathbf{0} & 1 & 1 & 1 \\
0 & 0 & 1 & 1 & 1 & 1 \\
0 & 1 & \mathbf{0} & 1 & 1 & 1 \\
0 & 1 & 1 & 0 & 1 & 0 \\
1 & 0 & \mathbf{0} & 1 & 1 & 1 \\
1 & 0 & 1 & 1 & 1 & 0 \\
1 & 1 & \mathbf{0} & 1 & 1 & 1 \\
1 & 1 & 1 & 1 & 1 & 1 \\
\hline
\end{array}
\end{aligned}
$
</span>
<br></br>
<span style="color:#d94f0b">
Only a couple of interpretations are *counter-model*.
<br></br>
**First case:** $x_1=0,x_2=1, x_3=1$. This means that the nodes of the cover in the dipoles should be: $\neg x_1$, $x_2$ and $x_3$.The remaining literals must be in the independent set. This forces us to put the literal $\neg x_3$ in $C_1$ in the cover, i.e. we enlarge the cover and reduce the independent set. The size of the cover is now $2m + n + 1$ (see {numref}`CoverSAT3`) and the formula is not satisfiable!
<br></br>
</span>
```{figure} ./images/Topic1/Cover-SAT3-Photoroom.png
---
name: CoverSAT3
width: 500px
align: center
height: 400px
---
Not satisfiable cover. Yellow nodes do not belong to the cover. 
```
<br></br>
<span style="color:#d94f0b">
**Secod case:** $x_1=1,x_2=0, x_3=1$. This means that the nodes of the cover in the dipoles should be: $x_1$, $\neg x_2$ and $x_3$.The remaining literals must be in the independent set. This forces us to put both the literals $\neg x_3$ in $C_1$ and $x_2$ in $C_2$ in the cover. As a result, we have to remove literal $\neg x_2$ from the cover and put in the independent set. The size of the cover is again $2m + n + 1$ (see {numref}`CoverSAT4`) and the formula is not satisfiable!
<br></br>
</span>
```{figure} ./images/Topic1/Cover-SAT4-Photoroom.png
---
name: CoverSAT4
width: 500px
align: center
height: 400px
---
Not satisfiable cover. Yellow nodes do not belong to the cover. 
```
<br></br>
<span style="color:#f88146">**Prove equivalence: 
<br></br>
$\text{Cover}\Rightarrow \text{SAT3}$**</span>. Suppose that $G=(V,E)$ has a cover $V'\subseteq V$ of order $J=2m + n$. This means that $V'$ has one vertex per dipole (we have $n$ dipoles) and two vertices per clause (we have $m$ clauses). Check it up by loking at the orange nodes of {numref}`CoverSAT2`. Actually the yellow nodes in each dipole encodes the truth values *not chosen* for a SAT assignment (we choose the truth values of the orange node in the dipole: $\neg x_1=1$,$x_2=1$,$\neg x_3=1$). 
<br></br>
<span style="color:#f88146">**$\text{SAT3}\Rightarrow\text{Cover}$**</span>. Suppose now that the SAT3 problem encoded by $G=(V,E)$ is satisfiable. As a result, *at least* one literal of each clause $C_i$ is true. Remember that each clause $C_i$ is encoded by a 3-clique. By construction, we know that $G=(V,E)$ will have a cover $V'\subseteq V$ of size $J=2m + n$. This means that two nodes per 3-clique (clause) cover all the edges in the clique. However, they also cover the edges between the 3-cliques and the dipoles. Exactly one this edges per clause will cover an edge with a truth assignment,  because we cannot cover both nodes of the dipole. Then is the CNF is satisfiable, it will have a cover of size $J=2m + n$.



## Hamiltonian cycle 
The following problem is a classic NP problem in AI: 

*Given a graph $G=(V,E)$, does $G$ a Hamiltonian cycle, that is an ordering $<v_1,v_2,\ldots,v_n>$ of the vertices of $G$ where $n=|V|$ such that $(v_n,v_1)\in E$ and $(v_{i},v_{i+1})\in E$, $\forall i,1\le i<n$*? 

This is the well-known <span style="color:#f88146">Hamiltonian-cycle</span> (HC) problem: we must find a cycle or tour that visits *all* nodes *once*. The problem is NP because instead of looking for subsets (<span style="color:#f88146">**subsetness flavor**</span>) we look for *all the orderings of a set* $<v_1,v_2,\ldots,v_n>$. Given a permutation $\pi:V\rightarrow V$, the solution has the form $<v_{\pi(1)},v_{\pi(2)},\ldots,v_{\pi(n)}>$. Remember that there are $n!$ possible orderings and looking if any of them is a Hamiltonian cycle is not polinomial at all. 

However, proving that <span style="color:#f88146">$\text{HC}\in \text{NP}$</span> deserves a reduction to any of the NP problems. Both Independent Sets (IS) and Vertex Cover (VC) are NP, i.e. <span style="color:#f88146">$\text{IS}\in \text{NP}$</span> and <span style="color:#f88146">$\text{VC}\in\text{NP}$</span> because they can be reduced to the Clique problem which is NP. 

However, the reduction of HC to the Clique problem is quite complex and difficult to understand. Even in the Garey & Johnson's text, they reduce HC to the VC. Herein, we even use a simpler and yet more intuitive approach: <span style="color:#f88146">we reduce HC to the SAT3 problem!</span>

Remember that a reduction is an **necessary and sufficient condition** i.e. an iff. Let us design a construction where a solution to the HC is encoded in a graph.

We commence by mapping the <span style="color:#f88146">Hamiltonian Path</span> (HP), instead, to SAT3: 

*Given a graph $G=(V,E)$, does $G$ a Hamiltonian path, that is an ordering $<v_1,v_2,\ldots,v_n>$ of the vertices of $G$ where $n=|V|$ such that $(v_{i},v_{i+1})\in E$, $\forall i,1\le i<n$*? 


```{figure} ./images/Topic1/Hamiltonian-gadget-removebg-preview.png
---
name: HPtoSAT3
width: 700px
align: center
height: 600px
---
Digraph mapping HP to SAT3. Yellow nodes encode the variables (plus one extra variable). 
```

The core of the construction, shown in {numref}`HPtoSAT3`, is called a <span style="color:#f88146">**variable gadget**</span> where:

1. For each vertex $v_i\in V$ of $G$ we have a couple of variables: $x_i$ and $x_{i+1}$. Actually, we have $n+1$ variables where $n=|V|$. These variables are shown in yellow. 

2. It is a **digraph** (directed graph), starting from the top vertex $x_1$ and going down to the bottom vertex $x_{n+1}$. Then, in the figure we $n=3$ variables in the 'yet to map' SAT3 and $x_4$ is an 'extra' variable. 

3. Note that, descending from $x_1$ to $x_4$ we may follow the **left** path (in blue) and the **right** path (in red). At each $x_i$ we are able to change from color (blue-to-red or read-to-blue). As a result, <span style="color:#f88146">**we may have $2^n$ different paths from $x_1$ to $x_4$**</span>. 

4. Below each $x_i$, $i=1,2,\ldots,n$ we have a bidirectional **path graph** with nodes $x_i$\_$p_1$ to $x_i$\_$p_{3\cdot c}$ where where $c$ is the **number of clauses**: $c=3$ in this case. 

5. These path graphs enable to keep going left-to-right, right-to-left or change of sense to reach the following **level** $x_{i+1}$. However, *if we want to visit all nodes $x_1$ to $x_4$ passing through the path graphs <span style="color:#f88146">**once**</span> we must <span style="color:#f88146">**choose one of the senses**</span> in each path graph*. 

6. We associate a <span style="color:#f88146">**truth value**</span> to each path. In the figure, 'True' paths have all their edges 'blue' and 'False' paths have all their edges 'red'. Black edges are interpreted as 'blue' (True) if they go left-to-right and 'red' (False) otherwise. This **construction trick** is key to force a Hamiltonian path from $x_1$ to $x_4$ visiting all the nodes in the constructed graph. 

7. However, we still must <span style="color:#f88146">**match a 'True' hamiltonian path** with a SAT solution, if there is one</span>. Otherwise, the Hamiltonian path will be false.

8. In orther to do that we use $c$ **additional modes** $c_i$ (one per clause). How do we use them?

Well, in {numref}`HPtoSAT3` we assume the following CNF: 

$$
\underbrace{(x_1\lor x_2\lor \neg x_3)}_{c_1}\land \underbrace{(x_1\lor x_2\lor x_3)}_{c_2}\land \underbrace{(x_1\lor x_2\lor \neg x_3)}_{c_3}
$$

We **proceed** as following: 

1. Since $x_1$ is 'positive' (True) in $c_1$, we must make  $x_1\rightarrow x_1\_p_1$, then go to $c_1$ using a 'green' **edge clause** and then come back to this path through $c_1\rightarrow x_1\_p_2$. This way, we may proceed towards the right through $x_1\_p_3$. 

2. Next, we are $x_1\_p_4$ (the first of a group of $3$ nodes coding the role of variable $x_1$ in clause $c_2$). Note that, as $x_1$ is also 'positive' in $c_2$, we go to $c_2$ and back as before and then return to $x_1\_p_5$. Next, we repeat the same coding for the role of $x_1$ in $c_3$. 

3. At this point, we have yet visited $c_1$, $c_2$ and $c_3$ because $x_1$ is positive in all of them. We <span style="color:#f88146">**yet know that the CNF is safistifable**, but we have still to progress to visiting all the nodes of the digraph once (Hamiltonian path)</span>. Can we do it?

4. Of course. Since $x_1$ is 'True' in all the clauses, we only need to go down $x_2$ and proceed from left to right again from $x_2\_p_1,\ldots,x_2\_p_9$ without visiting any clause node. Then go down $x_3$ and do the same until we end-up in $x_4$ where we have completed the following Hamiltonian path:

$$
\begin{align}
x_1, & x_1\_p_1, \mathbf{c_1}, x_1\_p_2, x_1\_p_3,x_1\_p_4, \mathbf{c_2}, x_1\_p_5, x_1\_p_6, x_1\_p_7, \mathbf{c_3}, x_1\_p_8, x_1\_p_9\\
x_2, & x_2\_p1,\ldots,x_2\_p_9\\
x_3, & x_3\_p1,\ldots,x_3\_p_9\\
x_4.  & \\
\end{align}
$$

5. Since, $x_2$ is 'True' in all clauses, we may proceed similarly, thus leading to the following path

$$
\begin{align}
x_1, & x_1\_p1,\ldots,x_1\_p_9\\
x_2, & x_2\_p_1, \mathbf{c_1}, x_2\_p_2, x_2\_p_3,x_2\_p_4, \mathbf{c_2}, x_2\_p_5, x_2\_p_6, x_2\_p_7, \mathbf{c_3}, x_2\_p_8, x_2\_p_9\\
x_3, & x_3\_p1,\ldots,x_3\_p_9\\
x_4.  & \\
\end{align}
$$

6. However, $x_3$ is 'negative' both in $c_1$ and $c_3$, and 'positive' in $c_2$. This fact *implies that $x_3$ is contributing to make the CNF unsatisfiable*. In other words, <span style="color:#f88146">waiting to visit the clause nodes until level $3$ **does not lead to a Hamiltonian path** since we have to go both right-to-left and left-to-right there</span>. The clause nodes should be visited before level $3$. 

Therefore, the above construction shows that HP is reducible to SAT3, i.e.: *there is a HP in the above graph iff the CNF is satisfiable*.

Extending this result to the **Hamiltonian Cycle** (HC) is straightforward: just create a back link between $x_4$ and $x_1$.
<br></br>
<span style="color:#d94f0b"> 
**Exercise**. Given the SAT3 $(x_1\lor x_2\lor \neg x_3 )\land (\neg x_1\lor\neg x_2\lor x_3)$ which is clearly unsatisfiable (a variable and its negation is present in all clauses). We ask the following: **a)** Build the HC-construction and **b)** Show that it has no Hamiltonian cycles.
</span>
<br></br>
<span style="color:#d94f0b"> 
Answer. We show thre HC-construction in {numref}`HPtoSAT3Ex`. Note that we have  $3\cdot 2 = 6$ nodes per path graph, since there are $c=2$ clauses. 
<br></br>
```{figure} ./images/Topic1/Hamiltonian-gadget-Excercise-removebg-preview.png
---
name: HPtoSAT3Ex
width: 700px
align: center
height: 600px
---
Digraph mapping HP to SAT3. Yellow nodes encode the variables (plus one extra variable). 
```
<br></br>
</span> 
<span style="color:#d94f0b">
Note that the two first path graphs go first to $c_1$ in left-to-right order, stating that respectively $x_1$ and $x_2$ must be 'True', but access in the reverse order to $c_2$, where they have to be 'False'. With the third path graph, we have the opposite since $x_3$ must be false in $c_1$ and true in $c_2$. As a result, it is impossible to draw a Hamiltonian cycle in the graph of {numref}`HPtoSAT3Ex`. 
</span> 



<!--
Vertex covers have two main applications in AI: 
- Firstly, given their relationship with independent sets, their size characterizes de <span style="color:#f88146">colorability of a graph</span> (how many different colors can we allocate). 
- Second, their core meaning makes them useful for proof the NP-completedness of other problems such as the finding of <span style="color:#f88146">Hamiltonian cycle</span> (a cycle that visits all the nodes of the graph once) it it exists. 

**k-Colorability**. In {numref}`Cover-color` we show that the mininal number of colors, or chromatic number $\alpha(G)$, for the above graph $G$ is $3$. However, it is well known that *if the cover size is $J=k$, then the graph admits $k+1$ colors (it is $k+1$ colorable)*. Proving that is simple: assign a *different* color to each of the $k$ nodes in the cover and then asign the *same* color to the nodes in the independent set. In the above example we could assign ($1=\text{red}$,$3=\text{green}$,$4=\text{blue}$) and assing $\text{orange}$ to $2$, $5$ and $6$ (i.e $4$ colors). 

```{figure} ./images/Topic1/Cover-color-Photoroom.png
---
name: Cover-color
width: 400px
align: center
height: 300px
---
Minimal coloring of a graph. 
```

**Hamiltonian cycles**. Deciding whether a graph has a Hamiltonian cycle is formulated as follows: 

*Given a graph $G=(V,E)$, does $G$ a Hamiltonian cycle, that is an ordering $<v_1,v_2,\ldots,v_n>$ of the vertices of $G$ where $n=|V|$ such that $(v_n,v_1)\in E$ and $(v_{i},v_{i+1})\in E$, $\forall i,1\le i<n$*? 

 Herein, the <span style="color:#f88146">**subsetness flavor**</span> is not so obvious</span>. Detecting Hamiltonian cycles in $G=(V,E)$ requires exploring all the permutations $P(n)$, where $n=|V|$ and determine if any of them deploys a cycle that visits all vertices exactly once. 
-->




<!--
**A simple problem**. For instance, given a directed graph $G=(V,E)$ (a **digraph**) consider its adjacency matrix $\mathbf{A}\in\{0,1\}^{n\times n}$ 
where $n=|V|$ is the number of nodes. A simple problem involving $\mathbf{A}$ is to *compute in how many way a node is reachable from some other in two hops*. Then
- Since the problem involves nodes, the graph can be parameterized by $n$. 
- The adjacency matrix tell us how to reach one node $i$ from another one $j$ *in one hop*. Simply, if $\mathbf{A}_{ij}=1$ then $j$ is reachable from $j$ *in one hop*.

Let $\mathbf{a}_{i:}$ and $\mathbf{a}_{:j}$ denote respectively, the row of node $i$ and the column of node $j$ in $\mathbf{A}$. The $1$s in $\mathbf{a}_{i:}$ are the neighboring nodes of $i$. Similarly, the $1$s in $\mathbf{a}_{:j}$ are the neighboring nodes of $j$. Then, *$j$ is reachable from $i$ in two hops* if they have (at least) a common neighbor $k$, called **the pivot** so that $\mathbf{A}_{ik}=1$ and $\mathbf{A}_{kj}=1$. Therefore $\mathbf{a}_{ik}=\mathbf{a}_{kj}=1$ (there is a $1$ at position $k$) both in $\mathbf{a}_{i:}$ and $\mathbf{a}_{:j}$. Therefore, the number of ways for reaching $j$ from $i$ is 

$$
\left<\mathbf{a}_{i:},\mathbf{a}_{j:}\right>:=\sum_k \mathbf{a}_{ik}\mathbf{a}_{kj}\;, 
$$

where $\left<,\right>$ is the dot product. This can be done in $O(n)$ (linear time). Herein, we use the [Big-O notation](https://web.mit.edu/16.070/www/lecture/big_o.pdfwhere) 

$$
f(n):=O(g(n))\;\text{means}\;\exists c: |f(n)
|\le c \cdot |g(n)|\; \forall n\ge 0. 
$$

If we want to compute the two-hop accesibility between $any$ pair of nodes, we have to compute $n^2$ dot products per pair $(i,j)\in V\times V$. Now the complexity is $O(n^3)$ (cubic). Essentially, what we are doing is to compute $\mathbf{A}^2$. The worst-case complexity of matrix mutiplication is actually $O(n^3)$ although it can be done in $O(n^{2.371552})$ from last year. Check up the complexities of matricial operations in [Wikipedia](https://en.wikipedia.org/wiki/Computational_complexity_of_matrix_multiplication). 

[//]: https://cs.stackexchange.com/questions/1147/complexity-of-computing-matrix-powers

What if we are now asked to **compute $k$-hop accessibilities** with $k>2$? Of course, this can be done by computing $\mathbf{A}^k$ which takes $O(n^3\cdot\log k)$. However, computing the eigenvalues (stored in the diagonal matrix $\Lambda$) and its eigenvetors (stored in the matrix $\Phi$) takes $O(n^3)$. Then, the *spectal theorem* leads to  $\mathbf{A}^k=\Phi\Lambda^k\Phi^T$. Since computing $\Lambda^k$ (the power of a diagonal matrix) takes $O(n\log k)$, we have an overall complexity of $O(n^3 + n\log k)$. 

**Intractable problems**. A vertex $j$ is accessible from $i$ in $k$-hops if $\mathbf{A}^{k}_{ij}=1$. Thus, a *path* $\Gamma=\{i=i_{1},i_{2},\ldots,i_k=j\}$ of length $k$ does exist between $i$ and $j$. Actually if $i=j$ then we have a *circuit* or *cycle* of lenth $k$ over $i$. However, such a circuit/cycle does not have any special meaning beyond its length. In real life, however, we are interested in finding circuits with special properties, namely *covering all the nodes once*. This is extremely useful in designing rounds for robots, planning fair tournaments and DNA sequencing among other applications. We are then looking for a <span style="color:#f88146">Hamiltonian</span> cycle. However, answering the question *"is there any hamiltonial cycle"* is an <span style="color:#f88146">intractable problem</span>. Why? Some considerations:
- Hamiltonian cycles, if they exist, have all length $n+1$. Therefore, we could investigate the diagonal of $\mathbf{A}^{n+1}$ and check up the elements $\mathbf{A}^{n+1}_{ii}\neq 0$. If all of them are zero, we are lucky! However this is not the case, in many <span style="color:#f88146">instances</span> of the problem:  **all** of them are not zero (for instance if the graph is non-directed and complete)!
- For each $\mathbf{A}^{n+1}_{ii}\neq 0$ we should investigate all the paths $\Gamma=\{i=i_{1},i_{2},\ldots,i_k=j\}$ of length $n$ starting at $i$ and ending at every $j$ where $\mathbf{A}_{ji}=1$. If any of them *visit all the nodes once*, the answer is given. If none of them does, the answer is also given. 
- Mathematically, *visit all the nodes once* means that  $\Gamma = \text{sort}(V)$, i.e. $\Gamma$ is given by reordering $V$ in a particular way to be determined. 


Reaching nodes *in two hops* requires computing $\mathbf{A}^2 = \mathbf{A}\mathbf{A}$, i.e, requires a matricial product. How difficult is to  
-->