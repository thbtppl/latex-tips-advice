# LaTeX tips, advice and best practices for scientific writing

## Who's this for?

As of 2021, LaTeX2e (LaTeX) has grown to a mature ecosystem for typesetting beautiful documents with endless design possibilities.
However, it has a steep learning curve, and writing a large document can be sometimes daunting, especially for people without a 'coding' mindset.
Since [LaTeX3 won't replace and unify LaTeX2e components](https://tex.stackexchange.com/a/572177), you still need to load manually many individual packages and will probably deal with rather obscure commands at times.

This series of tips and tricks targets beginner to intermediate LaTeX users and compiles recommendations gathered along the writing of my PhD thesis.
The following best practices are generic, but are mainly focused on the generation of scientific PDFs using the default `pdflatex` engine.
Feel free to raise an issue or submit a pull request if you see something is missing or if you disagree with the following claims!

## Contents

[My setup](#my-setup)

[General advice](#general-advice)

## My setup

* [TeXLive](https://www.tug.org/texlive/)
* [Visual Studio Code](https://code.visualstudio.com/) with the amazing [LaTeX workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) extension
* [Matplotlib](https://matplotlib.org/) for 2D vector graphics 
* [Ipe](http://ipe.otfried.org/) for vector drawing
* LaTeX engine: `pdflatex`

I recommend using `latexmk` (default in LaTeX workshop) to compile the document.
The [`latexmkrc`](examples/latexmkrc) config file placed at the root of your project allows to declare your main source file, the desired PDF output and the path to your self-defined class/package/preamble files.
You can also declare the folders containing your external graphics, which is more [memory efficient](http://www.texfaq.org/FAQ-graphicspath) than using `\graphicspath` within your `.tex` files.


## General advice

* Use as few packages as possible and understand the preamble of your LaTeX document.

* Focus on the content rather than the layout of the document, BUT do not ignore errors and warnings. The more effort you put to understand them, the more helpful it is to comprehend LaTeX's logic and the more efficient the writing process will be.

* As a corollary, it is better to work continuously with a warning-free camera-ready template rather than going in panic mode 10 hours before your deadline trying to solve layout issues.

* Do not follow any random advice found online. Many suggestions are plain wrong, and you're likely to use obsolete snippets of code.

* Instead, have at least a quick look in the documentation of packages you're using. This will help you to use their commands as intended, and it will most likely save you hours of debugging.

* Answers to your questions/issues are most likely on the [TeX FAQ](http://www.texfaq.org/) and on [TeX SE](https://tex.stackexchange.com/).

* In your document, separate the format/layout from the actual content:
    * Create a modular document with different files
    * Define commands with semantically appropriate names, such as `\newcommand*{\contentstitle} {Table of Contents}`
    * Do not put hard-coded settings within your content

* Use the `import` package to include other files if you have a multi-folder project structure. It is more flexible than the `\include` and `\input` commands to the extent that any relative path to the external file can be handled.

* Use `git` and set up a remote repository (e.g. GitHub). Besides offering a natural back-up, you can quickly see current changes within a complex document, and see where you introduced code that produced bugs.

* Use UTF-8 encoding (`\usepackage[utf8]{inputenc}`) to typeset your document using accents (ü,é) directly, and to allow spell-checkers parsing source files to correctly process such words.

* Do not use obsolete packages or commands. Add
    ```
    \usepackage[l2tabu,orthodox]{nag}
    \usepackage[all,warning]{onlyamsmath}
    ```
    in your preamble to automatically warn you when using deprecated features in terms of packages and commands, including maths.


## PDF production

Editors and universities often require your document to be compliant with the [PDF/A standard](https://www.pdfa.org/wp-content/uploads/2013/05/PDFA_in_a_Nutshell_211.pdf), that is suitable for long-term archiving with a device-independent display.
Among other features it must embed all fonts and include standardised metadata.

* The best way to obtain LaTeX documents close to PDF/A compliance is to use the [pdfx](https://ctan.org/pkg/pdfx?lang=en) package.

* You could then include the metadata of your document by adding before `\documentclass`,
    ```
    \begin{filecontents*}{\jobname.xmpdata}
      \Author       {John Doe}
      \Title        {My Document Title}
      \Language     {en-GB}
      \Keywords     {keyword1\sep keyword2\sep keyword3}
      \Subject      {PhD thesis}
      \Copyright    {\copyright 2021 John Doe}
      \CopyrightURL {https://creativecommons.org/licenses/by-nc-nd/4.0/legalcode}
    \end{filecontents*}
    ```

* On Linux, you can use the `pdffonts` utility to check whether your fonts are embedded into your generated PDF.

* To validate your PDF/A compliance and get a detailed report, you can use
    * [VeraPDF](https://verapdf.org/) (free)
    * The preflight tool of [Adobe Acrobat DC Pro](https://acrobat.adobe.com/us/en/acrobat/acrobat-pro.html) (non-free but free 7-day trial)

* The preflight tool can also repair non-compliant PDFs produced with `pdflatex`.


## Typesetting

Use one sentence per line in your document.
It helps to write readable and maintainable source code.
In this way, one change in your document will correspond to one sentence only.
This leads to easier to read commits and `git diff`'s.

The only recommended way to start a new paragraph is using a line break:
```
... and the sentence ends.

This is a new paragraph.
```
Never use `\par` or `\\` manually.
Note this impacts on how you should obtain correct vertical spacing when developing your ideas in the text.
For example, you should write
```
described in \cref{eq1},
\begin{equation}
    y = x
\end{equation}
```
and not
```
described in \cref{eq1},

\begin{equation}
    y = x
\end{equation}
```

To force whitespace after a user-defined commmand, add empty curly braces: `\com{}` instead of `\com`

Pay attention to correct spacing when using a period within a sentence.
The `.\` and `\@` commands tell LaTeX your sentence does not end.
    - `This is a sentence w.\ a forced inter-word space.`
    - `This is done by XYZ\@. This is a new sentence.`
The latter case should not happen often if you use the `glossaries[-extra]` package for your abbreviations.


## Syntax and spell-check

You can detect 'hidden' Unicode characters by searching the string `[^\x00-\x7f]` as a regular expression.

Use the Python package `pdfx` https://pypi.org/project/pdfx/ (not to be mistaken with the [pdfx](https://ctan.org/pkg/pdfx?lang=en) LaTeX package) to check for broken links in your PDF.

The `lacheck` utility is good at detecting questionable syntax in your .tex files. Note however some false positives will be raised due to the use of packages.
No point in using `chktex`.

`TeXCheckR` is at the moment the best syntax/spell checker for LaTeX.

You can check your bibliography using `biber --tool --validate-datamodel references.bib`

Floats (graphics and tables)

I suggest to output your graphics in the EPS format, and to convert them to PDF using the `epstopdf` utility included in TeX distributions.
The .pdf backend of matplotlib is not optimized, and I always observe a factor of size decrease between 10 and 100 between exporting in .eps and converting to pdf instead of saving directly in pdf.
Also, @wookai reports the quality of pdf is lesser than pdf.
The pgf format is useful, but is also known to yields bloated graphics files as opposed to eps @wookai.

The utility `pdfcrop` is very useful to remove the unneeded white space surrounding your figure.

Add `\g@addto@macro\@floatboxreset\centering` to your preamble to avoid the need of adding `\centering` for each figure.

Everyone says LaTeX is the worst for float placement.
This is simply wrong.
I would suggest using `\begin{figure}` without any placement specifier '`[!t]`' until you have the final version of the document.
From that point onwards, you can add '`[!t]`' to force the float at the top, or even '`[!h]`' is usually good at
Stop saying LaTeX messes up graphs!

## Packages

It is sad many journal editors force users to use the archaic `natbib` instead of `biblatex`, for example. 

% --- Warn when use of obsolete/deprecated/non-amsmath commands
% \RequirePackage[l2tabu,orthodox]{nag}
% \RequirePackage[all,warning]{onlyamsmath}

If compatible, try to pass the general options of your project in the `\documentclass` command.
They will be seen by relevant packages.
For example, the language option in `\documentclass[UKenglish]{article}` will be seen by `babel` and `cleveref`.


Mind the order of packages in the preamble:

inputend should be loaded before fontenc

if using the option [babel=true], microtype should be loaded after babel

biblatex should be loaded after babel

parskip should be loaded after tocloft

cleveref should be loaded after hyperref

bookmark sohuld be loaded after cleveref?

glossaries[-extra] should be loaded last

The manual of pdfx recommends loading among the first packages, but I would recommend loading it just before hyperref since it automatically loads hyperref.
An end of an preamble ensuring maximal compatibility looks like

```
\usepackage[a-2b]{pdfx}
\usepackage{hyperref}
\usepackage{cleveref}
\usepackage{bookmark}
```

## Bibliography

Find the biblatex package (e.g. biblatex-chicago) closest to the style you want to use.
Tweaking it to your needs should be straightforward as biblatex is easy to customise.
I used the plain biblatex package and defined my Harvard style in bibliography_style.tex

Remove the useless fields in the .bib entries

Never, ever trust .bib entries given by journals, editors or Zotero/Mandeley.
They are often filled with errors or typos.
It takes time to check individual fields, but I can't stress enough how many typos I was able to correct from a supposedly 'clean' entry.

Use title-case and capitalise all content words for titles. In this way, you can easily switch to sentence-case using a biblatex option.
Conversely if you forget that and the bibliography needs to be title-case, you will have add capitals manually if your biblatex style.

Surround words that remain capitalised at all times using curly brackets:
title = {A Numerical Study of the Temporal Eigenvalue Spectrum of the {Blasius} Boundary Layer}
But do not put the whole sentence between brackets (like stupid editors/software do) as it can affect the kerning between words.

For author names, use the "author = {Doe, John and Dummy, Name}" format. Using "author = {J. Doe}" is fine, but the former helps biblatex to clearly identify name particles and abbreviate correctly, especially for authors with prefixes.

Although many journals use the term "issue", the corresponding entry for biblatex is "number", not "issue".

Put an en-dash for pages: "100--200".

If an entry has "1" for the volume, double check whether there is an actual volume number.
Some editors mistakenly add it.

Use "date" instead of "year" with biblatex as it offers greater flexibility.

Add
```
\widowpenalty=10000
\clubpenalty=10000
```
to your preamble to force the avoidance of widows and orphans at the beginning/end of pages.
