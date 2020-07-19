---
title: "applications"
date: "2012-12-25"
draft: false
---

> The 'quick reference' pages here are 'crib files', which I typically use from the command line. They were originally plain text files which I grep-ed, or findstr-ed, or select-string-ed to look up bits of syntax
>
> More recently I've put some markdown tags around the text so that I could put them up here.

## Confluence (server)

#### Global Shortcuts

|                      |          |
| ---                  | ---      |
| Create Content       | c        |
| Go to Dashboard      | g then d |
| Open Notifications   | g then n |
| Open Recently Viewed | g then r |
| Browse Current Space | g then s |
| Open Shortcut Help   | ?        |
| Quick Search         | /        |
| Quick Search         | g then g |
| Toggle Space Sidebar | [        |

#### Page / Blog Post Actions

|                         |     |
| ---                     | --- |
| Add Blog                | b   |
| Edit                    | e   |
| Comment                 | m   |
| Next Inline Comment     | n   |
| Previous Inline Comment | p   |
| Close Inline Comment    | ]   |
| Watch Page              | w   |
| Share                   | s   |
| Edit Labels             | l   |
| View Attachments        | t   |
| Link to Page            | k   |


#### Block Formatting

|               |              |
| ---           | ---          |
| Paragraph     | Ctrl+0       |
| Heading 1     | Ctrl+1       |
| Heading 6     | Ctrl+6       |
| Preformatted  | Ctrl+7       |
| Quote         | Ctrl+8       |
| Bullet list   | Ctrl+Shift+B |
| Numbered list | Ctrl+Shift+N |

#### Rich Formatting

|                  |              |
| ---              | ---          |
| Files and images | Ctrl+M       |
| Link             | Ctrl+K       |
| Insert markup    | Ctrl+Shift+D |
| Macro            | Ctrl+Shift+A |
| Table            | Ctrl+Shift+I |
| Cut table row    | Ctrl+Shift+X |
| Copy table row   | Ctrl+Shift+C |
| Paste table row  | Ctrl+Shift+V |


#### Editing Actions

|         |              |
| ---     | ---          |
| Preview | Ctrl+Shift+E |
| Save    | Ctrl+S       |



#### Font Formatting

|                |               |
| ---            | ---           |
| Bold text      | *Bold*        |
| Underline text | +Underline+   |
| Italic text    | _Italic_      |
| Monospace text | {{Monospace}} |


#### Autocomplete

|             |                    |
| ---         | ---                |
| Image/Media | !                  |
| Link        | [                  |
| Macro       | left curly bracket |
| Date        | //                 |
| Mention     | @                  |



#### Tables

````
||||| + enter
||heading||heading||
````


#### Styles
|     |         |
| --- | ---     |
| h1. | Heading |
| h3. | Heading |
| bq. | Quote   |



#### Emoticons
|     |              |
| --- | ---          |
| (/) | Tick         |
| :)  | Smiley smile |



#### Lists
|     |                 |
| --- | ---             |
|     | # numbered list |
| *   | bullets         |
| []  | task            |




## Keyboard shortcuts

### IE

|     |                 |
| --- | ---             |
| Alt-D | Address bar |
| Ctrl-F4 | Close Tab |
| Alt-F4 | Next Tab |


### Outlook
|     |                 |
| --- | ---             |
| Ctrl-Enter | Send |
| Ctrl-N | New mail |
| Ctrl-Shift-V | Move to Folder |

### Excel
|     |                 |
| --- | ---             |
| F2  | Edit cell |
| ALT-Enter | Line break within a cell |
| Ctrl-Space | select column |
| shift-Space | select row |
| ctrl-Pg Up/Dn | cycles through worksheets |
| ctrl-Home | selects A1 on the current  |
| Ctrl+1 | Brings up the “Format cells”  |

### Word hotkeys
|     |                 |
| --- | ---             |
| CTRL+SHIFT+>  | Increase font size  |
| CTRL+Z  | Undo the last action  |
| CTRL+Y  | Redo the last action  |
| CTRL+F  | Find text |
| ALT+CTRL+Y  | Repeat find  |
| CTRL+G  | Go to |
| ALT+CTRL+Z  | Go back  |
| CTRL+LEFT ARROW  | One word to the left  |
| CTRL+RIGHT ARROW  | One word to the right  |
| CTRL+END  | End of doc |
| CTRL+HOME  | Start of doc |
| F6 | Other pane |
| Alt-1 | Heading 1 |
| Alt-C | Command |
| Alt-O | Output |


### rdp
|     |                 |
| --- | ---             |
| Ctrl-Alt-Break  | Minimize terminal services thing |


## Excel
### If, then, else
if the value of e22 is db then set this cell to C22 otherwise zero-ize it
````
=IF(E22="Db",C22,0)   
````
### Vlookup
````
=VLOOKUP(A7,Runningornot!$A$1:Runningornot!$B$200,2,FALSE)
````
-take the value of A7 and look it up in the table bounded by the next couple of reference. Return the 2nd column corresponding to the lookup
````
=VLOOKUP(D2,The_list!D$1:G$1000,4, FALSE)
````
### Insert a static date or time
|     |                 |
| --- | ---             |
| Current date | Select a cell and press CTRL+; |
| Current time | Select a cell and press CTRL+SHIFT+; |
| Current date and time | Select a cell and press CTRL+; then SPACE then CTRL+SHIFT+; |

### Number formatting
e.g. to get 001 rather than 1

Do Format > Cells > Custom > 

Overtype General (on the right) with 000

### Conditional formatting with more than one condition
````
= OR(D3="Drogba", D3="Torres")
````
Fires the formatting if cells is Drogba or Torres

### Inserting multiples lines
From <a href="http://www.exceltrick.com/how_to/insert-multiple-rows-in-excel/">How to Insert Multiple Rows in Excel</a>:
<blockquote>Open your spreadsheet, and first of all insert one row to your excel sheet manually.
Then simply repeatedly press the key on your keyboard, till the required number of rows are inserted.</blockquote>

### Show the month for a date

```
=TEXT(A2, "mmmm")
```

### Show first letter of day for a date

```
=LOWER(LEFT(TEXT(A5,"ddd"), 1))
```
### Keyboard shortcuts

| kb | action |
| -- | ------ |
| F11 | Generate a chart in another sheet |
| Alt-F1 | Generate a chart in same sheet |
| Ctrl and Tilda | show formulae in the sheet |

### Links
<a href="http://www.howtogeek.com/howto/45670/how-to-highlight-a-row-in-excel-using-conditional-formatting/">How to Highlight a Row in Excel Using Conditional Formatting</a>


## vim

### vimrc

 
  
```
" Either in:
" * C:\Program Files\Vim\_vimrc (Windows)</li>                                    " vimrc
" * /etc/vim/gvimrc (Linux)                                                       " vimrc
                                                                                  " vimrc
set nocompatible                                                                  " vimrc
                                                                                  " vimrc
source $VIMRUNTIME/vimrc_example.vim                                              " vimrc
source $VIMRUNTIME/mswin.vim                                                      " vimrc                " vimrc
behave mswin                                                                      " vimrc
                                                                                  " vimrc
set backupdir=C:\temp\vim                                                         " for the backup files " vimrc
set directory=C:\temp\vim                                                         " for the swap files   " vimrc
                                                                                  " vimrc
                                                                                  " vimrc
set softtabstop=4 shiftwidth=4 expandtab                                          " vimrc
set ignorecase                                                                    " vimrc
set smartcase                                                                     " vimrc
map B l?\$<CR>d2wuowrite-debug " `<Esc>pa: <<Esc>pA>  " <Esc>                     " vimrc
                                                                                  " vimrc
set tabstop=4 softtabstop=4 shiftwidth=4 expandtab                                " vimrc
color twilight                                                                    " vimrc
                                                                                  " vimrc
set guifont=Consolas:h11:cANSI                                                    " vimrc
inoremap ,bq <CR><blockquote>xx</blockquote><Esc>?xx<CR>cw                        " vimrc
inoremap ,h2 <Esc>I<h2><Esc>A</h2><Esc>0llll                                      " vimrc
inoremap ,h3 <Esc>I<h3><Esc>A</h3><Esc>0llll                                      " vimrc 
inoremap ,h4 <Esc>I<h4><Esc>A</h4><Esc>0llll                                      " vimrc
inoremap ,h5 <Esc>I<h5><Esc>A</h5><Esc>0llll                                      " vimrc
                                                                                  " vimrc
map ,fn O#### Footnotes and references<Esc>                                       " vimrc
map ,ic O#### Image credit<Esc>                                                   " vimrc
                                                                                  " vimrc
inoremap ,ml <Esc>?http<CR>d/ i<a href=" <Esc>pa" ><Esc>pa</a><Esc>?http<Cr>      " vimrc
                                                                                  " vimrc
noremap ,fun <Esc>:r c:\powershell\templates\function.ps1<CR>/xx<CR>              " vimrc
inoremap ,fun <Esc>:r c:\powershell\templates\function.ps1<CR>/xx<CR>             " vimrc

```

### Macros

````
Keyboard macros:                           " vim

qx - to start recording a macro called 'x' " vim

q - to stop recording the macro            " vim

@x - to execute it                         " vim
````

### Formatting instructions
````
/* vi: highlight MYHEADER guibg=blue */                    " vim
/* vi: syntax match MYHEADER /^#..*/ */                    " vim
/* vi: set readonly: * /                                   " vim
/* vi: set ts=2 sw=2 foldmethod=marker ignorecase: * /     " vim
````

### Using the sidebar thing
````
:vimgrep /^## / % " vim
:copen            " vim

Then CTRL-H W to move it to the side, if you want to       " vim
````
For Powershell I'd typically use:                          " vim
````
:vimgrep /^function / %                                    " vim
:copen                                                     " vim
````


### Folding at 'function'
```
::%g /^function/ normal! zf%                               " vim
```

### Spelling

````
:set spell                                                 " vim
]s - move to the next mispelled word                       " vim
[s - move to the previous mispelled word                   " vim
zg - add a word to the dictionary                          " vim
zug - undo the addition of a word to the dictionary        " vim
z= - view spelling suggestions for a mispelled word        " vim
map b zg]s - add to the dictionary then go to the next one " vim
<Ctrl-x>s - go back to spelling mistake in insert mode     " vim
````


## Windows - Arrr
| Command            | Application            |
| -------            | -----------            |
| access.cpl         | Microsoft Access       |
| clipbrd            | Clipboard Viewer       |
| compmgmt.msc       | Computer Management    |
| control            | Control Panel          |
| control admintools | Administrative Tools   |
| control schedtasks | Scheduled Tasks        |
| dcomcnfg           | Component Services     |
| desk.cpl           | Display Properties     |
| diskmgmt.msc       | Disk management        |
| eventvwr.msc       | Event Viewer           |
| excel              | Microsoft Excel        |
| explorer           | Windows Explorer       |
| findfast.cpl       | Findfast               |
| firefox            | Firefox (if installed) |
| helpctr            | Help and Support       |
| iexplore           | Internet Explorer      |
| mmsys.cpl          | Sounds and Audio       |
| mobsync            | Microsoft Synch Tool   |
| msconfig           | System Config Utility  |
| msinfo32           | System Information     |
| notepad            | Notepad                |
| odbccp32.cpl       | ODBC admin             |
| osk                | On Screen Keyboard     |
| powerpnt           | Microsoft Powerpoint   |
| services.msc       | Services               |
| sysdm.cpl          | System Properties      |
| sysedit            | System Config Editor   |
| Sndvol32           | Volume control         |
| taskmgr            | Task Manager           |
| utilman            | Utility Manager        |
| winver             | Windows Version        |
| winword            | Microsoft Word         |

## Markdown

````
## The second largest heading (an <h2> tag)               # Markdown
| Team          | Posn          | Pts   |                 # Markdown
| ------------- |--------------:| -----:|                 # Markdown
| Chelsea       |              1|   100 |                 # Markdown
| Spurs         |              2|     1 |                 # Markdown
| Arsenal       |              3|     0 |                 # Markdown
> Blockquotes                                             # Markdown
*italic* or _italic_                                      # Markdown
**bold** or __bold__                                      # Markdown
~~Strikethrough~~                                         # Markdown
* Item (no spaces before the *) or                        # Markdown
- Item (no spaces before the -)                           # Markdown
1. Item 1                                                 # Markdown
1. Furthermore, ...                                       # Markdown
2. Item 2                                                 # Markdown
`monospace` (backticks)                                   # Markdown
three or four backticks begin/end code block              # Markdown
[A link](http://mattypenny.net).                          # Markdown
![Descriptive Alt text](/images/picture.jpg).             # Markdown

````

## VS Code


|                   |                                                   |        |
| ----------------- | ------------------------------------------------- | ------ |
| Ctrl-Shift-P      | Command prompt                                    | vscode |
|                   |                                                   | vscode |
| F8                | Run selected code                                 | vscode |
| Ctrl-Shift-Space  | Parameter hints                                   | vscode |
| Ctrl-,            | Settings                                          | vscode |
| Alt-Up            | Move line up                                      | vscode |
| Ctrl-Shift-\      | Jump to bracket match                             | vscode |
| Ctrl-Home         | Start of file                                     | vscode |
| Ctrl-End          | End of file                                       | vscode |
| Ctrl-Shift+[      | Fold                                              | vscode |
| Ctrl-Shift+]      | Unfold                                            | vscode |
| Ctrl-K Ctrl+O     | Fold all                                          | vscode |
| Ctrl-K Ctrl+J     | Unfold all                                        | vscode |
| Ctrl-/            | Toggle line comment                               | vscode |
| Shift-Alt-A       | Toggle block comment                              | vscode |
| Alt-Z             | Toggle word wrap                                  | vscode |
| Ctrl-P            | Open file                                         | vscode |
| Ctrl-I            | Select current line                               | vscode |
| Ctrl-Shift-L      | Select all occurences of current selection        | vscode |
| Shift-Alt-Mouse   | Block select                                      | vscode |
| Ctrl-Space        | Kick in intellisense                              | vscode |
| Alt-F12           | Peek definition                                   | vscode |
| Shift-F12         | Show references                                   | vscode |
| Ctrl-B            | Toggle side bar                                   | vscode |
| Ctrl-=            | Zoom in                                           | vscode |
| Ctrl--            | Zoom out                                          | vscode |
| Ctrl-Shift-V      | Markdown preview                                  | vscode |
| Ctrl-K V          | Markdown preview to side                          | vscode |
| F2                | Change all occurences of current word             | vscode |

