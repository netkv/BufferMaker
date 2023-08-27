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

### bf_c

Array used for moving and clicking, mainly used by format buffer, but shouldn't be too hard to work with manually.

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

## Example - simple number counter
```bash
#!/bin/bash
source buffermaker
## Example of buffermaker tui program

add-mode testui
	:: '[left]' format-left
	:: '[up]' format-up
	:: '[right]' format-right
	:: '[down]' format-down
	:: 'RET' link-enter
	:: '[next]' 'scroll-down'
	:: '[prior]' 'scroll-up'
	:: "$(kbd C-a)" 'move-beginning-of-line'
	:: "$(kbd C-e)" 'move-end-of-line'
	:: "$(kbd C-p)" 'previous-line'
	:: "$(kbd C-n)" 'next-line'
	:: "$(kbd C-f)" 'forward-char'
	:: "$(kbd M-f)" 'forward-word'
	:: "$(kbd C-b)" 'backward-char'
	:: "$(kbd M-b)" 'backward-word'
	:: "$(kbd M-v)" 'scroll-down'
	:: "$(kbd C-v)" 'scroll-up'
	:: "$(kbd C-c)" 'die'
	:: "$(kbd M-x)" 'execute-extended-command'
	mode-options
		:: else :
		:: disable-global 1

function testui {
	declare-new-buffer
		:: line 5
		:: column 0
		:: info "Testui"
		:: mode testui
		:: filetype 'format'
		:: file "testui"
		:: modified 0
		:: isfile 0
		:: syntax syntax-format
		:: syntax-exec 1
	size-full
	copy-array index buffer	
}

function inc {
	((incnum++))
	make-render-line 3
	redraw
}

function dec {
	((incnum--))
	make-render-line 3
	redraw
}

function show-more {
	buffer[7]="${buffer[6]}"
	buffer[6]='More: '\
'<o> id: setnum select: set-number redraw right: obj close left: : up: show-less down: show-less '\
'text: <f> link Set number </f> '\
'</o> '\
'<o> id: close select: show-less left: obj setnum right: : up: show-less down: show-less '\
'text: <f> light-red x </f> '\
'</o>'
	make-render-area 6 7
	redraw
	obj close
}
function show-less {
	buffer[6]="${buffer[7]}"
	unset buffer[7]
	make-render-area 6 7
	redraw
	obj more
}

incnum=0

index=(
''
'<h> Super number counter </h>'
''
'<-> <f> highlight <v> incnum </f>'
''
# controls
'<o> id: inc select: inc redraw right: obj dec left: : up: : down: obj quit '\
'text: <f> link Increase </f> '\
'</o> '\
'<o> id: dec select: dec left: obj inc right: obj more up: : down: obj quit '\
'text: <f> link Decrease </f> '\
'</o> '\
'<o> id: more select: show-more left: obj dec right: : up: : down: obj quit '\
'text: <f> title ... </f> '\
'</o>'
# quit button
'<o> id: quit select: die right: : left: down: : up: obj inc text: <f> red Quit </f> </o>'
)

load-default-config
init-var
testui
redraw
main
```

(License BSD 0 (tldr - do anything you want))
