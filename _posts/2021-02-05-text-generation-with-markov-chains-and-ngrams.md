---
layout: post
title:  "Text generation with Markov Chains and NGrams"
tags:   markov chain text generation ngram
---

Hello, in this tutorial I will demonstrate how to generate text from text using
Markov Chains and NGrams.

<!--more-->

If you does not know what a Markov Chain or a NGram is, I will demonstrate. At
the end of this tutorial we will implement this text generation tool in Python
without using any library, from the absolute stratch.

# What is a Markov Chain?

Basically, a Markov Chain is something that will take a sequence of "things", 
these "things" can be anything that can anything that we can represent with
numbers. And, in computers, everything are numbers.

It takes this sequence of numbers and makes some computations to make a new
sequence of numbers, that is random, but still similiar to the original
sequence. I will show a example now.

We have a sequence of As, Bs and Cs: `ABCAABAACACBB`

First, think the chain as being the following:

![Markov Chain Eskeleton](/assets/markov-chain-eskeleton.png)

We need to calculate the number of times that after A comes B, after A comes A...

Lets make a table:

```
    A   B   C
A   0   0   0
B   0   0   0
C   0   0   0
```

The sequence is `ABCAABAACACBB`, at the start of it, we cal see that after `A`
there is a `B`. We can increment `A->B`, now the table is:

```
    A   B   C
A   0   1   0
B   0   0   0
C   0   0   0
```

And now `B->C`, `C->A`... The resulting table is the following:

```
    A   B   C
A   2   2   2
B   1   1   1
C   2   1   0
```

Now we need to calculate the number of `A->*`, `B->*` and `C->*`, by suming
the rows:

```
    A   B   C   *
A   2   2   2   6
B   1   1   1   3
C   2   1   0   4
```

Now, we will take every item and divide it by the sum:

```
    A   B   C
A   2/6 2/6 2/6
B   1/3 1/3 1/3
C   2/4 1/4 0/4
```

The resulting table is the following:

```
    A   B   C
A   0.3 0.3 0.3
B   0.3 0.3 0.3
C   0.5 0.2 0.0
```

I call it as `probabilites table` because it shows the probabilities of
`A->B`, `B->A`...

Now the last step: create a new sequence that looks similar to the
original one but it is not equal.

For doing it, we will first select a initial state, I will choose `A`

Now we need to choose the next state, how? Simple, take the row of the
last character:

```
A   0.3 0.3 0.3
```

Now we take do what I call `weighted random`, in the case of `A`, all
the possibilites have equals probabilities. Let's throw a dice, my magical
dice says that the next one is `B`.

Our sequence now is `AB`, now, lets make the same with `B`:

```
B   0.3 0.3 0.3
```

The random choose one is `C`

`ABC`

now with `C`, the things are a bit diferent:

```
C   0.5 0.2 0.0
```

There is more probability of getting `A` than getting `B`, and it is
impossible to get `C` again.

After keeping doing this for a while, you will have a sequence that
looks like the original one, but a bit different, like differents
executions of a generative art. If you can not perceive that it looks
like the original one, this is normal, humans usually can not find
patterns in this type of data, but there is something where we are
very good at: Text.

Text is our goal, generate text from text. We will use Markov Chains for
doing it, but not a normal Markov Chain, normals Markov Chains does not
works very well for creating text, but there is a alternative: Markov
Chains + NGrams.

# What is a ngram?

NGrams are ways to divide text in parts, there are different types of
N-Grams: Bigrams, Trigrams...

It may be confusing to undertand, let's look at a example:

Divide `Hello` in N-Grams of 3, or, Trigrams:

```
Hel
ell
llo
```

Simple, is not it?

Bigrams:

```
He
el
ll
lo
```

# Combining the two

How can we combine NGrams and Markov Chains? It is very simple. Each ngram
has a character after it:

```
He -> l
el -> l
ll -> o
```

So, we can make the table:

```
      'l' 'o'
'He'   1   0
'el'   1   0
'll'   0   1
```

Does it looks simpler now? Well, it is not something so difficult. Let's create
a new sequence from the ngram `He`.

The last bigram of `He` is `He`, so, weighted random in the row `'He'`, it is
`'l'`, so, now our sequence is `Hel`. The last ngram of `Hel` is `el`, check
the table, now the sequence is `Hell`, last ngram: `ll`, `Hello`, last ngram is
`lo`, but, and now? We never had `lo` before, how to continue making text?

My way to contornate it, is now we search for `*o` instead of `lo`, any ngram
that ends with `o`. But we still does not have any gram. In this case, take a
random ngram from the table, my dice says that it is `el`, add `el` to the
text, we have `Helloel`, after `el`, it comes `l`: `Helloell`.

That is it! We generated text, with a higher amount of text we can generate more
complex text. It will never be perfect, because Markov Chains are very weak
compared to RNNs and LSTMs, but sometimes it does the job, this is a example
of text generated with Markov Chain + NGrams:

```
Data can something.
Data can mean be fa.
Data can be sl, data can be fething, data can nothing, data is data can be sita, data, data can be slData.
Data can be sl nothing.
Data can nothing.
Data can something, data can be cg, data is data can be sl can nothing, data can something, data.
```

This was generated from:

```
This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow.
```

It is not perfect, but it is not absolute random, you can recognize words,
line breaks that does not break words... It is a bit smart, not 100% random.

If you want to know, it was generated using trigrams, ngrams of 3.

# Implementing it

Now, as I said, we will implement it. I choose Python because it is a simple
language, and so many people know it, so, let's use it.

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""
```

The first step is to separate this data into ngrams.

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]
```

And make a list called characters.

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]

characters = []
for i in range(len(data)):
    if (data[i] not in characters):
        characters.append(data[i])
```

Now we have a list of non-repeated characters that are on the data. Now we can
make the table.

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]

characters = []
for i in range(len(data)):
    if (data[i] not in characters):
        characters.append(data[i])

table = []
for i in range(len(ngrams)):
    table.append([])
    for j in range(len(characters)):
        table[i].append(0)
```

Now we can start counting and adding it to the table.


```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]

characters = []
for i in range(len(data)):
    if (data[i] not in characters):
        characters.append(data[i])

table = []
for i in range(len(ngrams)):
    table.append([])
    for j in range(len(characters)):
        table[i].append(0)

for i in range(len(ngrams)):
    table[ngrams.index(data[i : i + ngram_size])][characters.index(data[i + ngram_size])] += 1
```

Now, we need to take the sum of each row and divide each item for
the sum of it's row.

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]

characters = []
for i in range(len(data)):
    if (data[i] not in characters):
        characters.append(data[i])

table = []
for i in range(len(ngrams)):
    table.append([])
    for j in range(len(characters)):
        table[i].append(0)

for i in range(len(ngrams)):
    table[ngrams.index(data[i : i + ngram_size])][characters.index(data[i + ngram_size])] += 1

sum = []
for i in range(len(ngrams)):
    sum.append(0)
    for j in range(len(characters)):    
        sum[i] += table[i][j]
```

Now we need to divide the data for it's sum:

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]

characters = []
for i in range(len(data)):
    if (data[i] not in characters):
        characters.append(data[i])

table = []
for i in range(len(ngrams)):
    table.append([])
    for j in range(len(characters)):
        table[i].append(0)

for i in range(len(ngrams)):
    table[ngrams.index(data[i : i + ngram_size])][characters.index(data[i + ngram_size])] += 1

sum = []
for i in range(len(ngrams)):
    sum.append(0)
    for j in range(len(characters)):    
        sum[i] += table[i][j]

for i in range(len(ngrams)):
    for j in range(len(characters)):
        if (sum[i] > 0):
            table[i][j] /= sum[i]
```

Our table is done, now we can finally start generating a new
sequence using the variable `table`, but first, we will need to
create a function called `weighted_random`, to generate the weighted
random number that we need to create the sequence that, in this case,
is text.

```python
from random import random

def weighted_random(w):
    r = random()
    for i in range(len(w)):
        if (w[i] > 0 and r < w[i]):
            return i
    return len(w) - 1
```

Now we can add this function to our code

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]

characters = []
for i in range(len(data)):
    if (data[i] not in characters):
        characters.append(data[i])

table = []
for i in range(len(ngrams)):
    table.append([])
    for j in range(len(characters)):
        table[i].append(0)

for i in range(len(ngrams)):
    table[ngrams.index(data[i : i + ngram_size])][characters.index(data[i + ngram_size])] += 1

sum = []
for i in range(len(ngrams)):
    sum.append(0)
    for j in range(len(characters)):    
        sum[i] += table[i][j]

for i in range(len(ngrams)):
    for j in range(len(characters)):
        if (sum[i] > 0):
            table[i][j] /= sum[i]

from random import random

def weighted_random(w):
    r = random()
    for i in range(len(w)):
        if (w[i] > 0 and r < w[i]):
            return i
    return len(w) - 1
```

Before starting generating, we need something to start from,
in this case, I will use the string `Data`, it will be our
starting point.

```python
text = "Data"

def last_ngram():
    return text[len(text) - ngram_size : len(text)]
```

This function returns the last ngram of `text`, it will be useful,
because we need it to calculate the next character.

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]

characters = []
for i in range(len(data)):
    if (data[i] not in characters):
        characters.append(data[i])

table = []
for i in range(len(ngrams)):
    table.append([])
    for j in range(len(characters)):
        table[i].append(0)

for i in range(len(ngrams)):
    table[ngrams.index(data[i : i + ngram_size])][characters.index(data[i + ngram_size])] += 1

sum = []
for i in range(len(ngrams)):
    sum.append(0)
    for j in range(len(characters)):    
        sum[i] += table[i][j]

for i in range(len(ngrams)):
    for j in range(len(characters)):
        if (sum[i] > 0):
            table[i][j] /= sum[i]

from random import random

def weighted_random(w):
    r = random()
    for i in range(len(w)):
        if (w[i] > 0 and r < w[i]):
            return i
    return len(w) - 1

text = "Data"

def last_ngram():
    return text[len(text) - ngram_size : len(text)]

while last_ngram() in ngrams:
    text += characters[weighted_random(table[ngrams.index(last_ngram())])]
    print(text)
```

Now, we still have a problem, it can generate sequences like
`Data can be simple, data can w`, but it stops because there
had never been a ngram `n w`, that is a problem. As I said,
we can solve it by searching for ` w` and if we still did not
find, the `w`, and in the last case, where there is no `w`,
stop. We have the solution, let's implement it, it is not so
difficult, we can make a function for doing it. We can replace
`table[ngrams.index(last_ngram())]` for `getrow()`. Making it
simpler to handle.

```python
def getrow():
    if last_ngram() in ngrams:
        return table[ngrams.index(last_ngram())]
    else:
        for i in range(ngram_size):
            try:
                return table[
                    [j[i:ngram_size] == last_ngram()[i:ngram_size]for j in ngrams]
                    .index(True)
                ]
            except:
                pass
        return -1
```

After adding this to our code, that is our final result:

```python
data = """This is data, data is data.
Data can mean something, data can mean nothing.
Data can be simple, data can be complex.
Computations can be fast, computations can be slow."""

ngram_size = 3

ngrams = []
for i in range(len(data) - ngram_size):
    ngrams.append("")
    for j in range(ngram_size):
        ngrams[i] += data[i + j]

characters = []
for i in range(len(data)):
    if (data[i] not in characters):
        characters.append(data[i])

table = []
for i in range(len(ngrams)):
    table.append([])
    for j in range(len(characters)):
        table[i].append(0)

for i in range(len(ngrams)):
    table[ngrams.index(data[i : i + ngram_size])][characters.index(data[i + ngram_size])] += 1

sum = []
for i in range(len(ngrams)):
    sum.append(0)
    for j in range(len(characters)):    
        sum[i] += table[i][j]


for i in range(len(ngrams)):
    for j in range(len(characters)):
        if (sum[i] > 0):
            table[i][j] /= sum[i]


from random import random

def weighted_random(w):
    r = random()
    for i in range(len(w)):
        if (w[i] > 0 and r < w[i]):
            return i
    return len(w) - 1

text = "Data"

def last_ngram():
    return text[len(text) - ngram_size : len(text)]

def getrow():
    if last_ngram() in ngrams:
        return table[ngrams.index(last_ngram())]
    else:
        for i in range(ngram_size):
            try:
                return table[
                    [j[i:ngram_size] == last_ngram()[i:ngram_size]for j in ngrams]
                    .index(True)
                ]
            except:
                pass
        return -1


c = getrow()
for i in range(1000):
    if c == -1:
        break
    text += characters[weighted_random(c)]
    c = getrow()

print(text)
```

That is it! Thanks for reading. If you have seen an issue in the code
or a error in the text, please warn me in my eletronic mail:
`arthurbacci@protonmail.com`.
