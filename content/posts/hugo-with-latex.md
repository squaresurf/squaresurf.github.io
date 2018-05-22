---
title: "Hugo with LaTeX"
date: 2018-05-08T06:40:47-07:00
categories: ["blogging", "math"]
tags: ["hugo", "katex", "latex"]
---

As you may have already noticed, I am using [hugo] to generate this blog. [Hugo] gives me many benefits right out of the
box, which include, the ability to author with markdown, create shortcodes for writing (more on this in a moment), the
ability to blog without a database, not to mention [host the site quickly on GitHub](https://pages.github.com/).
However, one thing not set up out of the box is the ability to include [LaTeX] in your blog posts. Since this blog will
need [LaTeX] when talking about Data Science as well as multiple other Math topics, we will update our setup to have the
ability to write [LaTeX] with [KaTeX], a fast TeX math renderer for the web.
<!--more-->

The ideal workflow will be one where LaTeX can be inserted within our markdown. It will also need to be able to support
rendering the math inline as well as a block. Here are some examples of how it should work:


```md
The constant {{%/* math */%}}\pi{{%/* /math */%}} is useful when working with circles.

The definition of n choose k: {{%/* math block */%}}{n \choose k} = \frac{n!}{k!(n - k)!}{{%/* /math */%}}
```

Now you may be wondering where the `{{%/* math */%}}` tag is coming from. Well first off, that is in the form of a [hugo
shortcode](https://gohugo.io/content-management/shortcodes/). Shortcodes are simple snippets inside your content files
calling built-in or custom templates. We will extend our setup to support a math [custom
shortcode](https://gohugo.io/templates/shortcode-templates/).

First off add the following to `layouts/shorcodes/math.html`.

```md
{{if eq (.Get 0) "block"}}
<div class="math-block">{{.Inner}}</div>
{{else}}
<span class="math-inline">{{.Inner}}</span>
{{end}}
```

After that add the following to theme's header:

```
<!-- KaTeX -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.9.0/dist/katex.min.css" integrity="sha384-TEMocfGvRuD1rIAacqrknm5BQZ7W7uWitoih+jMNFXQIbNl16bO8OZmylH/Vi/Ei" crossorigin="anonymous">
<script src="https://cdn.jsdelivr.net/npm/katex@0.9.0/dist/katex.min.js" integrity="sha384-jmxIlussZWB7qCuB+PgKG1uLjjxbVVIayPJwi6cG6Zb4YKq0JIw+OMnkkEC7kYCq" crossorigin="anonymous"></script>
```

Finally add this to the theme's footer:

```
<script>
  function renderMath(nodes, displayMode) {
    for (i = 0; i < nodes.length; i++) {
      let n = nodes[i];
      katex.render(n.innerText, n, {displayMode: displayMode})
    }
  }

  renderMath(document.querySelectorAll('.math-block'), true);
  renderMath(document.querySelectorAll('.math-inline'), false);
</script>
```

## Example

Here is an example of how it will look once it's rendering the example above of how we'd like things to work.

The constant {{% math %}}\pi{{% /math %}} is useful when working with circles.

The Definition of n choose k: {{% math block %}}{n \choose k} = \frac{n!}{k!(n - k)!}{{% /math %}}

[KaTeX]: https://khan.github.io/KaTeX/
[LaTeX]: https://www.latex-project.org/
[hugo]: https://gohugo.io/
