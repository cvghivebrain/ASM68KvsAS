ASM68K and AS. What's the difference?

# Directives
ASM68K | AS | Purpose | Example
--- | --- | --- | ---
```equ``` | ```equ``` or ```constant``` | Define a value once. Forward references allowed. | <pre lang="asm">label: equ 1+2+3</pre><pre lang="asm">constant label,1+2+3 ; AS only</pre>
```set``` or ```=``` | ```set``` or ```=``` | Define a reusable value. Forward references not allowed. | <pre lang="asm">label: = 1+2+3</pre>
```equs``` | | Define a reusable string. Forward references not allowed.<br>Single or double quotes work. To include quotes in the string, use ```''``` where the quote should be. | <pre lang="asm">label: equs "string"</pre><pre lang="asm">label: equs 'string''s got a quote'</pre>
```equr``` | ```reg``` | Define label to represent a register. | <pre lang="asm">label: equr d0</pre>
```reg``` | | Define label to represent a series of registers for use with ```movem```. | <pre lang="asm">label: reg d0-d7/a1-a2</pre>
```rsset``` | ```phase``` and ```dephase``` | Sets the value of ```__rs```. | <pre lang="asm">rsset $FF0000</pre>
```rs``` | | Assigns the value of ```__rs``` to a label, and advances ```__rs``` by the specified number of bytes, words or longwords. | <pre lang="asm">label: rs.l $10 ; label = $FF0000; __rs = $FF0010</pre>
```rsreset``` | | Sets the value of ```__rs``` to 0. Used with a parameter, it behaves as ```rsset```. | <pre lang="asm">rsreset</pre>
