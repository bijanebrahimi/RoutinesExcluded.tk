BashBlog Junior

### History

here is the story. i missed blogging for a while but there was no motivation for me to start it again.
maintianing a blog is ... lets say painfull as CMSes like Wordpress gets bigger and bigger
and all i want is a very simple blog, untill i met [BashBlog](https://github.com/carlesfe/bashblog).

> bashblog: Its a Bash script that handles blog posting

very simple and also small (about 600 lines of code) script which creates blog html files. so i took
the oppurtunity and looked at the it's code. supperisingly it was very simple which was perfect
for me to begin my changes.

### What was missing?

the idea was great but there was still something missing [for me] and since the original Authur [carlesfe](https://github.com/carlesfe/bashblog)
released the code under GPL v3.0, it was ideal to fork the project and add new features.

<!-- BREAK -->

#### Alternative Markup Languages

since the script was easy to create posts, the blog's author still needs to write it in HTML which is 
unecessary since there is better alternatives [for those who are not WEB developers] like 
[Markdown](http://en.wikipedia.org/wiki/Markdown) and [Textile](http://en.wikipedia.org/wiki/Textile). so 
the first change should be an alternative markup language. i choosed **Markdown** because it is
simple and easy to write.

and since i decided to write the post's content using [Markdown](http://en.wikipedia.org/wiki/Markdown), i decided to store it as the sourcefile
so it can be used for rebuilding the structure if needed. 

#### Templates

the Original script supported Drafts which was the posts you don't want to publish yet. i turned it
into Templates. it is quiet the same but the Template doesn't get deleted after posting, so it is perfect
to create Templates for different Markup Languages. it is a kinda time-saving feature for me

#### General Improvements

and finnaly, this is what i love about [freesoftware](http://www.fsf.org). due to the freesoftware license of 
the project, i can improve the code and share it with others. i started by refactoring the 
rebuild process, edit function, adding a manual backup menu, and new stylesheet for default.


### BashBlogJunior

i called the modification [BashBlogJunior](https://github.com/bijanebrahimi/bashblogjunior)

#### Usage

Downlaod the script and give it execution permission

    $ chmod +x bbj.sh

#### Manual

To see the Manual usage just run the script without any arguments

    $ ./bbj.sh
    BashBlogImproved v0.0.1
    Usage: ./bbj.sh command [filename]
    
    Commands:
        init               creates initial files in the current path
        post [sourcefile]  insert a new blog post, or the SOURCEFILE of a Template to continue editing it
        edit [sourcefile]    edit an already created SOURCEFILE
        rebuild [option]   regenerates all/specific pages
                           option=[index|archive|posts|rss|css]
        reset              deletes blog-generated files. Use with a lot of caution and back up first!
        list               list all entries. Useful for debug
        backup [output]     backup sourcefiles
    
    For more information please open ./bbj.sh in a code editor and read the header and comments

#### Initial

Initial the blog by passing the **init** argument. it simply create two CSS files in the current directory.

    $ ./bbj.sh init
    Initializing  ... finished

#### Reset

to delete all the auto-generated files, pass the **reset** argument. it removes all the files created by the script.

    $ ./bbj.sh reset
    Are you sure you want to delete all blog entries? Please write "Yes, I am!"
    Deleted all posts, stylesheets and feeds.

#### Post

to create a new post pass the **post** argument. by default nano will be opened. you can change it in the script.
type in your first post content and close the EDITOR. the first line is the blog title and re rest will be
it's content. save the document and close the editor. the rest is interactive. you can choose to view a preview
and decide Whether it needs furthur improvements or not. when finished, post it by pressing **P**. next is to rebuild
the index, archive and rss feed pages. the script asks you to rebuild them automatically after posting new entry although you can do it manually, see **rebuild**.
you can [download](bashblog-junior.md) this post markdown sourcefile to see how it works 

#### Rebuild

you cna also rebuild the files manually by passing the **rebuild** argument. without any more argument, it
rebuilds all the files necessary. but you can choose to rebuild specific files. look at the usage section for more details.

    $ ./bbj.sh rebuild
    $ ./bbj.sh rebuild css

#### Edit

you can edit an antry by padding **edit** and name of the  Source/Template file. if you're using the default markup
, the sourcefiles extensions will be **.md**

    $ ./bbj.sh edit bashblog-junior.md

don't forget to rebuild the files after changing an entry

#### Backup

the only files you nedd to keep it in your backup is the markup language source files. use backup to store them
or do it manually. later you can use the rebuild command to build the html files from markdown files.
