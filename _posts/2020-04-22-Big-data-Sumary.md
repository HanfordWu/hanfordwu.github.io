## Supervised Classification
- Data preprocessing
- Definition of training set, k-fold cross validation
- Data set imbalance
- Evaluation metrics: Accuracy, F1 score, confusion matrix
- Decision tree: Entropy, Gini
- Overfitting
- Random Forest
- KNN

## Recommender System
- Utility Matrix
- Content based recommender system: Find features of item, create item and user profile, use cosine similarity to recommend.
- User profile is created by averaging item profiles that this user read, bought, etc.
- Collaborative Filtering: Utility Matrix has many missing values, we ask similar items(item-item collaborative) or users(user-user collaborative) to evaluate the missing values.
- Pearson correlation between items or users, to find similar item or user.
- Add baseline for the model
- Evaluation: RMSE, precision top 10.

## Clustering
- Hierarchical clustering
- Euclidean case: Centroid
- Non-Euclidean case: Clustroid.
- k-means
- Initializing centroids, k-means++
- Select k until average cluster distance changes little
- CURE algorithm
- Sample, cluster, select representative, move close to centroid, assign all points to representatives

## Frequent Item Set Mining
- Frequency support threshold
- Confidence
- Interest = confidence - Pr[j]
- Association Rules: A &rarr; I\A
- Naive algorithm: count all pairs
- Triangular Matrix, Triples.
- A-Priori algorithm: count frequent individual, pairs, triples. etc.
- PCY algorithm: count and also hash pairs to check frequency.
- PCY refinement 1: More hash functions in multi-stage
- PCY refinement 2: More hash functions in one pass.
- Algorithm that less than two pass:
  - Random Sampling, reduce the frequency threshold accordingly.
  - SON algorithm: Count frequent item set by chunks, also decrease the frequency threshold accordingly.

## Similar Items
- Shingling
- Min-hashing: Reduce the dimension of boolean Matrix, while preserving Jaccard similarity
- Locality Sensitive Hashing(LSH): Hashing bands, so that if two columns have at least one same band, they have a high probability of being more than s similar.

## Data Streams
- Sampling
- A fixed proportion, should not based on information we need, but on user ids, ect.
- A fixed sample size: Reservoir
- Counting distinct element:
  - Naive approach: Hash all distinct element
  - Flajolet-Martin approach: Estimating: 2^R
  - Flajolet-Martin refinement: Using multiple hash functions, get many R, then group them, take median, then take average.
- Filtering Data Streams:
  - Hashing "good" element to B
  - Let stream go though hash table.
  - Refinement: Bloom Filter - Hashing "good" element to B using many hash functions.

## Page Rank
- Link as vote
- Stochastic adjacency matrix M
- Solve M dot r = r
- Initialize r with any value, iteration converge the same result finally.
- Dead End
- Spider Trap
- Solution: Teleports!