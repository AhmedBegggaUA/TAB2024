# Heuristic Search 

##  Rubik's cube
**Motivation**. Heuristic search was born to address combinatorial problems in terms of <span style="color:#f88146">state-space expansion</span>. This is clearly exemplified by the well known Rubik's cube, with $3\times 3$ colored stickers per face. Then, given the **solved** cube (left), someone **scrambles** it (right) by applying a sequence of rotations. We have six types of $90^{\circ}$ clockwise rotations or <span style="color:#f88146">states</span> following the [standard notation](https://ruwix.com/the-rubiks-cube/notation/): 

| Clockwise | Rotational Description|
|---|---|
|U  | Upper-horizontal block (top-to-left) |
|D  | Down-horizontal block (front-to-right)|
|R  | Right-vertical block (front-to-top)|
|L  | Left-vertical block (front-to-down) |
|F  | Front-face block (front-to-right) |
|B  | Back-face block (back-to-left)|

| Solved Cube | Scrambled Cube|
|---|---|
| <img src="_images/RubikSol-removebg-preview.png" alt="Descripción de la imagen" width="400" height="400"> |  <img src="_images/Rubik-Scramble-removebg-preview2.png" alt="Descripción de la imagen" width="400" height="400">|

**Permutations**. Note that we have 3 types of move: horizontal, vertical and face (side) twist. In addition, if we rotate the upper block to the left, the opposite is to make the same rotation but **counter-clockwise**. Thus, we have six more rotations: U', D', R', L', F' and B'. Thus, strictly speaking, we have $12$ possible rotations to choose from any configuration. In the state-space language, <span style="color:#f88146">each state **may span** $n=12$ states</span>. 

Mathematically each configuration can be considered as a sequence of $6\times 3\times 3 = 54$ elements containing numbers from $1$ to $6$. <span style="color:#f88146">*How many permutations (with repetition) do we have?*</span> Well, for $n=6$ and $r=54$ we have $n^r\approx 10^{42}$. However, the [symmetries of the problem](https://web.mit.edu/sp.268/www/rubik.pdf) lead to the following reasoning: 
- **Corners**. There are $8$ corners in the cube. Then, we have $n_{corners}=8!$ corner arrangements.
- **Corners Orientations**. Each corner arrangement may have $3$ possible orientations (there are $3$ colors that can face up). Then, we have $n_{CO}=3^{8}$ possibilities per corner permutation. 
- **Edges**. There are $(9-4=5-1=4)\times 3$ non-corner and non-center pieces, called edges. These edges can be arranged in $n_{edges}=12!$ ways. 
- **Edges Orientations**. Since each edge may have $2$ orientations (colors), we have $n_{EO}=2^{12}$. 

The product rule of combinatorics leads to: 

$$
n_{corners}\cdot n_{CO}\cdot n_{edges}\cdot n_{EO} = 8!\cdot 3^{8}\cdot 12!\cdot 2^{12}
$$

Analyzing a bit more the cube, we have that: 
- Only $1/3$ of the permutations have the correct orientations. 
- Only $1/2$ of the permutations have the same edge orientations.
- Only $1/2$ of the latter permutations have the correct "parity" (a concept of **group theory**)

Then, we have

$$
\frac{n_{corners}\cdot n_{CO}\cdot n_{edges}\cdot n_{EO}}{3\cdot 2\cdot 3} = 4.3253\cdot 10^{19}\;\text{moves}\;.
$$

**God's number N**. Given a scrambled cube, *<span style="color:#f88146">What is the minimum number of steps to get back to the initial state?*</span> Well, remember that we can perform $n=12$ moves each time. However, given the first $12$, next time we can only do $11$ (the other one undoes the first move). Then: 

$$
12\cdot 11^{\mathbf{N}-1}\ge 4.3253\cdot 10^{19}\Rightarrow \mathbf{N} \ge 19\;.
$$

Actually, in 2013 [Rokicki et al.](https://tomas.rokicki.com/rubik20.pdf) proved that the "diameter of the Rubik's Cube is $\mathbf{N}=20$", i.e. it can be solved in $20$ moves or less. 

The sequence found for the above case is: 

R' D L' F R F' D' R' D' F' U L' U R R U R' F D R' D' F D' L F R F' D F R' F' L' R

and it has $33$ moves. How is it done? Answering this question leads to study the rudiments of **heuristic search** and a **particular approach** (called Iterative Deepening Search) for the Rubik's Cube (RC in the following). 


## Heuristic Search
### Search Tree 
Classical textbooks such as Pearl's one <span style="color:#f88146">"Heuristics: Intelligent Search Strategies for Problem Solving"</span>, address Heuristic Search (HS) in terms of *<span style="color:#f88146">expanding a search tree</span> from the <span style="color:#f88146">root</span> until the <span style="color:#f88146">target</span> is found*.
1) **Root**. <ins>Initial state</ins> $\mathbf{n}_0$. For the RC, it can be any of the $4.3253\cdot 10^{19}$ configurations corresponding to a scrambled RC.
2) **Target**. <ins>Final state</ins> $\mathbf{n}_F$. Obviously, for the RC we have the configuration where all the faces have uniform color. 
3) **Expansion**. Going from $\mathbf{n}_0$ to $\mathbf{n}_F$ is implemented by <ins>deploying a search tree</ins>, i.e. a graph. Such a deploying is performed acording o a given **search strategy**. One strategy is considered to be <ins>more intelligent than another</ins> if it founds the target minimizing the number of intermediate nodes $\mathbf{n}$ explored.

The well-known $A^{\ast}$ algorithm has the following features:
1) It holds a **search border** (the list OPEN) and a list of **interior nodes** (CLOSED).
2) Aims to minimize a **cost function** $f(\mathbf{n})$ for $\mathbf{n}\in \Omega$ (the search space). The cost function is additive and accounts both for the **best cost** from $\mathbf{n}_0$ to $\mathbf{n}$ as well as **approximated cost** from $\mathbf{n}$ to $\mathbf{n}_F$. 


$$
f(\mathbf{n}) = g(\mathbf{n}) + h(\mathbf{n})\;,
$$

where:
- $g(\mathbf{n})$ is the <span style="color:#f88146">cost of the current path</span> $\Gamma_{\mathbf{n}_0,\mathbf{n}}$ from $\mathbf{n}_0$ to $\mathbf{n}$. Note that the graph is a tree, i.e. we only hold backpointers encoding the path with minimal cost from $\mathbf{n}_0$ to $\mathbf{n}$. Obviously, it satisfies $g(\mathbf{n}_0)=0$. 
- $h(\mathbf{n})$ is an <span style="color:#f88146">**estimation** of the cost</span> from $\mathbf{n}$ to $\mathbf{n}_F$. This function is the <span style="color:#f88146">**heuristic**</span> and it satisfies $h(\mathbf{n}_F)=0$.

```{prf:algorithm} General $A^{\ast}$
:label: Astar

**Inputs** Start node $\mathbf{n}_0$\
**Output** Target node $\mathbf{n}_F$ and path $\Gamma_{\mathbf{n}_0,\mathbf{n}_F}$ or FAILURE.

1. $\text{OPEN}\leftarrow \{\mathbf{n}_0\}$. 
2. **while** $\text{OPEN}\neq\emptyset$:
    1. $\mathbf{n}\leftarrow \arg\min_{\mathbf{n}\in \text{OPEN}}f(\mathbf{n})$
    2. $\text{OPEN}\leftarrow \text{OPEN}-\{\mathbf{n}\}$ // Remove from OPEN and put in CLOSE
    3. $\text{CLOSE}\leftarrow \text{CLOSE}\cup\{\mathbf{n}\}$ 
    4. **if** $\mathbf{n}=\mathbf{n}_F$ **return** ($\mathbf{n}_F$,     $\Gamma_{\mathbf{n}_0,\mathbf{n}_F}$)
    5. ${\cal N}_{\mathbf{n}},\{\Gamma_{\mathbf{n}_0,\mathbf{n}'\in {\cal N}_{\mathbf{n}}}\}\leftarrow \text{EXPAND}(\mathbf{n})$ // Generate neighbors and backpointers
    6. **for** $\mathbf{n}'\in {\cal N}_{\mathbf{n}}$: 

        1. **if** $\mathbf{n}'\not\in\text{OPEN}$ and $\mathbf{n}'\not\in\text{CLOSED}$:

            $f(\mathbf{n}')=g(\mathbf{n}') + h(\mathbf{n}')$ with $g(\mathbf{n}') = g(\mathbf{n}) + c(\mathbf{n},\mathbf{n}')$

        2. **if** $\mathbf{n}'\in\text{OPEN}$ or $\mathbf{n}'\in\text{CLOSED}$:
       
            $\Gamma_{\mathbf{n}_0,\mathbf{n}'}\leftarrow \text{REDIRECT}(\Gamma_{\mathbf{n}_0,\mathbf{n}'})$

        3. **if** $\mathbf{n}'\in\text{CLOSED}$ and Reditect($\mathbf{n}'$)=True:

            1. $\text{CLOSE}\leftarrow \text{CLOSE}-\{\mathbf{n}'\}$ // Reopen
            2. $\text{OPEN}\leftarrow \text{OPEN}\cup\{\mathbf{n}'\}$
            
        
4. **return** FAILURE
```

In summary, $A^{\ast}$ proceeds as follows:
1) It selects the best node $\mathbf{n}$ wrt $f(.)$ in the border to **expand** it. Only when it is select it is moved to CLOSE, not when it is expanded!
2) Expanding a node $\mathbf{n}$ means to **create a neighborhood** of states ${\cal N}_{\mathbf{u}}$.
3) The algorithm **attends** $\mathbf{n}\in{\cal N}_{\mathbf{u}}$ to determine whether $f(.)$ is needed and the backpointers that hold the minimal-cost path $\Gamma_{\mathbf{n}_0,\mathbf{n}'}$ must be adjusted. Excepcionally we way re-open a node. 
4) The algorithm ends either when we find the target $\mathbf{n}_F$ or OPEN is empty. 

As example of application of $A^{\ast}$ to the 8-puzzle problem (see next section) we have {numref}`8-puzzle-Man` 

```{figure} ./images/Topic3/8-puzzle-tree-Man-removebg-preview.png
---
name: 8-puzzle-Man
width: 500px
align: center
height: 600px
---
8-puzzle with Manhattan: Nodes: $271$, Expanded: $164$ 
```

where almost $300$ nodes are expanded, i.e. selected according to Step 2.1. The backpointers from $\mathbf{n}_F$ to $\mathbf{n}_0$ are shown in red and the intensity of the node is the value of the heuristic $h(\mathbf{n})$ (the larger the higher) as we see in the next section. 

### Heuristics
Let us give some details about how to build a basic heuristic. 

**8-Puzzle**. A well-known simplification of jigsaw puzzle problems consists of defining a state $\mathbf{n}$ as an $3\times 3$ matrix of tiles $1\ldots 8$ plus a 'space' named as $0$. Given an initial permutation $\mathbf{n}_0\in\Pi_{8\cup 0}$, the objective is to reach a final permutation $\mathbf{n}_f\in\Pi_{8\cup 0}$ <span style="color:#f88146">by **moving the space**: 'up', 'down', 'left' or 'right'*</span>. 

As we see in {numref}`8-puzzle-show`, moving the space is equivalent to a more human-mind way of moving one of the up to $4$ adjacent tiles to fill the space. In the example, we move from the current-state from the next-state by moving the $0$ left, i.e. by moving the $5$ right. 

```{figure} ./images/Topic3/8puzzle-show-removebg-preview.png
---
name: 8-puzzle-show
width: 800px
align: center
height: 300px
---
8-puzzle: States showing the Manhattan geometry of moves. 
```

Note that *diagonals are not allowed*. This is consistent with the [Taxicab geometry or Manhattan World](https://en.wikipedia.org/wiki/Taxicab_geometry). This provides a **natural heuristic** $h_{Manhattan}(.)$ for estimating the cost from the current state to the target: 

$$
h_{Manhattan}(\mathbf{n})=\sum_{i>0}\underbrace{|\mathbf{n}(i,x)-\mathbf{n}_F(i,x)|}_{\text{x-diff}(i)} + \underbrace{|\mathbf{n}(i,y)-\mathbf{n}_F(i,y)|}_{\text{y-diff}(i)}
$$

where $\mathbf{n}(i,x)$ and $\mathbf{n}(i,y)$ are the $x$ **(col)** and $y$ **(row)**  coordinates of the $i-$th tile (without the space). In the example we have 

| Tile (current-state) | $\mathbf{n}$ coords | $\text{x-diff} + \text{y-diff}$| Cumulative $h_{Manhattan}$|
|---|---|---|---|
| 1 | (2,2)  |abs(2 - 0) + abs(2 - 0) = 4 | 4 |
| 2 | (2,1)  |abs(2 - 0) + abs(1 - 1) = 2 | 6 |
| 3 | (2,0)  |abs(2 - 0) + abs(0 - 2) = 4 | 12 |
| 4 | (1,2)  |abs(1 - 1) + abs(2 - 0) = 2 | 14 |
| 5 | (1,1)  |abs(1 - 1) + abs(1 - 1) = 0 | 14 |
| 6 | (0,0)  |abs(0 - 1) + abs(0 - 2) = 3 | 17 |
| 7 | (0,2)  |abs(0 - 2) + abs(2 - 0) = 4 | 21 |
| 8 | (0,1)  |abs(0 - 2) + abs(1 - 1) = 2 | 23 |
|   |        |                            | 23 |

Obviously $\max h_{Manhattan}(\mathbf{n}) = 8\times 4 = 32$, since $4$ is the largest shortest path in the Taxicab geometry between a tile and its ideal position. Any coincidence in row or column with the ideal position reduces the global cost. For instance, note that tile $5$ is correctly posed and its contribution is $0$. 

Note that computing $h_{Manhattan}$ for the new state $\mathbf{n}'\in {\cal N}_{\mathbf{n}}$ (expanded from $\mathbf{n}$) is quite **incremental**. If $j$ is the 'moved tile' we have 

$$
h_{Manhattan}(\mathbf{n}')=h_{Manhattan}(\mathbf{n}) + \nabla h_{Manhattan}(\mathbf{n}')
$$

where, for the moved tile $j$ we have

$$
\nabla h_{Manhattan}(\mathbf{n}') = \underbrace{|\mathbf{n}'(j,x)-\mathbf{n}_F(j,x)|}_{\text{x-diff}'(j)} + \underbrace{|\mathbf{n}'(j,y)-\mathbf{n}_F(j,y)|}_{\text{y-diff}'(j)} - (\text{x-diff}(j) + \text{y-diff}(j))\;,
$$

which only implies a couple of subractions! 

For the above example, where $h_{Manhattan}(\mathbf{n})=24$, the move of tile $j=5$ to the left (desplacement of the space to the right) makes: 

$$
\begin{align}
\nabla h_{Manhattan}(\mathbf{n}') &= \underbrace{|\mathbf{n}'(5,x)-\mathbf{n}_F(5,x)|}_{\text{x-diff}'(5)} + \underbrace{|\mathbf{n}'(5,y)-\mathbf{n}_F(5,y)|}_{\text{y-diff}'(5)} - (0 + 0)\\
 &= \underbrace{|1-1|}_{\text{x-diff}'(j)} + \underbrace{|0-1|}_{\text{y-diff}'(j)} - (0 + 0)\\
  &= 0 + 1 - (0 + 0)\\
  &= 1\;.
\end{align}
$$

As a result, since $\nabla h_{Manhattan}(\mathbf{n}')>0$ (the **gradient** is positive) we have that $h_{Manhattan}(\mathbf{n}')>h_{Manhattan}(\mathbf{n})$ and the new solution is worse.

Note also that for any $\mathbf{n}$ in the 8-Puzzle, we have that its maximum neighborhood's size is $4$ ($|{\cal N}_{\mathbf{u}}|\le 4$). In the above case, for the current-move (left) only $3$ moves are possible: $j=5$, $j=6$ and $j=3$. Then, we have

| $j$ | New position |Ideal position |$\nabla h_{Manhattan}$|
|---|---|---|---|
| 5 | (1,0) | (1,1)  | 0 + 1 - (0 + 0) = 1 |
| 6 | (1,0) | (1,2)  | abs(1-1) + abs(0-2)- 3 = -1|
| 3 | (1,0) | (0,2)  | abs(1-0) + abs(0-2)- 3 = 0|

which shows that the best local decision is to move $j=6$ down to the space (negative gradient).
<br></br>
<span style="color:#d94f0b"> 
**Exercise**. Given the 'New State' in the above figure (center), 
**a)** Compute the value of $h_{Manhattan}$ for this configuration. **b)** Compute the gradients for all the possible moves. **c)** Identify the best move and update $h_{Manhattan}$ accordingly.
<br></br> 
Answer:
<br></br> 
**a)** $h_{Manhattan}(\mathbf{n})=24$ (can be deduced from above). The associated table is 
<br></br>
</span>
<span style="color:#d94f0b">
$
\begin{aligned}
&\begin{array}{|c|c|c|c|c|}
\hline \hline \text{Tile} &  \text{Initial} &  \text{Ideal} & \text{x-diff + y-diff} & \text{Cumulative}  \\
\hline 
0 & (1,1) & (2,2) & 1+1=2 & 2\\
1 & (2,2) & (0,0) & 2+2=4 & 6\\
2 & (2,1) & (0,1) & 2+0=2 & 8\\
3 & (2,0) & (0,2) & 2+2=4 & 12\\
4 & (1,2) & (1,0) & 0+2=2 & 14\\
5 & (1,0) & (1,1) & 0+1=1 & 15\\
6 & (0,0) & (1,2) & 1+2=3 & 18\\
7 & (0,2) & (2,0) & 2+2=4 & 22\\
8 & (0,1) & (2,1) & 2+0=2 & 24\\
\hline
\end{array}
\end{aligned}
$
</span>
<br></br>
<span style="color:#d94f0b">
**b)** Gradients $\nabla h_{Manhattan}(\mathbf{n}')$ for $\mathbf{n}'\in {\cal N}_{\mathbf{n}}$. There are $4$ neighboring tiles with $j=8,5,2,4$ respectively.
</span>
<br></br>
<span style="color:#d94f0b">
$
\begin{aligned}
&\begin{array}{|c|c|c|c|c|}
\hline \hline j &  \text{New} &  \text{Ideal} & \text{x-diff'(j)}+ \text{y-diff'(j)} & \text{x-diff(j)}+ \text{y-diff(j)} & \nabla h_{Manhattan}(\mathbf{n}')\\
\hline 
8 & (1,1) & (2,1) & |1-2|+|1-1|=1 & 2 & 1-2 = -1\\
5 & (1,1) & (1,1) & |1-1|+|1-1|=0 & 1 & 0-1 = -1\\
2 & (1,1) & (0,1) & |1-0|+|1-1|=1 & 2 & 1-2 = -1\\
4 & (1,1) & (1,0) & |1-1|+|1-0|=1 & 2 & 1-2 = -1\\
\hline
\end{array}
\end{aligned}
$
</span>
<br></br>
<span style="color:#d94f0b">
**c)** Best move? The above table shows that all moves are equally good. Why? A human would tend to move $5$ to the center. Such a move would end up in $8$, $5$ and $2$ in the correct column. However, since $8$ and $2$ are in an **inverted position**, the Manhattan heuristic is not able to run in favor of centering $5$. Even moving $4$ to the center and approach it to its ideal column is equally valid! 
</span>

[//]:https://www.tjhsst.edu/~rlatimer/ai2007/Korf-slides.pdf
[//]:https://cse.sc.edu/~mgv/csce580sp15/gradPres/HanssonMayerYung1992.pdf

**Improving Manhattan**. Inversions or <span style="color:#f88146">linear conflicts</span> (two tiles in the correct row or column but in inverted order) are powerful structural violations due to the symmetry of the problem. The <span style="color:#f88146">Manhattan distance is **blind** to linear conflicts</span> because it accounts for the shortest paths (in the Taxicab geometry) of each tile, independently of the others. 

Basically, the following algorithm, proposed by [Hanson et al](https://www.sciencedirect.com/science/article/abs/pii/002002559290070O) in Information Sciences 

```{prf:algorithm} Linear-Conflicts
:label: LC

**Inputs** A 8-puzzle state $\mathbf{n}$\
**Output** $h_{Manhanttan}(\mathbf{n}) + h_{LC}(\mathbf{n})$

1. **for** each row $r_i$ of $\mathbf{n}$:
    1. $lc(r_i,\mathbf{n})\leftarrow 0$
    2. **for** each col $j\in r_i$:
       1. Compute $C(j,r_i)$: the number of LCs with $j$. 
       2. **while** $\exists j: C(j,r_i)>0$: 
         
            1. Remove $k$ with maximal LCs in $r_i$
            2. $C(k,r_i)\leftarrow 0$
            3. **for** each col $j$ which has in conflict with $k$: $C(j,r_i)\leftarrow C(j,r_i)-1$
            4. $lc(r_i,\mathbf{n})\leftarrow lc(r_i,\mathbf{n}) + 1$
2. **repeat** 1 for cols and compute $lc(c_j,\mathbf{n})$
3. $h_{LC}(\mathbf{n})\leftarrow 2(\sum_{i}lc(r_i,\mathbf{n}) + \sum_{j}lc(c_j,\mathbf{n}))$

4. **return** $h_{Manhattan}(\mathbf{n}) + h_{LC}(\mathbf{n})$
```

accounts for linear conflicts (LCs) in the following way:
1) Analize row by row. If a LC is found, add $2$ to Manhattan. 
2) Repeat for columns. 

The basic idea of **Linear-Conflicts** is that the Manhattan heuristic is only worried about the independent shortest paths of each tile. In this regard, an inversion is seen as 'doubling' the shortest paths needed (extra effort) because they 'become in conflict'.

For instance, in {numref}`8-puzzle-show` we have the following cases:
1) 'Current State' (left-image). Concerning rows, only $r_1$ has an inversion ($5-4$) and this results in a penalization of $2$. Concerning columns, only $c_1$ has inversion, but we have $2$ conflicts per tile  (e.g. $8-5$ and $5-2$). This results in two iterations of the while loop to make all zeros and the resulting penalization is $4$. Then 

$$
h_{Manhattan}(\mathbf{n}) + h_{LC}(\mathbf{n}) = 23 + 2 + 4 = 29\;.
$$

2) 'New State' (center). Regarding the rows, we have a unique conflict ($5-4$) as before (note that the space does not count). Actually, due to the presence of the space in the center, we have only a column conflict. As a result

$$
h_{Manhattan}(\mathbf{n}) + h_{LC}(\mathbf{n}) = 24 + 2 + 2 = 28\;.
$$

#### Admissible heuristics
Heuristics $h(\mathbf{n})$ <span style="color:#f88146">**approximate** the true (but unknown)</span> cost $h^{\ast}(\mathbf{n})$ from $\mathbf{n}$ to the target $\mathbf{n}_F$. There are formal reasons recommending 

$$
h(\mathbf{n})\le h^{\ast}(\mathbf{n})\;\;\forall \mathbf{n}\in\Omega, 
$$

which is <span style="color:#f88146">called the **admissibility**</span> of the heuristic. The intuition behind admissibility is that as far as we make 'optimistic' approximations we are sure that $A^{\ast}$ will find the target $\mathbf{n}_F$. Otherwise, i.e. considering a state worse that it really is (being pessimisit), $A^{\ast}$ may skip it and produce a sub-optimal solution if any. 

Some formal considerations (see Pearl's book for more details): 
1) <ins>Admissibility ensures</ins> that at any time before termination, there will be *at least* a node $\mathbf{n}\in\text{OPEN}$ whose expansion will lead to find $\mathbf{n}_F$. 
2) This can be expressed in terms of $f(\mathbf{n})\le C^{\ast}$, where $C^{\ast}$ is the optimal cost from $\mathbf{n}_0$ to $\mathbf{n}_F$ and it is consistent with the <ins>principle of optimality</ins> (all parts of an optimal path are also optimal).

**Is Manhattan admissible?** Yes, it is. But why?
1) Remember that $h_{Manhattan}(\mathbf{n})$ adds the shortest paths from any tile to its ideal position in the Taxicab geometry (no diagonals), while *assuming no obstacles in between*.
2) For a particular tile, it is impossible to make less movements since there are frequently other tiles in between. 

**What about the admissibility of $h_{Manhattan}(\mathbf{n}) + h_{LC}(\mathbf{n})$?** Well, this is a bit tricky. 
1)  We know that $h_{Manhattan}(\mathbf{n})$ is admissible. Thus, the above question reduces whether to penalizing LCs as we do is still admissible.
2) The proof is reduced to test whether at each 'line' (row or column) we calculate the <ins>minimum number of tiles which must take non-shortest paths</ins>. 
3) The algorithm removes conflicting tiles and each removal counts $2$ moves, which is the minimal number of moves to solve a LC. 
4) The big question is whether the LCs in a line are independent of those in another. They really are because removing a tile for solving a conflict will not affect the others. If the tile is not in its ideal solution this is obvious. Otherwise, moving it out of this line will not affect possible conflicts in the perpendicular line since we leave a space. 

Thus, $h_{Manhattan}(\mathbf{n}) + h_{LC}(\mathbf{n})$ is still admissible!

#### Pruning power 
Let us start by defining the <span style="color:#f88146">**heuristic power**</span> of a given $h(\mathbf{n})$ as the <span style="color:#f88146">*number of nodes expanded* for the same problem instance</span>. 

In {numref}`8-puzzle-Man`, we showed that for $h_{Manhattan}(.)$, $A^{\ast}$ generates $271$ nodes, from which $164$ are expanded. Remember that 'expansion' of $\mathbf{n}$ implies that this node is 'selected' from OPEN according to minimizing $f(\mathbf{n})$. 

However, $h_{Manhattan}(\mathbf{n}) + h_{LC}(\mathbf{n})$ leads to $198$ nodes, where $120$ are expanded (see {numref}`8-puzzle-LC`) for the same initial state which is:

$$
\mathbf{n}_0 = [2, 3, 0, 1, 8, 6, 5, 7, 4]
$$

where we linearize the $3\times 3$ puzzle by stacking their rows. 

```{figure} ./images/Topic3/8-puzzle-tree-LC-removebg-preview.png
---
name: 8-puzzle-LC
width: 500px
align: center
height: 600px
---
8-puzzle with LCs: Nodes: $198$, Expanded: $120$ 
```

Intuitively, we see that $h_{Manhattan}(\mathbf{n}) + h_{LC}(\mathbf{n})$ is <span style="color:#f88146">**more informed**</span> than the plain $h_{Manhattan}(\mathbf{n})$. More formally, $h_2(.)$ is more informed than $h_1(.)$ if both are admissible and 

$$
h_2(\mathbf{n}) > h_1(\mathbf{n})\;\;\forall \mathbf{n}\in\Omega\;. 
$$

This results in the fact that the number of nodes expanded by $A^{\ast}$ with $h_2(.)$ is **upper-bounded** by those expanded with $h_1(.)$. In other words, the <span style="color:#f88146">**pruning power** of more pessimistic admissible heuristic is larger</span>. 

The rationale exposed in Pearl's book is as follows: 
1) $h^{\ast}(\mathbf{n})$ is a **perfect discriminator**, i.e. it provides the minimal number of expansions. 
2) Since $h_2(\mathbf{n}) \ge h_1(\mathbf{n})$, we have 

$$
h_1(\mathbf{n}) \le h_2(\mathbf{n})\le h^{\ast}(\mathbf{n})\;,
$$

under admissibility.

This is a consequence of <span style="color:#f88146">**Nilsson's theorem**: *Any node expanded by $A^{\ast}$ cannot have an $f$ value exceeding the optimal cost $C^{\ast}$*</span> i.e. 

$$
f(\mathbf{n})\le C^{\ast}\;\;\text{for all expanded nodes}\;.
$$

In other words, *every node on OPEN for which $f(\mathbf{n})<C^{\ast}$ will be eventually expanded by $A^{\ast}$*.

Remember that we cannot select a non-expanded node to determine whether we have found $\mathbf{n}_F$. This node must be a yet expanded node and this means that is satisifies $f(\mathbf{n})\le C^{\ast}$. In other words, the nodes in ${\cal S}=\{\mathbf{n}:f(\mathbf{n})>C^{\ast}\}$ are definitely **excluded from expansion**. This means that better informed heuristics provide better upper bounds and larger **excluded-from-expansion sets** (see a more formal proof in Pearl's page 81). 

**Relaxation**. Since there are configuations where we do not have LCs, what we have is $h_{Manhattan}(\mathbf{n})\le h_{Manhattan}(\mathbf{n}) + h_{LC}(\mathbf{n})$ instead of a $<$. Then, we can relax a bit the requirement of pruning power and admit $\le$. 

**Computational cost**. It seems a kind of obvious that more informed heuristic require more computational cost, for instance, $h_{LC}$ takes $O(N^{1.5})$ whereas $h_{Manhattan}$ takes $O(N)$ where $2\sqrt{N+1}$ is the number of lines (horizontal and vertical) of the puzzle. 

Thus, <span style="color:#f88146">pruning power (spatial complexity) and computational cost (temporal complexity) are tied by a strict trade-off</span>. 'Ask me for memory or for computer power', quoted Steve Jobbs. 


### Failure condition
Remember, that the **failure condition** of $A^{\ast}$ is $\text{OPEN}=\emptyset$, i.e. there are no more nodes to expand and $\mathbf{n}_F$ was not found. This cannot happen *unless* the target cannot be found for any reason. 

[//]:https://math.stackexchange.com/questions/293527/how-to-check-if-a-8-puzzle-is-solvable

**8-Puzzle and even LCs**. Consider the $\mathbf{n}_0$ used in the previous example: 

$$
\mathbf{n}_0 = [2, 3, 0, 1, 8, 6, 5, 7, 4]\;,
$$

reformatted properly as a $3\times 3$ matrix: 

$$
\mathbf{n}_0=\begin{bmatrix}
2 & 3 & 0\\
1 & 8 & 6\\
5 & 7 & 4\\
\end{bmatrix}
$$

has $0$ LCs (herein $0$ is 'even'). 

However the following initial state: 

$$
\mathbf{n}_0=\begin{bmatrix}
1 & 2 & 3\\
4 & 5 & 6\\
0 & 8 & 7\\
\end{bmatrix}
$$

has $1$ LC (the one given by $8-7$). Well, <span style="color:#f88146">as $1$ is **'odd'**, this 8-puzzle **cannot** be solved!</span> Let us see why. 

[//]:https://puzzling.stackexchange.com/questions/52110/8-puzzle-unsolvable-proof

**Permutations and parity**. Given the sequence $\pi_1=[1,4,3,2,5]$, the canonical order $\pi_0[1,2,3,4,5]$ can be obtained by a single (odd) interchance or <span style="color:#f88146">transposition</span> (simply interchanging $4$ and $2$). However undoing $\pi_2=[4,1,3,2,5]$, **sequentialy** requires two (even) moves: (1) first, interchange $4$ and $1$, thus arriving to $\pi_1=[1,4,3,2,5]$ and then (2) undo $\pi_1$ by interchanging $2$ and $4$ back to the canonical sequence. 

It can be proved that solving an even (odd) number of transpositions requires and even (odd) number of moves. A given permutation can be solved in, say $5$ moves even if it has $3$ transpositions, but what is invariant in the <span style="color:#f88146">**parity** of a permutation (odd or even) is always preserved</span>.

As a result, it is almost trivial to proof that <span style="color:#f88146">8-puzzles have **even** parity</span>. In other words, if the initial state has an odd parity, the puzzle is unsolvable! 

One may think that we can exploit the 'space' to change the parity of the permutations encoded by the 8-puzzle but:
1) Look that horizontal moves do not change the permutation at all. 
2) Only vertical moves change the permutations and we know that the original parity **must** be perserved. 
3) In other words, <span style="color:#f88146">*it is impossible to achieve a state with even parity from the target puzzle backwards!*</span>

Therefore, for avoiding the failure condition is convenient to run a parity check before calling to $A^{\ast}$.

### Pattern Databases
**Computational cost**. It seems a kind of obvious that more informed heuristic require more computational cost, for instance, $h_{LC}$ takes $O(N^{1.5})$ whereas $h_{Manhattan}$ takes $O(N)$ where $2\sqrt{N+1}$ is the number of lines (horizontal and vertical) of the puzzle: $N=8$ in the 8-Puzzle. 

Thus, <span style="color:#f88146">pruning power (spatial complexity) and computational cost (temporal complexity) are tied by a strict trade-off</span>. 'Ask me for memory or for computer power', quoted Steve Jobbs. However, even with this trade-off at hand, the time needed to solve N-Puzzles can be un-practical, since N-Puzzles are NP-hard problems in general due to the their permutational nature. 


[//]:https://web.cs.umass.edu/publication/docs/1987/UM-CS-1987-071.pdf

**Lookup Tables**. Let us try to <ins>pre-compute</ins> the <span style="color:#f88146">perfect discriminator</span> $h^{\ast}(\mathbf{n})$ to keep the number of expanded nodes in $\mathbf{A}^{\ast}$ at a minimum. 

For problems such as 8-Puzzle or Rubik where the <ins>final state is always the same</ins>, lookup tables become a useful tool. They work as follows: 

1) **Start** from the 'target' state and its distance to it (zero). 
2) **Extract** next state to explore from a QUEUE (push/pop FIFO operators). 
3) **Expand** the search tree using <span style="color:#f88146">BFS (Best-First Search)</span> and register the effective distance to the target. 
4) Create an **entry** for each 'new state' is found (not seen before). Store the state and its cost to the target: $T(\mathbf{n},\text{cost})$. 
5) **Stop** when not new state can be found. 

More algorithmically, we have

```{prf:algorithm} Lookup-Table
:label: LU

**Inputs** Goal node $\mathbf{n}_F$\
**Output** Lookup table $T:\Omega\rightarrow \mathbb{N}$

1. $\text{OPEN}\leftarrow \{(\mathbf{n}_F,0)\}$. 
2. $T(\mathbf{n}_F)=0$
3. **while** $\text{OPEN}\neq\emptyset$:
    1. $(\mathbf{n},d)\leftarrow \text{pop}(\text{OPEN})$
    2. ${\cal N}_{\mathbf{n}}\leftarrow \text{EXPAND}(\mathbf{n})$  
    3. **for** $\mathbf{n}'\in {\cal N}_{\mathbf{n}}$: 

        1. **if** $\mathbf{n}'\not\in T$:

            1. $T(\mathbf{n}')=d+1$

            2. $\text{OPEN}\leftarrow \text{push}(\text{OPEN},\mathbf{n}')$
                    
4. **return** $T$
```

Then, since $T(\mathbf{n})=h^{\ast}(\mathbf{n})$, lookup tables allow us to characterize the distribution of optimal distances. In {numref}`8-puzzle-LUT` we show that the 8-puzzle can be solve in $31$ moves. In addition, we see that: 
-  Most of the states have distances between 20-25 moves. 
-  The distribution is asymmetric towards large distances, but it is very far from being equiprobable. 
- If our target state changes (for instance placing the 'space' in the center), we should re-compute the table. 

```{figure} ./images/Topic3/8-puzzle-lookup-removebg-preview.png
---
name: 8-puzzle-LUT
width: 600px
align: center
height: 500px
---
Distribution of distances for the Lookup table of the 8-Puzzle
```

Interestingly, the fact that $T(\mathbf{n})=h^{\ast}(\mathbf{n})$ minimizes the number of expansions. For the same initial space as before we expand only $21$ nodes (see {numref}`8-puzzle-astar-lookup`).


```{figure} ./images/Topic3/8-puzzle-astar-lookup-removebg-preview.png
---
name: 8-puzzle-astar-lookup
width: 500px
align: center
height: 600px
---
8-puzzle with Lookup: Nodes: $39$, Expanded: $21$ 
```

However, note that the lookup table is huge in larger problems (e.g. Rubik's Cube) and cannot be applied to problems where the target state changes (e.g. graph matching, TSPs, etc.)


## Iterative Deepening
Iterative Deepening is the practical approach of $A^{\ast}$-inspired techiques fueled by a huge lookup table. This has been the *de facto* standard for thr Rubik's Cube until very recently! 

### Mixed Strategies  
We should see $A^{\ast}$ as a **mixed strategy** between BFS (Breath-First-Search) and DFS (Deep-First Seatch). Actually: 
- <ins>BFS results from seting $h(\mathbf{n})=0$ (only $g(\mathbf{n})$ counts)</ins>. This result in an innaceptable memory requirement.  
- <ins>DFS results, however, from setting $g(\mathbf{n})=0$ instead (only $h(\mathbf{n})$ counts)</ins>, and expanding the nodes until a given depth cutoff $d$ is reached. This solves the problem of memory requirement but $d$ is generally unknown. 

### DF Iterative Deepening
This is a *brute force* algorithm that suffers neither the drawbacks of BFS nor DFS. It works as follows:
1) Perform DFS for $d=1$.
2) **Discard** the nodes generated in the $d$ search and make a new search for $d=d+1$
3) Do 2) until the target state is found. 

Discarding all the nodes generated for a given $d$ and start again for $d+1$ seems to be very inefficient. However, [Richard E. Korf](https://www.cse.sc.edu/~mgv/csce580f09/gradPres/korf_IDAStar_1985.pdf) proved that this is not the case. The algorithm is **asymptotically** optimal among brute-force tree searches in terms of space, time and the length of the solution. 

The proof is quite ilustrative of how **branching processes** work in practice. 
1) Consider a tree starting at the root $\mathbf{n}_0$ and a constant branching factor $b=|{\cal N}_{\mathbf{n}}|$.
2) Then, the total number of nodes generated at depth $d$ are: 

$$
S_b = b^0 + b^1 + \ldots b^{d} = \sum_{i=0}^d b^d = b\left(\frac{1-b^{d+1}}{1-b}\right)\;,
$$

i.e. the sum of a geometric series with ratio $r = \frac{b^{d+1}}{b^{d}}=b$. For instance, is $b=2$ we have 

$$
S_2=2\frac{(1-2^{d+1})}{-1}=2^d - 1\;.
$$
 
 Well, when DF Iterative Deepening (DFID) is applied we have the following for depth $d$:
 - The root is generated $d$ times. 
 - The first level of successors is generated $d-1$ times. 
 - The $i-$th level of successors is generated $d-i$ times.
 - Level $d$ is generated only once.  
 
 Then the number of nodes generated up to level $d$ is: 

 $$
 (d-0)b^1 + (d-1)b^2 + \ldots (d-i)b^{i+1} + \ldots + 3b^{d-2} + 2b^{d-1}+ b^d
 $$

 Inverting the order we have 

 $$
 b^d + 2b^{d-1} + 3b^{d-2} + \ldots + db\;.
 $$

 Factoring $b^d$ yields

 $$
 b^d(1 + 2b^{-1} + 3b^{-2} + \ldots + db^{1-d})\;,
 $$

 and making $x=b^{-1}$ unveils an interesting series

 $$
 b^d(1 + 2x + 3x^2 + \ldots dx^{d-1})
 $$

which converges for $d\rightarrow\infty$ as follows

$$
b^d(1 + 2x + 3x^2 + \ldots )\rightarrow b^d(1-x)^{-2}\;\;\text{for}\; |x|<1\;. 
$$

Since $(1-x)^{-2}=(1-1/b)^{-2}$ is a constant that is independent of $d$, for $b>1$ we have that the **temporal complexity** of DFID is $O(b^d)$, basically that of BFS. This is because the geometric part of the series dominates the arithmetic part (there are so much nodes generated as depth increases that their number grows faster than their repeats).

Considering now the **space complexity**, since DFID is engaged in a DFS it only stores the nodes of the branch leading to the maximum depth, wich only takes $O(d)$.

Naturally, the **waste factor** is upper-bounded by $(1-1/b)^{-2}$, i.e. <span style="color:#f88146">the largest the branching factor $b$, the smaller is the maximal waste</span>. Taking derivatives, the rate of such decrease is $O(1/b^2)$. 

### ID$A^{\ast}$
[Iterative-Deepening $A^{\ast}$](https://en.wikipedia.org/wiki/Iterative_deepening_A*) results from combining DFID with a BFS such as $A^{\ast}$. The general idea can be summarized as follows:

1) Instead of repeating the search **blindly** as in DFID, ID$A^{\ast}$ expands many deep-first searches from the root $\mathbf{n}_0$ until one of them 'hits' the target $\mathbf{n}_F$ or it discovers that it is much far away. 
2) The depth of each of these searches is controled by a **bound** which starts with $h(\mathbf{n}_0)$ and cuts off a branch ending in $\mathbf{n}$ when $f(\mathbf{n})=g(\mathbf{n})+h(\mathbf{n}) > t$ where $t$ is a threshold. At each iteration, $t$ is the minimum of all the values that exceeded the current threshold (the less agressive excess).

The algorithm is as follows: 

```{prf:algorithm} ID$A^{\ast}$
:label: IDAstar

**Inputs** Root $\mathbf{n}_0$\
**Output** Path $\Gamma$ to target node $\mathbf{n}_F$ and Bound, NOT_FOUND or FAILURE

1. $\text{bound}\leftarrow h(\mathbf{n}_0)$. 
2. $\Gamma\leftarrow [\mathbf{n}_0]$
3. **while** True:
    1. $t \leftarrow \text{BoundedSearch}(\Gamma, 0, \text{bound})$
    2. **if** $t=$FOUND **then** **return** $\Gamma,\text{bound}$
    3. **if** $t=\infty$ **then** **return** NOT_FOUND
    4. $\text{bound}\leftarrow t$
```
Where $\text{BoundedSearch}$ is a recursive bounded DFS guided by $f = g + h$ as follows: 

```{prf:algorithm} $\text{BoundedSearch}$
:label: IDAstar2

**Inputs** Path, $g$ and $\text{bound}$\
**Output** $t$ or FOUND

1. $\mathbf{n}\leftarrow \text{last}(\Gamma)$ 
2. $f\leftarrow g + h(\mathbf{n})$
3. **if** $f>\text{bound}$ **then** **return** $f$ // Returns $f$ as $t$
4. **if** $\mathbf{n}=\mathbf{n}_F$ **then** **return** FOUND 
5. $\text{min}\leftarrow \infty$
6. **for** $\mathbf{n}'\in {\cal N}_{\mathbf{n}}$:
    1. **if** $\mathbf{n}'\not\in\Gamma$:
        1. $\Gamma\leftarrow \text{push}(\Gamma,\mathbf{n}')$

        2. $t \leftarrow \text{BoundedSearch}(\Gamma, g + c(\mathbf{n},\mathbf{n}'), \text{bound})$

        3. **if** $t=$FOUND **then** **return** FOUND

        4. **if** $t<\text{min}$ **then** $\text{min}\leftarrow t$

        6. $\Gamma\leftarrow \text{pop}(\Gamma)$ // Alternative DFS

7. **return** $\text{min}$ 
```

Some considerations: 
1) The search **succeeds** (returns FOUND) as soon as one of the paths expanded by the DFS reaches $\mathbf{n}_F$. 
2) If so, there will be other partial expanded paths, because thei **exceeded** the bound and did not find the target. 
3) If $h$ is **admissible**, ID$A^{\ast}$ always finds a solution of least cost if it exists!

This results in a even more 'skeletal' search (see {numref}`8-puzzle-IDA-lookup`) in comparision with $A^{\ast}$ with lookup table (see {numref}`8-puzzle-astar-lookup`).

```{figure} ./images/Topic3/8-puzzle-IDA-lookup-removebg-preview.png
---
name: 8-puzzle-IDA-lookup
width: 500px
align: center
height: 600px
---
8-puzzle with ID$A^{\ast}$ using Lookup: Nodes: $24$, Expanded: $24$ 
```

### ID$A^{\ast}$ for Rubik
ID$A^{\ast}$ with lookup table has been the standard approach to solve Rubik's Cube (RC) until very recently. Remember that the size of the search space is 

$$
|\Omega| = 43,252,003,274,489,856,000\;,
$$

which requires $128$ GB of memory! If you can access a Google Colab account with a 51 GB limit, we can only account for moves up to length $8$. In {numref}`Rubik-LUT` we show the distances of $8\times 10^6$ moves. Note the exponential increment in the number of nodes with distance!

```{figure} ./images/Topic3/Rubik-Table-Photoroom.png
---
name: Rubik-LUT
width: 600px
align: center
height: 500px
---
(Some) Distribution of distances for the Lookup table of Rubik
```



[Richard E. Korf](https://www.cs.princeton.edu/courses/archive/fall06/cos402/papers/korfrubik.pdf) addressed this problem in 1997 by applying ID$A^{\ast}$ making  interesting findings: 
1) The <ins>3D generalization of the Manhattan</ins> distance (number of moves required to correctly position and oriented each 'cubie'), again considering the cubies independent. The sum of the moves of all cubies is divided by $8$ to ensure admissibility. 
2) <ins>A better heuristic</ins> is to take the maximum of the Manhattan distances of the corner cubies (3 orientations each) and the edge cubies (2 orientations each). The expected distance for the edge cubies is $5.5$ whereas that of the corner ones is $3$. 
3) Another solution consists of computing <ins>partial lookup tables storing Manhattan distances</ins> (e.g. for the corner cubies, for the edge cubies, etc) and combine them. 

However, the <ins>above solutions are not enough to contain the above combinatorial explosion</ins> and ID$A^{\ast}$ is only able to compute some movemens per day!