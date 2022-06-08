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
```dc.b```, ```dc.w``` and ```dc.l``` |  | Writes a series of bytes, words or longwords. The automatic even option (```\ae```) ensures words and longwords are aligned to word. | <pre lang="asm">dc.b 1,$20,-$30</pre>
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

# Files
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```include``` |  | Include an ASM file. | <pre lang="asm">include "file.asm"</pre>
```incbin``` | binclude | Include a binary file. | <pre lang="asm">incbin "file.bin"&#13;incbin "file.bin",$10 ; start at address $10 in file&#13;incbin "file.bin",$10,$20 ; start at address $10, include only $20 bytes</pre>

# Functions
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```ref(x)``` |  | Returns _true_ if label ```x``` has been previously encountered. | <pre lang="asm">if ref(label) ; false on first run, true on second&#13;label: rts&#13;endif</pre>
```def(x)``` |  | Returns _true_ if label ```x``` has been defined. | <pre lang="asm">label: equ 1&#13;if def(label) ; true</pre>
