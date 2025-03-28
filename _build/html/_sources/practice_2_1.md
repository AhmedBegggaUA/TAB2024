# Clustering

## Practical Session 2.1: Deterministic Annealing

### Introduction

In this session, we will explore the concept of deterministic annealing, a technique used for clustering data points into groups. Deterministic annealing is an optimization algorithm that extends the idea of simulated annealing to clustering problems. It allows us to find the optimal clustering of data points by gradually reducing the temperature parameter.

### Deterministic Annealing
[Deterministic Annealing (DA)](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=726788) is a search technique easily applicable to the central clustering problem (see more details in [Keneth Rose's PhD Thesis](https://thesis.library.caltech.edu/2858/1/Rose_k_1991.pdf)). 

The basic idea of DA is that for each value of $\beta=\frac{1}{T}$ we <span style="color:#f88146">**interate two phases**</span> for minimizing
the free energy $F_{\beta}({\cal C})$:

1) **Expectation**. For <ins>fixed centers</ins> $\mathbf{c}_i$, we estimate the <span style="color:#f88146">**probability**</span> that any point $\mathbf{x}_a$ "belongs" to each cluster ("membership"). We denote such probabilities as $\langle \mathbf{M}_{ai} \rangle$ and they are given by: 

$$
\langle \mathbf{M}_{ai} \rangle = \frac{\exp(-\beta D(\mathbf{x}_a,\mathbf{c}_i))}{\sum_{i'=1}^k \exp(-\beta D(\mathbf{x}_a,\mathbf{c}_{i'}))}\;.
$$

2) **Update**. For <ins>fixed probabilities</ins> $\langle \mathbf{M}_{ai} \rangle$, we update the <span style="color:#f88146">**centers**</span>. If $D(\mathbf{x}_a,\mathbf{c}_i)=||\mathbf{x}_q-\mathbf{c}_i||^2$ (squared Euclidean distance), then we have 

$$
\mathbf{c}_i =  \frac{\sum_{a=1}^m \mathbf{x}_a \langle \mathbf{M}_{ai}\rangle}{\sum_{a=1}^m\langle \mathbf{M}_{ai}\rangle}\;,
$$

i.e. the new ("fuzzy") centers are the expected centers according to the fixed probabilities or ("memberships"). 

Some considerations about the above algorithm: 
- It is **deterministic**, i.e. we do not draw random numbers at any step of the algoorithm.
- The **initial (inverse) temperature** $\beta=1/T_{max}$, where $T_{max}=T(0)$, is used as in SA, to make all the assignments almost equally probable: note that $e^{-\beta D(\mathbf{x}_a,\mathbf{c}_i)}\rightarrow 1$, if $\beta\rightarrow 0$. Under these conditions, we have $\langle M_{ai}\rangle\approx 1/k$.
- **Initialization**. $T_{max}$ is set to the maximum variance $\sigma^2_{max}$ of  the data. If the data is multidimensional, as it happens usually, $\sigma^2_{max}$ has an spectral interpretation (PCA).
- However, as the algorithm evolves, $\beta$ increases and the **exponential decay becomes more selective**: given $D(\mathbf{x}_a,\mathbf{c}_i)\le D(\mathbf{x}_a,\mathbf{c}_j)+\alpha$, the second distance decays exponentially faster than the first as $\beta$ increases.
- **Alternating Expectation and Update**. Given the probabilities $\langle M_{ai}\rangle$ we update the centers $\mathbf{c}_i$ and then we re-compute the probabilities until a "fixed point" (stable assignment/centers) is reached. 
- **Convervence**. The algorithm converges to the nearest local optimum of the free energy to the initialization point ${\cal C}^0$. We add the condition $\sum_{ai}|\langle \mathbf{M}_{ai}\rangle - \langle \mathbf{M}^{old}_{ai}\rangle|\le\epsilon$ with $\epsilon>0$, which means that the algorithm stops if the $\langle \mathbf{M}_{ai}\rangle$ are stable enough. For instance, if we start by setting $\langle M_{ai}\rangle\approx 1/k$, the algorithm only performs a single iteration. Why? Because the centers in step 4.2. are not modified at all.
- **Complexity**. Each iteration takes $O(mk)$ and the number of iterations can be accelerated by a faster annealing schedule. 
- **Outputs**. The algorithm returns the best centers ${\cal C}^{\ast}$ and the optimal assignments $\langle \mathbf{M}_{ai}^{\ast}\rangle$ where a point $\mathbf{x}_a$ is assigned to the cluster centered at $\mathbf{c}_{i'}$ if: 

$$
i' = \arg\max_{1\le i\le k}\langle \mathbf{M}_{ai}^{\ast}\rangle\;.
$$

## Exercise 1: 

Implement a function that generates points from Gaussian distributions. The function should take the following parameters:

```{code-block} python
def generar_datos_gaussianos(n_muestras=100, n_clusters=3, centros=None):
    """
    Genera datos sintéticos siguiendo distribuciones gaussianas de forma simplificada.
    
    Parámetros:
    -----------
    n_puntos_por_cluster : int
        Cantidad de puntos para cada cluster
    n_clusters : int
        Número de clusters a generar
    centros : array o None
        Coordenadas de los centros (x,y).
    
    Retorna:
    --------
    X : array
        Coordenadas (x,y) de todos los puntos
    y : array
        Etiqueta del cluster al que pertenece cada punto
    """
    return X, y
```

After generating the data, plot the points using different colors for each cluster. And the result should be like this:

```{figure} ./images/datos_sinteticos.png
---
width: 800px
name: gaussian_data
---
Visualización de datos generados con distribuciones gaussianas
```


## Exercise 2: Implementing Deterministic Annealing

Implement the Deterministic Annealing algorithm for clustering. You can use the following template:

```{code-block} python
import numpy as np
class DeterministicAnnealing:
    def __init__(self, n_clusters=3, T_min=0.01, T_max=5.0, epsilon=1e-6, max_iter=100):
        """
        Implementación del algoritmo Deterministic Annealing para clustering.
        
        Parámetros:
        -----------
        n_clusters : int
            Número de clusters a encontrar
        T_min : float
            Temperatura mínima para detener el enfriamiento
        T_max : float
            Temperatura inicial
        epsilon : float
            Umbral de convergencia para la matriz de pertenencia
        max_iter : int
            Número máximo de iteraciones
        """
        self.n_clusters = n_clusters
        self.T_min = T_min
        self.T_max = T_max
        self.epsilon = epsilon
        self.max_iter = max_iter
        self.centroids = None
        self.M = None  # Matriz de pertenencia
    
    def _calcular_distancia(self, X, centroides):
        """
        Calcula la matriz de distancias euclidianas al cuadrado entre puntos y centroides.
        
        Utilizamos la identidad: ||a-b||² = ||a||² + ||b||² - 2<a,b>
        donde <a,b> es el producto escalar.
        """
        
        
        # Evitar errores numéricos
        return np.maximum(distancias, 0)
    
    def _actualizar_pertenencia(self, X, centroides, T):
        """
        Actualiza la matriz de pertenencia (probabilidades).
        
        Esta es la parte clave del algoritmo deterministic annealing:
        la probabilidad de pertenencia depende de la distancia y la temperatura.
        """
        
        return M
    
    def _actualizar_centroides(self, X, M):
        """
        Actualiza las posiciones de los centroides basado en la matriz de pertenencia.
        
        Cada centroide se actualiza como un promedio ponderado de todos los puntos,
        donde los pesos son las probabilidades de pertenencia.
        """
    
    def fit(self, X):
        """
        Ajusta el modelo de Deterministic Annealing a los datos.
        
        Proceso:
        1. Inicializar centroides y temperatura
        2. Bucle principal de annealing:
           - Calcular matriz de pertenencia
           - Actualizar centroides
           - Reducir temperatura
           - Verificar convergencia
        """
        n_muestras, n_dimensiones = X.shape
        
        # Iniciamos con el bariocentro de los datos
        self.centroids = np.mean(X, axis=0, keepdims=True)
        self.centroids = np.repeat(self.centroids, self.n_clusters, axis=0)
        
        # Inicializar parámetros para el bucle
        t = 0  # Contador de iteraciones
        T = self.T_max  # Temperatura inicial
        convergencia = False
        M_anterior = np.ones((n_muestras, self.n_clusters)) / self.n_clusters
        
        # Bucle principal
        while not convergencia:
            # Actualizar matriz de pertenencia con temperatura actual
            
            # Actualizar centroides
            
            
            # Actualizar contador y temperatura
            
            
            # Verificar convergencia
            
            # Guardar matriz actual para próxima iteración
        
        return self
    
    def predict(self, X):
        """
        Predice el cluster para cada punto en X.
        
        Retorna el índice del cluster con mayor probabilidad para cada punto.
        """
    
    def fit_predict(self, X):
        """
        Ajusta el modelo y predice los clusters en un solo paso.
        """

```


##  Exercise 3: Testing the Algorithm

Generate synthetic data using the function implemented in Exercise 1 and apply the Deterministic Annealing algorithm to cluster the data. You should try at least the following configurations:

1. Generate data with 3 clusters and apply the algorithm with 6 clusters.
2. Generate data with 3 clusters and apply the algorithm with 2 clusters.
3. Generate data with 5 clusters and apply the algorithm with 5 clusters.
4. Generate data with 5 clusters and apply the algorithm with 2 clusters.
5. Generate data with 5 clusters and apply the algorithm with 10 clusters.

##  Exercise 4: Entropy

Implement a function that calculates the entropy of the clustering. The function should take the following parameters:

```{code-block} python
def calcular_entropia_por_punto(self):
        """
        Calcula la entropía condicional H(C|X), que es la entropía por punto.
        
        Esta entropía mide la incertidumbre en la asignación de clusters para cada punto.
        """
    
    def calcular_entropia_por_cluster(self):
        """
        Calcula la entropía condicional H(X|C), que es la entropía por cluster.
        
        Esta entropía mide qué tan uniforme es la distribución de puntos dentro de cada cluster.
        También devuelve un array con las entropías individuales de cada cluster.
        """

```
Now re-discuss the previous experiments using the entropy as a metric to evaluate the quality of the clustering. You are encouraged to create more experiments to test the algorithm.

##  Exercise 5: Automated Clustering

After implementing the Deterministic Annealing algorithm and the entropy function, you can now create a function that automatically clusters the data. So, now the question is: how to choose the number of clusters?
The criteria will be given by the elbow method, which is a heuristic used in cluster analysis to determine the optimal number of clusters in a dataset. The elbow method involves plotting the explained variance as a function of the number of clusters and looking for the "elbow" point where the rate of decrease sharply changes. 

The elbow point indicates the optimal number of clusters, as adding more clusters beyond this point does not significantly improve the explained variance.

```{figure} ./images/automated_entropy.png
---
width: 800px
name: entropy_data
---
Visualization of the entropy as a function of the number of clusters
```