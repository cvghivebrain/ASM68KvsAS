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
```alias``` and ```disable``` |  | Change the names of constants and functions, allowing them to be redefined. | <pre lang="asm">sqrtnew: alias sqrt&#13;disable sqrt</pre>
|| ```charset``` | Redefine the value of an ASCII character. Use without parameters to reset all characters to default. | <pre lang="asm">charset 'a',255 ; a = $FF&#13;charset ; a = $61</pre>
|| ```codepage``` | Switch between named ASCII character sets. ```STANDARD``` is default. | <pre lang="asm">codepage new&#13;charset 'a',255 ; a = $FF&#13;codepage STANDARD ; a = $61</pre>
|| ```enum``` and ```nextenum``` | Define a series of values which increment. Starts at 0 unless specified. ```nextenum``` continues the same series on another line. | <pre lang="asm">enum x,y,z&#13;dc.b x,y,z ; same as dc.b 0,1,2</pre>
|| ```enumconf``` | Change the increment for ```enum```. | <pre lang="asm">enumconf 2&#13;enum x=2,y,z&#13;dc.b x,y,z ; same as dc.b 2,4,6</pre>
|| ```save``` and ```restore``` | Saves current ```cpu```, ```codepage``` and other things. | 

# Data
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```dc.b```, ```dc.w``` and ```dc.l``` | ```dc``` | Writes a series of bytes, words or longwords. In ASM68K, the automatic even option (```ae+```) ensures words and longwords are aligned to word. | <pre lang="asm">dc.b 1,$20,-$30</pre>
```dcb``` |  | Writes a series of bytes, words or longwords of single value. | <pre lang="asm">dcb.b 5,1 ; same as dc.b 1,1,1,1,1</pre>
```ds``` | ```ds``` | Writes a series of bytes, words or longwords of value 0. | <pre lang="asm">ds.b 5 ; same as dc.b 0,0,0,0,0</pre>
```hex``` |  | Writes a series of bytes. | <pre lang="asm">hex 010203 ; same as dc.b 1,2,3</pre>
```datasize``` and ```data``` |  | Writes a series of bytes with padding. ```datasize``` can be 1 to 256. | <pre lang="asm">datasize 4&#13;data 1,2 ; same as dc.l 1,2</pre>

# Program counter control
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```org``` | ```org``` | Changes program counter to start writing anywhere.<br>Broken in ASM68K - always goes to 0. | <pre lang="asm">org $100 ; goto address $100</pre>
|| ```rorg``` | Add value to program counter. Can be negative. | <pre lang="asm">rorg $100 ; goto *+$100</pre>
```even``` |  | Align to word. | <pre lang="asm">dc.b 1,2,3&#13;even ; same as dc.b 1,2,3,0</pre>
|| ```padding``` | Always align to word when turned ```on```. | <pre lang="asm">padding on&#13;padding off</pre>
```cnop x,y``` |  | Align to ```y``` and append ```x``` bytes. ```x``` can be 0 but ```y``` cannot. | <pre lang="asm">cnop 2,16 ; align to 16 and append 2 bytes</pre>
```obj``` and ```objend``` | ```phase``` and ```dephase``` | Makes program counter believe it's at an address, without actually going there. | <pre lang="asm">obj $100&#13;dc.l * ; writes dc.l $100 to current location&#13;objend</pre>
|| ```align``` | Align to value. | <pre lang="asm">dc.b 1,2,3&#13;align 2 ; same as dc.b 1,2,3,0</pre>

# Conditionals
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```if```, ```elseif```, ```else``` and ```endif```/```endc``` | ```if```, ```elseif```, ```else``` and ```endif``` | Assemble different code depending on specified conditions. ```endif``` and ```endc``` are interchangeable. | <pre lang="asm">if var=0&#13;dc.b 'var is 0'&#13;elseif var=1&#13;dc.b 'var is 1'&#13;else&#13;dc.b 'var is something else'&#13;endc</pre>
```case``` and ```endcase``` |  | Alternative to ```if```. Seems broken in ASM68K. | <pre lang="asm">case var&#13;=0&#13;dc.b 'var is 0'&#13;=1&#13;dc.b 'var is 1'&#13;=?&#13;dc.b 'var is something else'&#13;endcase</pre>
|| ```switch```, ```case```, ```elsecase``` and ```endcase``` | Alternative to ```if```. | <pre lang="asm">switch var&#13;case 0&#13;dc.b 'var is 0'&#13;case 1&#13;dc.b 'var is 1'&#13;elsecase&#13;dc.b 'var is something else'&#13;endcase</pre>
```rept x``` and ```endr``` | ```rept x``` and ```endm``` | Assembles code ```x``` number of times. Useful in combination with ```narg``` in macros. | <pre lang="asm">rept 3&#13;dc.b 0 ; same as dc.b 0,0,0&#13;endr</pre>
```while``` and ```endw``` | ```while``` and ```endm``` | Assembles code repeatedly while condition is met.<br>```equs``` does not work inside a ```while``` loop. | <pre lang="asm">var: = 0&#13;while var<3&#13;dc.b var ; same as dc.b 0,1,2&#13;var: = var+1&#13;endw</pre>
```do``` and ```until``` |  | Assembles code repeatedly until condition is met. | <pre lang="asm">var: = 0&#13;do&#13;dc.b var ; same as dc.b 0,1,2&#13;var: = var+1&#13;until var>2</pre>
```end``` |  | Stops assembly. Place at the end of the main ASM file. | 
|| ```ifdef x``` | True if ```x``` is defined. | 
|| ```ifndef x``` | True if ```x``` is undefined. | 
|| ```ifused x``` | True if ```x``` is referenced in the code (so far). | 
|| ```ifnused x``` | True if ```x``` is unreferenced in the code (so far). | 
|| ```ifexist x``` | True if file ```x``` exists. | 
|| ```ifnexist x``` | True if file ```x``` doesn't exist. | 
|| ```ifb x,..,y``` | True if listed parameters are all blank. | 
|| ```ifnb x,..,y``` | True if listed parameters aren't all blank. | 

# Macros
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```macro``` and ```endm``` |  | Defines a macro. Parameters can be named or left blank (in which case they are referenced by ```\1```, ```\2``` etc.| <pre lang="asm">writebyte: macro num&#13;dc.b \num&#13;endm&#13;writebyte 5 ; same as dc.b 5</pre>
```macros``` |  | Single-line macro. ```endm``` not needed. | <pre lang="asm">writebyte: macros&#13;dc.b \1</pre>
```mexit``` | ```exitm``` | Exits macro prematurely. | 
```\0``` | ```ATTRIBUTE``` | Size parameter. Whatever is after the ```.``` when a macro is used, usually ```b```, ```w``` or ```l``` but can be anything. | <pre lang="asm">writeany: macro&#13;dc.\0 \1&#13;endm&#13;writeany.l 999 ; same as dc.l 999</pre>
```\@``` |  | Underscore followed by the number of times the macro has been used. Useful for creating unique labels. | <pre lang="asm">setvalue: macro&#13;value\\@: equ \1&#13;endm&#13;setvalue 5 ; same as value_1: equ 5</pre>
```\#``` and ```\$``` |  | Value of variable output as a string. ```\#``` is decimal and ```\$``` is hex. | <pre lang="asm">value: equ 5&#13;string: equs "\\#value" ; same as string: equs "5"</pre>
```\_``` | ```ALLARGS``` | All parameters, including the commas. | 
```\*``` |  | Value of label where macro was used. ```*``` must be the first parameter, and ```\*``` must be defined. Label must be on the same line. | <pre lang="asm">readself: macro *&#13;&#92;\*: equ *&#13;self: equ &#92;\*&#13;endm&#13;readself ; same as self: equ *</pre>
|| ```{INTLABEL}``` and ```__LABEL__``` | Value of label where macro was used. ```{INTLABEL}``` must be the last parameter. Label must be on the same line. | <pre lang="asm">readself: macro {INTLABEL}&#13;self: equ \_\_LABEL\_\_&#13;endm&#13;readself ; same as self: equ *</pre>
```narg``` | ```ARGCOUNT``` | Number of parameters used in a macro. AS will include all named parameters, even if they aren't used. | <pre lang="asm">nargout: macro&#13;dc.b narg&#13;endm&#13;nargout 1,2,3,4 ; same as dc.b 4</pre>
```shift``` | ```shift``` | Deletes the first parameter and moves the rest left. Useful in combination with ```narg```. | 
```pushp``` and ```popp``` |  |  | 
```purge``` |  | Deletes a macro. | <pre lang="asm">purge macroname</pre>
|| ```!``` | AS allows redefining any instruction with macros. ```!``` uses the original unaltered instruction. | <pre lang="asm">move macro&#13;endm&#13;!move.b d0,d1</pre>
|| ```function``` | Custom function. Last parameter is the expression, all parameters before that are the function's parameters. | <pre lang="asm">times2: function x,x+x&#13;dc.b times2(4) ; same as dc.b 8</pre>

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
```strlen(x)``` | ```strlen(x)``` | Returns length of string ```x```. | <pre lang="asm">if strlen("text")=4 ; true&#13;label: equs "four"&#13;if strlen(label)=4 ; true</pre>
```strcmp(x,y)``` |  | Returns _true_ if string ```x``` matches string ```y```. Variables require quotes and backslash. | <pre lang="asm">label: equs "text"&#13;if strcmp("\label","text") ; true</pre>
```instr(x,y)``` and ```instr(s,x,y)``` |  | Returns position of substring ```y``` in string ```x``` (starts at 1). Returns 0 if not found. ```s``` specifies starting position. | <pre lang="asm">label: equs "string"&#13;dc.b instr("\label","r") ; 3&#13;dc.b instr(2,"\label","r") ; 3&#13;dc.b instr(4,"\label","r") ; 0</pre>
```substr``` |  | Assigns substring to variable. Actually more like a macro than a function. Its parameters are start, end (both optional) and string. | <pre lang="asm">label: equs "string"&#13;sub1: substr 2,5,"\label" ; sub1 = trin&#13;sub2: substr 2,,"\label" ; sub2 = tring&#13;sub3: substr ,5,"\label" ; sub3 = strin&#13;sub4: substr ,,"\label" ; sub4 = string</pre>
|| ```bitcnt(x)``` | Returns count of bits set in ```x```. | 
|| ```firstbit(x)``` | Returns number of the lowest bit set in ```x```, or -1 if not found. | 
|| ```lastbit(x)``` | Returns number of the highest bit set in ```x```, or -1 if not found. | 
|| ```bitpos(x)``` | Returns number of the (only) bit set in ```x```, or fails assembly if not found. | 
|| ```sgn(x)``` | Returns 0, 1 or -1 if ```x``` is 0, positive or negative respectively. | 
|| ```abs(x)``` | Returns absolute value of ```x```. | 
|| ```upstring(x)``` | Returns uppercase equivalent of ```x```. ```upstring("x")``` returns ```X```. | 
|| ```lowstring(x)``` | Returns lowercase equivalent of ```x```. | 
|| ```substr(x,y,z)``` | Returns substring of ```x```, starting at position ```y``` and of length ```z```. | 
|| ```charfromstr(x,y)``` | Returns the character in ```x``` at position ```y```. | 
|| ```strstr(x,y)``` | Returns position of substring ```y``` in string ```x``` (starts at 0). Returns -1 if not found. Similar to ASM68K's ```instr()```. | 
|| ```val(x)``` | Converts ```x``` from string to expression. | <pre lang="asm">dc.b val("1+1") ; same as dc.b 2</pre>
|| ```exprtype(x)``` | Returns type of ```x``` &mdash; 0 for integer, 1 for floating (not relevant here) and 2 for string. Similar to ASM68K's ```type()```. | 

# Options
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```opt``` |  | Sets an option. ```+``` enables and option and ```-``` disables it. | 
```opt ae+``` |  | Enable automatic even before ```dc.w```, ```dc.l``` and similar. | 
```opt an+``` |  | Enable alternative numbers. i.e. those in Z80 assembly (100h, 0Ah). | 
```opt c+``` |  | Enable case sensitivity. ```c-``` is default. | 
```opt d+``` |  | Descope local labels? ```d-``` is default. | 
```opt e+``` |  | Error text in command line output. | 
```opt lx``` |  | Set local label prefix to ```x```. ```@``` is default. | 
```opt w+``` |  | Warning text in command line output. | 
```opt ws+``` |  | Allow whitespace in instructions (e.g. ```1 + 2```). ```ws-``` is default. | 
```opt op+``` |  | Optimise ```(pc)``` relative from long addressing if possible. ```op-``` is default. | 
```opt os+``` |  | Optimise branches if possible. ```os-``` is default. | 
```opt ow+``` |  | Optimise longwords to words if possible. ```ow-``` is default. | 
```opt oz+``` |  | Optimise ```0(a0)``` to ```(a0)```. ```oz-``` is default. | 
```opt oaq+```, ```osq+``` and ```omq+``` |  | Optimise ```add```, ```sub``` and ```move``` to ```addq```, ```subq``` and ```moveq``` if possible. ```oaq-```, ```osq-``` and ```omq-``` are default. | 
```pusho``` and ```popo``` |  | Save and restore options, allowing for temporary options to be used. | 
```nolist``` and ```list``` |  | Disable and enable list generation temporarily. | 
```inform x,y,z``` |  | Outputs the error message ```y``` to command line. ```x``` can be 0 (show text only), 1 (text as warning), 2 (text as error) or 3 (stops assembly). ```y``` can contain ```%d``` (decimal), ```%h``` (hex) or ```%s``` (string) which are substituted by expression ```z```. | if value>max&#13;inform 3,"Value $%h is too high.",value&#13;endc
|| ```message x``` and ```warning x``` | Outputs the message ```x``` to command line. | 
|| ```error x``` and ```fatal x``` | Outputs the message ```x``` to command line and stops assembly. | 
```fail``` |  | Stops assembly. |
