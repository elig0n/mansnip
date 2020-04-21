### The Problem

Some man pages can be very unwieldy and hard to navigate.  Let's say you were looking for the syntax of the built-in `declare` command inside of `bash(1)`: 

> I fire up `man bash` and just simply search for the string "declare" with the "/" command! Easy peasy!

Here's the problem:

    $ man bash | grep -c declare
    34

You'll need to incrementally slodge through 34 of them using the 'n' key, which here stands for 'nope, nope, nope'. 

This is the best optimization I know of:

    $ man bash
      -N        turn line numbering on
      &declare  show all the results for "declare", scan them (remember the line number for which one you want)
      &[enter]  turn off the filtered view
      g(number) go to that line

There you go. I remember navigating in the 1980s as well. 

This is insane. Why is this basic functionality not there?

## Introducting Mansnip! 

Why can't I just do something like this:

    $ mansnip bash declare
    3812 SHELL BUILTIN COMMANDS
           declare [-aAfFgilnrtux] [-p] [name[=value] ...]
           typeset [-aAfFgilnrtux] [-p] [name[=value] ...]
                  Declare variables and/or give them attributes.  If no names are given then display the
                  values  of  variables.   The  -p option will display the attributes and values of each
                  name.  When -p is used with name arguments, additional options, other than -f and  -F,
                  are  ignored.   When  -p  is  supplied without name arguments, it will display the at‐
                  tributes and values of all variables having the attributes specified by the additional
                  options.   If  no  other  options  are  supplied with -p, declare will display the at‐
                  tributes and values of all shell variables.  The -f option will restrict  the  display
                  to  shell functions.  The -F option inhibits the display of function definitions; only
                  the function name and attributes are printed.  If the extdebug shell option is enabled
                  using  shopt, the source file name and line number where each name is defined are dis‐
           ...
    $

Well, now you can! 

Watch how mansnip shows everything with a "-z" option in the 25,888 lines of the zsh man pages (zshall)

![zshall for all](http://i.9ol.es/mansnip.webp)

But wait, there's more! You'll also get the line number and hierarchical context totally free!

You'll even get to install it easily [through pypi](https://pypi.org/project/mansnip-kristopolous/): `pip3 install mansnip-kristopolous`

## Why hasn't this existed forever?

Man pages don't really encode a lot of semantic detail. The format is pretty old. There's been a number of attempted replacements, such as [GNU info](https://www.gnu.org/software/texinfo/manual/info-stnd/) and [BSD mdoc](https://mandoc.bsd.lv/) (`man 7 mandoc_mdoc`) but the ones you use on your system are probably just the traditional boring old man files. Ah, inertia.


Being old, it's primarily concerned with formatting and not any kind of meta-information. And boy can it format! Try using the `groffer(1)` tool and do something like `groffer git-config`. You'll hopefully get a very beautiful PDF popping up on your screen, excellent for printing out and keeping in a 3-ring binder next to your Rolodex and FAX machine. Did you know groff is in a lineage that goes back to the [1964 RUNOFF](https://en.wikipedia.org/wiki/TYPSET_and_RUNOFF) program on MIT's IBM 7094 CTSS? A mere year after Licklider's [Intergalactic Computer Network](https://en.wikipedia.org/wiki/Intergalactic_Computer_Network) memo leading to what *you* probably call "the internet" these days.

Anyway, so inside the document source that leads to the pretty man page, there is next to no indication whether something has any special meaning. Let's go back to `bash(1)` and specifically the source lines for "declare" and "typeset":

    .TP
    \fBdeclare\fP [\fB\-aAfFgilnrtux\fP] [\fB\-p\fP] [\fIname\fP[=\fIvalue\fP] ...]
    .PD 0
    .TP
    \fBtypeset\fP [\fB\-aAfFgilnrtux\fP] [\fB\-p\fP] [\fIname\fP[=\fIvalue\fP] ...]
    .PD

Alright, what do those things mean? You can see that in `man 7 man` or actually, 

    $ mansnip 7 man .TP .PD .B .P .I
    55   DESCRIPTION
         Fonts
           .B  Bold

    61     .I  Italics

    97   Normal paragraphs
           .P       Same as .PP (begin a new paragraph).

    124  Indented paragraph macros
           .TP i    Begin paragraph with hanging tag.  The tag is given on the next line,  but  its  re‐
                    sults are like those of the .IP command.

    147  Miscellaneous macros
           .PD d    Set  inter-paragraph  vertical  distance to d (if omitted, d=0.4v); does not cause a
                    break.



Hrmm, well that's a problem. It's effectively just a stylesheet. In fact, the format doesn't look fundamentally different than it did in [UNIX v0 in 1970](https://github.com/DoctorWkt/pdp7-unix/blob/master/man/stat.1). A mere 2 years after [Engelbart demoed a prototype hypertext](https://en.wikipedia.org/wiki/The_Mother_of_All_Demos) system, this is well before the [semantic web](https://en.wikipedia.org/wiki/Semantic_Web).

"But wait," you say. "If I do `man --html bash` and then wait for the glacially slow [groff HTML post-processor](http://git.savannah.gnu.org/cgit/groff.git/tree/src/devices/grohtml) I do indeed get links!"

Yes, the only two semantic concessions given in the modern man format is `.SH` which is for sections and `.TH` for the title and ... that's ... it.

"Well let's fix this!" you say! 

A third time? lol, ok, have fun. Might work. For now we're stuck with these.

"So there's no surefire easy way to find these snippets then, you can only guess?"

Uh, yep.


### It's not that terrible though!

Man pages are ridiculously consistent as far as non-semantically structured text goes.  This tool usually works fine and if it doesn't, file a bug and go hunting and pecking like you usually do. I'll fix it eventually.

Thanks. enjoy.

