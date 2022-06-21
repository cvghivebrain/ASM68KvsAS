ASM68K and AS. What's the difference?

# Directives
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```equ``` | ```equ``` | Define a value once. Forward references allowed. | <pre lang="asm">label: equ 1+2+3</pre>
|| ```constant``` | Same as ```equ``` with different syntax. | <pre lang="asm">constant label,1+2+3</pre>
```set``` or ```=``` | ```set``` or ```=``` | Define a reusable value. Forward references not allowed. | <pre lang="asm">label: = 1+2+3</pre>
```equs``` | | Define a reusable string. Forward references not allowed.<br>Single or double quotes work. To include quotes in the string, use ```''``` where the quote should be. | <pre lang="asm">label: equs "string"&#13;label2: equs 'it''s a nested quote'</pre>
```equr``` | ```reg``` | Define label to represent a register. | <pre lang="asm">label: equr d0</pre>
```reg``` | | Define label to represent a series of registers for use with ```movem```. | <pre lang="asm">label: reg d0-d7/a1-a2</pre>
```rsset``` and ```rsreset``` | | Sets the value of ```__rs```. | <pre lang="asm">rsset $100&#13;rsreset ; __rs = 0</pre>
```rs``` | | Assigns the value of ```__rs``` to a label, and advances ```__rs``` by the specified amount. | <pre lang="asm">rsset $100&#13;label: rs.l $10 ; label = $100; __rs = $110</pre>
|| ```phase``` and ```dephase``` | Similar to ```rsset```. ```phase``` sets the current address, ```dephase``` returns to previous. | <pre lang="asm">phase $100&#13;dephase</pre>

# Data
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```dc.b```, ```dc.w``` and ```dc.l``` |  | Writes a series of bytes, words or longwords. The automatic even option (```ae+```) ensures words and longwords are aligned to word. | <pre lang="asm">dc.b 1,$20,-$30</pre>
```dcb``` |  | Writes a series of bytes, words or longwords of single value. | <pre lang="asm">dcb.b 5,1 ; same as dc.b 1,1,1,1,1</pre>
```ds``` |||
```hex``` |  | Writes a series of bytes. | <pre lang="asm">hex 010203 ; same as dc.b 1,2,3</pre>

# Program counter control
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```org``` |  | Changes program counter to start writing anywhere.<br>Broken in ASM68K - always goes to 0. | <pre lang="asm">org $100 ; goto address $100</pre>
```even``` |  | Align to word. | <pre lang="asm">dc.b 1,2,3&#13;even ; same as dc.b 1,2,3,0</pre>
```cnop x,y``` |  | Align to ```y``` and append ```x``` bytes. ```x``` can be 0 but ```y``` cannot. | <pre lang="asm">cnop 2,16 ; align to 16 and append 2 bytes</pre>
```obj``` and ```objend``` |  | Makes program counter believe it's at an address, without actually going there. | <pre lang="asm">obj $100&#13;dc.l * ; writes dc.l $100 to current location&#13;objend</pre>

# Conditionals
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```if```, ```elseif```, ```else``` and ```endif```/```endc``` |  | Assemble different code depending on specified conditions. ```endif``` and ```endc``` are interchangeable. | <pre lang="asm">if var=0&#13;dc.b 'var is 0'&#13;elseif var=1&#13;dc.b 'var is 1'&#13;else&#13;dc.b 'var is something else'&#13;endc</pre>
```case``` and ```endcase``` |  | Alternative to ```if```. Seems broken in ASM68K. | <pre lang="asm">case var&#13;=0&#13;dc.b 'var is 0'&#13;=1&#13;dc.b 'var is 1'&#13;=?&#13;dc.b 'var is something else'&#13;endcase</pre>
```rept x``` and ```endr``` |  | Assembles code ```x``` number of times. Useful in combination with ```narg``` in macros. | <pre lang="asm">rept 3&#13;dc.b 0 ; same as dc.b 0,0,0&#13;endr</pre>
```while``` and ```endw``` |  | Assembles code repeatedly while condition is met.<br>```equs``` does not work inside a ```while``` loop. | <pre lang="asm">var: = 0&#13;while var<3&#13;dc.b var ; same as dc.b 0,1,2&#13;var: = var+1&#13;endw</pre>
```do``` and ```until``` |  | Assembles code repeatedly until condition is met. | <pre lang="asm">var: = 0&#13;do&#13;dc.b var ; same as dc.b 0,1,2&#13;var: = var+1&#13;until var>2</pre>
```end``` |  | Stops assembly. Place at the end of the main ASM file. | 

# Macros
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```macro``` and ```endm``` |  | Defines a macro. Parameters can be named or left blank (in which case they are referenced by ```\1```, ```\2``` etc.| <pre lang="asm">writebyte: macro num&#13;dc.b \num&#13;endm&#13;writebyte 5 ; same as dc.b 5</pre>
```macros``` |  | Single-line macro. ```endm``` not needed. | <pre lang="asm">writebyte: macros&#13;dc.b \1</pre>
```mexit``` |  | Exits macro prematurely. | 
```\0``` |  | Size parameter. Whatever is after the ```.``` when a macro is used, usually ```b```, ```w``` or ```l``` but can be anything. | <pre lang="asm">writeany: macro&#13;dc.\0 \1&#13;endm&#13;writeany.l 999 ; same as dc.l 999</pre>
```\@``` |  | Underscore followed by the number of times the macro has been used. Useful for creating unique labels. | <pre lang="asm">setvalue: macro&#13;value\\@: equ \1&#13;endm&#13;setvalue 5 ; same as value_1: equ 5</pre>
```\#``` and ```\$``` |  | Value of variable output as a string. | 
```\_``` |  | All parameters, including the commas. | 
```\*``` |  | Value of label where macro was used. ```*``` must be the first parameter, and ```\*``` must be defined. | <pre lang="asm">readself: macro *&#13;&#92;\*: equ *&#13;self: equ &#92;\*&#13;endm&#13;readself ; same as self: equ *</pre>
```narg``` |  | Number of parameters used in a macro. | <pre lang="asm">nargout: macro&#13;dc.b narg&#13;endm&#13;nargout 1,2,3,4 ; same as dc.b 4</pre>
```shift``` |  | Deletes the first parameter and moves the rest left. Useful in combination with ```narg```. | 
```pushp``` and ```popp``` |  |  | 
```purge``` |  |  | 

# Files
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```include``` |  | Include an ASM file. | <pre lang="asm">include "file.asm"</pre>
```incbin``` | ```binclude``` | Include a binary file. | <pre lang="asm">incbin "file.bin"&#13;incbin "file.bin",$10 ; start at address $10 in file&#13;incbin "file.bin",$10,$20 ; start at $10, include only $20 bytes</pre>

# Functions
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```ref(x)``` |  | Returns _true_ if label ```x``` has been previously encountered. | <pre lang="asm">if ref(label) ; false on first run, true on second&#13;label: rts&#13;endif</pre>
```def(x)``` |  | Returns _true_ if label ```x``` has been defined. | <pre lang="asm">label: equ 1&#13;if def(label) ; true</pre>
```type(x)``` |  | Returns info on label ```x``` as a word bitfield. | 
```strlen(x)``` |  | Returns length of string ```x```. | <pre lang="asm">if strlen("text")=4 ; true&#13;label: equs "four"&#13;if strlen(label)=4 ; true</pre>
```strcmp(x,y)``` |  | Returns _true_ if string ```x``` matches string ```y```. Variables require quotes and backslash. | <pre lang="asm">label: equs "text"&#13;if strcmp("\label","text") ; true</pre>
```instr(x,y)``` and ```instr(s,x,y)``` |  | Returns position of substring ```y``` in string ```x``` (starts at 1). Returns 0 if not found. ```s``` specifies starting position. | <pre lang="asm">label: equs "string"&#13;dc.b instr("\label","r") ; 3&#13;dc.b instr(2,"\label","r") ; 3&#13;dc.b instr(4,"\label","r") ; 0</pre>
```substr``` |  | Assigns substring to variable. Actually more like a macro than a function. Its parameters are start, end (both optional) and string. | <pre lang="asm">label: equs "string"&#13;sub1: substr 2,5,"\label" ; sub1 = trin&#13;sub2: substr 2,,"\label" ; sub2 = tring&#13;sub3: substr ,5,"\label" ; sub3 = strin&#13;sub4: substr ,,"\label" ; sub4 = string</pre>
