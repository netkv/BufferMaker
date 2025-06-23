# BufferMaker
## BashboX Text Buffer Maker

Pure Bash TUI framework

BufferMaker is the result of me trying to add too stuff into the Ebashs project, from which BufferMaker was separated, since it may be usesfull on its own.

Ebashs and thus BufferMaker are based on https://github.com/comfies/bed.

# BX*

Now Buffermaker has been split even more, in future these splitted scripts will be usable on their own, but nows it's just BXInput and BXCommon that can have any use outside of buffermaker.
Buffermaker depends on them all.

## BXcommon

This is collection of functions from that have no relation to making TUI and are useful outside of BufferMaker

BufferMaker depends on it for function.

## Basics

First step in creating user interface is creating a buffer. Buffer is collection of arrays used to display stuff.

Buffers are completelly independent and you can use how many you need

### buffer

Array used for the source of displaying data.

### bf_s

Array used for displaying. Shouldn't be accesed directly (unless you want to use custom redrawing function), instead `buffer` array is automatically converted to `bf_s` during the redraw function if the given line hadn't been rendered already. If needed the rendering to `bf_s` can be invoked manual via `make-render` (renders all visible lines), `make-render-area` <start line> <end line>, `make-render-line` <line> (renders current line if no arguments passed).

#### Rendering

(syntaxing in Ebashs terms)

To render a buffer, a syntax function is required. There are two types of syntax functions:

- "normal" - `set-face` depending on `$word`
- "executing" (bf_d[syntax-exec]=1) - you need to do lot of stuff manually

#### Faces

Faces are what defines a look. Faces are defined via array in which every odd item is face name, and every even item is face value (terminal escape code, either directly or via helper functions). These faces have to be loaded in via `load-theme <array_name>`.

To help setting faces, and bring better compatibility options (like linux framebuffer tty full color mode), you should use face setting helper function when possible.

##### :foreground <color>
Set foreground
##### :background <color>
Set background

Color can be either named color, one of 256 colors (defined as `c222`) or hex color (like `#ff0000`).

##### :weight
Set boldness or dimness.

##### :mode
Set inversion.

##### :slant
Set italics.

These functions can be stacked together, like for example:

```bash
pink_theme=(
	light "$(:foreground '#000000' :background '#ffa0af' :slant italic :weight dim)"
	dark "$(:foreground '#000000' :background '#ffa0af' :mode inverse)"
)
load-theme pink_theme
```

### bf_c

Array used for moving and clicking, mainly used by format buffer, but shouldn't be too hard to work with manually.

## Input

Input map is declared via `mode`. To create new mode use `add-mode` function. Each mode also has its own options, defined via `mode-options`.

For example:
```bash
add-mode fun
    :: ESC die
	:: "$(kbd x)" die
	:: "$(kbd C-x)" die
	:: "$(kbd M-x)" die
	:: [f5] redraw
```
Pressing ESC key will cause the program to halt to death instantly. Same for `x`, `Ctrl + x` and `Alt + x`. F5 will cause program to redraw.

* [function-key]
* [arrow-key]
* [prior | next]
* RET
* DEL
* [delechar]
* hex code ending with 0

### mode-options

```bash
add-mode something
    :: key function
    mode-options
        :: name value	
```

## format

BufferMaker's own formating markup language.

### color
```
<f> face text <f>
```
### uppercase
```
<u> text </u>
```
### title
```
<h> text </h>
```
### link
```
<link> function : text </link>

or

<a> function : <f> link text </f> </a>
```
### object

The most powerfull of format nonsense. Use `obj <id>` function to move cursor to object with matching id.
```
<o> id: id select: to-do-on-enter right: on-move-right left: on-left up: on-up down: down text: text </o>
```
### large buttons

They behave similarly to objects, and in fact are objects, so you can't put object into lbt or other way around.

They're 3 lines tall, and require 3 instances of `<lbt></lbt>` pairs with same id as shown in example.

width: is optional, without it buffermaker calculates needed minimum width automatically

```
'<lbt> id: id left: to-do-on-left up: to-do-on-up down: to-do-on-down right: to-do-on-right select: to-do-on-enter width: width text: text </lbt>'
'<lbt> id: id </lbt>'
'<lbt> id: id </lbt>'
```

### positioning
```
<s> number-of-spaces

<indent> &tab
text
...
</indent>

<tab> (= <->)
```
### variables
```
<v> varname
```

### integrated buffers
```bash
function msgbox-test {
	local -a test_msgbox
	local test_msgbox_title
	
	test_msgbox_title='Title'
	test_msgbox=(
		'<f> red A very serious error!!! </f>'
		'<f> yellow But it might be just fine </f>'
		"don't worry"
	)
	msgbox test_msgbox
}

function dialogbox-test {
	local test_dialogbox_title='Question?'
	local -a test_dialogbox=(
		'<f> blue Would you like to buffer? </f>'
		"<f> hint it's fine to buffer </f>"
		'and now choose...'
	)
	dialogbox test_dialogbox
}
function test_dialogbox.yes {
	box.close
	<do something when yes>
}
function test_dialogbox.no {
	box.close
	<do something when no>
}
```

## BXcommon functions

### Array manipulation (both asscociative and indexed arrays)
`copy-array source target`
`append-array source target`

## Example - simple number counter

see example file

(License BSD 0 (tldr - do anything you want))
