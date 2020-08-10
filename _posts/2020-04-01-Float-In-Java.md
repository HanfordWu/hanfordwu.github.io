## IEEE 754

![alt](https://media.geeksforgeeks.org/wp-content/uploads/Single-Precision-IEEE-754-Floating-Point-Standard.jpg)

![alt](https://lh3.googleusercontent.com/proxy/PK5vZkO4Sk7iJXfdlXnJ2EZ829DUJfexoxtjU-cFRU47A9kSGFuTEJH_iZ4YCgSzUvxuf2nHrzIFYbT0hgmx3DjzRxPnpG9fLoI9-o1hQizwvGoQVMya_J-csET_vDB7ztkmr86MGZEDumspIw)

Eg.
![alt](https://i.stack.imgur.com/WENuF.png)

We want to transfer an long integer i to float j:
- If i is very big, eg, +2^63, it will be shift first: i = 1.11111111111...(62 1s)* 2^62, but because in Single Precision, there are only 23 bits for decimals, we must **discard** 62-23=39 1s, it becomes i = 1.11111....(23 1s)* 2^62, so, s=0, e=00111110, m = 1111111111...(23 1s) in IEEE 754.
- If i is small, we don't discard so many effective digits, the transformation is more accurate.

## Primary type in Java
![alt](https://segmentfault.com/img/remote/1460000015349457)

### Automatic transformation

Smaller range type can be transformed into bigger range type.

![alt](https://segmentfault.com/img/remote/1460000015349458)

Solid line means there is no digits lose, dashed line means possible digit lose.

For byte/short/char, before calculating, they will be transformed to int type.

```java
byte  b = 50;
byte  a = 80;

byte d = a + b // error, a + b is int, cannot auto transformed to byte.
byte e = (byte) a + b; //-126, transformation lose some digits
int c = a + b; //130
```



### Mind auto transformation overflow

```java
int count = 10000000;
int price  = 1999;
long total = count * price;
```
total got negative result, because it overflow when calculating count * price, after then the transformation begin.
