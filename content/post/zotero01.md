+++
title = "Citation Tools - Zotero"

date = 2018-04-25
lastmod = 2018-04-25
draft = false


tags = ["dates", "R"]
summary = "Setting up Zotero to work with Dropbox"


[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-default.png"
caption = "Default"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-ocean.png"
caption = "Ocean"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-dark.png"
caption = "Dark"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-forest.png"
caption = "Default"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-coffee-playfair.png"
caption = "Coffee theme with Playfair font"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-1950s.png"
caption = "1950s"
+++



[Zotero](https://www.zotero.org/) looks like it might be a good replacement for [Mendeley](https://www.mendeley.com/), which quite frankly is a complete waste of everyones time. Zotero is cross platform, seems reasonably well supported as suggested from the helpful information provided in the discussion forums and has extension options via plugins. This latter feature is of interest to me as I my setup involves retaining my collection meta data in one place and my pdf files on a dropbox account. Zotero explicity advises against storing your metadata on a cloud account (as does endnote) but I have found that setting this up required a fair bit of trial and error.

I am assuming you are starting from a clean install with the following images characterising the full pre-requistes steps.

|                                  |                                    |
:---------------------------------:|:-----------------------------------:
![](/media/zotero01.JPG)           | ![](/media/zotero02.JPG) 
![](/media/zotero03.JPG)           | ![](/media/zotero04.JPG) 
![](/media/zotero05.JPG)           | ![](/media/zotero06.JPG) 
![](/media/zotero07.JPG) 

In Mendeley I selected all my entries then went to File -> Export -> and then save as a BibTex file. Next launch Zotero and go File -> Import and select the BibTex file. If your Mendeley library was large like mine then you would be well placed to go and get a cup of tea. 

Some time later...

If you expand and look at the citations in Zotero, you may see the following:

![](/media/zotero07.JPG)

You see how the upper file is linked but the bottom one is an attachment. What has happened I do not know but I do know that if you right click on the upper entry and select 'Show File' it will take you to the DropBox folder but if you do the same on the bottom one it will take you Zotero storage area. Clearly this sub-optimal, and I haven't a clue why it happens although I thought it had something to do with the meta data not having an author, but that is not the case. Anyway, it can be fixed.

First do an advanced search (the magnifier glass) and select "Attachment file type" is "PDF". This will show you all the files that have attached the files into the Zotero storage rather than the cloud storage. Save the search, e.g. attachedpdfs and you will see it appear in the sidebar. Now, click on that saved search and then select all the files enclosed, right click and select "Manage Attachments" -> "Rename Attachments" and wait.

![](/media/zotero09.JPG)

Once the renaming process is done you should be able to go to the storage directory and find NO pdfs.

![](/media/zotero10.JPG)

Fin (for now).
