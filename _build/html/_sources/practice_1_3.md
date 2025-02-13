# Graph Matching

## Practical Session 1.3: Graph Matching: Hungarian Algorithm or Munkres Algorithm

## Introduction
The Hungarian Algorithm, developed by Harold Kuhn in 1955, is an optimization algorithm that solves the assignment problem in polynomial time $O(n^3)$. This algorithm finds the optimal way to assign n tasks to n workers, minimizing the total cost of the assignments.

The name "Hungarian" comes from the fact that it was based on the earlier works of two Hungarian mathematicians: Dénes König and Jenő Egerváry.

## Problem Definition
Given:
- A cost matrix C of size n×n where C[i,j] represents the cost of assigning worker i to task j
- The goal is to find an optimal assignment that minimizes the total cost
- Each worker can be assigned to exactly one task and each task must be assigned to exactly one worker

## Basic Principles
The algorithm works by transforming the cost matrix through a series of steps:
1. Row and column reductions to create zeros
2. Finding a maximum matching using these zeros
3. Creating additional zeros if needed by modifying the matrix

## Pseudocode
```
FUNCTION HungarianAlgorithm(cost_matrix):
    # Step 1: Row Reduction
    FOR each row in cost_matrix:
        min_value = minimum value in row
        subtract min_value from each element in row

    # Step 2: Column Reduction
    FOR each column in cost_matrix:
        min_value = minimum value in column
        subtract min_value from each element in column

    WHILE optimal assignment not found:
        # Step 3: Cover zeros with minimum lines
        lines = find_minimum_lines_to_cover_zeros()
        
        IF number of lines equals n:
            optimal assignment found
            BREAK
        
        # Step 4: Create additional zeros
        min_uncovered = find_minimum_uncovered_value()
        FOR each uncovered element:
            subtract min_uncovered
        FOR each element covered twice:
            add min_uncovered

    # Step 5: Find optimal assignment
    assignment = zeros_matrix(n,n)
    FOR each marked zero in final matrix:
        assignment[row,col] = 1
    
    RETURN assignment
```

## Algorithm Steps in Detail
1. **Matrix Reduction**
   - Subtract row minima
   - Subtract column minima
   - This creates at least one zero in each row and column

2. **Zero Covering**
   - Find minimum number of lines to cover all zeros
   - If number of lines equals matrix dimension, solution found
   - Otherwise, proceed to step 3

3. **Matrix Modification**
   - Find smallest uncovered value
   - Subtract it from all uncovered elements
   - Add it to elements covered twice
   - This creates new zeros

4. **Optimal Assignment**
   - Select zeros such that each row and column has exactly one selected zero
   - These selections represent the optimal assignments

## Time Complexity
- Overall complexity: $O(n^3)$
- Space complexity: $O(n^2)$
- Where n is the dimension of the cost matrix

This algorithm guarantees finding an optimal solution to the assignment problem, making it a fundamental tool in operations research and combinatorial optimization.




## Exercise

In the previous session, we learned about graph construction methods for matching keypoints in images. In this session, we will implement the Hungarian Algorithm to solve the assignment problem in graph matching. We will use the keypoints extracted from images to create a cost matrix and find the optimal assignment of keypoints between two images.

### Exercise 1: Implement the Hungarian Algorithm for Graph Matching

Implement the Hungarian Algorithm to find the optimal assignment of keypoints between two images, using the cost matrix created from the Euclidean distances between keypoints, and you must use the `scipy.optimize.linear_sum_assignment` function to solve the assignment problem.

```{code-block} python
import numpy as np
from PIL import Image
import scipy.io as sio
import matplotlib.pyplot as plt
from scipy.spatial import Delaunay 
from scipy.optimize import linear_sum_assignment

def load_and_preprocess_images(img_path1, img_path2, mat_path1, mat_path2):
    """
    Carga y preprocesa pares de imágenes y sus keypoints
    """
    return img1, img2, kpts1, kpts2
def delaunay_triangulation(kpt):
    """
    Generate adjacency matrix based on Delaunay triangulation
    """
    return A

def simple_spatial_matching(kpts1, kpts2):
    """
    Realiza matching entre puntos usando distancia euclidiana y algoritmo húngaro optimizado
    
    Args:
        kpts1: Array de keypoints del primer grafo (2xN)
        kpts2: Array de keypoints del segundo grafo (2xN)
    
    Returns:
        matching: Matriz binaria donde matching[i,j]=1 indica correspondencia entre puntos
    Hints:
        - Primero deberás crear una matriz de costes basada en distancias euclidianas,
        cuyo tamaño será (n1, n2) donde n1 y n2 son el número de keypoints en cada grafo.
        - Seguidamente, deberás rellenar dicha matriz con las distancias euclidianas entre
        los keypoints de ambos grafos.
        - A continuación, deberás aplicar el algoritmo húngaro usando la función linear_sum_assignment, que recibe la matriz de costes y devuelve los índices de los puntos emparejados.
        - Finalmente, deberás crear una matriz de matching a partir de los índices obtenidos.

    """
    return matching

def visualize_matching_full(img1, img2, kpts1, kpts2, adj_matrix1, adj_matrix2, matching):
    """
    Visualiza el matching completo entre dos grafos
    """

```
### Exercise 2: Visualize the Optimal Assignment

Visualize the optimal assignment by connecting the matched keypoints with lines on the images.
```{figure} ./images/duck_matching_full_bueno.png
---
width: 800px
name: duck_matching_good
---
Visualizing the Duck Images with Keypoints, Delaunay Triangulation and Matching (Good Result)
```
```{figure} ./images/duck_matching_full_malo.png
---
width: 800px
name: duck_matching_bad
---
Visualizing the Duck Images with Keypoints, Delaunay Triangulation and Matching (Bad Result)
```
### Exercise 3: Evaluate the Matching Results

Finally, we ask you to extract the accuracy of the matching results. The accuracy is defined as the number of correctly matched keypoints divided by the total number of keypoints. As you know, you have 5 categories of images, so you should evaluate the accuracy for each category, and create a csv file with the results of each category, reporting the mean accuracy for each category and the standard deviation.
