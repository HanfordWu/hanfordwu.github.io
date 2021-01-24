Got know a new data structure.

### What is Binary Indexed Tree?
Using array to mimic a tree.
We know tree node has children, so elements in binary indexed tree are also derived from their children.

### What is it for?
For a regular array, if we want to know the sum of previous elements, we have to sum them all.
If we have a binary indexed array, we only have to sum `log(n)` of them.

For a regular array, if we want to update a sequence of elements, we have to update them one by one.

If we have a binary indexed array, we only have to update two elements: the first one and the one after them.

### Looks like
![alt](https://i.imgur.com/AlLVNQK.png)

Black boxes are original regular array.

Red boxes are built binary indexed tree array.
```
C[1] = A[1];
C[2] = A[1] + A[2];
C[3] = A[3];
C[4] = A[1] + A[2] + A[3] + A[4];
C[5] = A[5];
C[6] = A[5] + A[6];
C[7] = A[7];
C[8] = A[1] + A[2] + A[3] + A[4] + A[5] + A[6] + A[7] + A[8];
```

So what is the pattern?

C[i] = A[i - 2k+1] + A[i - 2k+2] + ... + A[i];   //k is i & (-i)

i & (-i) will take the right most bits of i.

10: 0b'1010, 10 & (-10) = 0b'10 = 2.

5: 0b'1001, 5 & (-5) = 0b'0001 = 1.

**How do we know the Sum_i?**

`SUMi = C[i] + C[i-2k1] + C[(i - 2k1) - 2k2] + ....`

It's just like a **Snowball**.

#### Implementation of a binary indexed tree

```c++
int n;
int a[1005],c[1005]; //对应原数组和树状数组

int lowbit(int x){
    return x&(-x);
}

void updata(int i,int k){    //在i位置加上k
    while(i <= n){
        c[i] += k;
        i += lowbit(i);
    }
}

int getsum(int i){        //求A[1 - i]的和
    int res = 0;
    while(i > 0){
        res += c[i];
        i -= lowbit(i);
    }
    return res;
}
```


### What about updating a part of the array?

According to above idea, updating one element we need to update all relevant elements, even this is much less than a regular array, but it's still too many.

Above implementation, we store the sum of previous elements to the correct element.

Adding k to part of the array, the difference between elements don't change.

So we can store the difference between elements, and the base of the sequence. all elements can be derived from the base and the difference.

