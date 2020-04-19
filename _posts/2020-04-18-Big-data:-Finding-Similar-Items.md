**Data Format(Example):**
Text documents
**Goal**: Given a large number(millions or billions) of documents, find similar documents.
**Note:**
We are finding near neighbors in high-dimension, so there must be a distance metric. This article is talking about Jaccard similarity:
$$sim(C1, C2) = \frac{|C1 \bigcap C2|}{|C1 \bigcup C2|}$$
We cannot compare all each other to find similar ones, too expensive.

Three Steps:
1. Shingling
2. Min-Hashing
3. Locality-Sensitive Hashing

### Shingling
Convert documents to sets.

If we just use words or important words to represent documents, we are not the order of words(sentence), and the frequency of words, For long documents, there will be many similar documents.

We use shingles!

Tokens can be characters, sentence, words:
Shingle 1: Hello
Shingle 2: Wor
Shingle 3: ld!
Shingle 4: How are you?
...
We may first define a shingle "dictionary", with index(we are going to hash its index). But in my opinion, we can hash shingle directly:
- We may don't have a "dictionary", but when we read each document, hash its content pieces of random length. 
- In the conception of "dictionary", this way will generate a very long dictionary. But we only need hash the existing shingles.
- In each document, use M hash functions to hash existing shingles, for hash function Ki, store its minimal hash value of all shingles.
- Now we get a vector(signature) with M hash values, use it to represent this document.
- Once all documents have their signature, we got signature matrix.

But the problem of above implementation is that documents may have very less identical shingles, so that we cannot find similar documents. 
Therefore, the following discussion will follow the way that we create a "dictionary" of shingles. We use it to map shingles to index.

k-shingle:
Example: k=2; document D1 = "abcab", Set of 2-shingles: S(D1) = {ab, bc, ca}
Compressing(hashing):
h(D1) = {1, 5, 7}

### MinHashing
Problem: 
Document is now a collection of hash value, if we compare the similarity of them, with LSH(later talk about it), we must use a full length of dictionary to represent a document (it may be sparse). It is too long.
Goal: 
Reduce the dimension of representative, but preserving the Jaccard similarity.
Solution: MinHashing

Theoretical:
We transfer hash values to binary full length dictionary.
-|C1|C2
----|---|---
shingle A | 1 | 1
shingle B | 1 | 0
shingle C | 0 | 1
shingle D | 0 | 0
We don't consider shingle D, cuz both C1, C2 have no D.
$$sim(C1, C2) = \frac{|C1 \bigcap C2|}{|C1 \bigcup C2|} = \frac{\# of A}{A+B+C}$$

$$Pr[hash(shingle \ of \ C1) == hash(shingle \ of \ C2)]= Sim(C1, C2) = 1/3$$
Because any shingle is hashed to be the minimal value with the same probability.
$$Pr[\pi(y) = min(\pi(X))] = 1/|X|$$ 
X is the shingle "dictionary".
If
$$\pi(y) = min(\pi(C1\bigcup C2))$$
Then,
$$\pi(y) = min(\pi(C1)) \ Or \ \pi(y) = min(\pi(C2))$$
So the prob. that both are true is the prob. $y\in C1 \bigcap C2$
Therefore:
$$Pr[min(\pi(C1))=min(\pi(C2))]=|C1\bigcap C2|/|C1\bigcup C2|$$
This is exactly the Jaccard similarity.
**Conclusion:**
The similarity of two columns(documents) is the fraction of the hash values where they are equal in signature matrix.

Above is the basis of why we cam use MinHashing to calculate Jaccard similarity.

**But how to MinHashing?**
![alt](https://drive.google.com/uc?id=1-dSJEeamtOhrupRsVHg66LOZPIcFYRPp)

### Locality Sensitive Hashing (LSH)
**Problem:**
Even though now we have signature matrix, it can substitute original binary matrix, and it has lower dimension.
However, we still have billions, millions columns(documents), we cannot compare them all.
**Solution:**
Split signature vector to bands, if two documents have at least one identical band, they have the similarity(say above 80%) with the probability of p. (true positive)
If two documents don't have any identical band, they have the similarity(say above 80%) with the probability of q. (false negative)
**Goal:** We want p to be as high as possible, q to be as low as possible.

What to do?
Hash columns of signature matrix M b times to buckets.
![alt](https://drive.google.com/uc?id=1nKVdu2LVJhiAXhrh0eW9X7fS2DYxlMgT)
Note:
- There should be enough buckets that bands are unlikely to hash to the same bucket unless they are identical.

Our goal is any two columns have at least one band hashed into same bucket, the two columns reach a certain similar(let's say 80%).

#### Theoretical:
N - The number of hash functions.
N = b * r
**Goal:** Find pairs that are at least s(say 80%) similarity.
If two columns are 80% similarity, then the probability of the row are equal is also s.
Prob. of that band are equal: $(s)^r$
Prob. of that band are  not equal: $1-(s)^r$
Prob. of all band are not equal: $(1-(s)^r)^b$
Prob. of at least one band are equal: P = $1- (1-(s)^r)^b$
If s > 80%, P is probability of true positive probability, if s < 80%, P is probability of false positive probability.
Curve of P:
![alt](https://drive.google.com/uc?id=12lROS5TgHNoG_Wj4hAE06ZjQYYBfPv40)
- Under the curve = True positive rate + false positive rate = All positive
- Above the curve = True negative rate + false negative rate = All negative
- We want the slop of the curve to be as sharp as possible.

Finally, After we have found all similar documents by MinHashing, they are still candidates. We read those documents again to verify those candidates, eliminate false positive.