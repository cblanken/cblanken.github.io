+++
title = "Cryptoverse 2022 - Super Guesser"
date = "2022-10-26"
updated = "2022-11-2"
description = "Writeup for the Super Guesser challenge from the Cryptoverse 2022 CTF"

[extra]
show_only_description = true
mathjax = true
toc = true

[taxonomies]
tags = ["ctf", "infosec", "python"]
+++

## Problem description
Super Guesser is an _easy_ rated reversing challenge for the Crytoverse 2022 CTF.
We are given a short challenge description:
> Only the true guessing _king_ can solve this challenge.

## Analysis
We are provided a link to download `guesser.pyc`, a compiled python binary.
Just to confirm there aren't any shenangins with the file extension, let's check the file type.
```
$ file guesser.pyc
guesser.pyc: Byte-compiled Python module for CPython 3.8, timestamp-based, .py timestamp: Sun Sep 11 18:00:05 2022 UTC, .py size: 682 bytes
```
The `file` utility tells us that we are, in fact, dealing with a compiled python binary. In this case CPython 3.8 is the version.

_Note_: it's important that we use the correct version of python when running the compiled `.pyc` file otherwise we might get a _magic number_ error as shown below and be unable to execute the program.
```
$ python guesser.pyc
RuntimeError: Bad magic number in .pyc file
```

If your system has an incompatible version of Python, I'd recommend installing a tool like [`pyenv`](https://github.com/pyenv/pyenv) to install and manage other python versions side by side on your system.

Now let's try running this program to see what we get as output.
```
$ python guesser.pyc
Guess: test
Invalid

$ python guesser.pyc
Guess: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Invalid
```

Okay it look's like the program is checking our input. Perhaps it is checking against the challenge's flag.

Referring to the challenge title we know that this is a reversing challenge, so the next logical step is to try decompiling the python binary.
One popular python decompiler is [decompyle3](https://pypi.org/project/decompyle3), which we can see from the PyPI page supports python versions 3.7+ which should work fine for the bytecode we have.

We can decompile `guesser.pyc` with the command:
```
$ decompyle3 guesser.pyc
```
This gets us the source code `guesser.py`
```python, linenos, hl_lines=18-20
# decompyle3 version 3.9.0
# Python bytecode version base 3.8.0 (3413)
# Decompiled from: Python 3.8.0 (default, Oct 29 2022, 20:02:52)
# [GCC 12.2.0]
# Embedded file name: /home/guesser.py
# Compiled at: 2022-09-11 14:00:05
# Size of source mod 2**32: 682 bytes
import hashlib, re
hashes = [
 'd.0.....f5...5.6.7.1.30.6c.d9..0',
 '1b.8.1.c........09.30.....64aa9.',
 'c.d.1.53..66.4.43bd.......59...8',
 '.d.d.076........eae.3.6.85.a2...']

def main():
    guesses = []
    for i in range(len(hashes)):
        guess = input('Guess: ')
        if len(guess) <= 4 or len(guess) >= 6 or re.match('^[a-z]+$', guess):
            exit('Invalid')
        if not re.match('^' + hashes[i].replace('.', '[0-9a-f]') + '$', hashlib.md5(guess.encode()).hexdigest()):
            exit('Invalid')
        else:
            guesses.append(guess)
    else:
        print(f"Flag: {guesses[0]}" + '{' + ''.join(guesses[1:]) + '}')


if __name__ == '__main__':
    main()
# okay decompiling guesser.pyc
```

At first glance, this seems to be a fairly simple python script, but let's break it down line by line.
First we have the declaration of a list of `hashes`
```python, linenos
hashes = [
 'd.0.....f5...5.6.7.1.30.6c.d9..0',
 '1b.8.1.c........09.30.....64aa9.',
 'c.d.1.53..66.4.43bd.......59...8',
 '.d.d.076........eae.3.6.85.a2...']
```
These will be important in a moment, but for now let's follow the logic to see what the _guesser_ is checking for.

We can see in the main function, we're iterating over the list of hashes, and for each hash, we take a string as user input and check if it's length is equal to 5 __AND__ if it matches the regex string `^[a-z]+$` which would be interpreted as a string of exclusively lowercase alphabetic letters with a length greater than or equal to one. If this condition is met, the program exits and outputs 'Invalid' just as we saw in initial test.
```python, linenos, linenostart=18
guess = input('Guess: ')
if len(guess) <= 4 or len(guess) >= 6 or re.match('^[a-z]+$', guess):
    exit('Invalid')
```

The second `if` statement is taking an md5 hash of our guess string and checking that it matches the hash from our hashes list. The hash converts each `.` (dot) character to `[0-9a-f]`. This is necessary to only match on valid md5 hashes, otherwise we exit just as before.
```python, linenos, linenostart=21
if not re.match('^' + hashes[i].replace('.', '[0-9a-f]') + '$', hashlib.md5(guess.encode()).hexdigest()):
    exit('Invalid')
```

And finally, if our guess passes both of those checks it is appended to the `guesses` list.
```python, linenos, linenostart=23
else:
    guesses.append(guess)
```

This `guesses` list is joined and printed as the flag output provided the program doesn't exit prematurely by receiving an incorrect guess.
```python, linenos, linenostart=25
else:
    print(f"Flag: {guesses[0]}" + '{' + ''.join(guesses[1:]) + '}')
```

One important part of the last line, is that the first element of `guesses` is used as the beginning of our flag which, according to the flag format should correspond to `cvctf`. With that in mind we should be able to confirm our understanding of the program by finding the md5 hash of `cvctf` which should match the first hash in `hashes`.

We can get the md5 hash by running `echo -n cvctf | md5sum` in our terminal. This gives us an md5 hash of `d70146aef5a8e5364791d3006ccd9c00`

Placing the hashes side by side we can see that they match.
```
d.0.....f5...5.6.7.1.30.6c.d9..0
d70146aef5a8e5364791d3006ccd9c00
```

We should be able to test this by entering `cvctf` as our first guess when running the challenge binary.
```
$ python guesser.pyc
Guess: cvctf
Guess: again
Invalid
```

We can see that the program only exited after the guess of "again", meaning the guess of "cvctf" was correct. That's great but you may have noticed this seems to contradict a bit of our analysis. Recall that the first `if` check appeared to exclude any guesses that were 5 characters in length or all lowercase and alphabetic. This exactly matches our guess of "cvctf"!

### Bad Decompilation?
As we can see, the behavior of the program when running the decompiled code does not match the behavior of the original `guesser.pyc`. Now I tried several decompilers, and as far as I can tell this is an error in the decompilation process. That line in particular should actually have decompiled to this:
```python
if not(len(guess) <= 4 or len(guess) >= 6 or re.match('^[a-z]+$', guess)):
    exit('Invalid')
```
Instead of this
```python
if len(guess) <= 4 or len(guess) >= 6 or re.match('^[a-z]+$', guess):
    exit('Invalid')
```

In this case, the program would exit if the guess is __NOT__ 5 alphabetic characters. This matches the behavior we've seen when running the compiled binary and leads us to different assumptions about the possible problem space. Restricting our guess to all lowercase alphabetic characters means we only have \$ 26^5 \$ possibilities. That comes out to a measly 11,881,376 possible guesses. We should be able to write a program to do that for us. Enter `brute.py`.

## Solution
```python, linenos
# brute.py
import hashlib
import re
from pwnlib.util.iters import bruteforce, mbruteforce

hash_patterns = [
 '^d.0.....f5...5.6.7.1.30.6c.d9..0$',
 '^1b.8.1.c........09.30.....64aa9.$',
 '^c.d.1.53..66.4.43bd.......59...8$',
 '^.d.d.076........eae.3.6.85.a2...$']
 
hash_regexes = [re.compile(x) for x in hash_patterns]

guess_len = 5
charset = "abcdefghijklmnopqrstuvwxyz"

for regex in hash_regexes:
    print(f"Searching for match for {regex}...")
    found = mbruteforce(lambda guess:
        regex.match(hashlib.md5(guess.encode()).hexdigest()),
        alphabet=charset,
        length=guess_len
    )
    if found is None:
        print("No matches found")
    else:
        print(f"FOUND: {found}, MD5: {hashlib.md5(found.encode()).hexdigest()}")
```
The program uses [pwntools](https://docs.pwntools.com/en/stable/util/iters.html?highlight=mbruteforce#pwnlib.util.iters.mbruteforce) to generate all the possible combinations given our set of lowercase alphabetic characters. It then generates the md5 hash using the [`hashlib`](https://docs.python.org/3/library/hashlib.html#module-hashlib) library and compares it against the hash from the `hashes` list.

```
$ python brute.py
Searching for match for re.compile('^d.0.....f5...5.6.7.1.30.6c.d9..0$')...
FOUND: cvctf, MD5: d70146aef5a8e5364791d3006ccd9c00
Searching for match for re.compile('^1b.8.1.c........09.30.....64aa9.$')...
FOUND: hashi, MD5: 1bd8d1fc2b9ad2bb0943056ecf64aa97
Searching for match for re.compile('^c.d.1.53..66.4.43bd.......59...8$')...
FOUND: snotg, MD5: cdd01c536d66a4943bd8bf6f5d59c0c8
Searching for match for re.compile('^.d.d.076........eae.3.6.85.a2...$')...
FOUND: uessy, MD5: 8d1d70762f431cd9eaec3967859a2b4b
```

# Flag
Et voilà, we have our flag! `cvctf{hashisnotguessy}`

