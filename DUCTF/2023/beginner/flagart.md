On initial inspection it would appear that we're missing some information like `message.txt` and `mask.txt`. However, we don't need to know these.

Focusing on these lines of code first:
```python

palette = '.=w-o^*'
template = list(open('./mask.txt', 'r').read())

canvas = ''
for c in message:
    for m in [2, 3, 5, 7]:
        while True:
            t = template.pop(0)
            if t == 'X':
                canvas += palette[c % m]
                break
            else:
                canvas += t

```
We can note that:
- Every character in our input message (which is a concat of message.txt and flag.txt) will result in FOUR symbols as defined in our `palette`
- The `palette` symbols are only printed out whenever our template file `mask.txt` eventually pops with an "X" character. 4 X's are needed until we progress to the next character in our concatenated message.

Based on this, we can generate our own cipher key / lookup table. 
- First I create a message file that contains more or less all the ASCII symbols that would interest us in a flag
	- `abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-=[]\;',./~!@#$%^&*()_+{}|:"<>?` (plus backtick)
- Then I define an "infinitemask", which is really just a super long string of X's. I didn't want to mess with the source code too much so that's why I opted for this method.
- Then I adapt the python script slightly to define our cipher key and reverse cipher keys.

```python
message = open('./charmap.txt', 'rb').read()

palette = '.=w-o^*'
template = list(open('./infinitemask.txt', 'r').read())

canvas = ''
cipherkey = {}
reversecipherkey = {}
for c in message:
    for m in [2, 3, 5, 7]:
        while True:
            t = template.pop(0)
            if t == 'X':
                canvas += palette[c % m]
                break
            else:
                canvas += t
    # print(chr(c)+":"+str(canvas))
    cipherkey[chr(c)]=canvas;
    reversecipherkey[canvas]=chr(c);
    canvas=''
print(canvas)
print(reversecipherkey)
```

With some knowledge of the flag structure (i.e. it always starts with `DUCTF{`), using this resulting cipher key I search for the string of palette characters that would map to that string from the `output.txt` file. Then after copying the output file,
- Delete all preceding characters (this would've been the `message.txt`),
- Delete all whitespace and newline characters
- Save this copy as "suspect cipher.txt"

Again with the knowledge that every 4 symbols corresponds to an ASCII character, I use the reversecipherkey dictionary generated prior against this suspect cipher to reconstruct the flag.
```python

cipher = list(open('./suspect cipher.txt', 'rb').read())
chunk=''
decodedmsg=''
chunkctr=0
for c in cipher:
    if chr(c)==' ':
        continue
    else:
        chunkctr+=1
        chunk+=chr(c)
        if chunkctr == 4:
            decodedmsg+=reversecipherkey[chunk]
            chunk=''
            chunkctr=0
    # decodedmsg+=reversecipherkey[chunk];
    # chunk=''
print(decodedmsg)
```

Flag: `DUCTF{r3c0nstruct10n_0f_fl4g_fr0m_fl4g_4r7_by_l00kup_t4bl3_0r_ch1n3s3_r3m41nd3r1ng?}`

Note: no idea what chinese remaindering is.