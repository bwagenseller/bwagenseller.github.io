# Docsify: Getting Started
<!-- clear for public use -->

# What's The Point / What Is This?

This page is written using the Docisfy framework, which you can learn more about [at the Docisfy website](https://docsify.now.sh/). The base file is a .md (read: Markdown) file that is simply a text file with snazzy formatting.  
If you wish to document something, you can perform a write-up of the subject in a .md file and store it to a spot on the shared drive and we will permanently have the documentation on file.

# Setting-up Docsify

## Local Setup

For local docsify setup, [read my article here](operating_systems/ubuntu/server_build?id=docsify).

## Github Pages

Its possible to set up Docsify on [GitHub Pages](https://pages.github.com/) for free. They actually lay out how to utilize GitHub Pages [on the initial screen](https://pages.github.com/), but to recap (with a Docsify flavor):

1\. Go to [GitHub](https://github.com/), make an account if necessary.

2\. Make a [new repository](https://github.com/new) and call it `username.github.io`, where `username` is your username (or organization name) on GitHub.

3\. Assuming you are [using Git](operating_systems/ubuntu/linux_notes?id=using-git) on Ubuntu, [initialize git](operating_systems/ubuntu/linux_notes?id=initializing-git) if you have not done so already.

4\. Make a directory that will be used for editing your page, and then change to that directory; from here, clone your new repository with the command:
```
git clone https://github.com/username/username.github.io
```
 * `username` is your actual username for GitHub that you made in step 1.

5\. Step 4 will create a directory called `username.github.io` in the current directory; change the directory to that directory with `cd username.github.io`.

6\. From here you can write an `index.html` file in this folder - this index.html is critical, as your page will use this file to 'start' your webpage.

7\. According to [the Docsify quickstart guide](https://docsify.now.sh/quickstart), you have to modify your `index.html` in a certain fashion to use Docsify. To do so, copy this basic template to your `index.html` file in the folder `username.github.io` (if it does not exist, create it):

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="description" content="Description">
  <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <link rel="stylesheet" href="//unpkg.com/docsify/lib/themes/vue.css">
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      name: 'My Docsify Page',
      loadSidebar: true,
      subMaxLevel: 2,
      maxLevel: 4
    }
  </script>
  <script src="//unpkg.com/docsify/lib/docsify.min.js"></script>
</body>
</html>
```

8\. Now make a file called `README.md` inside the folder `username.github.io`; put a few lines in there just to test this out (it can be a few sentences).

9\. Now you will have to push your changes to github; to do this, run these commands from inside the folder `username.github.io`:

```
git add --all
git commit -m "Some commit comment, its up to you"
git push -u origin master
```
 * Its a good practice to change the commit comment each time.
 * You will be asked for your GitHub username and password.
 
10\. You can now be browse to https://username.github.io (replacing `username` with your own, of course) and your own Docsify page is up on GitHub Pages!
 * To actually navigate to your _Docsify_ implementation, you must use https://username.github.io/#/ instead

11\. Read more [on the Docisfy website](https://docsify.now.sh/).


# What is Markdown?

Markdown is like a computer language (loosely defined), and its a simplified way of displaying text on web pages and in certain applications; instead of having to use HTML for text formatting, Markdown can be used instead and it is far simpler to use. Markdown is widely used in computer science / academia / business.

# Where Do We Put Files?

[See these notes here](operating_systems/ubuntu/server_build?id=docsify) for more information on where files should go.


# Files Needed

For general purposes, [Docisfy](https://docsify.now.sh/) requires three files for each folder under the main directory:
* README.md - This file is written in the Markdown language and acts as a 'hub' for the folder.  Typically, the contents of README.md is a simple listing of the markdown files available in the current folder, but you could put other things in here as well if you wanted. EVERY folder in the 'library' folder in docsify MUST have a README.md. 
* _sidebar.md - This file populates the sidebar navigation menu on the left of the page.
* _navbar.md - This file populates the navigation menu.

Docsify allows different 'pages' under its main folder. Your Docsify will be located in one (or many) of these folders. For each folder in this main folder, at least three files must be present:
* README.md - This file is written in the Markdown language and acts as a 'hub' for the folder.  Typically, the contents of README.md is a simple listing of the markdown files available in the current folder, but you could put other things in here as well if you wanted. EVERY folder MUST have a README.md. 
* _sidebar.md - This file populates the sidebar navigation menu on the left of the page.
* ???.md - This file will be named something logical that describes your documentation (so if this was documentation about birds, it may be called birds.md, for example). This file will be the main document that houses all of the critical information contained in your documentation, describing whatever process you are covering in detail.

If you want more information on the needed files for Docsify, [we have put together some information here](/operating_systems/ubuntu/server_build?id=basics-of-docsify-files-and-locations).


# How Do We Make the .md Files?

You can make these files in any text editor, but just make sure they end in .md. The best way to do this is look in the 'Type' column in Windows Explorer.

# A Note on Docsify Links

While you can read more about Markdown [here](/learn_to_code/docsify/markdowntutorial), you will need to know the basics of constructing links in the README.md and _sidebar.md files. The format is:

```
[Some Text That will appear as a link in these brackets](/directory/to/your/document/file/YourDocumentWithoutTheExtension)
```

Note the above links to a .md file, but you do NOT put the .md on the end - leave that off, its assumed.  
Now, this is for your document file - but if you wanted to link to a directory you would list it with a / at the end like so:

```
[Some Text That will appear as a link in these brackets](/path/to/the/directory/)
```

This is where README.md becomes important, as whatever in the README.md is used as the default display when you navigate to a directory this way.


For an example, say we have a file called gettingstarted.md (which is this file) and its located in the folder /mnt/docsify_library/learn_to_code/docsify (as specified [here](operating_systems/ubuntu/server_build?id=docsify-setup); when referencing the file you lop off the base, so this will just be /learn_to_code/docsify/.  If we wanted to link to this file in Markdown we could say

```
[Click HERE](/learn_to_code/docsify/gettingstarted)
```

Live, this looks like this: [Click HERE](/learn_to_code/docsify/gettingstarted)

**Please note that capitalization is critical in both directory and file names** - so be precise!


# More on ???.md (The Main Document File)

The format of the main documentation file is open-ended; at the least you probably should use headers to separate ideas into navigable sections. You will need to use Markdown, and [the tutorial on that is here](/learn_to_code/docsify/markdowntutorial).  
If you have absolutely no idea where to begin, use this small example as a starting point; it has a main header, two sub-headers and some text:

```
# My Awesome Documentation

# What Is The Point?

The point of this document should be obvious to anyone who has even a passing interest in  
the subject material.

# Want To Read More?

Google Is your friend.
```

# More on README.md

This file is used when a user navigates to a folder on the web page; ideally, it should house links to your Main Document files, but you can do whatever you wish (most will just list links, though.  
<br>
You will most likely have to make several Markdown links here; a quick tutorial is [in the 'A Note on Docsify Links' above](/learn_to_code/docsify/gettingstarted?id=a-note-on-docsify-links).

A basic example of a README.md file is:
```
# Main Title
This is just a small description of this directory.

# First Header
- [Words to Describe Link](/directory/markdownFileNameWithoutExtension) - How to fluff a pillow.

# Second Header
- [Words to Describe Second Link](/directory/markdownFileNameWithoutExtension) - How to live with skunks.
```

# More on _sidebar.md

This file populates the side navigation bar to the left. Its contents is fairly rigid, so there is not much room for creativity here (as opposed to the other files).  

It simply should list the links you wish to display. This can be internal markdown files, and how to do this can be [found in the 'A Note on Docsify Links' above](/learn_to_code/docsify/gettingstarted?id=a-note-on-docsify-links). Note that each line should start with a dash, a space, and then the link - thats it.

Example (for this very page):
```
- [**Getting Started**](/learn_to_code/docsify/gettingstarted)
- [**Markdown Help**](/learn_to_code/docsify/markdowntutorial)
```

The double asterisks are not required, but they do bold the text - something you may want to do.  This is Markdown format, so you need the two beginning and end asterisks to bold the text.  

If you use headers in your main document (that you list in _sidebar.md), the headers will simply show up and work when you click them. Wow!  
It should be noted that you only need one _sidebar.md for each folder - so if you put your document in an existing folder you will not have to write this from scratch (although you will probably have to update the existing _sidebar.md file to link to your new document).
