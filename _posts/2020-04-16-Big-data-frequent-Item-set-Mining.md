### Format of data
![alt](https://i.imgur.com/KLw61th.png)
- Each row is a basket.
- Each basket has many items.

**Goal**: Find association rules, people who bought {Diaper, Milk}, tend to buy {Beer}.
Alternatively, Basket = sentences; Items = documents that contain this sentence.
- Documents that appear together too often could be plagiarism.
- Notice that items don't have to be "in" basket.(Documents are not in sentences), we can still transform it to be a frequent item set problem.

### Steps
   1. Find frequent item sets.
        Support of item set: Number of appearing times.
        For example, item set I = {Diaper, Milk}, from the data we know Support(I) = 3 (Basket 3,4.5)
        Then we have a support threshold **s**, any item set that its support greater than **s**, we say it is a <u>**frequent item set**</u>. 
   2. Generate rules.
       If-then rules. If I1, then I2: I1 => I2. If {Diaper, Milk}, then {Beer}, etc.
       $confident(I \rArr j) = \frac{support(I \bigcup j)}{support(I)}$
       - $support(I \bigcup j) <= support(I)$, so, $confident(I \rArr j)$ <=1
       - If support(j) has a very high value, so support( I U j ) is closed to support( I ), so conf( I => j ) is very closed to 1. But, this not interesting.
       - Interest(I → j ) = conf( I → j ) - Pr(j), Pr(j) = support(j)/Number of basket.

If  I => j has high support and confidence, then both I and { I U j } must be frequent item set.

### Find frequent item sets
Data (Baskets) are stored on disk.
Algorithm read baskets one by one, using k nested loops to generate all item sets of size k, for example, k=2:
```
for (each basket):
    for i in range(length(basket)):
        for j in range(i+1, length(basket)):
            output(basket[i], basket[j]);
```
One time read the whole data, we call one pass.

We may need to find frequent item set size k of 2,3,4,etc. But the most set and most likely to be a frequent set is size of 2. So it is critical.

1. Naive algorithm:
   Using 2-nested loop read all pairs, count them. Need N(N-1)/2 pairs to be stored in memory. Two storing approaches:
    - Triangular matrix.
    - Using "Triples" data structure: [i, j, count] (i, j are index of all items dictionary). Only store pairs that count > 0.
  - Comparing above two approaches:
    - Triangular matrix need 4 bytes per pair.
    - Uses 12 bytes per pairs.
    - Conclusion: If less than 1/3 pairs' count > 0, then "Triples" win, otherwise, Triangular win.

However, the problem is we may don't have enough memory to store in memory and counting takes time.

2.  A-Priori Algorithm

**Key idea: If item i does not appear in s baskets, then no pair including i can appear in s baskets.**

Pass 1:
    Read baskets and count all individual items. (At the same time we should give each item an fixed index).
Pass 2:
    Get individual frequent items from pass 1.
    Read again, only count the pairs whose both elements are frequent individuals.