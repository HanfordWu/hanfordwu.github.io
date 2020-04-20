### Data Format:
![alt](https://drive.google.com/uc?id=1vnj3I8ncqTENDjZP5-wQOzkJdhR7Fv0Y)
$r_j$ - Rank score of page j;
i &#10145; j means link from page i to page j;
$$r_j = \sum_{i \rarr j} \frac{r_i}{d_i}$$

**Example:**

![alt](https://drive.google.com/uc?id=1V9ZW9CmdllyJ84LPy_FmbbtlgDYZ2seP)

$r_y = r_y/2 + r_a/2$
$r_a = r_y/2 + r_m$
$r_m = r_a/2$

There is no unique solution, all solutions are linear correlation.
We add $r_a + r_y + r_m = 1$
Get solution: 
$r_y = 2/5$ 
$r_a =2/5$
$r_m =1/5$
In real application, we use iteration to solve the following equation:
$$r^{t+1} = M \bullet r^{t}$$ 
Stop when $|r^{t+1}-r^{t}|_1 < \varepsilon$.

Matrix M:
![alt](https://drive.google.com/uc?id=1_IGGlvOSnbnIBWW5pDAH9AyzTpdwwH1a)
It's called **column stochastic matrix**, note its column sum to 1(Can be any constant).

For above example, the iteration likes this:
![alt](https://drive.google.com/uc?id=1MsSzneJ9I2zpD_SNqL8YRkeBgcUvHjH5)
The same result.

Doesn't matter what r we start, it will reach stationary result.

> For graphs that satisfy certain conditions, the stationary distribution is unique and eventually will be reached no matter what the initial probability distribution at time t = 0

So we can do **"Random walk"** to get the column stochastic matrix: M
At any page, we follow its out-link randomly, repeat this all the time.

### Problems

#### I. Spider Trap
![alt](https://drive.google.com/uc?id=1vUkt9_W-dYbNpuSw1GZP0T7vj-oJriNM)

Once **Random walk** goes into the trap, there is no rank for out side of it. Just like some group/one don't share chance to other guys. Personal can make use of this "bug" enhance its page rank(Only write a link point itself).

It's hard to identify **Spider Trap**, technically speaking, the whole internet is a big spider trap. Rather than identify it, Google solve it in this way:
> At each page, the random walker has two options:
> - With prob. $\beta$ follow a link
> - With prob. $1-\beta$ jump to any random page.
> 
> Common values of $\beta$ in range 0.8 to 0.9

Essentially, on any page, the out-link share the probability of $\beta$, all other pages(include it self) share the probability of $1-\beta$.
![alt](https://drive.google.com/uc?id=1OiJfD3nlMebc1KUASwNaHWp8hc6f3wik)
$$r_j = \sum_{i\rarr j}\beta \frac{r_i}{d_i}+(1-\beta)\frac{1}{N}$$
The matrix M become new matrix A:
![alt](https://drive.google.com/uc?id=19poJIxQWlfewdfpr8VSgN0donweZmprO)
We can solve the equation: $A\bullet r = r$

#### II. Dead End
A web page has no out link, so that **Random walk** once get into it, cannot get out of it:
![alt](https://drive.google.com/uc?id=1Hmxtq6iNL04ks2RttwfT6kENRVe6Gubo)

As a result, we got the solution of the equation like this:
![alt](https://drive.google.com/uc?id=1VUO3nfOfA9wFNXevqrksGdbuMXxYEcTZ)

**Solution:**
If there is no out-link in one page, we randomly select one page from all pages to go:
![alt](https://drive.google.com/uc?id=1crWHRuOTT8GC4I5ComfLhCqBtASL5JED)

Use the matrix above, we can solve the equation as before.

The result vector r is the rank of all page.