# Markdown Tutorial
<!-- clear for public use -->

**What is Markdown?** Markdown is a simplified way of displaying text on web pages and in certain applications; instead of having to use HTML for text formatting, Markdown can be used instead and it is far simpler to use. 
<br><br>
In a nutshell, you will write text in a .md file that utilizes Markdown formatting; thats it, simply write text in a .md file with some special formats as described here. This tutorial will teach you the basics of Markdown so you can write these .md files.


# Heading Example

```
To make a heading, simply start a line with X number of hashes and then the text of your 
heading (and then make a new line by pressing enter). The heading will look like this 
in the code: 
## This is a heading

Note that I said X hashes - its variable, from 1 to 4. Using 1 hash makes the largest
heading possible, with 2 through 4 being incrementally smaller in print.

The cool thing about headings is: it will automatically make a link to the heading in the 
sidebar, which is clickable and good for navigation. Note that the number of hashes matters 
here too.... one hash is the uppermost level in the side menu, two is just under that...
all the way down to 4 hashes, which is the furthest child-heading possible.
```
# First Level Heading
## Second Level Heading
### Third Level Heading
#### Fourth Level Heading


# Text Formatting Examples

## Italics

To italicize something, _use underscores like this, which starts with an underscore and then ends with one around what you want in italics_.

Code:
```
_This is in italics_.
```

## Bold
To bold something, **use two asterisks like this, which starts with two asterisks and then ends with them around what you want in bold**.

Code:
```
**This is bold**.
```

## Enter / New Line

You cant simply press enter in markdown if you want a new line; instead, you must use < br> (without the space) to make a new line.

So to go to the new line, <br> I typed the secret code < br>.

Code:
```
So to go to the new line, <br> I typed the secret code <br>.
```

This is known as a hard break; there is also a soft break as well. To enable a soft break at the end of a line,  
simply place TWO spaces at the end of the line (the line above has two spaces at the end).

Code:
```
There is also a soft break as well. To enable a soft break at the end of a line,  
simply place TWO spaces at the end of the line (the line above has two spaces at the end).
```

## Font Color

Markdown does _not_ natively support font color; instead, what can be done is you simply use raw HTML (and, yes, you _CAN_ use raw HTML in most instances of Docsify, although there is no guarantee you can in all implementations due to security).

If you want to <font color="red"> change the color of font, this is how you do that</font>; just exit HTML to end the color formatting.

Code:
```
If you want to <font color="red"> change the color of font, this is how you do that</font>; just exit HTML to end the color formatting.
```

## Font Size

Controlling font size is similar to controlling the font color; you must use raw HTML. You must use the font size tags to change the size of the text.   <font size="18">This is one example,</font> <font size="3">this is another, </font> <font size="6">and this is another.</font>
<font size="6">This is some text!</font> 

```
<font size="18">This is one example,</font> <font size="3">this is another, </font> ...
```

## Blockquotes

There is a simple way of displaying text that stands out in a blockquote; to do this , simply start the line iwth a > and then type the line

> This is an example of a block quote

Code:
```
> This is an example of a block quote
```

> This is an example of multiple lines
> 
> Note that EVERY line MUST start with a > until you no longer want to continue the blockquote.

Code:
```
> This is an example of multiple lines
> 
> Note that EVERY line MUST start with a > until you no longer want to continue the blockquote.
```

You can also create a warning blockquote - to do this, simply put a ! in front of the > like so:

!> This is a warning blockquote

Code:
```
!> This is a warning blockquote
```

## Code Block Example
```Starfish
The three back-ticks ``` (above the Tab key) indicate a code block. This is helpful to make
a separate unformatted area for things like code.  All of this is in a code block.

Note that you end it with three ``` as well, so it starts and ends with ```.

Note that there is no formatting in code blocks - so you cant use headers, bold, etc.

To display a certain word in the upper right, write the word directly after the first set 
of ``` (as I did with Starfish above). It doesnt work well with spaces.
```

# Escape Character

Sometimes you need to type something but it also doubles as markdown language; you need something called an escape character.  Say you needed to type \*\*Hello\*\* but trying to type that always yields **Hello** as markdown interprets \*\* as bold; use the \ character before each character you want to print literally:

Code:
```
\*\*Hello\*\*
```

# Links

This is an example of how to [link to GitHub](https:\\www.github.com). Note the display text is between brackets [] and the actual link follows in parenthesis ().

Code:
```
[link to GitHub](https:\\www.github.com)
```

# Lists

To make a list, simply make the list with asterisks at the beginning of the line. Note the asterisk MUST be followed by a space.

Example: My Shopping List
* Coffee
* Hot Sauce
* Batteries
* Rope

Code:
```
Example: My Shopping List
* Coffee
* Hot Sauce
* Batteries
* Rope
```

If you wanted to add levels, just add a space for each level in front of the desired asterisk, like so:
Example: Subjects

* Math
 * Trigonometry
 * Discrete
 * Algebra
  * Algebra 1
  * Algebra 2
* English
 * Middle
 * Literature
* History
 * Roman
   * BC
   * AD
 * Greek
* Science
 * Physics
 * Chemistry

Code:
```
Example: Subjects

* Math
 * Trigonometry
 * Discrete
 * Algebra
   * Algebra 1
  * Algebra 2
* English
 * Middle
 * Literature
* History
 * Roman
   * BC
   * AD
 * Greek
* Science
 * Physics
 * Chemistry
```

Note that the nested items are not sequential; for the first nesting you use one space, but for the second you use 3 spaces; for the third you will use 5 spaces.  I am not entirely sure why this is, but you will have to play with the spacing for 4+ nestings.

# Numbered Lists

Its pretty easy to use numbered lists - simply list the number, a dot, a space, and then the text. There is one caveat: if you place any markdown text in between the numbers it may re-set the numbers. Observe, as this should be two numbered items, a code block, and then a continuation with item #3:

1. item 1
2. item 2

```
code
```

3. item 3

Unfortunately, the numbers re-started (the last item should be listed as #3, but it is re-set to #1).

To get around this, use a tab in front of the markdown (such as the code block) like so:

1. item 1
2. item 2

	```
	code
	```

3. item 3

Another way is to make sure there are NO empty lines in between any of your numbered listings. Pick the format you like best!

# Emojis

Markdown supports emojis; simply wrap the emoji word using the ':' symbol. Example: `:100:` is :100:.

Notice how that 100 is very large? Unfortunately, either the version of markdown Docsify uses (called 'marked') or our setup causes this; however there is a trick using HTML to shrink its size. We can wrap the emoji in a `<span>` HTML object; using a specific setup, we can shrink the emoji size to something more manageable, like this <span style='width: 20px; display:inline-block'>:100:</span>.

Here is the code:
```
... more manageable, like this <span style='width: 20px; display:inline-block'>:100:</span>.
```

We simply added a style with a width property as well as a display: inline-block property to the span. Both are necessary. You can alter the width size, but do not alter anything else about the `<span>` tag.

For a full list of emojis you can use, [visit the emoji cheat sheet site](https://www.webpagefx.com/tools/emoji-cheat-sheet/).


# Pictures

Pictures are very similar to links; the only difference is an exclamation point is at the very beginning before the bracket.

This is an example of an embedded picture, which is of our beloved professor Bailey:<br>
![Bailey](https://bwagenseller.github.io/#/learn_to_code/docsify/ProfessorBailey.jpg)

Code:
```
![Bailey](https://bwagenseller.github.io/#/learn_to_code/docsify/ProfessorBailey.jpg)

Note you must have some text in the [brackets] but it wont display.
```

The example above is an example of using the full FQDN path to the image - this is discouraged for images that reside in the local share and this should only be used IF you are linking to external image (which should be uncommon).

If a picture you want to link to is **external** please use the URL linking method above.  If the image is local (same folder or folder within the share drive) you should use the relative location examples below.


This is an example of an embedded picture that is located in the **SAME** directory as the .md file 
![begone](begone.png)

Code:
```
![begone](begone.png)

Note you must have some text in the [brackets] but it wont display.
```

This is an example of an embedded picture that is located in a **nested** directory called img/:  
![An image of Felix the Cat](img/felix.png)

Code:
```
![An image of Felix the Cat](img/felix.png)

Note you must have some text in the [brackets] but it wont display.
```

# Embedding Files

You can also embed files in markdown by including a ':include' immediately after the target file name:

![Some Music That Comes With Windows 10](SleepAway.mp3 ':include')


Code:
```
![Some Music That Comes With Windows 10](SleepAway.mp3 ':include')
```

The following is supported:
* iframe: .html, .htm
* markdown: .markdown, .md
* audio: .mp3
* video: .mp4, .ogg

!> If an embedded file cannot be read, it will simply render as a code block.

## Embedding YouTube Videos

Embedding YouTube videos are supported, but its not instantly recognized; you need to alter the link a bit and also specify `:type=iframe` in the markdown.

For example, say we wanted to embed https://www.youtube.com/watch?v=aknucxb0xSo into our page. Take the value of `v=` (in our case its 'aknucxb0xSo') and put it at the end of `https://www.youtube.com/embed/` like so: `https://www.youtube.com/embed/aknucxb0xSo`.

Then, put the qualifier `':include :type=iframe'` in the markdown code (you also may want to put the width and height as well).  Here is an example:

```
![AI Christmas Tale](https://www.youtube.com/embed/aknucxb0xSo ':include :type=iframe width=315px height=400px')
```

And here is what it will look like:

![AI Christmas Tale](https://www.youtube.com/embed/aknucxb0xSo ':include :type=iframe width=315px height=400px')


You can also specify the width and height as a percentage; for example,
```
![Synthwave](https://www.youtube.com/embed/wOMwO5T3yT4 ':include :type=iframe width=100% height=400px')
```

Which is:

![Synthwave](https://www.youtube.com/embed/wOMwO5T3yT4 ':include :type=iframe width=100% height=400px')

!> These seem to be very touch-and-go: if they are immediately above or below other markdown they will not display. If you are having problems, make sure there is a carriage return above and below the embedded video first, and then move and test from there.

# Tables

Tables are not natively supported in Markdown, but a few flavors do support it - and ours does.  
Basically, its completely text-based, with a pipe separating the columns, dashes (NO LESS THAN 3 BUT IT CAN BE MORE THAN 3) separating the column headers from the data, and colons around the dashes to align the columns.

* :--- aligns the text to the left 
* --- aligns the text to the left 
* :---: centers the text 
* ---: aligns the text to the right

Note that markdown can _also_ be used in the table.

Example

| Column 1      | Column 2      | Column 3    |
| ------------- |:-------------:| -----------:|
| This column   | This column   | This column |
| is left-      | is            | is right-   |
| aligned       | centered      | aligned     |

Code:
```
| Column 1      | Column 2      | Column 3    |
| ------------- |:-------------:| -----------:|
| This column   | This column   | This column |
| is left-      | is            | is right-   |
| aligned       | centered      | aligned     |
```
Note the outside pipes are optional (but the inside pipes are _not_, as this is how the columns are delineated).

# Horizontal Rules

If you want to place a horizontal rule in between any sections or elements you can.  Horizonal rule example

Three or more...

---

Hyphens

***

Asterisks

___

Underscores


Code:
```
Three or more...

---

Hyphens

***

Asterisks

___

Underscores
```

# HTML Usage

Our version of Markdown can utilize raw HTML, so if all else fails you can do that; that said, note that all forms of Markdown may not support this due to security issues.

# Be Creative!

You can mix and match Markdown code; for example, you can bold and put links in a list:
* This is a first bullet point **that has bold text**
* This is a second point [that contains a link to a site](https://www.packtpub.com/packt/offers/free-learning) and then <font color="green"> has some green text thanks to HTML</font>.

# Further Links

* [Markdown Tutorial](https://www.markdowntutorial.com/)
* [John MacFarlane](http://johnmacfarlane.net/babelmark2/faq.html)
* [Markdown Guide](https://www.markdownguide.org)
