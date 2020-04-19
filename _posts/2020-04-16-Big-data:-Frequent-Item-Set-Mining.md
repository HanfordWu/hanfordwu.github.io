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
- Read baskets and count all individual items. (At the same time we should give each item an fixed index).

Pass 2:
- Get individual frequent items from pass 1.
- Read again, only count the pairs whose both elements are frequent individuals.
- Find all frequent pairs from the count.

If we want item set size of k=3, we use frequent individuals and frequent pairs to generate all possible frequent triples candidate, then go to third pass to verify the candidate, eliminate false positive.
The process as following:
![alt](https://drive.google.com/uc?export=view&id=1-QcMKLsaGHwO6ItYnf_hFMocGDa7W3W4)

3. PCY Algorithm

In pass 1 of A-Priori, most memory is idle, we make use of it to reduce the number of candidate in pass 2, so that reduce the required memory.

Pass 1:
- In addition of individual item counting, we hash all pairs into memory as many buckets as possible.
```
FOR (each basket) : 
    FOR (each item in the basket) : 
        add 1 to item’s count; w 
    // New in PCY
    FOR (each pair of items) : 
        hash the pair to a bucket;
        add 1 to the count for that bucket;
```
Check the count of each bucket, only interested in count>= s.

Pass 2:
- Replace the buckets by a bit-vector.
- Read data again.
- Count pairs (candidates) that meet two conditions:
  - Both elements are frequent (From pass 1)
  - The pair index {i, j} hashes to a bucket that is 1.

After pass 2, we already get frequent pairs.
Questions to note:
  - On second pass, we can only use "Triple" data structure to represent frequent item set. Because, We have N individual frequent items, In A-Priori, we re-order frequent individual items, and keep the mapping relationship with original order, so that after we find frequent pairs in pass2, we can go back to identify which item exactly are frequent items, then we can build a N * N triangular metrix to store the counts in order. 
  However, in PCY, we don't know the number of frequent pairs, (we can still build a N \*N triangular metrix, but then PCY is meaningless). so we cannot re-order the candidate, so we cannot build a N*N triangular metrix. Therefore, we have to use triple to represent a pair an its count.
- PCY has to use triple to represent candidates in pass 2, it uses 3 times more space than triangular metrix, so that only if the hashmap bits elliminate 2/3 of all the possible pairs, PCY can use less space than A-Priori.

4. Refinement of PCY: Multistage Algorithm

Add one more pass and one more hash function:
Pass 1: Same as PCY
Pass 2: Same as Pass 1, but with a different hash function, to reduce false positive.
Pass 3: Same as PCY pass 2, but with two bitMap.

5. Refinement of PCY: Multi-hash

Key idea: Instead of adding more pass, we use more independent hash functions on the first pass. But the number of buckets will be halved. (More collisions, but more hash functions to validate)
If done properly(independent and high quality hash functions), we get benefit like multistage did, but with only two passes.

6. Random Sampling

Do sampling on baskets, reduce threshold to s * p. p is sampling rate. All sample fit in main memory, we don't read whole data any more.
Risk: we may have false negatives.

7. SON Algorithm

Key idea: If the number of one itemset is N, frequent threshold is S and we divide the whole basket into m parts, we adjust frequent threshold for each partition as S/m, so, if N>S, then at least one partition has number of this itemset > S/m.
Pass 1:
Process the entire file in memory by chunks.
Pass 2:
Read the data, verify frequent pairs.

SON is good to distributed data mining: Distribute candidates to all nodes