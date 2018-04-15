# muban.el

This simple package allows you to quickly insert pre-defined template strings with just a few keystrokes.

## Syntax
The definitions of templates are placed in a single file `~/.emacs.muban`.
They have a very simple syntax:
```
#muban-begin <formula1>
<template-body1>
#muban-begin <formula2>
<template-body2>
...
```
The best way to understand it is through examples.
(In the following I assume you have bind `key` to the command `muban-apply`)

## Basic Usage
### Example 1
Put the following in `~/.emacs.muban`:
```
#muban-begin example
This is a very looooong string.
```
In this example, `example` is a simple formula and the line below is the template body.
Then after you type `example` at any point in an Emacs buffer and hit `key`, the template string appears in replace of the formula `example`:
```
Something here...example
```
becomes
```
Something here...This is a very looooong string.
```

### Example 2
Put the following in `~/.emacs.muban`:
```
#muban-begin img
<img src="@url here@" height=@image height@ width=@image width@>
```
The content between two `@` is intended to be modified when applying the template.
After you type `img` at any point in an Emacs buffer and hit `key`, the template string appears in replace of the formula `img`:
```
Something here...img
```
becomes
```
Something here...<img src="|" height=image height width=image width>
```
Note the `|` indicates where the cursor is.
Then you can input the image url there. Having done that, you type `TAB` to jump to the next position specified by another pair of `@`:
```
Something here...<img src="http://url.to.image/" height=| width=image width>
```
Likewise, having input the height, use `TAB` again to jump to the width.
After all the contents between pairs of `@` are already input, hit another `TAB` and you jump to the end of the template string:
```
Something here...<img src="http://url.to.image/" height=100 width=100>|
```
Then you can continue to do your work.

### Example 3
Put the following in `~/.emacs.muban`:
```
#muban-begin img#0
#0
<img src="@url here@" height=@image height@ width=@image width@>#0
```
`#digit` means to repeat the content between them, where `digit` is a number from 0-9. Note that the position of `#digit` in the formula does not matter. You can use `im#0g` if you like.
After you type `img10`(if you use `im#0g` above, then you should type `im10g` instead) at any point in an Emacs buffer and hit `key`, the template string appears in replace of the formula:
```
Something here...img10
```
becomes
```
Something here...
<img src="|" height=image height width=image width>
<img src="url here" height=image height width=image width>
... 7 duplicates here ...
<img src="url here" height=image height width=image width>
```
Note all the characters between `#0` are repeated, including newline `\n`.
Then you can input the image url and type `TAB` and jump to the next position.

That's it! `@` and `#digit` are all of the syntax. You can mix them, nest them to create more complicated templates(see below).

## Advanced Usage
### Example 4
Put the following in `~/.emacs.muban`:
```
#muban-begin list#0.#1
#0
<ul>#1
 <li>@content@</li>#1
</ul>#0
```
Note again the form of the formula is free, so you can try something like `haha#2hehe#9ahah`(the template body should be modified accordingly of course).
After you type `list2.3` at any point in an Emacs buffer and hit `key`, the template string appears in replace of the formula:
```
Something here...
<ul>
 <li>|</li>
 <li>content</li>
 <li>content</li>
</ul>
<ul>
 <li>content</li>
 <li>content</li>
 <li>content</li>
</ul>
```
Then you can continue to edit like the examples above.

### Example 5
Of course you can put multiple templates in `~/.emacs.muban`:
```
#muban-begin list#0.#1
#0
<ul>#1
 <li>@content@</li>#1
</ul>#0

#muban-begin img#0
#0
<img src="@url here@" height=@image height@ width=@image width@>#0
```
The blank line between the two template definitions can be safely ignored. In fact, all the blank characters between the last character of the last template definition and the `#muban-begin` of the next definition, including `\t` and `\n`, are ignored.

## Notes
* To use literal `#` and `@` in you template, use `\` to escape: `\@string\#1@content@\@string\#2` will become `@string#1|@string#2`, where `|` is the cursor.