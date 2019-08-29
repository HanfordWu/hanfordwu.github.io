---
layout: post
title: How to use MathJax in Jekyll powered markdown editor
date: 2019-08-28
---

I came across a problem that Github page can't interpret MathJax syntax. I find the solution [here](https://haixing-hu.github.io/programming/2013/09/20/how-to-use-mathjax-in-jekyll-generated-github-pages/). A few differences, I don't change the markdown processor, I am still using kramdown. In that solution, layout file needs three lines' code. If the new included file has ext name, the code should specify it.

1. Create a new html file in _include folder with following codes:

   ```html
   <script type="text/x-mathjax-config">
     MathJax.Hub.Config({
       TeX: {
         equationNumbers: {
           autoNumber: "AMS"
         }
       },
       tex2jax: {
         inlineMath: [ ['$','$'], ['\(', '\)'] ],
         displayMath: [ ['$$','$$'] ],
         processEscapes: true,
       }
     });
   </script>
   <script type="text/javascript"
    src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
   </script>
   ```

   2. Find the layout file you are gonna write equation into, add following codes before "``</header>``" (This is the end of the header part).

```html
{% if page.use_math %}
   {% include mathjax_support.html %}
{% endif %}
```

​		  3. Add front matter in the post file ``use_math: true``.