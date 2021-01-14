# LaTeX tips, advice and best practices for scientific writing

## Who's this for?

As of 2021, LaTeX2e (LaTeX) has grown to a mature ecosystem for typesetting beautiful documents with endless design possibilities.
However, it has a steep learning curve, and writing a large document can be sometimes daunting, especially for people without a 'coding' mindset.
Since [LaTeX3 won't replace and unify LaTeX2e components](https://tex.stackexchange.com/a/572177), you still need to load manually many individual packages and will probably deal with rather obscure commands at times.

This series of tips and tricks targets beginner to intermediate LaTeX users and compiles recommendations gathered along the writing of my PhD thesis.
The following best practices are generic, but are mainly focused on the generation of scientific PDFs using the default `pdflatex` engine.

Feel free to raise an issue or submit a pull request if you see something is missing or if you disagree with the following claims!

## Contents

* [My setup](#my-setup)
* [General advice](#general-advice)
* [Packages](#packages)
* [Some typesetting advice](#some-typesetting-advice)
* [Maths](#maths)
* [Floating objects and captions](#floating-objects-and-captions)
* [Including graphics](#including-graphics)
* [PDF production](#pdf-production)
* [Bibliography](#bibliography)
* [Other resources](#other-resources)


## My setup

* [TeXLive](https://www.tug.org/texlive/)
* [Visual Studio Code](https://code.visualstudio.com/) with the amazing [LaTeX workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) extension
* [Matplotlib](https://matplotlib.org/) for data-driven vector graphics
* [Ipe](http://ipe.otfried.org/) for vector drawing
* LaTeX engine: `pdflatex`

I recommend using `latexmk` (default in LaTeX workshop) to compile the document.
The [`latexmkrc`](examples/latexmkrc) config file placed at the root of your project allows to declare your main source file, the desired PDF output and the path to your self-defined class/package/preamble files.
You can also declare the folders containing your external graphics, which is cleaner than using `\graphicspath` within your `.tex` files.


## General advice

* Use as few packages as possible and understand the preamble of your LaTeX document.

* Focus on the content rather than the layout of the document, BUT do not ignore errors and warnings. The more effort you put to understand them, the more helpful it is to comprehend LaTeX's logic and the more efficient the writing process will be.

* As a corollary, it is better to work continuously with a warning-free camera-ready template rather than going in panic mode 10 hours before your deadline trying to solve layout issues.

* Do not follow any random advice found online. Many suggestions are plain wrong, and you're likely to end up with conflicting snippets of code in your document.

* Instead, have at least a quick look in the documentation of packages you're using. This will help you to use their commands as intended, and it will most likely save you hours of debugging.

* Answers to your questions/issues can be found on the [TeX FAQ](http://www.texfaq.org/) or on [TeX SE](https://tex.stackexchange.com/).

* In your document, separate the format/layout from the actual content:
    * Create a modular document with different files
    * Define commands with semantically appropriate names <!--, such as `\newcommand*{\contentstitle} {Table of Contents}`-->
    * Do not put hard-coded settings within your content

Add point that one modification affects whole document use vec and chapterspace example

* Use the `import` package to include other files if you have a multi-folder project structure. It is more flexible than the `\include` and `\input` commands to the extent that any relative path to the external file can be handled. For example, you could have in your main file

    ```
    \import{./}{titlepage.tex}
    \import{./chapters/chapter1/}{chapter1.tex}
    \import{./chapters/chapter2/}{chapter2.tex}
    \import{./appendices/}{appendix1.tex}
    ```
    Don't forget the trailing '`/`' !

* Use `git` and set up a remote repository (e.g. GitHub). Besides offering a natural back-up, you can quickly see current changes within a complex document, and see where you introduced code that produced bugs.

* Use UTF-8 encoding (`\usepackage[utf8]{inputenc}`) to typeset your document using accents (ü,é) directly, and to allow spell-checkers parsing source files to correctly process such words.

* Do not use obsolete packages or commands. Add
    ```
    \usepackage[l2tabu,orthodox]{nag}
    \usepackage[all,warning]{onlyamsmath}
    ```
    in your preamble to automatically warn you when using deprecated features in terms of packages and commands, including maths.


## Packages

* Packages you should probably use

    * `import`: include external files from different folders
    * `fontenc`: font encoding
    * `ìnputenc`: input encoding
    * `babel`: use proper typographical rules adapted to document language
    * `microtype`: automatic font spacing adjustments for improved aesthetics
    * `mathtools`: math support with improvements to the widely used `amsmath` package
    * `biblatex`: bibliography
    * `csquotes`: in-line and display quotations consistent with the document language
    * `tocloft`: customize layout of the list of figures/tables
    * `titlesec`: customize section titles and page headers/footers
    * `tocbibind`: include bibliography in the table of contents
    * `appendix`: appendix support and typesetting of appendix titles
    * `setspace`: line spacing of the document
    * `geometry`: customize page layout and margins
    * `parskip`: adjust spacing between paragraph and indentation
    * `footmisc`: customize the appearance of footnotes
    * `booktabs`: the only package for publication-quality tables
    * `graphicx`: include external graphics
    * `tikz`: produce vector graphics
    * `caption`: customize the appearance of captions
    * `subcaption`: allows individual caption for each subfigure of a figure
    * `siunitx`: ensure consistent use of numbers and/or units across the document
    * `pdfx`: for PDF/A compliance
    * `xcolor`: support for colors with different color models
    * `hyperref`: to produce hyperlinks everywhere in the document
    * `cleveref`: smart cross-referencing for document components
    * `bookmark`: handle PDF bookmarks
    * `glossaries-extra`: support for glossaries, lists of symbols/acronyms

* If compatible, try to pass the general options of your project to the `\documentclass` command. They will be seen by relevant packages.
For example, the language option in `\documentclass[UKenglish]{article}` will be seen by both `babel` and `cleveref`.

* Mind the order of packages in the preamble (the joys of LaTeX):

    * `inputenc` should be loaded before `fontenc`
    * If using the option [babel=true], `microtype` should be loaded after babel
    * `biblatex` should be loaded after `babel`
    * Some font packages like `newpxtext` should be loaded after `babel`
    * `parskip` should be loaded after `tocloft`
    * `cleveref` should be loaded after `hyperref`
    * `bookmark` should be loaded after `cleveref`
    * `glossaries[-extra]` should be loaded last

    The manual of `pdfx` recommends to load it as early as possible, but since it automatically loads `hyperref` I would recommend loading it just before `hyperref`.


## Some typesetting advice

* Use one sentence per line in your document:

    ```
    This is a sentence.
    Followed by another sentence.
    ```
    and not

    ```
    This is a sentence. Followed by another sentence.
    ```
    On the one hand, it helps to write readable and maintainable source code. On the other hand, it leads to easier to read commits and `git diff`'s as one change in your document will correspond to one sentence only.

* The only correct way to start a new paragraph is using a line break:

    ```
    ... and the sentence ends.

    This is a new paragraph.
    ```
    Never use `\par` or `\\`. Note this impacts on how you obtain correct vertical spacing when developing your ideas in the text. For example, you should write

    ```
    described in \cref{eq1},
    \begin{equation}
      y = x
    \label{eq1}
    \end{equation}
    ```
    and not

    ```
    described in \cref{eq1},

    \begin{equation}
      y = x
    \label{eq1}
    \end{equation}
    ```
    as you would get wrong vertical spacing.

* To force whitespace after a user-defined commmand that produces text, add empty curly braces: `\com{}` instead of `\com`

* Use '`~`'

* Pay attention to correct spacing when using a period within a sentence. The `.\` and `\@` commands tell LaTeX your sentence does not end.
    * `This is a sentence w.\ a forced inter-word space.`
    * `This is done by XYZ\@. This is a new sentence.`

    The latter case should not happen often if you use the `glossaries[-extra]` package for your abbreviations with a `\gls{xyz}` command.

* Add to your preamble

    ```
    \widowpenalty=10000
    \clubpenalty=10000
    ```
    to force LaTeX to avoid widows and orphans (dangling lines at the beginning or end of a paragraph separated from the rest).


## Maths

* Use the `mathtools` or at least the `amsmath` package.

* Use `\(x = y + z\)` rather than `$x = y + z$` for in-line mathematics. The former is proper LaTeX syntax, whereas the latter is primitive TeX syntax and yields obscure error messages.

* Similarly, for displayed, centered equations, use `\begin{equation} ... \end{equation}` (or `\[ ... \]`) instead of `$$ ... $$` which is again a TeX primitive and gives wrong vertical spacing.

* For long, multi-line or several equations, read the section 3 of the [`amsmath` manual](http://mirrors.ctan.org/macros/latex/required/amsmath/amsldoc.pdf) to see which command is more suitable for your needs: `subequations`, `align`, `split`, `gather`...

* Use `\DeclareMathOperator` to define up-right operators with correct spacing. Don't use `\mathrm`. For example: `\DeclareMathOperator{\spn}{span}`.

* Use `\boldsymbol` to reliably get bold math variables.

* Do not use the vertical bar character '`|`'. You can get scalable vertical bars by using `\lvert` or `\rvert`.

* `mathtools` provides the handy `\DeclarePairedDelimiter` command to declare scaling delimiter operators. You could do:

    ```
    \DeclarePairedDelimiter\abs{\lvert}{\rvert}    % absolute value: |x|
    \DeclarePairedDelimiter\norm{\lVert}{\rVert}   % norm: ||x||
    \DeclarePairedDelimiter\itor{\lbrack}{\lbrack} % right open interval: [x,y[
    ```

* Display equations are part of the flow of your document and you should treat them as an integral element of your paragraph. Therefore, if they end a sentence, they should end with a period. I would argue that using commas at the end of equations is more a matter of style, and could clutter your document.

* To get correct horizontal alignment in such necessary cases, you can use the `\phantom` command to mask the character and simulate the spacing:

    ```
    \begin{equation}
      A_{i} =
      \begin{cases}
        1 & \text{if } i = 0\phantom{.} \\
        2 & \text{if } i \neq 0.
      \end{cases}
    \end{equation}
    ```

* Adding a small space ('`\,`') in your integrals between the integrand and the differential is nicer:

    ```
    \begin{equation}
      I = \int_{0}^{+infty}f(x)\,dx
    \end{equation}
    ```

* Use the `siunitx` package to get consistent display of numbers and units across your document:

    ```
    \DeclareSIUnit{\nauticalmile}{NM}

    The radius of the circle is \SI{10}{\milli\metre}.
    Pressure is usually expressed in \si{\kilogram\per\metre\per\second}.
    It seems the ship has travelled \SI{100}{\nauticalmile}.
    I estimate at least \num{15000} parts in this work and probably \num{e6} cells in this experiment.
    ```
    and not

    ```
    The radius of the circle is 10 mm.
    Pressure is usually expressed in kg.m\textsuperscript{-1}.s\textsuperscript{-1}.
    It seems the ship has travelled 100 NM.
    I estimate at least 15,000 parts in this work and probably \(10^{6}\) cells in this experiment.
    ```
    If you're writing in British English, you can activate automatically the comma separator between thousands.

* If you're missing vertical space between multiple equations, you can adjust it as follows:

    ```
    \newcommand*{\mymathspace}{0.30\baselineskip}

    \begin{subequations}
      \begin{align}
        f_{1}(x) = g(x) - h(x)\\[\mymathspace]
        f_{2}(x) = g(x) + h(x)
      \end{align}
    \end{subequations}
    ```

## Floating objects and captions

Many people say LaTeX is the worst for float placement. This is simply wrong. By default, LaTeX endeavours to put it closest to the surrounding text in the source file, at the top or on its own page if deemed appropriate.

* I would suggest to *not* add any placement specifier '`[!htbp]`' to your figures until you have the final version of the document.

* From that point onwards, you can add '`[!t]`' to put the float at the top, or even 'here if possible and at the top otherwise' ('`[!ht]`') if your object is placed in the wrong section. Think twice before leaving a figure in the middle of a page in terms of aesthetics.

* Add `\g@addto@macro\@floatboxreset\centering` to your preamble to avoid the need to add `\centering` for each figure.

* If you want to display a shorter version of your figure caption in the table of figures/tables, use the optional argument `[]` of `caption`.

* I find it more elegant to put the symbols (legend) of your figure in the caption. This is easily done with `tikz`. However, these symbols require a preceding `\protect` since `\caption` is a [fragile](http://www.texfaq.org/FAQ-protect) command.

* An example combining the two previous points is

    ```
    \usepackage{tikz}

    \newcommand*{\blackcircle}{\tikz{\node[draw=black,thick,scale=0.5,circle,fill=none] () {};}}

    \begin{figure}
      \includegraphics{chap_intro_diagram}
      \caption[Diagram with experimental data]{Diagram with experimental data (\protect\blackcircle)}
      \label{fig:chap-intro-diagram}
    \end{figure}
    ```


## Including graphics

* Use one script per figure and put a comment in your LaTeX document that gives the command used to create the figure and its path. This is an efficient way of working as you'll most likely need to adjust or recreate your graphics.

* With Matplotlib, I suggest to output graphics in the EPS format. Then, use the `epstopdf` utility included in most TeX distributions to convert them to PDF.

* Finally, use the `pdfcrop` utility to remove the unneeded white space surrounding your figure. Using `bbox_inches = 'tight'` in the `savefig` function of Matplotlib is not enough.

* The reason is that the PDF backend of Matplotlib is not optimized. I always observe a O(10) or a O(100) factor of size decrease between exporting in EPS and converting to PDF instead of saving directly in PDF.

* If you have many data points and plot it (with e.g. `plot`, `scatter` or a `pcolormesh`) the vector format is not appropriate and will lead to bloated, slowly-loading files. Use the `rasterized = True` keyword to alleviate that issue. Note that you need a DPI high enough to ensure quality, and you'll have to save the figure directly in PDF.

* Ensure your Matplotlib figure layout and fonts match that of your LaTeX document. I recommand using a [style sheet](https://matplotlib.org/3.3.3/gallery/style_sheets/style_sheets_reference.html) where you can replicate your LaTeX preamble and globally define your plot settings.

* Your [style sheet](./examples/latex.mplstyle) should be put in `MPLCONFIGDIR/stylelib`. On Mac OS, `MPLCONFIGDIR` is usually `~/.matplotlib`. On Linux, `MPLCONFIGDIR` is usually `~/.config/matplotlib`. You can get the value of `MPLCONFIGDIR` by calling the Matplotlib function `get_configdir()` in Python. Then in your figure script, add the line `matplotlib.pyplot.style.use('thesis')` to use your style sheet. You could then create a style sheet for your thesis, for your articles and reports and quickly jump from one to another.

* Directly plot your graphics at the correct, final size to ensure consistent fonts with your LaTeX document. I found that the `set_size` function in the [J. Walton's guide](https://jwalton.info/Embed-Publication-Matplotlib-Latex/) does the job perfectly. Determine your LaTeX document text width using `\showthe\textwidth` or by checking the log file. Then you need to decide which portion of the width your figure should take. Eventually, just include the figure in LaTeX, and do *not* adjust nor distort its size with `[width=XXX\textwidth]` or `[scale=XXX]`.

* Use the same colors in matplotlib and LaTeX. I like to use HTML color codes in Matplotlib (`MyRed= '#cc0000'`) and in LaTeX thanks to the `xcolor` package: `\definecolor{MyRed}{HTML}{cc0000}`.

* Do not add the figure extension in `\includegraphics`.

In your LaTeX source files, including figures should look like

```
% python3 ${HOME}/paper1/plots/plot_variable.py
\begin{figure}
  \includegraphics{chap_results_variable}
  \caption{My results}
  \label{fig:chap-results_variable}
\end{figure}
```


## Syntax and spell-check

* The R package [`TeXCheckR`](https://github.com/HughParsonage/TeXCheckR) is an excellent syntax/spell checker for LaTeX. It can also check the structure of your `.bib` file.

* The [LTeX](https://marketplace.visualstudio.com/items?itemName=valentjn.vscode-ltex) extension of VS Code is a good spell/grammar checker, based on [LanguageTool](https://languagetool.org/).

* The `lacheck` utility shipped with most LaTeX distributions is also good at detecting questionable syntax in your `.tex` files. Note some false positives will be raised due to the use of some packages. `chktex` performs poorly compared to `lacheck`.

* You can check your bibliography file using `biber --tool --validate-datamodel main.bib`.

* You can detect 'hidden' Unicode characters potentially messing up your files by searching the string `[^\x00-\x7f]` as a regular expression.

* Use the Python package [`pdfx`](https://pypi.org/project/pdfx/) (not to be mistaken with the [`pdfx`](https://ctan.org/pkg/pdfx?lang=en) LaTeX package) to detect broken hyperlinks in your PDF.


## Bibliography

* First, you may want to use the recent `biblatex` *package* with the `biber` *backend* which handles accentuated letters seamlessly. Avoid using the old `natbib` *package* which uses the `bibtex` *backend*. People are often [confused](https://tex.stackexchange.com/questions/25701/bibtex-vs-biber-and-biblatex-vs-natbib).

* Find the biblatex package (e.g. `biblatex-phys`) closest to the bibliography style you want to use. Tweaking it to your needs is straightforward as `biblatex` is easy to customize.

* **Never ever trust** `.bib` entries given by journals, editors or Zotero/Mandeley. They are often filled with errors and do not reflect the author/title information of the publication. Yes, it takes time to check individual fields, but I can't stress enough how many typos I was able to find.

* Remove the useless fields in the `.bib` entries.

* The [doi2bib](https://doi2bib.org/) website is very handy.

* Capitalise all content words for titles:

    ```
    title = {On the Electrodynamics of Moving Bodies}
    ```
    instead of

    ```
    title = {On the electrodynamics of moving bodies}
    ```
    In this way, you can easily switch from title-case to sentence-case using a `biblatex` option. Conversely if you forget that and the bibliography needs to be title-case, you will have to add all missing capitals manually.

* Surround words that remain capitalised at all times using curly brackets:

    ```
    title = {Protein Measurement with the {Folin} Phenol Reagent}
    ```
    Make sure it is the entire word and not the first letter (e.g. not `{F}olin`) as the kerning (spacing) between letters could be affected.
    But do not put the whole title between double brackets (like some stupid editors automatically do) such as

    ```
    title = {{Protein Measurement with the Folin Phenol Reagent}}
    ```
    since it won't be subjected to style changes.

* For author names, use the `author = {LastName, FirstName}` format. Using `author = {FirstName LastName}` is fine for simple names, but the former helps `biblatex` to clearly identify name particles and abbreviate correctly, especially for authors with prefixes like 'de' in French or 'van' in Dutch or whose last name contains two words.

* For long given/last names (e.g. Brazilian), you may use

    ```
    author = {given=Carlos Santos de Azevedo, given-i=C S {de\nopunct} A, family=Silva}
    ```
    to get correct abbreviation in your reference list.

* The only acceptable separator between several authors is '`and`'.

* Although many journals use the term "issue" as a subdivision in the volume, the corresponding entry for numerical values in `biblatex` is `number`, not `issue`.

* The correct hyphenation for pages is an en-dash: `pages = {100--200}`, not a hyphen '`-`'.

* If an entry has `volume = {1}`, double check whether there is an actual volume number. Some editors mistakenly add it.

* Use `date` instead of `year` with `biblatex` as it offers greater flexibility.

* If an entry is in a different language than the main language of the document, use the `langid` field to get correct hyphenation patterns.


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


## Other resources

* [An essential guide to LaTex2e usage](http://mirrors.ctan.org/info/l2tabu/english/l2tabuen.pdf)
* [Chicago Manual of Style](https://www.chicagomanualofstyle.org/)
