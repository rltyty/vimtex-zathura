# vimtex-zathura
A method to latex edit and preview side by side

## Purpose

This is about how to co-work between zathura, synctex and vimtex.

To use Vim + vimtex plugin + a document viewer with [synctex][2] support, so I
can get instant view updates of the PDF alongside while I'm editing a tex
script. Skim (a PDF viewer on macOS) is great, fast and works out of box.
However, it is built around Apple's PDFKit which doesn't support recolor of
the page background. What I want is to change the viewer's bg color from white
to some dark color, especially the same as my Vim's(like this). Zathura can
make this happen, except that no pre-built package for macOS and its source
doesn't ship with synctex. That's why I need some hacks.

![][1]

## How to hack

### build libsynctex.a:
1. Fetch the latest synctex source from https://github.com/jlaurens/synctex
2. Build.  
    Method 1:  
    The README tells how to build the client tool "synctex" with a
    Xcode CLI project, we can easily adapt to a Xcode Library project for
    libsynctex.a.  
    Method 2:  
    I wrote a [Makefile](Makefile.libsyntex) from scratch, just make it.  
    Note: 

        a. -DSYNCTEX_WORK mentioned in README should be -D__SYNCTEX_WORK__
        b. make sure you have zlib
        c. In synctex_parser.c, change "/usr/include/zlib.h" to "<zlib.h>"
            # ifdef __SYNCTEX_WORK__
            //# include "/usr/include/zlib.h"
            # include <zlib.h>


## Hack Zathura build script to link libsynctex.a
1. Follow the instructions of https://pwmt.org/projects/zathura/installation/ to build zathura and the dependencies.  
    Note: No synctex support by now. Check the zathura binary file,

             nm zathura | grep synctex | wc -l
             11

2. Put the synctex source folder in Zathura's source folder, e.g. zathura-0.4.5/zathura/synctex.
3. Hack meson.build, to add synctex support.
    Insert the following two lines before "if synctex.found()"
    build_dependencies += synctex
    defines += '-DWITH_SYNCTEX'
    if synctex.found()
4. Remove and recreate "build" folder and redo "meson build"
5. Hack build/build.ninja, to link libsynctex.a.  
   Edit L240, LINK_ARGS of the zathura target, add -L<path of zathura-0.4.5>/zathura/synctex -lsynctex:

       LINK_ARGS = -Wl,-dead_strip_dylibs -Wl,-undefined,error -Wl,-headerpad_max_install_names libzathura.a -L<path of zathura-0.4.5>/zathura/synctex -lsynctex ...

6. ninja & ninja install  
   Now, zathura should contain synctex functions. Check again,

        nm zathura | grep synctex | wc -l
        523

[1]: <Resources/vimtex_zathura.png> "screenshot"


[//]: # (vim: tw=78:ts=8:sts=4:sw=4:noet:ft=markdown:norl:)
