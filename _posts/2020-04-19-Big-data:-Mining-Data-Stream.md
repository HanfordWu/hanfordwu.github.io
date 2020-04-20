### Data Format:
Data Streams come from external source, eg. Queries, Tweets, etc.

**Difference with Video stream, download stream:**
- Video or download stream only need to be received and processed, just like a pipeline, showing on the screen or storing to disk is the final step of it. If we want to retrieve something, we have to ask the Video data again or go to the disk.
- In contrast, stream processing requires ingesting a sequence of data, and incrementally updating metrics, reports, and summary statistics in response to each arriving data record. So we can provide those metrics, reports, statistics **on realtime**. Often the records are to many so local memory cannot handle. Here we need algorithm to solve this.

We may also have many input ports, maybe two kinds of data we need at the same time.

### Sampling From a Data Stream

Data stream keeps coming infinitely, we cannot store all elements, so we sample it from the beginning.

Two problems have to solve:
1. How to sample a fixed proportion of elements in the stream?
2. How to maintain a fixed size of samples over a potentially infinite stream, while guarantee each element we have seen so far has the same probability of being sampled. Eg. 1,2,3,4,5 have come, we want keep each of them with probability of 1/5, after 6 coming, we want keep each of them with probability of 1/6.
----
1. How to sample a fixed proportion of elements in the stream?

First we don't consider the situation in question 2, just assume we know we have space to store 1/10 of the whole stream(actually we don't know the size of whole stream).

So we with the stream coming, we take each element with the probability of 1/10. Our goal is after sampling, we still have the property of the data. But the **problem** is:
- Assume we the stream is 1,2,3,4,5,4,5, before sampling, there are x = 3 distinction, d = 2 duplicates pair. So the proportion of duplicates is d/x+d = 0.4.
- If we sample it, with probability of each element 1/10, after sampling, the proportion of duplicates become to d/(10x + 19d), we have changed the property of data.

Solution:
Instead of taking sample on the information we need, we take sample on the information we don't need.
Example: 
Element (user, query, time), we need query and time, so we take sample according to user id. Taking 1/10 of users' query and time. 
Use a hash function that hashes the user name or user id into 10 buckets, store it only when its user name hash to the first bucket.
If we want take a/b fraction, we use b buckets, only take a buckets of b.

2. How to maintain a fixed size of samples over a potentially infinite stream?

With an infinite stream, we can only store s elements, which is the size of main memory. Assume we have already seen n elements.
Question:
How to let each of s element stay in memory with the probability of s/n? (s is the size we have)
Solution:
Reservoir Sampling.
Principle:
Suppose we already seen n element, if n < s, we will take any element that comes, once n >=s, we have no space for (n+1)th element, we will start to take the following element with the probability of s/n(If it's 0.125, we can use 8 buckets to hash "user id“, only take one of them), if it is taken, we select one element form Reservoir randomly, replace it with the taken element.
Note:
- With the elements coming, the chance of being taken (s/n) is decreasing.
- The earlier element comes, the easier it will be taken, but the probability of being taken out is accumulating at the same time. So in average, every element we have seen so far, will be sampled with the probability of s/n.

### Counting Distinct Element

Naive approach:
Keep a hash table of all distinct elements seen so far.
But it is possible local memory is not big enough, we need an algorithm.

Flajolet-Martin Approach:
Using an estimation, it may be not accurate, but with a high probability of being accurate, and a low probability of being in-accurate.

Details:
- Pick a hash function h that maps each of the N elements to at least $log_2N$ bits.
- Record the most 0s of hash values: R = maximum(hash(a)) seen.
- Estimated number of distinct elements = $2^R$

Behind of it:
m - The real number of distinct elements.
$2^R$ - Our estimation.
p - The probability of finding a hash value that has R zero tail.

$$p = 1-(1-2^{-r})^m\approx e^{-m*2^{-r}}$$
![alt](https://drive.google.com/uc?id=10ALZzXsr2XyIXUvG3UZrhiTsW6T2Tj3t)

Thus, $2^R$ will almost always be around m! The estimation is valid.

However, r is not continuos but only integer, if we don't see real R but see R-1(lower probability), the difference of $2^R$ and $2^{R-1}$ is is big.

Solution:
- Use k hash functions, so we can get k $2^R$s.
- Partition them into groups.
- Take median of each group.
- Take average of all medians.

### Filtering Data Streams

Example: Spam filtering
We have 1 billion good email address. If an email comes from one of these, it is not spam, if doesn't, it is.

**Naive approach: Hash table**

If S = 1 billion email address, we have B = 8 billion bits memory(1GB).
**Note:**
The number of bit is the bigger, the better, the worst case is that B <= |S|, in this case, 1 billion good email address will occupy almost all the bits, set them to one. when a bad address comes, it will definitely hash to a "1", so that it will not be filtered.

1. We hash good address to bit buckets. The fraction of 1s will be $1-(1-1/B)^S = 1-e^{-1/8} = 0.1175$ Almost the best case: 0.125
2. Then hash the coming address. One "bad" address may also be possible hash to "good" bits with the probability of 0.1175, therefore there are 0.1175 false positive. There is not false negative.

Refinement: Bloom Filter
Instead of only one hash function, we use k hash function(Same B bit bucket).

1. We hash each good address to bit buckets k times, mark this k bits as "1".
2. We hash coming address k times, only if all these k times hash to "1", it is "good" address, any hashing get "0" bits, it is "bad" address.
3. There are still false positive, the probability of false positive = $(1-e^{-kS/B})^k$

But note, k is not the bigger the better, image k is extraordinary big, most B bits will be marked as "1", then no address is filtered.

![alt](https://drive.google.com/uc?id=1KEiEsGnoT_TS0YrZjpo3WfW4-kXZsJ5B)

The "Optimal" value of k = $\frac{B}{S}ln2$