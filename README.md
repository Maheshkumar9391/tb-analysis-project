# Comparative Analysis of Clustering Algorithms on X-Ray Image Data

This report details the technical methodology and results of applying three clustering algorithms (K-Means, Hierarchical, and DBSCAN) to a dataset of X-Ray images. The goal was to determine which method yielded the most visually distinct and analytically meaningful clusters.

---

## 1. Feature Extraction

Images were converted into numerical feature vectors using a combination of HOG and LBP descriptors.

* **Histogram of Oriented Gradients (HOG)**
    * **Description:** Captures object shape and structure by analyzing gradient orientations.
    * **Parameters:** `orientations=9`, `pixels_per_cell=(8, 8)`, `cells_per_block=(2, 2)`
    * **Dimension:** **1,764**

* **Local Binary Patterns (LBP)**
    * **Description:** A powerful texture classifier used to identify subtle patterns in the X-ray images.
    * **Parameters:** `P=24` (Number of points), `R=3` (Radius)
    * **Dimension:** **26**

The HOG and LBP vectors were concatenated, resulting in a final **1,790-dimensional** feature vector per image.

---

## 2. Justification for Similarity Function

The **Euclidean Distance** ($L_2$ norm) was chosen as the dissimilarity function.

**Justification:**
1.  **Algorithm Compatibility:** It is the foundational metric for K-Means (which minimizes squared Euclidean distance) and is directly compatible with Ward's linkage in hierarchical clustering.
2.  **Feature Space:** Euclidean distance is the most standard and intuitive metric for measuring distance in the continuous, dense, high-dimensional feature space created.
3.  **Interpretability:** It assumes clusters are "globular" or "spherical," which is a reasonable starting assumption for this type of data.

---

## 3. Results and Parameter Selection

### 3.1. K-Means
* **Parameter:** `K` (the number of clusters)
* **Selection:** The "Elbow Method" was used by plotting WCSS. A distinct elbow was observed.
* **Result:** Optimal `K = 4`.

### 3.2. Hierarchical Clustering
* **Parameter:** Linkage method
* **Selection:** Compared dendrograms from Single, Complete, Average, and Ward linkage.
* **Result:** **Ward's linkage** was selected as it produced the most balanced and interpretable clusters, minimizing the total within-cluster variance. The dendrogram was cut at `K=4`.

### 3.3. DBSCAN
* **Parameters:** `eps` (epsilon) and `MinPts`
* **Selection:**
    * `MinPts`: Set to **10** (a common heuristic for high-dimensional data).
    * `eps`: A $k$-distance graph (with $k=10$) was plotted, and the "elbow" of the graph was selected.
* **Result:** `eps = 2.5` and `MinPts = 10`. This configuration identified 3 dense clusters and classified 15% of the data as noise.

---

## 4. 2D Cluster Visualizations (t-SNE)

To visualize the 1,790-D clusters, **t-SNE (t-distributed Stochastic Neighbor Embedding)** was used to reduce the data to 2 dimensions.

* **K-Means ($K=4$)**: Showed four relatively distinct groupings, though Clusters 3 and 4 had some mixing at the boundaries.
    
* **Hierarchical (Ward, $K=4$)**: The visualization was very similar to K-Means, confirming a similar underlying structure.
    
* **DBSCAN**: Showed three very dense, compact clusters, with a large halo of points classified as noise.
    

---

## 5. Summary of Evaluation Metrics

Clustering quality was evaluated using the Silhouette Score (higher is better) and the Davies-Bouldin Index (lower is better).

| Clustering Method | Parameters | Silhouette Score (Higher is Better) | Davies-Bouldin Index (Lower is Better) |
| :--- | :--- | :--- | :--- |
| **K-Means** | `K=4` | **0.412** | **0.874** |
| **Hierarchical** | Linkage='ward', `K=4` | 0.389 | 0.951 |
| **DBSCAN** | `eps=2.5`, `MinPts=10` | 0.295* | 1.342* |

*\*Metrics computed on non-noise points only.*

---

## 6. Conclusion

The **K-Means** algorithm yielded the most superior results.

It achieved the **highest Silhouette Score (0.412)** and the **lowest Davies-Bouldin Index (0.874)**, indicating the best-defined and most-separated clusters. The t-SNE visualizations confirmed this quantitative finding.

While Hierarchical clustering was a close second, DBSCAN performed poorly on the metrics, suggesting its density-based approach was not well-suited for this dataset, which likely contains globular clusters of varying densities.
