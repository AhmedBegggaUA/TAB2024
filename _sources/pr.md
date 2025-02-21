# Graph Matching with Topological Features

## Practical Session 1.4: Enhanced Graph Matching Using Graph Embeddings

## Introduction
In our previous session, we explored basic graph matching using spatial coordinates and the Hungarian Algorithm. While this approach provides a foundation for matching keypoints between images, it only considers geometric distances. In this session, we'll enhance our matching by incorporating topological features using node2vec and commute times embeddings.

## Theoretical Background

### Node2vec
Node2vec is an algorithmic framework for learning continuous feature representations for nodes in networks. It maps nodes to a low-dimensional space of features that maximizes the likelihood of preserving network neighborhoods of nodes. The key advantages are:
- Captures structural equivalence and homophily
- Flexible random walk strategy
- Scalable to large networks

### Commute Times Embedding
The commute time between two nodes is the expected number of steps a random walker takes to go from one node to the other and return. Commute times embedding:
- Provides a robust measure of node similarity
- Is less sensitive to small perturbations in graph structure
- Captures global graph properties

## Enhanced Matching Algorithm

We'll modify our previous approach by:
1. Computing node embeddings using both node2vec and commute times
2. Creating a combined similarity matrix using both spatial and topological features
3. Applying the Hungarian Algorithm to find optimal matches

## Exercise 1: Implement Node2vec Embedding

```python
import networkx as nx
from node2vec import Node2Vec
import numpy as np

def create_graph_from_keypoints(keypoints, adj_matrix):
    """
    Create a NetworkX graph from keypoints and adjacency matrix
    
    Args:
        keypoints: Array of shape (N, 2) containing keypoint coordinates
        adj_matrix: Binary adjacency matrix of shape (N, N)
    
    Returns:
        G: NetworkX graph with node attributes
    """
    G = nx.from_numpy_array(adj_matrix)
    
    # Add spatial coordinates as node attributes
    for i in range(len(keypoints)):
        G.nodes[i]['pos'] = keypoints[i]
    
    return G

def compute_node2vec_embeddings(G, dimensions=128, walk_length=30, num_walks=200):
    """
    Compute node2vec embeddings for the graph
    
    Args:
        G: NetworkX graph
        dimensions: Embedding dimension
        walk_length: Length of each random walk
        num_walks: Number of random walks per node
    
    Returns:
        embeddings: Dictionary mapping node IDs to their embeddings
    """
    # Initialize node2vec model
    node2vec = Node2Vec(
        G,
        dimensions=dimensions,
        walk_length=walk_length,
        num_walks=num_walks,
        workers=4
    )
    
    # Train the model
    model = node2vec.fit(window=10, min_count=1)
    
    # Get embeddings
    embeddings = {node: model.wv[str(node)] for node in G.nodes()}
    
    return embeddings
```

## Exercise 2: Implement Commute Times Embedding

```python
def compute_commute_times_embedding(adj_matrix, dim=128):
    """
    Compute commute times embedding
    
    Args:
        adj_matrix: Adjacency matrix
        dim: Number of dimensions for the embedding
    
    Returns:
        embedding: Matrix of shape (N, dim) containing node embeddings
    """
    # Compute the graph Laplacian
    D = np.diag(np.sum(adj_matrix, axis=1))
    L = D - adj_matrix
    
    # Compute eigenvalues and eigenvectors of the Laplacian
    eigenvals, eigenvecs = np.linalg.eigh(L)
    
    # Remove the first eigenvalue/eigenvector (corresponding to constant function)
    eigenvals = eigenvals[1:dim+1]
    eigenvecs = eigenvecs[:, 1:dim+1]
    
    # Scale eigenvectors by inverse square root of eigenvalues
    scaling = 1 / np.sqrt(eigenvals)
    embedding = eigenvecs * scaling
    
    return embedding
```

## Exercise 3: Enhanced Graph Matching

```python
def enhanced_spatial_matching(kpts1, kpts2, adj_matrix1, adj_matrix2):
    """
    Perform enhanced matching using both spatial and topological features
    
    Args:
        kpts1, kpts2: Arrays of keypoints
        adj_matrix1, adj_matrix2: Adjacency matrices
    
    Returns:
        matching: Binary matching matrix
    """
    # Create graphs
    G1 = create_graph_from_keypoints(kpts1, adj_matrix1)
    G2 = create_graph_from_keypoints(kpts2, adj_matrix2)
    
    # Compute embeddings
    node2vec_emb1 = compute_node2vec_embeddings(G1)
    node2vec_emb2 = compute_node2vec_embeddings(G2)
    
    commute_emb1 = compute_commute_times_embedding(adj_matrix1)
    commute_emb2 = compute_commute_times_embedding(adj_matrix2)
    
    # Create cost matrix combining different features
    n1, n2 = len(kpts1), len(kpts2)
    cost_matrix = np.zeros((n1, n2))
    
    for i in range(n1):
        for j in range(n2):
            # Spatial distance
            spatial_dist = np.linalg.norm(kpts1[i] - kpts2[j])
            
            # Node2vec similarity
            node2vec_dist = np.linalg.norm(
                node2vec_emb1[i] - node2vec_emb2[j]
            )
            
            # Commute times similarity
            commute_dist = np.linalg.norm(
                commute_emb1[i] - commute_emb2[j]
            )
            
            # Combine distances with weights
            cost_matrix[i,j] = (
                0.4 * spatial_dist +
                0.3 * node2vec_dist +
                0.3 * commute_dist
            )
    
    # Apply Hungarian algorithm
    row_ind, col_ind = linear_sum_assignment(cost_matrix)
    
    # Create matching matrix
    matching = np.zeros((n1, n2))
    matching[row_ind, col_ind] = 1
    
    return matching
```

## Exercise 4: Evaluation and Comparison

Compare the results of the enhanced matching with the previous spatial-only matching:

```python
def evaluate_matching_methods(image_pairs, categories):
    """
    Evaluate and compare different matching methods
    
    Args:
        image_pairs: List of image pair paths
        categories: List of category names
    
    Returns:
        results: DataFrame with evaluation metrics
    """
    results = []
    
    for category in categories:
        for img_pair in image_pairs[category]:
            # Load images and compute keypoints
            img1, img2, kpts1, kpts2 = load_and_preprocess_images(
                img_pair['img1'], 
                img_pair['img2'],
                img_pair['kpts1'],
                img_pair['kpts2']
            )
            
            # Compute adjacency matrices
            adj1 = delaunay_triangulation(kpts1)
            adj2 = delaunay_triangulation(kpts2)
            
            # Compute matchings
            spatial_matching = simple_spatial_matching(kpts1, kpts2)
            enhanced_matching = enhanced_spatial_matching(
                kpts1, kpts2, adj1, adj2
            )
            
            # Compute accuracies
            spatial_acc = compute_accuracy(spatial_matching, ground_truth)
            enhanced_acc = compute_accuracy(enhanced_matching, ground_truth)
            
            results.append({
                'Category': category,
                'Spatial_Accuracy': spatial_acc,
                'Enhanced_Accuracy': enhanced_acc
            })
    
    return pd.DataFrame(results)
```

## Expected Outcomes

The enhanced matching should show improvements in several scenarios:
1. When objects have similar structure but different scales
2. When there are perspective transformations
3. When local appearance varies but global structure is preserved

## Homework

1. Implement the enhanced matching algorithm using both node2vec and commute times embeddings
2. Compare the results with the previous spatial-only matching
3. Create visualizations showing the improvements
4. Write a brief report (max 2 pages) analyzing:
   - Accuracy improvements per category
   - Cases where topological features help most
   - Limitations of the enhanced approach

## References

1. Grover, A., & Leskovec, J. (2016). node2vec: Scalable feature learning for networks
2. Lovász, L. (1993). Random walks on graphs: A survey
3. Kuhn, H. W. (1955). The Hungarian method for the assignment problem