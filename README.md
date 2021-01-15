# LaTeX tips, advice and best practices for scientific writing

## Who's this for?

As of 2021, LaTeX2e (LaTeX) has grown to a mature ecosystem for typesetting beautiful documents with endless design possibilities.
However, it has a steep learning curve, and writing a large document can be sometimes daunting, especially for people without a 'coding' mindset.
Since [LaTeX3 won't replace and unify LaTeX2e components](https://tex.stackexchange.com/a/572177), you still need to load manually many individual packages and will probably deal with rather obscure commands at times.

This series of tips and tricks targets beginner to intermediate LaTeX users and compiles modern recommendations gathered along the writing of my PhD thesis.
The following best practices are generic, but are mainly focused on the generation of scientific PDFs using the default `pdflatex` engine.
You can see in action what is listed here in the [PhD thesis template](https://github.com/thbtppl/latex-thesis-imperial) I shared.

Feel free to raise an issue or submit a pull request if you see something is missing or if you disagree with the following claims!


## Contents

* [My setup](#my-setup)
* [General advice](#general-advice)
* [Packages](#packages)
* [Some typesetting advice](#some-typesetting-advice)
* [Maths](#maths)
* [Floating objects and captions](#floating-objects-and-captions)
* [Including graphics](#including-graphics)
* [Syntax and spell check](#syntax-and-spell-check)
* [Bibliography](#bibliography)
* [PDF production](#pdf-production)
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

* Do not follow any random advice found online. Many suggestions are either outdated or plain wrong, and you're likely to end up with conflicting snippets of code in your document.

* Instead, have at least a quick look in the documentation of packages you're using. This will help you to use their commands as intended, and it will most likely save you hours of debugging.

* Answers to your other questions/issues can be found on the [TeX FAQ](http://www.texfaq.org/) or on [TeX SE](https://tex.stackexchange.com/).

* In your document, separate the format/layout from the actual content:
    * Create a modular document with different folders and files
    * Do not put hard-coded settings/values within your content (e.g. `\vspace{10cm}`)
    * Instead, define commands with semantically appropriate names for your settings, lengths, maths, colors...

* By defining global commands, your source files are more readable, and you ensure a consistent display of your document. Furthermore, only one line shall be changed if you wish to adjust the setting, instead of manually correcting each occurrence. For example, you can define a bold matrix in math mode following `\newcommand*{\mat}[1]{\boldsymbol{#1}}` and can be quickly changed to a double-bar notation everywhere using `\newcommand*{\mat}[1]{\overline{\overline{#1}}}`.

* Use `\newcommand*` and `\renewcommand*` instead of the non-starred variants as a good practice. The [starred](https://tex.stackexchange.com/questions/1050/whats-the-difference-between-newcommand-and-newcommand) variants ensure you're defining a 'short' command whose arguments cannot contain a line break or a new paragraph. If using a much longer argument, you should probably think about [defining a new environment](https://www.overleaf.com/learn/latex/Environments#Defining_environments_with_parameters).

* Use the [`import`](https://ctan.org/pkg/import?lang=en) package to include other files if you have a multi-folder project structure. It is more flexible than the `\include` and `\input` commands to the extent that any relative path to the external file can be handled. For example, you could have in your main file:

    ```latex
    \import{./}{titlepage.tex}
    \import{./chapters/chapter1/}{chapter1.tex}
    \import{./chapters/chapter2/}{chapter2.tex}
    \import{./appendices/}{appendix1.tex}
    ```
    Don't forget the trailing '`/`' !

* Use `git` and set up a remote repository (e.g. GitHub). Besides offering a natural back-up, you can quickly see current changes within a complex document, and see where you introduced code that produced bugs.

* Use UTF-8 encoding (`\usepackage[utf8]{inputenc}`) to typeset your document using accents (ü, é) directly, and to allow spell-checkers parsing source files to correctly process such words.

* Do not use obsolete [packages](https://tex.stackexchange.com/a/26200) or commands. Add
    ```latex
    \usepackage[l2tabu,orthodox]{nag}
    \usepackage[all,warning]{onlyamsmath}
    ```
    in your preamble to automatically warn you when using deprecated features in terms of packages and commands, including maths.

* Using the [`microtype`](https://ctan.org/pkg/microtype?lang=en) package will always improve the aesthetics of your document, even with the default settings. However, do not forget to disable character protrusion in listed environments such as the table of contents:

    ```latex
    \microtypesetup{protrusion=false}

    \tableofcontents
    \listoffigures
    \listoftables

    \microtypesetup{protrusion=true}
    ```

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

    The manual of `pdfx` recommends loading it as early as possible, but since it automatically loads `hyperref` I would recommend loading it just before `hyperref`.


## Some typesetting advice

* Use one sentence per line in your document:

    ```latex
    This is a sentence.
    Followed by another sentence.
    ```
    and not

    ```latex
    This is a sentence. Followed by another sentence.
    ```
    On the one hand, it helps to write readable and maintainable source code. On the other hand, it leads to easier to read commits and `git diff`'s as one change in your document will correspond to one sentence only. Do not hesitate to break long lines, as the sentence will continue.

* The only correct way to start a new paragraph is using a line break:

    ```latex
    ... and the sentence ends.

    This is a new paragraph.
    ```
    **Never use** '`\par`' or '`\\`'. Note this consideration impacts on how you obtain correct vertical spacing when developing your ideas in the text. For example, you should write:

    ```latex
    described in \cref{eq1},
    \begin{equation}
      y = x
      \label{eq1}
    \end{equation}
    ```
    and not

    ```latex
    described in \cref{eq1},

    \begin{equation}
      y = x
      \label{eq1}
    \end{equation}
    ```
    as you would get wrong vertical spacing.

* To force whitespace after a user-defined command that produces text, add empty curly braces: `\com{}` instead of `\com`.

* Understand the power of '`~`' to indicate a non-breaking space, and prevent LaTeX from inserting a line break at this location. Examples include
    * (if you're not using `cleveref`) in references to named parts: `Chapter~1`, `Appendix~A`
    * between multiple given names of people: `Donald.~E. Knuth`, `Louis~XVI`
    * in mathematical sentences: `less than~\(\epsilon\)`, `define the function~\(f(x)\)`
    * in enumerations: `this is (1)~dumb; (2)~useless.`
    * in-text citations with a non-superscript numeric style ('[1]') bibliography : `as demonstrated in~\textcite{smith2000}`

    but are far from being exhaustive. Think of any sentence bit that must 'flow' and would bother the eye of the reader if the line is broken.

* Use the [`csquotes`](https://ctan.org/pkg/csquotes?lang=en) package to ensure the quotation marks are consistent with the main language of your document, with the `\textquote` command:

    ```latex
    % \usepackage[french]{babel}
    \usepackage[british]{babel}
    \usepackage{csquotes}

    % He said: \textquote{bonjour} % produces << bonjour >>
    He said: \textquote{hello}     % produces `Hello'
    ```
    Don't write quotations marks manually (backtick and quote signs).

* Pay attention to correct spacing when using a period within a sentence.
    * The `.\` command tells LaTeX that your sentence does not end: `This car w.\ a blue livery is nice.`
    * Use `\@` for a sentence-ending dot after a capital letter as LaTeX otherwise assumes an abbreviation: `France is part of the EU\@. The UK isn't anymore.`

    The latter case should not often happen if you use the `glossaries[-extra]` package for your abbreviations with a `\gls{xyz}` command.

* The correct way to obtain an ellipsis `(...)` punctuation symbol is to use `\ldots`. The `csquotes` package provides `\textelp{}` for `[...]`. 

* Use the `draft` option of `\documentclass` to highlight in black where your text or figures exceeds the margin boundary. In that case, don't forget to load `microtype` with the `final` option if using that package.

* Understand the difference between hyphens, en-dashes, em-dashes and minus signs: '`-`', '`--`', '`---`' and '`\(-\)`', respectively.

* Add to your preamble

    ```latex
    \widowpenalty=10000
    \clubpenalty=10000
    ```
    to force LaTeX to avoid widows and orphans (dangling lines at the beginning or end of a paragraph separated from the rest).

* If you use 'exotic' words for LaTeX, it doesn't know how to hyphenate them, potentially leading to [overfull box warnings](https://tex.stackexchange.com/a/34727). Use '`\hyphenation{Chi-ches-ter}`' in your preamble to manually declare hyphenation patterns.

* Also, LaTeX doesn't hyphenate words that are already hyphenated. In necessary cases you need to add '`\-`' in-text to force hyphenation, such as '`This is a three-dimen\-sional phenomenon.`'.

* Avoid manual spacing in your document like `\hspace{5mm}` or `\vspace{1cm}`. You should rather use commands predefined by your document class such as `\medskip`, `\bigbreak`, or font-dependant commands like `\enskip` or `\quad`. Great lists for horizontal and vertical spacing commands may be found [here](https://tex.stackexchange.com/a/7435) and [there](https://tex.stackexchange.com/a/41488).


## Maths

* Use the `mathtools` or at least the `amsmath` package.

* Use `\(x = y + z\)` rather than `$x = y + z$` for in-line mathematics. The former is proper LaTeX syntax, whereas the latter is primitive TeX syntax and yields obscure error messages.

* Similarly, for displayed, centered equations, use `\begin{equation} ... \end{equation}` (or `\[ ... \]`) instead of `$$ ... $$` which is again a TeX primitive and gives wrong vertical spacing.

* For long, multi-line or several equations, read the section 3 of the [`amsmath` manual](http://mirrors.ctan.org/macros/latex/required/amsmath/amsldoc.pdf) to see which command is more suitable for your needs: `subequations`, `align`, `split`, `gather`...

* [Need a symbol for which you can't find the command?](http://detexify.kirelabs.org/classify.html)

* Use `\DeclareMathOperator` to define up-right operators with correct spacing. Don't use `\mathrm`. For example: `\DeclareMathOperator{\spn}{span}`.

* Use `\boldsymbol` to reliably get bold math variables.

* Do not use the vertical bar character '`|`'. You can get scalable vertical bars by using `\lvert` or `\rvert`.

* `mathtools` provides the handy `\DeclarePairedDelimiter` command to declare scaling delimiter operators. You could do:

    ```latex
    \DeclarePairedDelimiter\abs{\lvert}{\rvert}    % absolute value: |x|
    \DeclarePairedDelimiter\norm{\lVert}{\rVert}   % norm: ||x||
    \DeclarePairedDelimiter\itor{\lbrack}{\lbrack} % right open interval: [x,y[
    ```

* Otherwise, use `\left`/`\right` for delimiters whose size can dynamically change depending on the content:

    ```latex
    \begin{equation}
        f(x) = \left(x + \frac{1}{2}\right)
    \end{equation}
    ```

* Using `\dfrac` forces the fraction into display mode, which can be useful if you reckon `\frac` is not large enough in an equation.

* Display equations are part of the flow of your document, and you should treat them as an integral element of your paragraph. Therefore, if they end a sentence, they should end with a period. I would argue that using commas at the end of equations is more a matter of style, and could clutter your document.

* To get correct horizontal alignment in such necessary cases, you can use the `\phantom` command to mask the character and simulate the spacing:

    ```latex
    \begin{equation}
      A_{i} =
      \begin{cases}
        1 & \text{if } i = 0\phantom{.} \\
        2 & \text{if } i \neq 0.
      \end{cases}
    \end{equation}
    ```

* Adding a small space ('`\,`') in your integrals between the integrand and the differential is arguably nicer:

    ```latex
    \begin{equation}
      I = \int_{0}^{+infty}f(x)\,dx
    \end{equation}
    ```

* You should favor the `\quad` and `\qquad` commands for horizontal spacing in an equation.

* Use the `siunitx` package to get consistent display of numbers and units across your document:

    ```latex
    \DeclareSIUnit{\nauticalmile}{NM}

    The radius of the circle is \SI{10}{\milli\metre}.
    Pressure is usually expressed in \si{\kilogram\per\metre\per\second}.
    It seems the ship has travelled \SI{100}{\nauticalmile}.
    The angle was \ang{4.5}.
    I estimate at least \num{15000} parts in this machine and probably \numrange{e6}{e7} cells in this experiment.
    ```
    and not

    ```latex
    \usepackage{textcomp}

    The radius of the circle is 10 mm.
    Pressure is usually expressed in kg.m\textsuperscript{-1}.s\textsuperscript{-1}.
    It seems the ship has travelled 100 NM.
    The angle was 4.5\textdegree.
    I estimate at least 15,000 parts in this machine and probably \(10^{6}\) to \(10^{7}\) cells in this experiment.
    ```
    If you're writing in British English, you can activate automatically the comma separator between thousands.

* If you're missing vertical space between multiple equations, you can adjust it as follows:

    ```latex
    \newcommand*{\mymathspace}{0.30\baselineskip}

    \begin{subequations}
      \begin{align}
        f_{1}(x) = g(x) - h(x)\\[\mymathspace]
        f_{2}(x) = g(x) + h(x)
      \end{align}
    \end{subequations}
    ```

* In maths mode, use `\text` to get 'normal' text matching the main font of your document.


## Floating objects and captions

Many people say LaTeX is the worst for float (figures, tables) placement. This is simply wrong. By default, LaTeX endeavors to put it closest to the surrounding text in the source file, at the top or on its own page if deemed appropriate.

* Put the floating object just before the first in-text reference.

* I would suggest to *not* add any placement specifier '`[!htbp]`' to your figures until you have the final version of the document.

* From that point onwards, you can add '`[!t]`' to put the float at the top, or even 'here if possible and at the top otherwise' ('`[!ht]`') if your object is placed in the wrong section. Think twice before leaving a figure in the middle of a page in terms of aesthetics.

* Add `\g@addto@macro\@floatboxreset\centering` to your preamble to avoid the need to add `\centering` for each figure.

* If you want to display a shorter version of your figure caption in the table of figures/tables, use the optional argument `[]` of `caption`.

* I find it more elegant to put the symbols (legend) of your figure in the caption. This is easily done with `tikz`. However, these symbols require a preceding `\protect` since `\caption` is a [fragile](http://www.texfaq.org/FAQ-protect) command.

* An example combining the two previous points is

    ```latex
    \usepackage{tikz}

    \newcommand*{\blackcircle}{\tikz{\node[draw=black,thick,scale=0.5,circle,fill=none] () {};}}

    \begin{figure}
      \includegraphics{chap_intro_diagram}
      \caption[Diagram with experimental data]{Diagram with experimental data (\protect\blackcircle)}
      \label{fig:chap-intro-diagram}
    \end{figure}
    ```

* Loading the [`caption`](https://ctan.org/pkg/caption?lang=en) package fixes a known problem of `hyperref` where the hyperlink anchor placement of a figure points to the caption rather than the beginning of the figure.

## Cross-referencing

* For a multi-part or multi-chapter project, do not number folders\files nor use labels with numbers (chap1, chap2, ...) as it will cause complications if swapping them. Use a short key instead (chap-intro, chap-method, ...).

* Add systematically a `\label` for every part, chapter, section, subsection, appendix, equation, figure or table and adopt a consistent syntax. To find my way around, I like to use the chapter location in every label:

    ```latex
    \label{chap:intro}
    \label{chap:result}
    \label{sec:chap-intro-problem-statement}
    \label{eq:chap-intro-myeq}
    \label{fig:chap-intro-sketch}
    \label{tab:chap-result-mydata}
    ```

* I also find it convenient to use a label (with dashes) matching the filenames of the figure itself and the generating script. See the example in the next section.

* Use [`cleveref`](http://mirrors.ctan.org/macros/latex/contrib/cleveref/cleveref.pdf) to guarantee typographically correct and consistent names for what you're referencing across your document. Instead of writing:

    ```latex
    We discussed this in Chap.~\ref{chap:intro}.
    Equation~\ref{eq:chap-intro-myeq} is important, as shown in Fig.~\ref{fig:chap-intro-sketch}.
    ```
    just use

    ```latex
    We discussed this in \cref{chap:intro}.
    \Cref{eq:chap-intro-myeq} is important, as shown in \cref{fig:chap-intro-sketch}.
    ```
    If you only need the label in the text, use `\labelcref`:

    ```latex
    ... described by the Maxwell equations \labelcref{eq:chap-intro-maxwell}.
    ```

* Adopt a consistent key system for your bibliography entries, with a letter prefix to disambiguate works in a same year for one author:

    ```
    @article{johnson2000, ...
    @article{smith2020a, ...
    @article{smith2020b, ...
    ```

* With `biblatex`, use the `\autocite` command for reference citations. You can adjust its behavior by passing a package option. If your citation is an object or a subject in a sentence, use `\textcite`. The capitalized commands are there for usage at the beginning of a sentence (`\Autocite`,`\Textcite`).

## Including graphics

* Use one script per figure and put a comment in your LaTeX document that gives the command used to create the figure and its path. This is an efficient way of working as you'll most likely need to adjust or recreate your graphics.

* With Matplotlib, I suggest to output graphics in the EPS format. Then, use the `epstopdf` utility included in most TeX distributions to convert them to PDF. The reason is that the PDF backend of Matplotlib is not optimized. I always observe a O(10) or a O(100) factor of size decrease between exporting in EPS and converting to PDF instead of saving directly in PDF.

* Finally, use the `pdfcrop` utility to remove the unneeded white space surrounding your figure. Using `bbox_inches = 'tight'` in the `savefig` function of Matplotlib is not enough.

* If you have many data points and plot it (with e.g., `plot`, `scatter` or `pcolormesh`) the vector format is not appropriate and will lead to bloated, slowly-loading files. Use the `rasterized = True` keyword to alleviate that issue and rasterize the large dataset, while maintaining vector graphics elsewhere. Note that you need a DPI high enough to ensure quality, and you'll have to save the figure directly in PDF.

* Ensure your Matplotlib figure layout and fonts match that of your LaTeX document. I recommand using a [style sheet](https://matplotlib.org/3.3.3/gallery/style_sheets/style_sheets_reference.html) where you can replicate your LaTeX preamble and globally define your plot settings.

* Your [style sheet](./examples/latex.mplstyle) should be put in `MPLCONFIGDIR/stylelib`. On macOS, `MPLCONFIGDIR` is usually `~/.matplotlib`. On Linux, `MPLCONFIGDIR` is usually `~/.config/matplotlib`. You can get the value of `MPLCONFIGDIR` by calling the Matplotlib function `get_configdir()` in Python. Then in your figure script, add the line `matplotlib.pyplot.style.use('thesis')` to use your style sheet. You could then create a style sheet for your thesis, for your articles and reports and quickly jump from one to another.

* Directly plot your graphics at the correct, final size to ensure consistent fonts with your LaTeX document. I found that the `set_size` function in the [J. Walton's guide](https://jwalton.info/Embed-Publication-Matplotlib-Latex/) does the job perfectly. Determine your LaTeX document text width using `\showthe\textwidth` or by checking the log file. Then you need to decide which portion of the width your figure should take. Eventually, just include the figure in LaTeX, and do *not* adjust nor distort its size with `[width=XXX\textwidth]` or `[scale=XXX]`.

* Use the same colors in matplotlib and LaTeX. I like to use HTML color codes in Matplotlib (`MyRed= '#cc0000'`) and in LaTeX thanks to the `xcolor` package: `\definecolor{MyRed}{HTML}{cc0000}`.

* Do not add the file extension in `\includegraphics`.

* For tables, use the [`bookmarks`](https://ctan.org/pkg/booktabs?lang=en) package and do *not* use vertical lines. Use `\toprule`, `\midrule` and `\bottomrule` only for horizontal lines.

* Follow that [great guide](https://speakerdeck.com/cherdarchuk/clear-off-the-table) for more attractive tables.

In your LaTeX source files, including figures should look like

```latex
% python3 ${HOME}/paper1/plots/chap_results_variable.py
\begin{figure}
  \includegraphics{chap_results_variable}
  \caption{My results}
  \label{fig:chap-results-variable}
\end{figure}
```
while the table captions are always above the data itself, therefore you should type

```latex
\begin{table}
  \caption{My table}
  \begin{tabular}{rrr}
    \toprule
    Class & Number & Age \\
    \midrule
    A     & 20     & 43 \\
    B     & 26     & 30 \\
    C     & 42     & 24 \\
    \bottomrule
  \end{tabular}
  \label{tab:chap-results-data}
\end{table}
```


## Syntax and spell-check

* The R package [`TeXCheckR`](https://github.com/HughParsonage/TeXCheckR) is an excellent syntax/spell checker for LaTeX. It can also check the structure of your `.bib` file.

* The [LTeX](https://marketplace.visualstudio.com/items?itemName=valentjn.vscode-ltex) extension of VS Code is a good spell/grammar checker, based on [LanguageTool](https://languagetool.org/).

* The `lacheck` utility shipped with most LaTeX distributions is also good at detecting questionable syntax in your `.tex` files. Note some false positives will be raised due to the use of some packages. There is also `chktex`.

* You can check your bibliography file using `biber --tool --validate-datamodel main.bib`.

* You can detect 'hidden' Unicode characters potentially messing up your files by searching the string `[^\x00-\x7f]` as a regular expression.

* Use the Python package [`pdfx`](https://pypi.org/project/pdfx/) (not to be mistaken with the [`pdfx`](https://ctan.org/pkg/pdfx?lang=en) LaTeX package) to detect broken hyperlinks in your PDF.


## Bibliography

* First, you may want to use the recent `biblatex` *package* with the `biber` *backend* which handles accentuated letters seamlessly. Avoid using the archaic `natbib` *package* which uses the `bibtex` *backend*. People are often [confused](https://tex.stackexchange.com/questions/25701/bibtex-vs-biber-and-biblatex-vs-natbib).

* Find the `biblatex` package (e.g. `biblatex-phys`) or style (e.g. the option '`[style=apa]`') closest to the bibliography style you want to use. Tweaking it to your needs is straightforward as `biblatex` is easy to customize.

* The entry types and fields (book, article, proceedings, DOI, date, publisher...) are well explained in the [`biblatex` manual, Section 2](http://mirrors.ctan.org/macros/latex/contrib/biblatex/doc/biblatex.pdf).

* **Never ever trust** `.bib` entries given by journals, editors or Zotero/Mandeley. They are often filled with errors and do not reflect the author/title information of the publication. Yes, it takes time to check individual fields, but I can't stress enough how many typos I was able to find.

* Remove the useless fields in the `.bib` entries.

* The [doi2bib](https://doi2bib.org/) website is very handy to provide the `.bib` entry using the DOI.

* Capitalize all content words for titles:

    ```
    title = {On the Electrodynamics of Moving Bodies}
    ```
    instead of the *sentence-case*

    ```
    title = {On the electrodynamics of moving bodies}
    ```
    In this way, you can easily switch from title-case to sentence-case using a `biblatex` option. Conversely, if you forget that and the bibliography needs to be title-case, you will have to add all missing capitals manually.

* Surround words that remain capitalized at all times using curly brackets:

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

* Let `biblatex` abbreviate author first names and don't do it yourself.

* For long given/last names (e.g. Brazilian), you may use

    ```
    author = {given=Carlos Santos de Azevedo, given-i=C S {de\nopunct} A, family=Silva}
    ```
    to get correct abbreviation in your reference list.

* The only acceptable separator between several authors is '`and`'.

* Although many journals use the term 'issue' as a subdivision in the volume, the corresponding entry for numerical values in `biblatex` is `number`, not `issue`.

* The correct hyphenation for pages is an en-dash: `pages = {100--200}`, not a hyphen '`-`'.

* If an entry has `volume = {1}`, double check whether there is an actual volume number. Some editors mistakenly add it.

* Use `date` instead of `year` with `biblatex` as it offers greater flexibility.

* If an entry is in a different language than the main language of the document, use the `langid` field to get correct hyphenation patterns.


## PDF production

Editors and universities often require your document to be compliant with the [PDF/A standard](https://www.pdfa.org/wp-content/uploads/2013/05/PDFA_in_a_Nutshell_211.pdf), that is suitable for long-term archiving with a device-independent display.
Among other features it must embed all fonts and include standardized metadata.

* The best way to obtain LaTeX documents close to PDF/A compliance is to use the [pdfx](https://ctan.org/pkg/pdfx?lang=en) package.

* You could then include the metadata of your document by adding before `\documentclass`,
    ```latex
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

* [Chicago Manual of Style](https://www.chicagomanualofstyle.org/)
* [An essential guide to LaTex2e usage](http://mirrors.ctan.org/info/l2tabu/english/l2tabuen.pdf)
* [Do not use these LaTeX commands](https://profajroberts.github.io/LaTeX/ltxbanned.html)
* [Short Math Guide for LaTeX](http://mirrors.ctan.org/info/short-math-guide/short-math-guide.pdf)
* [Semantics & Pragmatics: Guidelines and typesetting resources](https://info.semprag.org/style)
