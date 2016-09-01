---
title: Print output
---

# Print output
{:.no_toc}

* toc
{:toc}

## Introduction

We use [PrinceXML](http://princexml.com/) to turn Jekyll's HTML into beautiful, print-ready book files. We haven't found anything as good as Prince, so we reckon it's worth its price tag. And you can use the trial version to get your print output perfect before committing to the price. So our CSS files for print are designed specifically for Prince.

If you want to learn about using CSS to control print output using Prince, [this is a great tutorial](http://www.smashingmagazine.com/2015/01/designing-for-print-with-css/).

## Quick output

Run the template's `-pdf` script for your operating system:

* On Windows, run `win-print-pdf.bat` by double-clicking it from your file explorer. For it to work, you must already have Jekyll and Prince installed and working.
* On Linux, run `linux-print-pdf.sh`. You may have to run it from a terminal, and first enter `chmod +x linux-print-pdf.sh` to give it permissions, then `bash linux-print pdf.sh`.
* On Mac, we're still testing `mac-print-pdf.command`. YMMV.

The same applies to `-screen-pdf` scripts.

## Detail

### Creating PDF files with the Prince GUI

1.	Find your book's HTML files in your `_html` folder. (Remember to run Jekyll locally to generate the latest version; if you're getting your HTML from a GitHub repo's `_html` folder, trust that the last contributor synced up-to-date, reliable HTML generated by their local Jekyll instance.)
2.	Drag the HTML files into Prince. Make sure they're in the right order. Only include the files you need in print. For instance, you'll usually leave out `index.html`. You'll usually only include `cover.html` when creating a PDF ebook.
3.	Tick ‘Merge all…’ and **specify an output file** location and name. Do not let Prince output to your `_html` folder. (If you skip this, Prince will output to your `_html` folder, which will cause permissions issues when you want to modify or delete the file, because Jekyll owns the `_html` folder and can overwrite it, and because Git will try to commit and sync the output PDF, which you probably don't want.)
4.	Prince will find your book's print CSS, which you specified in `_config.yml`. If you want a PDF with a completely different CSS file, either:
    * change it in `_config.yml` and restart Jekyll to update your files with the new config, or
    * leave it blank (`stylesheet-print: ""`) and manually add the output CSS to Prince.
5.	Click Convert.

> Tech detail: Our default HTML `head` includes links to both the screen stylesheet and the print stylesheet specified in `_config.yml`. Prince knows which one to use because they're specified with `media="screen"` and `media="print"`.

### Creating PDFs with Prince from the command line

For general instructions, see [the official PrinceXML user guide](http://www.princexml.com/doc/command-line/). We like to use this workflow:

1.  In each book's folder (alongside the markdown) we keep a file called `file-list`. The `file-list` file is just a list of the HTML files that must be included in the print PDF. E.g.:

    ~~~
    0-1-titlepage.html
    0-2-copyright.html
    0-3-contents.html
    1.html
    2.html
    3.html
    ~~~

    If you include the list of files for a given output format in `meta.yml`, this file is automatically generated by the default `file-list` file in our template.

2.  At your command prompt, navigate to the book's folder in `_html`. (With the right settings, you can launch your command line app in the right folder from a file explorer in [Windows](http://lifehacker.com/5989434/quickly-open-a-command-prompt-from-the-windows-explorer-address-bar), [Mac](http://lifehacker.com/launch-an-os-x-terminal-window-from-a-specific-folder-1466745514) and [Linux](http://www.howtogeek.com/192865/how-to-open-terminal-to-a-specific-folder-in-ubuntus-file-browser/).)
3.  At the prompt, type `prince -l file-list -o /temp/filename.pdf`.

> In that last step, `/temp` is an existing folder at the root of your drive (e.g. `C:/temp` if you're working on your C drive in Windows) and `filename` is a sensible file name. If you just used `filename.pdf` without the folder path, the PDF would be saved to the same folder as the HTML in `_html`. We don't recommend this because Jekyll will overwrite that whole folder if you build new HTML, and you'll lose your PDF.
{:.box}

### Refining print layout

You will doubtless want to refine your print layout by editing your markdown and adding custom CSS to the `print.scss` file in your theme.

For instance, to save widows and orphans, when using the Classic theme, you can tighten letter-spacing by adding `{:.tighten-5}` and `{:.loosen-5}` tags to the lines after paragraphs. The number in the tag refers to how many thousands of an em you're affecting letter-spacing by. For instance, `{:.tighten-10}` will tighten letter-spacing by 10/1000 em. In good typography, you should avoid tightening or loosening by more than 10/1000 wherever possible.

And to add manual hyphens to improve spacing, use discretionary hyphens (aka soft hyphens) by adding the HTML entity `&shy;` where you want the soft hyphen.

#### Faster print refinement

When refining print layout (e.g. fixing widows and orphans) with the EBWorkflow, this is happening:

1. You edit the markdown
2. Jekyll generates new HTML
3. You convert to PDF in Prince
4. You open the new PDF to see your changes.

There are two delays in this process:

1. Jekyll can take several seconds to regenerate the entire book (even with `--incremental` enabled)
2. You have to click 'Convert' and Prince must regenerate the file(s)
3. If your PDF viewer locks the file you're viewing, you have to close it before generating a new PDF in Prince.

To speed this up:

1.  Temporarily stop Jekyll generating any files you aren't working on. In `_config.yml`, add a line that excludes those files. In this example, I've listed the CSS folder and all my book's chapters, and them commented out with `#` the one I'm working on, so that Jekyll does regenerate that:

    ~~~
    # Jekyll will ignore these files and folders. 
    # Useful for temporarily speeding up processing for particular files.
    exclude: [
    css,
    book-one/0-0-cover.md, 
    book-one/0-1-half-title.md, 
    book-one/0-2-titlepage.md, 
    book-one/0-3-copyright.md,
    book-one/0-4-contents.md,
    #book-one/1.md,
    book-one/2.md,
    book-one/3.md,
    ]
    ~~~

2.  Using Prince there isn't a universal way to speed up conversion, except to only convert the files you really need to be working on. For instance, in your `file-list`, comment out files you're not working on; or if you're generating your `file-list` from a list in `meta.yml`, comment out the relevant lines there. (Also, if you've used Javascript in your HTML, that can slow Prince down. [We wrap scripts, like Google Analytics tags, in a test that checks for Prince](https://github.com/electricbookworks/electric-book-workflow/blob/gh-pages/template/_includes/header.html).)
3.  Use a PDF Viewer that [doesn't lock the file](http://superuser.com/questions/599442/pdf-viewer-that-handles-live-updating-of-pdf-doesnt-lock-the-file). 
	*	On Windows, [Sumatra](http://www.sumatrapdfreader.org/free-pdf-reader.html) is perfect for this, unlike Acrobat and Windows Preview, which locks the file. If you edit/regenerate a PDF it has open, Sumatra will allow that to happen and will automatically reload the new file, at the same page you had open.
	*	On Mac OSX, Preview does the same, though we've found it doesn't open the regenerated file to the page you were on, sending you back to the start of the book, which is a pain. A much better alternative on OSX is [Skim](http://skim-app.sourceforge.net/), an open-source PDF reader. In its Preferences > Sync, set Skim to watch for file changes and update automatically. You may also want to set PDFs to open to the last viewed page in Preferences > General.

### Managing hyphenation in Prince

Our default stylesheets ask Prince to hyphenate paragraphs and lists (`p, ul, ol, dl`), with a few exceptions (such as text on the title and contents pages). Prince includes a range of hyphenation dictionaries by default, which do a good job. However, you might need to add dictionaries or lists of specific words that Prince doesn't support. You can find `.dic` files online for [various languages](http://www.ctan.org/tex-archive/language/hyph-utf8/tex/generic/hyph-utf8/patterns/txt) and specialities, or you can compile your own.

We provide a blank `.dic` file for you to add your own list of hyphenation rules to. It is at `css/dictionaries/hyph.dic`. Our `print.css` asks Prince to look here when hyphenating.

A `.dic` file is a plain-text file with one word or word-fragment on each line. Each one is called a pattern.

*	If the pattern starts with a `.`, it will apply to, or match, any word that starts with that pattern. E.g. `.foo` will match the words 'food' and 'foobar' but not 'fastfood'.
*	If the pattern ends with a `.`, it will apply to any word that ends with that pattern. E.g. `port.` will match 'port' and 'sport' but not 'portico'.
*	Thus, if the pattern starts *and* ends with a `.`, it will apply to only that pattern exactly. E.g. `.port.` will only ever match 'port'.

To show where a word or word-fragment can hyphenate, you add digits (1 to 9) to the pattern. The digits have special meanings:

*	Insert odd digits (1, 3, 5, 7, 9) where the word may hyphenate.
*	Insert even digits (2, 4, 6, 8) where the word should not hyphenate.
*	The higher the number, the more important the rule. That is, a `1` says 'hyphenate here if you must', but a `9` says 'this is the best place to hyphenate'. A 2 says 'don't hyphenate here if you can help it', but an `8` says 'Do not, not, not hyphenate here.'

For user discussion, see [the Prince forums here](http://www.princexml.com/forum/topic/542/prince-hyphenate-patterns-none-url-patterns-url): If you need to hack Prince's built-in hyphenation dictionaries more deeply, see [this forum post](http://www.princexml.com/forum/topic/1474/prince-and-hyphenation).