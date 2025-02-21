# Graph Construction for Matching

## Practical Session 1.2: Graph Matching: Graph Construction

### Introduction

In this session, we will extend our previous work on feature matching by constructing graphs based on keypoint annotations. You will implement two different graph construction methods:

1. **Delaunay Triangulation**
2. **k-Nearest Neighbors (k-NN)**

Your goal is to construct graphs using these methods and visualize them by overlaying the edges onto the images.

---

### **Graph Construction Algorithms**
#### **Delaunay Triangulation**
Delaunay Triangulation is a geometric technique that connects keypoints in a way that maximizes the minimum angle between edges, avoiding thin triangles. This method is commonly used for mesh generation and spatial interpolation.

**Algorithm Steps:**
1. Extract keypoints from the annotated dataset.
2. Apply Delaunay triangulation using the `scipy.spatial.Delaunay` module.
3. Construct the adjacency matrix based on the triangulation.

#### **k-Nearest Neighbors (k-NN)**
k-NN connects each keypoint to its `k` closest neighbors based on Euclidean distance.

**Algorithm Steps:**
1. Extract keypoints from the annotated dataset.
2. Compute the pairwise Euclidean distances between keypoints.
3. For each keypoint, select its `k` nearest neighbors.
4. Construct the adjacency matrix accordingly.

### **Implementation**

```{code-block} python
import numpy as np
import matplotlib.pyplot as plt
from scipy.spatial import Delaunay
def plot_image_with_graph(img, kpt, edges):
    """
    Function to visualize an image with keypoints and graph edges
    
    Parameters:
    - img: Input image
    - kpt: Keypoints (2, N) array
    - edges: List of (i, j) tuples representing graph edges
    """
    plt.imshow(img)
    plt.scatter(kpt[0], kpt[1], c='w', edgecolors='k')
    
    for i, j in edges:
        plt.plot([kpt[0, i], kpt[0, j]], [kpt[1, i], kpt[1, j]], 'r-')
    plt.show()

def delaunay_graph(kpts):
    """Constructs a Delaunay triangulation graph from keypoints."""
    # Implement Delaunay triangulation
    return list(edges)

def knn_graph(kpts, k):
    """Constructs a k-NN graph from keypoints."""
    # Implement k-NN graph construction
    return list(edges)
```

The result should be like this:

```{figure} ./images/duck_matching_delaunay.png
---
width: 800px
name: duck_matching
---
Visualizing the Duck Images with Keypoints and Delaunay Triangulation
```
```{figure} ./images/duck_matching_knn_3.png
---
width: 800px
name: duck_matching_knn_3
---
Visualizing the Duck Images with Keypoints and k-NN Graph (k=3)
```
```{figure} ./images/duck_matching_knn.png
---
width: 800px
name: duck_matching_knn_4
---
Visualizing the Duck Images with Keypoints and k-NN Graph (k=4)
```

### Exercise

In this session, we will extend our previous visualization work with the Willow-Object-Class Dataset by implementing and comparing different graph construction methods. You will need to create graph representations using both Delaunay triangulation and K-Nearest Neighbors (KNN) with varying k values.
```{warning}

The exercises involve multiple visualizations and graph construction methods. We strongly recommend creating reusable functions for:
1. Loading images and keypoints
2. Computing Delaunay triangulation
3. Computing KNN graphs with different k values
4. Plotting images with their corresponding graph structures
This modular approach will make your code more maintainable and easier to debug.
```

#### Exercise 1: Delaunay Triangulation

1. Load the Car, Face, Duck, Motorbike, and Winebottle images and keypoints from the Willow-Object-Class Dataset, at least 8 images, per category.
2. Implement the `delaunay_graph` function to construct a Delaunay triangulation graph.
3. Visualize the image with the Delaunay triangulation graph overlay, in a 2x4 grid.

In the end, you should have 8 images, each with the Delaunay triangulation graph overlayed.

#### Exercise 2: k-Nearest Neighbors (k-NN)

1. Load the Car, Face, Duck, Motorbike, and Winebottle images and keypoints from the Willow-Object-Class Dataset, at least 8 images, per category.

2. Implement the `knn_graph` function to construct k-NN graphs with varying k values (e.g., k=3, k=5, k=7).

3. Visualize the images with the k-NN graphs overlayed, in a 2x4 grid for each k value.



### Summary

In this session, you learned about graph construction methods for matching keypoints in images. You implemented Delaunay triangulation and k-NN graph construction algorithms and visualized the graphs overlayed on images from the Willow-Object-Class Dataset. These graph representations will be used in the next session to perform graph matching and alignment tasks.