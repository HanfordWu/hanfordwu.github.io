Big-data Clustering
2. Hierarchical clustering:
    - Euclidean Case:
        1. How to represent a cluster of many points?
            Use centroid.
        2. How to determine "nearness" of cluster?
            By distance of centroids.
    - Non-Euclidean Case:
        1. How to represent a cluster of many points?
            Use clustroid: point “closest” to other points.
            Possible meanings of "closest":
                - Smallest maximum distance to other points
                - Smallest average distance to other points
                - Smallest sum of squares of distances to other points 
        2. How to determine "nearness" of cluster?
            - Inter-cluster distance: Nearest of two points, one from each cluster.
            - Considering "Cohesion": 
                - Diameter of merged cluster: Max distance among merged cluster.
                - Average distance of all pairs of points in merged cluster: N^2 calculation.
                - Density based approach: Above two metric divide number of points.
3. CURE Algorithm
In above clustering, we are assuming data points are normal distributed in each dimension. And axes are fixed.
However, assumptions may not be proper.
Solution: 
Instead of using one centroid/clustroid represent a cluster, we use multiple representative points. So that each representative can grow a cluster, they together form a irregular shape of cluster.
Steps:
   - First pass:
      0.  Pick random sample of points that fit in main memory.
      1.  Using regular Hierarchical clustering technique cluster those samples(how many clusters depends).
      2.  For each cluster(each has a centroid), pick a few of representatives(let's say 4 points) as dispersed as possible.
      3.  Move those representatives (say) 20% toward the belonged centroid.
    - Second pass:
      1. Scan the whole dataset, assign them to representatives.
      2. Update centroids, re-sample few representatives in each cluster.
      3. Repeat 1 and 2, until convergence.
1. k-means
Hierarchical clustering is expensive, k-means is more feasible.
k-means assumes Euclidean distance. It starts by picking k.
Steps:
   1. Pick one point at random, then k-1 other points, each as far away as possible from previous one.
   (Later talk about k-means++)
   1. For each point, label it as the cluster whose centroid is nearest.
   2. After all points are assigned, update the location of centroids.

        Repeat 2 and 3 until convergence: Points don’t move between clusters and centroids stabilize.

5. k-means++
Same as k-means, but different at initialization of centroids:
1a. Take one centroid c1 at random.
1b. Take new centroid ci, with probability $\frac{D(x)^2}{\sum_{x\in X}D(x)^2}$, X is the whole dataset, x is one of them. Essentially, D(x) denotes the shortest distance from a data point to the closest center we have already chosen. Denominator denotes the sum of all data points' nearest distance from centroids.
```python
while len(centroids) < k:
        for x in All_points:
            probability = nearest/phi_value
            if random.random() < proba: # take a point with probability 'proba'
                centroids.append(x)
                phi_value = phi(point_list, centroids)
                if len(centroids) == k: // If we have find enough centroids.
                    break
```

Step 2 and 3 are the same as k-means.