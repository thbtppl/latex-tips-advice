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
* [Correct typesetting](#correct-typesetting)
* [Maths](#maths)
* [PDF production](#pdf-production)
* [Bibliography](#bibliography)
* [Typesetting](#typesetting)

## My setup

* [TeXLive](https://www.tug.org/texlive/)
* [Visual Studio Code](https://code.visualstudio.com/) with the amazing [LaTeX workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) extension
* [Matplotlib](https://matplotlib.org/) for 2D vector graphics 
* [Ipe](http://ipe.otfried.org/) for vector drawing
* LaTeX engine: `pdflatex`

I recommend using `latexmk` (default in LaTeX workshop) to compile the document.
The [`latexmkrc`](examples/latexmkrc) config file placed at the root of your project allows to declare your main source file, the desired PDF output and the path to your self-defined class/package/preamble files.
You can also declare the folders containing your external graphics, which is cleaner and more [memory efficient](http://www.texfaq.org/FAQ-graphicspath) than using `\graphicspath` within your `.tex` files.


## General advice

* Use as few packages as possible and understand the preamble of your LaTeX document.

* Focus on the content rather than the layout of the document, BUT do not ignore errors and warnings. The more effort you put to understand them, the more helpful it is to comprehend LaTeX's logic and the more efficient the writing process will be.

* As a corollary, it is better to work continuously with a warning-free camera-ready template rather than going in panic mode 10 hours before your deadline trying to solve layout issues.

* Do not follow any random advice found online. Many suggestions are plain wrong, and you're likely to use obsolete snippets of code.

* Instead, have at least a quick look in the documentation of packages you're using. This will help you to use their commands as intended, and it will most likely save you hours of debugging.

* Answers to your questions/issues are most likely on the [TeX FAQ](http://www.texfaq.org/) and on [TeX SE](https://tex.stackexchange.com/).

* In your document, separate the format/layout from the actual content:
    * Create a modular document with different files
    * Define commands with semantically appropriate names <!--, such as `\newcommand*{\contentstitle} {Table of Contents}`-->
    * Do not put hard-coded settings within your content

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
    * `babel` (or `polyglossia` if you're using `XeLaTeX` or `LuaLaTeX`): use proper typographical rules adapted to document language
    * `microtype`: automatic font spacing adjustments for improved aesthetics
    * `mathtools`: math support with improvements to `amsmath`
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

* Mind the order of packages in the preamble:

    * `inputenc` should be loaded before `fontenc`
    * If using the option [babel=true], `microtype` should be loaded after babel
    * `biblatex` should be loaded after `babel`
    * Some font packages like `newpxtext` should be loaded after `babel`
    * `parskip` should be loaded after `tocloft`
    * `cleveref` should be loaded after `hyperref`
    * `bookmark` should be loaded after `cleveref`
    * `glossaries[-extra]` should be loaded last

    The manual of `pdfx` recommends to load it as early as possible, but since it automatically loads `hyperref` I would recommend loading it just before `hyperref`.


## Correct typesetting

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

* The only recommended way to start a new paragraph is using a line break:

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

* To force whitespace after a user-defined commmand that produces text, add empty curly braces: `\com{}` instead of `\com`

* Pay attention to correct spacing when using a period within a sentence. The `.\` and `\@` commands tell LaTeX your sentence does not end.
    * `This is a sentence w.\ a forced inter-word space.`
    * `This is done by XYZ\@. This is a new sentence.`
    
    The latter case should not happen often if you use the `glossaries[-extra]` package for your abbreviations.

* Add to your preamble

    ```
    \widowpenalty=10000
    \clubpenalty=10000
    ```
    to force LaTeX to avoid widows and orphans (dangling lines at the beginning or end of a paragraph separated from the rest).

## Maths



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


## Syntax and spell-check

* The R package [`TeXCheckR`](https://github.com/HughParsonage/TeXCheckR) is an excellent syntax/spell checker for LaTeX. It can also check the structure of your `.bib` file.

* The [LTeX](https://marketplace.visualstudio.com/items?itemName=valentjn.vscode-ltex) extension of VS Code is a good spell/grammar checker, based on [LanguageTool](https://languagetool.org/).

* The `lacheck` utility shipped with most LaTeX distributions is also good at detecting questionable syntax in your `.tex` files. Note some false positives will be raised due to the use of some packages. `chktex` performs poorly compared to `lacheck`.

* You can check your bibliography file using `biber --tool --validate-datamodel main.bib`.

* You can detect 'hidden' Unicode characters in your files by searching the string `[^\x00-\x7f]` as a regular expression.

* Use the Python package `pdfx` https://pypi.org/project/pdfx/ (not to be mistaken with the [`pdfx`](https://ctan.org/pkg/pdfx?lang=en) LaTeX package) to detect broken hyperlinks in your PDF.


## Floating objects

* Many people say LaTeX is the worst for float placement. This is simply wrong.
I would suggest using `\begin{figure}` without any placement specifier '`[!t]`' until you have the final version of the document.
From that point onwards, you can add '`[!t]`' to force the float at the top, or even '`[!h]`' is usually good at
Stop saying LaTeX messes up graphs!

* Add `\g@addto@macro\@floatboxreset\centering` to your preamble to avoid the need to add `\centering` for each figure.

## Including graphics 

I suggest to output your graphics in the EPS format, and to convert them to PDF using the `epstopdf` utility included in TeX distributions.
The .pdf backend of matplotlib is not optimized, and I always observe a factor of size decrease between 10 and 100 between exporting in .eps and converting to pdf instead of saving directly in pdf.
Also, @wookai reports the quality of pdf is lesser than pdf.
The pgf format is useful, but is also known to yields bloated graphics files as opposed to eps @wookai.

The utility `pdfcrop` is very useful to remove the unneeded white space surrounding your figure.


## Bibliography

* First, you may want to use the recent `biblatex` *package* with the `biber` *backend* which handles accentuated letters seamlessly. Avoid using the old `natbib` *package* which uses the `bibtex` *backend*. People are often [confused](https://tex.stackexchange.com/questions/25701/bibtex-vs-biber-and-biblatex-vs-natbib).

* Find the biblatex package (e.g. `biblatex-phys`) closest to the bibliography style you want to use. Tweaking it to your needs is straightforward as `biblatex` is easy to customize.

* Never, ever trust `.bib` entries given by journals, editors or Zotero/Mandeley. They are often filled with errors. Yes, it takes time to check individual fields, but I can't stress enough how many typos I was able to find.

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

* For author names, use the `author = {Last Name, First Name}` format. Using `author = {J. Doe}` is fine for simple names, but the former helps `biblatex` to clearly identify name particles and abbreviate correctly, especially for authors with prefixes like 'de' in French or 'van' in Dutch or whose last name contains two words.

* For long given/last names (e.g. Brazilian), you may use `author = {given=Carlos Santos de Azevedo, given-i=C S {de\nopunct} A, family=Silva}` to get correct abbreviation in your reference list.

* Although many journals use the term "issue" as a subdivision in the volume, the corresponding entry for numerical values in `biblatex` is `number`, not `issue`.

* The correct hyphenation for pages is an en-dash: `pages = {100--200}`, not a hyphen `-`.

* If an entry has `volume = {1}`, double check whether there is an actual volume number. Some editors mistakenly add it.

* Use `date` instead of `year` with `biblatex` as it offers greater flexibility.

* If an entry is in a different language than the main language of the document, use the `langid` field to get correct hyphenation patterns.
