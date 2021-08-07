# Latest-news-from-the-R-Markdown-family

Happy spring from the R Markdown family! We are excited to share a few package updates that have been keeping us busy so far this year.
1. rmarkdown
Latest release
Last rmarkdown release 2.7 cran badge

We are proud to share that rmarkdown (https://pkgs.rstudio.com/rmarkdown/) version 2.7 is on CRAN. rmarkdown is a package that helps you create dynamic documents that combine code, rendered output (such as figures), and markdown-formatted text.

You can install rmarkdown from CRAN with:

install.packages("rmarkdown")

First, the rmarkdown package’s documentation site recently got a makeover and new place to live, now at: https://pkgs.rstudio.com/rmarkdown/

You may also notice a new hex sticker design too — thanks to our artist Allison Horst for reimagining our iconic quill!
The new rmarkdown hex sticker design with a green quill, by Allison Horst

Figure 1: rmarkdown hex by Allison Horst

Below, we share highlights from the latest release, but you might want to look at the release notes for the full details.
Sass and SCSS support for HTML-based output

The biggest news is that it is now possible to use Sass (and, by extension, SCSS) to style your HTML-based outputs. This new functionality builds on the sass package, which provides bindings to LibSass, a fast Sass compiler written in C++. Sass is a mature and stable CSS extension language that makes styling modern websites less complex and more composable. If you want to learn more, this sass vignette is a solid place to start.

How is Sass useful for R markdown users?

    “Sass lets you use features that don’t exist in CSS yet like variables, nesting, mixins, inheritance and other nifty goodies that make writing CSS fun again.”

    https://sass-lang.com/guide

Files with .sass or .scss extension provided to html_document’s css parameter are now compiled to CSS using the sass package (thanks, [@cpsievert](https://github.com/cpsievert), #1706).

In the rest of this post, we’ll walk through a simple way to start using Sass with R Markdown. Even if you never thought CSS was fun to write in the first place, we hope this will help you the value of using Sass to simplify your styling of R Markdown HTML-based outputs. And, in case you missed it, Pandoc has added support that makes it easier to style your markdown text with CSS. We’ll start with showing you those features first with plain CSS, then add in the Sass layer on top.
Using CSS to style an html_document

Let’s start with a simple single document with this in the YAML:

---
output:
  html_document:
    css: custom.css
---

Inside that .css file, you could define CSS custom properties for some colors, then use them inside a simple CSS rule like my-color:

:root {
  --green: #212D2C;
  --sky: #A9FDFF;
}

.my-color {
  background-color: var(--green);
  color: var(--sky); 
  padding: 1em;
}

To apply your CSS rules in the body of your document, you could write raw HTML:

This is a color word.

would produce…

This is a color word.

This works, but can clutter up your .Rmd with HTML. Instead, you may achieve the same result writing with Pandoc’s bracketed Spans, which allows you to keep your markdown text cleaner:

This is a [color]{.my-color} word.

would produce…

This is a color word.

To apply CSS to a full sentence, or even longer text, you can create divs using Pandoc’s fenced Div blocks:

::: {.my-color}
All of these words are colored.
:::

would produce…

All of these words are colored.

Now, so far this is still a plain .css file- nothing too fancy yet! Let’s make it a .scss file instead, and add some fancier logic.
Using Sass to style an html_document

We’ll refactor our previous CSS from above to sass-ify it. We’ll use the SCSS (Sassy CSS) syntax. Sassy CSS builds on the existing syntax of CSS, and is a good way to get started using Sass because any valid CSS is also valid SCSS. The first thing we’ll do is save our file now with the .scss file extension:

---
output:
  html_document:
    css: custom.scss # update this!
---

Just like CSS, SCSS uses semi-colons and curly braces. The main difference is that we’ll use the $ symbol to make something a variable:

$green: #212D2C;
$sky: #A9FDFF;

.my-color {
  background-color: $green;
  color: $sky;
  padding: 1em;
}

We apply the style in the exact same way as before:

This is a [color]{.my-color} word.

would produce…

This is a color word.

But there is still a lot more that we can do. Among other things, Sass allows you nest your CSS selectors in a way that follows the same visual hierarchy of your HTML. This can make your CSS rules easier to read and write. Let’s add a simple nested rule to change the link text color to a new variable $green:

$green: #212D2C;
$sky: #A9FDFF;
$cyan: #36C9B4;
  
.my-color {
  background-color: $green;
  color: $sky;
  padding: 1em;
  
  a {
    color: $cyan;
  }
}

Let’s switch to using Pandoc divs in the body of our document to apply this style:

::: {.my-color}
This is a link that will be [green](https://pkgs.rstudio.com/rmarkdown/).
:::

would produce…

This is a link that will be green.

Sass also provides additional processing abilities like @extend rules, which you’d use when one class should have all the styles of another class, as well as its own specific styles.

Let’s separate my-color now from a new class called my-link. This new class will do some clever, sassy things:

    Build on the my-color class with the @extend rule,

    Add a unique $cyan for link text,

    Add hover effects for links using the Sass parent selector, &, and

    Use the Sass rgba() color function to apply an alpha level to our existing $green color variable (note that it begins life as a hex color!).

$green: #212D2C;
$sky: #A9FDFF;
$cyan: #36C9B4;
  
.my-color {
  background-color: $green;
  color: $sky;
  padding: 1em;
}

.my-link a {
  @extend .my-color;
  color: $cyan; 

  &:hover {
      background-color: rgba( $green, .5 );  
      color: white;
  }
}

::: {.my-link}
This is a link that will be [cyan](https://pkgs.rstudio.com/rmarkdown/).
:::

would produce…

This is a link that will be cyan.

Now we’ve made it so links will have a lighter background color upon hover, and the text turns white. One more little thing: Pandoc also works with ID attributes. Let’s add a special ID selector for links with the cookbook ID. We’ll start the rule with the hash (#) character (instead of the . we used for classes), followed by the id of the element.

#cookbook a {
  text-decoration-style: wavy;
}

::: {#cookbook}
This is a link that will be [wavy](https://bookdown.org/yihui/rmarkdown-cookbook/).
:::

would produce…

This is a link that will be wavy.

You can combine IDs, classes, and even nest bracketed spans inside fenced divs. Let’s do it all!

::: {.my-link}
This is a link that will be [cyan](https://pkgs.rstudio.com/rmarkdown/), 
but you can read more in [[The Cookbook](https://bookdown.org/yihui/rmarkdown-cookbook/)]{#cookbook}.
:::

would produce…

This is a link that will be cyan, but you can read more in the Cookbook.

We hope this short walk-through inspires you to test out this new feature, and consider how using Sass might help you customize your HTML-based outputs with R Markdown. To learn more, check out the sass R package vignette. Look out for our future blog posts and resources that will showcase how to use these features with R Markdown.

For shorter styling rules, you could instead use knitr’s sass or scss engines (also powered by the sass package) to provide those rules inline in a code chunk (without lugging around a separate external style file in your project). Thanks to Emily Reiderer for this contribution!

The sass/scss code chunks are compiled through the sass::sass() function. This means you can write Sass code directly into a code sass chunk and the resulting CSS will be included in the output document. For example, this kind of code chunk can exist anywhere in your .Rmd to add styles:

```{scss}
$green: #212D2C;
$sky: #A9FDFF;
$cyan: #36C9B4;
  
.my-color {
  background-color: $green;
  color: $sky;
  padding: 1em;
}
```

When you knit, the style will be applied to the whole document, so you can put these chunks at either the top or bottom of your .Rmd and they will work the same way (i.e., styles will be applied to all content, not just content below the sass/scss chunk). You can read more about these knitr engines in the R Markdown Cookbook.
Simplified custom blocks support for LaTeX

The Pandoc syntax for fenced divs is a powerful feature (as shown above), but one limitation is that Pandoc currently only processes fenced divs for HTML output; fenced divs are silently ignored when rendering to other output formats. For R Markdown users, the rmarkdown package extends the fenced div syntax to better support LaTeX/PDF outputs. Since rmarkdown 1.16, it is possible to opt-in and use fenced divs to produce LaTeX environments by adding a special attribute.

::: {.verbatim data-latex=""}
We show some _verbatim_ text here.
:::

Its LaTeX output will then be:

\begin{verbatim}
We show some \emph{verbatim} text here.
\end{verbatim}

rmarkdown 2.7 simplifies the opt-in of this feature by using a shorter name for the attribute: latex. This example would lead to the same result as above:

::: {.verbatim latex=true}
We show some _verbatim_ text here.
:::

In addition, latex=1 could also be used as an alias for latex=true. This attribute accepts a string that will be appended to the opening line of the LaTeX environment:

::: {.name latex="[options]"}
content that can be Markdown syntax 
:::

would produce

\begin{name}[options]
content that can be Markdown syntax
\end{name}

In the R Markdown ecosystem, we call these “Custom Blocks”. For the bookdown package, custom blocks replace the special block and block2 knitr engines (see: https://bookdown.org/yihui/bookdown/custom-blocks.html).
2. pagedown
Latest release
Last pagedown release 0.14 cran badge

We are proud to announce that pagedown version 0.14 is now on CRAN. pagedown is a package to help paginate HTML output with CSS print and paged.js.

You can install pagedown from CRAN with:

install.packages("pagedown")

Below we share important highlights from the latest release, but you might want to look at the release notes for the full details.
Paged.js upgrade

pagedown is powered by the awesome paged.js (https://www.pagedjs.org/) library and thanks to the help of Romain Lesur, it has been updated from 0.1.32 (03-2019) to 0.1.43 (10-2020). This is an important upgrade as it offers speed improvement and fixes several bugs.

All features of paged.js v0.1.43 can now be used with pagedown. To read up on these features, see the release notes and a series of posts:

    https://www.pagedjs.org/posts/2020-02-25-weekly/
    https://www.pagedjs.org/posts/2020-03-03-update-pagedjs-0-1-39/
    https://www.pagedjs.org/posts/2020-04-01-pagedjs-0-1-40/
    https://www.pagedjs.org/posts/2020-06-22-pagedjs-0-1-42/

On the R package side, this upgrade has fixed an issue with counter-reset CSS property. We have updated our templates accordingly, and we advise template authors to do the same. The default value of counter-reset is now correctly set to 0 instead of 1. This means that to reset the page CSS counter to 1, you now need to use counter-reset: page 1; instead of counter-reset: page;

/* reset page numbering for main content */
.main .level1:first-child h1 {
  counter-reset: page 1;
}

And more little things…

We have also made some smaller but important changes to the pagedown formats:

    New lot-unlisted and lof-unlisted arguments to remove the list of figures and the list of tables in html_paged().
    Support for fig_caption = FALSE in html_resume().
    Better flextable support in html_paged().


