On loading the webpage we're served a javascript file (accessible via devtools) with this source code:

```js
prompt = function (fun, x) {
    let answer = fun(x);
    if (!/^[a-z]{13}$/.exec(answer)) return "";
    let a = [], b = [], c = [], d = [], e = [], f = [], g = [], h = [], i = [], j = [], k = [], l = [], m = [];
    let n = "blue";
    a.push(a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, a, b, a, a, a, a, a, a, a, a);
    b.push(b, b, b, b, c, b, a, a, b, b, a, b, a, b, a, a, b, a, b, a, a, b, a, b, a, b);
    c.push(a, d, b, c, a, a, a, c, b, b, b, a, b, c, a, b, b, a, c, c, b, a, b, a, c, c);
    d.push(c, d, c, c, e, d, d, c, c, c, c, b, c, c, d, c, b, d, a, d, c, c, c, a, d, c);
    e.push(a, e, f, c, d, e, a, e, c, d, c, c, c, d, a, e, b, b, a, d, c, e, b, b, a, a);
    f.push(f, d, g, e, d, e, d, c, b, f, f, f, a, f, e, f, f, d, a, b, b, b, f, f, a, f);
    g.push(h, a, c, c, g, c, b, a, g, e, e, c, g, e, g, g, b, d, b, b, c, c, d, e, b, f);
    h.push(c, d, a, e, c, b, f, c, a, e, a, b, a, g, e, i, g, e, g, h, d, b, a, e, c, b);
    i.push(h, a, d, b, d, c, d, b, f, a, b, b, i, d, g, a, a, a, h, i, j, c, e, f, d, d);
    j.push(b, f, c, f, i, c, b, b, c, j, i, e, e, j, g, j, c, k, c, i, h, g, g, g, a, d);
    k.push(i, k, c, h, h, j, c, e, a, f, f, h, e, g, c, l, c, a, e, f, d, c, f, f, a, h);
    l.push(j, k, j, a, a, i, i, c, d, c, a, m, a, g, f, j, j, k, d, g, l, f, i, b, f, l);
    m.push(c, c, e, g, n, a, g, k, m, a, h, h, l, d, d, g, b, h, d, h, e, l, k, h, k, f);
    walk = a;
    for (let c of answer) {
      walk = walk[c.charCodeAt() - 97];
    }
    if (walk != "blue") return "";
    return {toString: () => _ = window._ ? answer : "blue"};
  }.bind(null, prompt);
  eval(document.getElementById('challenge').innerText);
```

Furthermore, inspecting the document markup source gives us this script in the <head/> block:
```js
    function cross() {
      prompt("What is your name?");
      prompt("What is your quest?");
      answer = prompt("What is your favourite colour?");
      if (answer == "blue") {
        document.getElementById('word').innerText = "flag is DUCTF{" + answer + "}";
        cross = escape;
      }
      else {
        document.getElementById('word').innerText = "you have been cast into the gorge";
        cross = unescape;
      }
    }
  
```
From this we can see that the only prompt that matters to us is the favourite colour prompt.
Let's take a look at some important lines from the javascript source file:
- `if (!/^[a-z]{13}$/.exec(answer)) return "";` = this is a regex selecting only lowercase letters, and exactly 13 of them. So the answer we need must be all lowercase, 13 characters long
- From the numerous arrays, we can see that they're all 26 entries long, and the very last one has the element/var "n" (which holds the value "blue", which is what we want to obtain)
- From this loop, we can deduce that the code takes each character of our answer, and retrieve the reference according to its zero-based character code index
```js
    for (let c of answer) {
      walk = walk[c.charCodeAt() - 97];
    }
```

In other words, we just need to find out which entries in each of the lettered arrays refers to the next array letter of the alphabet, and get its zero-based index.

The way I did this was utilising VSCode's symbol highlighting and working backwards. Before messing around with this i created a copy of this source code to work with.
By clicking the `m` symbol in `m.push()`, VSCode highlighted the same `m` symbol in the line above it (`l.push`). Whenever it did this I would replace the highlighted symbol with a string like "x" to mark its position, and I would proceed to mark everything backwards until I have a full trace to the `n` symbol in the `m[]` array. I would also mark the `n` symbol similarly in the `m[]` array.

I then pasted these modified variable definitions in the chrome devtools consoles, and from there I would utilise the `.IndexOf()` method on each of these variables, and I would get a number for each of my modified arrays.
![](attachments/Pasted%20image%2020230903143231.png)
I'd then offset this back by the value of 97, and then use this as a character code to get the actual character needed for my prompt
![](attachments/Pasted%20image%2020230903143326.png)

Here I forgot to properly mark the `m` array, but from the resulting phrase I deduced that the desired prompt is `rebeccapurple`.
Funnily enough the prompt answer is the flag itself.

Flag: `DUCTF{rebeccapurple}`