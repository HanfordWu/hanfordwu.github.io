### 0. Intro

Sort algorithm is kind of basic, I understand them and implemented them, but they often becomes obscure after a while. By this post, I hope I can master them.

### 1. Classification

There are many different classification, but here I am talking about whether they are doing comparison:

- Comparison Sort: Comparison sort examines the data only by comparing two elements with a comparison operator.
- Non-comparison Sort: It's possible break through the bottle neck of comparison.


### 2. Bubble sort

The idea of bubble sort is, we are going to iterate the array n times, for each time, we find the biggest number, and move it step by step, to the tail.

Description:

- From i = 0, compare a[i] to a[i+1]
- If a[i] > a[i+1], swap;
- i++, repeat above two steps;
- Repeat above three steps;

Gif:
![alt](https://www.runoob.com/wp-content/uploads/2019/03/bubbleSort.gif)

Implementation:

```JavaScript
function bubbleSort(arr) {
    var len = arr.length;
    for(var i = 0; i < len - 1; i++) {
        for(var j = 0; j < len - 1 - i; j++) {
            if(arr[j] > arr[j+1]) {        // 相邻元素两两对比
                var temp = arr[j+1];        // 元素交换
                arr[j+1] = arr[j];
                arr[j] = temp;
            }
        }
    }
    return arr;
}
```
Time complexity: $O(n^2)$
Space complexity: $O(1)$, in-place

Stability: If two elements are equal, we don't swap them, then it's stable.


### 3. Selection Sort

In bubble Sort, each iteration, we find the greatest, then move it step by step.

But actually, we don't have to move it step by step. We can keep the greatest in a variable, after iteration, we assign it to the tail.

Or, we can find the smallest, assign it to the head.

Gif:
![alt](https://www.runoob.com/wp-content/uploads/2019/03/selectionSort.gif)

Implementation:

```JavaScript
function selectionSort(arr) {
    var len = arr.length;
    var minIndex, temp;
    for(var i = 0; i < len - 1; i++) {
        minIndex = i;
        for(var j = i + 1; j < len; j++) {
            if(arr[j] < arr[minIndex]) {     // 寻找最小的数
                minIndex = j;                 // 将最小数的索引保存
            }
        }
        temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
    return arr;
} 
```
Time complexity: $O(n^2)$
Space complexity: $O(1)$ in-place

Stability: When we are scanning, if there is an equal, we don't use its index, then it's stable.

Versus Bubble:
The idea is the same, but we use a variable to keep the target, instead of moving it through the whole line. So we saved operations.




### 4. Insertion Sort

The principle is building an ordered sequence from scratch.

Description:

- First assume the first element is an ordered sequence.
- Start from seconde element, we scan previous elements
- If the element is greater than current element, move the element backward one step.
- If the element is smaller than current element, insert current to after the element.
- Repeat step 2-4.

![alt](https://www.runoob.com/wp-content/uploads/2019/03/insertionSort.gif)

Implementation:

```JavaScript
function insertionSort(arr) {
    var len = arr.length;
    var preIndex, current;
    for(var i = 1; i < len; i++) {
        preIndex = i - 1;
        current = arr[i];
        while(preIndex >= 0 && arr[preIndex] > current) {
            arr[preIndex + 1] = arr[preIndex];
            preIndex--;
        }
        arr[preIndex + 1] = current;
    }
    return arr;
}
```

Time complexity: $O(n^2)$, if the array is already sorted, it's linear time. The trick is if the array is already sorted, we don't have to compare and move all the previous elements, because previous elements is ordered.

Space complexity: $O(1)$ in-place

Stability: If two elements are equal, we don't move them, then it's stable.

Versus Bubble:

Bubble Sort is doing elements exchanging, we have to do three assignments.

Insertion Sort is doing elements moving, it has less assignments.

Bubble sort will do comparison even the array is already sorted. But insertion Sort doesn't have to compare to all previous elements if it's already sorted.


### 5. Shell Sort

The first Sort algorithm under $O(n^2)$. It's improved insertion sort. The improvement is, it prioritizes the farther elements.

Shell sort split array into multiple sub-array, for example, if we define step = 4, we do insertion Sort every 4 elements. That means we split the array into 4 sub-arrays, and do insertion Sort for all 4 sub-arrays.

Then reduce step to 3 or 2 or 1, when step=1, that is original insertion Sort. By doing step=4 or 3 or 2 insertion Sort, the array become more ordered, therefore when step=1, we don't have to move too many times.

Original insertion Sort means step=1, we have to move elements one every time. But if step=4, we can move element four every time, that's why shell sort is faster.

Description:

- Define a steps sequence: e.g. [4,2,1], the final step must be 1.
- Do insertion sort for all steps, note the index should increment by 1 in each type of step.

Gif:

![alt](https://www.programmersought.com/images/661/3b3a42d516614222b9244f13a4efa06d.gif)

Implementation:

```JavaScript
const shellSort = (array) => {
	let steps = [4,2,1]
	for(let i = 0; i < steps.length; i++){
		const step = steps[i]
		for(let j = 0; j<step; j++){
			for(let k = j+step; k<array.length; k=k+step){
				let currentElement = array[k]
				let currentIndex = j;
				while(currentIndex>=0&&array[currentIndex]>currentElement){
					array[currentIndex+step] = array[currentIndex]
					currentIndex = currentIndex - step
				}

			}
		}
	}
}
```
We are using four nested loop, but note outside two loop is just iterating the steps sequence.

We can do better instead of hard coding steps sequence:

```JavaScript
const shellSort2 = (array) => {
	let step = Math.floor(array.length/3)
	while(step>0){
		for(let j = 0; j<step; j++){
			for(let k = j+step; k<array.length; k=k+step){
				let currentElement = array[k]
				let currentIndex = j;
				while(currentIndex>=0&&array[currentIndex]>currentElement){
					array[currentIndex+step] = array[currentIndex]
					currentIndex = currentIndex - step
				}
			}
		}
		step = Math.floor(step/3)
	}
}
```

Time complexity: If steps = [1], it become pure insertion Sort, it's $O(n^2)$. But if steps != [1], because it enhances the moving mechanism, it must be better than $O(n^2)$.

There is a *Hibbard* increment steps, the time complexity is $O(n^{3/2})$

Space complexity: We are using another sequence of steps, the extra space depends on the length.

Stability: Unstable. Because we are Sort sub-arrays, we may change the order of different sub-arrays.


### 6. Merge Sort

The idea of merge sort is **Divide and Conquer**. We make each sub-array sorted, then combine them as a sorted array.

Description:

- Split the array into two n/2 long arrays.
- If sub-array.length != 1, recursively do splitting
- If sub-array.length == 1, merge two arrays.
- Above steps are recursive.

Gif:
![alt](https://www.runoob.com/wp-content/uploads/2019/03/mergeSort.gif)

Implementation: (Switch to python)

```python
def mergeSort(arr):
    if(len(arr)<2):
        return arr
    middle = len(arr)/2
    left, right = arr[0:middle], arr[middle:]
    return merge(mergeSort(left), mergeSort(right))

def merge(left,right):
    result = []
    while left and right:
        if left[0] <= right[0]:
            result.append(left.pop(0))
        else:
            result.append(right.pop(0));
    while left:
        result.append(left.pop(0))
    while right:
        result.append(right.pop(0));
    return result
```

Time complexity: The time performance is fixed, whatever the origin array like, it will tale $O(n\log n)$

Space complexity: Because it's recursion, it needs $O(n)$ space.

### 7. Quick Sort

The idea is still **Divide and Conquer**. Pick random one from the collection, put those elements that are smaller than it to its left, put those elements that are greater than it to its right. Now we have split the original collection into two parts, and the pivot has been the right position. Then if left or right has more than one elements, call above method recursively.

**How to put smaller(s) to left, put greater(s) to right?**

Easy way is creating another array, but we can do it in-place:
<!-- Move the pivot the right most. Having two pointer start from head(moving right) and tail(moving left), if head < pivot, head++, if tail > pivot, tail--, until head > pivot. Before head?pivot, if head>pivot and tail < pivot, swap head and tail. -->
<!-- 
After head > pivot, we swap pivot and head. Till now, all smaller is left, greater is right. -->
Move the pivot to the left most. Having two pointers: scanIndex(start from left+1), countIndex(start from left+1), we are going to use scanIndex go through the collection, if element is smaller than pivot, we move it right after pivot by swapping(swapping guarantee no elements got lost), then increase countIndex. After scanning the collection, all smaller elements is following pivot with length of countIndex. Then we swap pivot with countIndex+1.

Gif:
![alt](https://www.runoob.com/wp-content/uploads/2019/03/quickSort.gif)


Implementation:

```python
def quickSort(arr, left=None, right=None):
    left = 0 if not isinstance(left, (int)) else left
    right = len(arr)-1 if not isinstance(right, (int)) else right

    if left<right:
        partitionIndex=partitionAndSort(arr, left, right)
        quickSort(arr, left, partitionIndex-1)
        quickSort(arr, partitionIndex+1, right)
    return arr

def partitionAndSort(arr, left, right):
    pivot = left
    counter = left+1
    scanner = left+1
    while scanner <= right:
        if arr[scanner]< arr[pivot]:
            arr[scanner], arr[counter] = arr[counter], arr[scanner]
            counter += 1
        scanner+=1
    arr[counter-1], arr[pivot] = arr[pivot], arr[counter-1]
    return counter-1
```

Time complexity: The worst case, it's still $O(n^2)$, that is every time, we select the smallest or greatest one as pivot(bad luck)!

But in average, it's $O(n \log n)$. And in fact, it's often faster than $O(n \log n)$. The constant is often small.

Space complexity: $O(1)$ in-place

Stability: Not stable, because the pivot is selected randomly.

### 8. Heap Sort

Heap Sort is making use of the feature of Heap.

Heap is a complete binary tree that all parents are greater/smaller() or equal to their children.

Types: MaxHeap and MinHeap

We can use array to store heap.

How to create a heap?

- Start from root, index=0
- Append element to array one by one.
- After each append, bubble up.

Description:

- Create a heap
- Remove top(the greatest)
- Move the last one to the top
- Bubble down the top.

**Why does ascending sort need a MaxHeap? And descending sort need a MinHeap?**

Remember we are sorting in an array. When we are trying to getTop(), if we don't want to break the heap, we have to put it to the last(heap size reduce 1), the top should be the greatest if we want to have a ascending sort, so it's MaxHeap.

Gif of Heap sort:

![alt](https://www.runoob.com/wp-content/uploads/2019/03/heapSort.gif)

If we want to create the heap in-place, we will bubble down elements from the top to the half of the array(That's enough to heapify the array).

Implementation:

```python
def heapSort(arr):
    global arrLen
    arrLen = len(arr)
    buildMaxHeap(arr)
    for i in range(len(arr)-1,0,-1):
        swap(arr,0,i)
        arrLen -=1
        bubbleDown(arr, 0)
    return arr


def buildMaxHeap(arr):
    for i in range(len(arr)/2,-1,-1):
        # from top, bubble down
        bubbleDown(arr,i)

def bubbleDown(arr, i):
    left = 2*i+1
    right = 2*i+2
    largest = i
    # find the largest index of left and right
    if left < arrLen and arr[left] > arr[largest]:
        largest = left
    if right < arrLen and arr[right] > arr[largest]:
        largest = right

    if largest != i:
        swap(arr, i, largest)
        bubbleDown(arr, largest)

def swap(arr, i, j):
    arr[i], arr[j] = arr[j], arr[i]

```

Time complexity: So first we do *heapify*, we bubble down n/2 elements, it's $\frac{n}{2} \times \log n$. Second we remove the top to right one by one, after each remove, perform bubble down: $n\log n$, so finally we get $n\log n$ complexity.

Space complexity: We perform everything in-place, so it's $O(1)$

Stability: We cannot guarantee the order of equal elements. So it's unstable.

### 9. Counting Sort

Non-comparing sort.

The big idea is, transform the value to index(kind of key), so that  we can find an ordered position for the elements, then we fetch the elements back according to the order.

Description:

- Find the largest value of array.
- Create another array C with all elements of 0;
- Iterate the array, each time, C[elements]++
- Return index of C back to array.

Gif:
![alt](https://www.runoob.com/wp-content/uploads/2019/03/countingSort.gif)

Implementation:

```python
def countingSort(arr, maxValue):
    bucketLen = maxValue+1
    bucket = [0]*bucketLen
    sortedIndex =0
    arrLen = len(arr)
    for i in range(arrLen):
        if not bucket[arr[i]]:
            bucket[arr[i]]=0
        bucket[arr[i]]+=1
    for j in range(bucketLen):
        while bucket[j]>0:
            arr[sortedIndex] = j
            sortedIndex+=1
            bucket[j]-=1
    return arr
```
Time complexity: $O(len(C))$. If the value of array is very large, the length of C will be very large. In this case, we can shift whole range by using min and max value of array, so the length of C will be max-min. But, if max-min is very large, we still get a big complexity.

Space complexity: The same as time complexity.

Stability: It can be stable, if we store the order of equalled elements.

When max and min value of array is not big difference, counting sort is good to use.



### 10. Bucket Sort

The limitation of counting sort is that it can only sort integers. Because we are using the value as index, index must be integer.

Bucket sort is an enhancement of counting sort.

We transform elements from integer or float to integers or a range(with the same linear function), each integer or range represents a bucket. One bucket may contain multiple elements. The buckets themselves are ordered.

Inside of the bucket, we use another kind of sorting algorithm. Then we collect all elements according to the order of bucket and the sorted list in each bucket.

Gif: 
![alt](https://i.makeagif.com/media/5-18-2016/a7ppGv.gif)

```python
def bucket_sort_simplify(arr, max_num):
    buf = {i: [] for i in range(int(max_num)+1)}  # 不能使用[[]]*(max+1)，这样新建的空间中各个[]是共享内存的
    arr_len = len(arr)
    for i in range(arr_len):
        num = arr[i]
        buf[int(num)].append(num)  # 将相应范围内的数据加入到[]中
    arr = []
    for i in range(len(buf)):
        if buf[i]:
            arr.extend(sorted(buf[i]))  # 这里还需要对一个范围内的数据进行排序，然后再进行输出
    return arr
```
Time complexity and space complexity, Stability is the same as counting sort.


### 11. Radix Sort(LSD Sort)

It's like alphabetic sorting, first we sort the most left digit/character, then move to left continue sorting.

Only for integer or words sorting, but we can transform any other form to integers.

![alt](https://www.runoob.com/wp-content/uploads/2019/03/radixSort.gif)


```python
def radix_sort(s):
    i = 0 # 记录当前正在排拿一位，最低位为1
    max_num = max(s)  # 最大值
    j = len(str(max_num))  # 记录最大值的位数
    while i < j:
        bucket_list =[[] for _ in range(10)] #初始化桶数组
        for x in s:
            bucket_list[int(x / (10**i)) % 10].append(x) # 找到位置放入桶数组
        print(bucket_list)
        s.clear()
        for x in bucket_list:   # 放回原序列
            for y in x:
                s.append(y)
        i += 1
```