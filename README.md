# BufferMaker

Pure Bash TUI framework

BufferMaker is the result of me trying to add too stuff into the Ebashs project, from which BufferMaker was separated, since it may be usesfull on its own.

Ebashs and thus BufferMaker are based on https://github.com/comfies/bed.

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
### positioning
```
<s> number-of-spaces

<i> number-of-spaces
text
...
</i>

<tab> (= <->)
<i-tab>
text
...
</i>
```
### variables
```
<v> varname
```

## Helper functions

### Array manipulation (both asscociative and indexed arrays)
`copy-array source target`
`append-array source target`

## Example - simple number counter

see example file

(License BSD 0 (tldr - do anything you want))
