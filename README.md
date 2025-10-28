
# Clustering Pneumonia X-Rays using Computer Vision Features

This project explores unsupervised clustering on the "Chest X-Ray Images (Pneumonia)" dataset from Kaggle. Instead of using a deep learning model, this notebook applies traditional computer vision techniques to extract a high-dimensional feature vector from each image.

Three different clustering algorithms (K-Means, Agglomerative Hierarchical, and DBSCAN) are then applied to group the images. The results are evaluated visually using t-SNE and quantitatively using intrinsic cluster metrics like the Silhouette Score and Davies-Bouldin Index.

##  Dataset

The project uses the **[Chest X-Ray Images (Pneumonia)](https://www.kaggle.com/datasets/paultimothymooney/chest-xray-pneumonia)** dataset from Kaggle.

The notebook handles the download and setup automatically using the Kaggle API.

## Project Workflow

1.  **Environment Setup**: Installs the Kaggle library and sets up the API credentials.
2.  **Data Acquisition**: Downloads and unzips the X-Ray dataset.
3.  **Image Preprocessing**: Loads all images from the `train`, `test`, and `val` sets. Each image is converted to grayscale and resized to a uniform `(256, 256)` resolution.
4.  **Feature Extraction**: A composite feature vector is generated for each image by combining four classical computer vision descriptors (see details below).
5.  **Normalization**: The resulting high-dimensional feature matrix is normalized using `sklearn.preprocessing.StandardScaler`.
6.  **Clustering**: Three distinct clustering algorithms are fit to the scaled feature data.
7.  **Evaluation & Visualization**: The resulting clusters are visualized using a t-SNE plot and evaluated using the Silhouette Score and Davies-Bouldin Index.

---

## Feature Extraction

A key component of this analysis is the creation of a feature vector for each image. This vector is a concatenation of four different feature types:

1.  **Intensity Histogram**: A 256-bin histogram calculated from the grayscale pixel values.
2.  **Histogram of Oriented Gradients (HOG)**: Captures information about object shape and edges by describing the distribution of gradient orientations.
3.  **Local Binary Patterns (LBP)**: A powerful texture descriptor that works by labeling pixels based on their neighborhood.
4.  **Hu Moments**: A set of seven moments that are invariant to image translation, scale, and rotation, which helps in describing shape.

The concatenation of these vectors results in a single, high-dimensional feature vector (`X`) for each image, which is then used for clustering.

---

## Clustering Algorithms & Analysis

This project implements, analyzes, and compares three primary clustering algorithms. The target number of clusters is `K=2`, corresponding to the ground truth labels ("NORMAL" and "PNEUMONIA").

### 1. K-Means
* **Analysis**: The "Elbow Method" is used to plot cluster inertia for `K` values from 2 to 10 to help justify the choice of `K`.
* **Implementation**: The final model is run with `n_clusters=2`.

### 2. Agglomerative Hierarchical Clustering
* **Analysis**: A dendrogram is plotted using a large sample of the data to visualize the hierarchical relationships and merge distances.
* **Implementation**: The final model is run with `n_clusters=2` and `linkage='ward'`.

### 3. DBSCAN (Density-Based Spatial Clustering of Applications with Noise)
* **Analysis**: A *k-distance graph* is plotted to help determine the optimal `eps` (epsilon) parameter, which defines the neighborhood radius.
* **Implementation**: The model is run with the chosen `eps` value to identify density-based clusters and label outlier points as noise.

---

## Evaluation

The performance of the clustering algorithms is assessed in two ways:

1.  **Visual Evaluation (t-SNE)**
    To visualize the high-dimensional clusters, PCA is first used to reduce the data to 50 dimensions, followed by t-SNE to reduce it to 2 dimensions. A 1x4 subplot is generated to visually compare the `(x, y)` coordinates colored by:
    * Ground Truth Label
    * K-Means Cluster
    * Hierarchical Cluster
    * DBSCAN Cluster

2.  **Intrinsic Metrics**
    The quality of the clusters (excluding noise points for DBSCAN) is calculated and displayed in a table using:
    * **Silhouette Score**: Measures how similar an object is to its own cluster compared to other clusters. A score closer to +1 is better.
    * **Davies-Bouldin Index**: Measures the average similarity between each cluster and its most similar one. A score closer to 0 is better.

---

## How to Run

This notebook is designed to be run in **Google Colab**.

1.  **Get Kaggle API Key**:
    * Go to your Kaggle account page.
    * Under the "API" section, click `Create New API Token`.
    * This will download a `kaggle.json` file.

2.  **Run the Notebook**:
    * Open the `.ipynb` file in Google Colab.
    * Run **Cell 1** (`1. Install Kaggle library...`). It will prompt you to upload a file.
    * Upload the `kaggle.json` file you just downloaded.
    * Run the remaining cells in order.

3.  **Notes**:
    * **Cell 5** (Feature Extraction) and **Cell 7** (t-SNE) are computationally intensive and may take several minutes to complete.
    * You may need to tune the `EPSILON` value in **Cell 6** based on the k-distance plot generated for your specific run.
