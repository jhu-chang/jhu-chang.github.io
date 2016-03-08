---
layout: post
title: "Jupyter Magics"
category: "Doc"
tags: [magic,jupyter,python]
---

Jupyter has a system of commands we call 'magics' that provide effectively a mini command language that is orthogonal to the syntax of Python and is extensible by the user with new commands. Magics are meant to be typed interactively, so they use command-line conventions, such as using whitespace for separating arguments, dashes for options and other conventions typical of a command-line environment.

Magics come in three kinds:

- **Line magics**: these are commands prepended by one `%` character and whose arguments only extend to the end of the current line.
    1. `%cd PATH` - change current directory of session
    2. `%connect_info` - show connection information
    3. `%download URL [FILENAME]` - download file from URL
    4. `%html CODE` - display code as HTML
    5. `%install_magic URL` - download and install magic from URL
    6. `%javascript CODE` - send code as JavaScript
    7. `%latex TEXT` - display text as LaTeX
    8. `%lsmagic` - list the current line and cell magics
    9. `%magic` - show installed magics
    10. `%reload_magics` - reload the magics from the installed files
    11. `%shell COMMAND` - run the line as a shell command
    12. `%time COMMAND` - show time to run line

- **Cell magics**: these use two percent characters as a marker (`%%`), and they receive as argument both the current line where they are declared and the whole body of the cell. Note that cell magics can only be used as the first line in a cell, and as a general principle they can't be 'stacked' (i.e. you can only use one cell magic per cell). A few of them, because of how they operate, can be stacked, but that is something you will discover on a case by case basis.
    1. `%%file FILENAME` - write contents of cell to file
    1. `%%html` - display contents of cell as HTML
    1. `%%javascript` - send contents of cell as JavaScript
    1. `%%latex` - display contents of cell as LaTeX
    1. `%%shell` - run the contents of the cell as shell commands
    1. `%%time` - show time to run cell

- **Shell shortcut**:
    1. `! COMMAND ...` - execute command in shell


The `%lsmagic` magic is used to list all available magics, and it will show both line and cell magics currently defined.

Any cell magic can be made persistent for rest of session by using `%%%` prefix.

Help on items:

  1. `??item` - get detailed help on item
  1. `?item` - get help on item
