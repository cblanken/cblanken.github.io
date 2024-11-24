+++
title = "OverTheWire - Leviathan"
date = "2024-11-24"
description = "Walkthrough for the Leviathan cyber wargame."

[extra]
show_only_description = true
toc = true

[taxonomies]
tags = ["ctf", "infosec", "wargame", "OverTheWire", "Linux"]
+++

## Introduction

[Leviathan](https://overthewire.org/wargames/leviathan/) is another
[wargame](https://en.wikipedia.org/wiki/Wargame_(hacking)) available at
[overthewire.org](https://overthewire.org/).

In this walkthrough I'm going to show the process for solving each challenge
while also providing some insight into what each challenge is trying to teach
and why it's useful. Keep an eye out for callouts like those below.

{% callout(type="tip") %}

Access to each level is made over SSH. The username will correspond to the
index of the level starting at 0. Always make sure you're logging into the
correct level!

If you're new to CTFs and wargames you may want to check out the [Bandit
wargame walkthrough](@/blog/OverTheWireBandit.md) first.

{% end %}

None of the Leviathan levels have any additional descriptions. Presumably,
since they're relatively simple, any hints would probably give away the
solution. Instead you're dropped into a regular user session after login with
the task of identifying any important files available to that user and using
them to find the password to the next level.

## Walkthrough

### Level 0

To start, there only appears to be a few hidden dotfiles in the home directory.
![level 0 ls](/images/otw-leviathan/0_ls.png)

The only atypical thing here is the `~/.backup` directory which contains a
`bookmarks.html` file.

#### Solution

It's certainly possible to manually read through the bookmarks for anything
interesting, but `grep` can be utilized to search for some keywords which might
be useful. For example, "password" or "flag".

![level 0 flag](/images/otw-leviathan/0_flag.png)

{% callout(type="tip") %}

This example is a bit contrived for the sake of the wargame, but this is a
common tactic used by attackers. Searching a user's files with `grep` for
things like usernames, passwords, bank or credit card details, and any other
kind of sensitive information is one of the first things an attacker might do
once they've compromised a user's account.

It should go without saying that you should never store any passwords or other
sensitive information in plain text files if it can be avoided. Ideally use a
password manager with secure encryption to store any digital account details.

{% end %}

### Level 1

![level 1 ls](/images/otw-leviathan/1_ls.png)

This level presents us with a [setuid](https://en.wikipedia.org/wiki/Setuid)
binary. On some systems this is evident by the red background color, but to
verify, you just need to check the file permissions with `ls -l`. There will be
an `s` in place of the typical `x` for the user level execution permissions.

![level 1 setuid](/images/otw-leviathan/1_setuid.png)

![level 1 run check](/images/otw-leviathan/1_run_check.png)

After running it with an input of `abc`, it's clear from the output that the
input is being checked against some password string for correctness.

We can do some preliminary analysis of the binary's execution with the
[`ltrace`](https://www.man7.org/linux/man-pages/man1/ltrace.1.html) program
which will record the C level dynamic library function calls made by `check`.
This includes common standard library functions like
[`strcmp`](https://www.man7.org/linux/man-pages/man3/strcmp.3.html).

#### Solution

![level 1 solve check](/images/otw-leviathan/1_solve_check.png)

From the output of the above `ltrace`, we can see the parameters passed to the
`strcmp` function call. The first is our test password, and the second is the
target password that `check` is using to verify the input.

{% callout(type="tip") %}

The `ltrace` program can be quite useful to perform some initial analysis of a
binary's execution before transitioning to more advanced tools like the `gdb`
debugger.

{% end %}

### Level 2

Here's another `setuid` binary.

![level 2 ls](/images/otw-leviathan/2_ls.png)

Executing prints from usage information to the console.

![level 2 run printfile](/images/otw-leviathan/2_run_printfile.png)

Following the usage description, a file path may be passed as an argument and
be printed to the console.

![level 2 run printfile](/images/otw-leviathan/2_try_print_password.png)

Unfortunately, printing the `level3` password file isn't allowed.

To investigate, let's use the `ltrace` command again.

![level 2 run printfile](/images/otw-leviathan/2_ltrace.png)

The `ltrace` output shows calls to
[`access`](https://man7.org/linux/man-pages/man3/access.3p.html),
[`snprintf`](https://www.man7.org/linux/man-pages/man3/snprintf.3p.html), and
[`system`](https://man7.org/linux/man-pages/man3/system.3.html).

![level 2 run printfile](/images/otw-leviathan/2_try_print_bashrc.png)

Attempting to read another file for which we have read permissions, e.g.
`.bashrc`, shows that `printfile` executes the shell command `/bin/cat
<FILEPATH>`  when the `access` function call succeeds where `<FILEPATH>` is
whatever you pass as the first argument to `printfile`.

{% callout(type="note") %}

The above output was truncated since the `.bashrc` file is actually quite long.
In fact, you might want to redirect stdout to `/dev/null` to only display the
results from `ltrace`. Just to make it a bit more readable.

![level 2 ltrace /dev/null output](/images/otw-leviathan/2_ltrace_dev_null.png)

{% end %}

#### The setuid bit

We saw this in the previous level as well, but I wanted to call attention to
the fact that `printfile` has the [`setuid`
bit](https://en.wikipedia.org/wiki/Setuid) set.

We can even see the function call to `setreuid` which will set the real and
effective user ID for the process. This level and the last are showcasing the
dangers of giving any file setuid permissions. Without it `printfile` wouldn't
be able to access the password file for level 3.

Our goal is to craft some input to `printfile` such that the `system` call will
execute for the password file (`/etc/leviathan_pass/leviathan3`). But, to reach
that point, the path passed in must also be readable by the `leviathan2` user.

With that in mind, one method we can use to trick `printfile` is by providing a
path with a variable. This way the variable will be expanded when the path is
passed to the `system` function, but will be interpreted literally by the call
to `access`.

{% callout(type="tip") %}

When attacking the input of a program, always try to identify different points
of entry. In this case there are two points at which the input path is passed
into the program. The first is to the call to `access` and the second to
`system`. Both of these functions process input differently. When you have
multiple points like this without some sort of input validation or
normalization, there's always an opportunity to disrupt the expected behavior
of the program.

On the flip-side of this, as a developer this showcases why input validation is
such an important concept. Never implicitly trust user input.

More specifically for this level, `system` should never be used in a setuid
program and user input should never be passed directly to a call to `system`
without sanitization. The [`system` manual
page](https://www.man7.org/linux/man-pages/man3/system.3.html) explicitly warns
against this practice due to the potential for compromising system security.

![system manual caveat](/images/otw-leviathan/2_system_manual_caveat.png)

{% end %}

#### Solution

![level 2 solution](/images/otw-leviathan/2_solution.png)

### Level 3

This challenge is almost identical to [level 1](#level-1), except that you are
dropped into a shell where you can easily `cat` the password file for level 4.

The intended solution for level 1 probably differs slightly from the one
provided here since having near duplicate solutions usually isn't done by
wargame and CTF authors.

Either way, this one is left as a challenge to the reader. Have fun!

### Level 4

In this level, we have some more hidden files. In the `.trash` directory is
another setuid binary.

![level 4 binary](/images/otw-leviathan/4_binary.png)

![level 4 ltrace](/images/otw-leviathan/4_ltrace.png)

#### Solution

All the leviathan challenges are possible without any programming, but in this
case, you could also solve it with a short python script.

```python
print(
   "".join(
      [chr(int(x, 2)) for x in "00110000 01100100 01111001 01111000 01010100 00110111 01000110 00110100 01010001 01000100 00001010".split()]
   )
)
```

This script splits the text into a list of binary numbers and converts each
binary number into decimal with `int(x, 2)`. Each integer is then translated
into the corresponding ASCII letter with the `chr` function before joining each
letter back into a single string.

Of course it's also possible to translate it by hand with an [ASCII
table](https://www.rapidtables.com/code/text/ascii-table.html) though you'll
need to convert the binary numbers into either hexadecimal or decimal. Another
option is to use one of the many online translators, for example [Binary to
Text
Translator](https://www.rapidtables.com/convert/number/binary-to-ascii.html).

### Level 5

![level 5 first run](/images/otw-leviathan/5_first_run.png)

It looks like the binary expects a file at `/tmp/file.log`. Let's make that
file with some dummy content and `ltrace` the binary to see what's going on.

![level 5 first run](/images/otw-leviathan/5_ltrace_with_log_file.png)

We have two important things going on in this binary.

1. The contents of the `/tmp/file.log` are being printed.
2. `/tmp/file.log` is deleted. See the [`unlink` manual
   page](https://www.man7.org/linux/man-pages/man2/unlink.2.html) for details.

The deletion of the file is less of a concern since we can remake the file as
needed. More importantly, we have control of the file being printed.

#### Solution

Obviously we don't care too much about the content of `/tmp/file.log`. But,
what if, we could link `/tmp/file.log` to a more interesting file. Like
`/etc/leviathan_pass/leviathan6` for example.

![level 5 solution](/images/otw-leviathan/5_solution.png)

Here we've simply made a symbolic link to `/etc/leviathan_pass/leviathan6` and
placed the link file at `/tmp/file.log`. When `leviathan5` is executed, the
link is followed to the password file for the next level which is printed to
stdout.

### Level 6

Hopefully you've gotten the hang of the first few steps now.

1. Check for interesting files in the home directory
2. Execute the binary and provide any required input
3. Run the binary via `ltrace` to see what function calls it's making

![level 6 binary identification and
ltrace](/images/otw-leviathan/6_first_run_ltrace.png)

Blast it, this time they've managed to foil our use of `ltrace`. There's
clearly some sort of check going on to verify the input code. We can probably
assume the check is happening in a simple `if` statement comparing the input
code to some hardcoded number. Which would explain why it doesn't show up in
`ltrace` since no library function call is being made.

At this point we're reaching beyond the capabilities of most basic Linux
commands. Instead you may have to reach for something like
[`gdb`](https://www.man7.org/linux/man-pages/man1/gdb.1.html), the GNU
Debugger. The debugger will allow you to step through the program one
instruction at a time. Unfortunately it does require you understand a bit about
C and [Assembly language](https://en.wikipedia.org/wiki/Assembly_language).

![level 6 gdb assembly 1](/images/otw-leviathan/6_gdb_asm1.png)

In the disassembly of `leviathan6` above there are a few important lines.

1. At line `main+76` we can see the function call to `atoi` which you should
   recognize from the preliminary analysis with `ltrace`. Another important
concept to understand is that for x86 binaries like this one, the return
results of functions are usually assigned to the `eax` register.
2. A `cmp` instruction which is comparing the returned value of the `atoi`
   function call in `eax` to another value on the stack at `[ebp-0xc]`.

Use the `run` command to start the program. The program will begin executing
and stop at the first breakpoint it finds.

After the program stops, we can examine the arguments of the `cmp` instruction.
The command `info registers` will display the values for all the registers, or
the target register can be passed as an argument as shown below.

The format for the register listing is `register name | hexadecimal | decimal`.
You can see the decimal representation matches the PIN provided to the program
`1234`.

The other argument to `cmp` is a bit trickier. It's actually using a pointer.
Meaning the value at `ebp - 0xc` is actually a memory address and it must be
dereferenced to examine the value. The `x` command can be used to examine
memory like this. You can read more in the [Examining Memory
section](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Memory.html) of
the [GDB
documentation](https://sourceware.org/gdb/current/onlinedocs/gdb.html/index.html),
but, in summary, the command used here (`x/1d $ebp-0xc`) examines the memory at
the address computed by subtracting `0xc` from the value in the `ebp` register.
One integer value at the resolved memory address is then printed to the console
thanks to the display format of `1d`.

![level 6 gdb breakpoint pin](/images/otw-leviathan/6_gdb_breakpoint_pin.png)

## Conclusion

You can do quite a lot with just some basic terminal commands.

Supposedly all the Leviathan levels are possible without any programming
knowledge, so I suspect [level 6](#level-6) has a simpler intended solution.

Either way, know your tools. I think `ltrace` in particular is useful for
understanding the actions of a binary without needing to resort to tools for
debugging and reverse engineering like gdb or Ghidra.

{% callout(type="warning") %}

Don't forget to clean up any files you've created.

`rm -rf /tmp/MY_TEMP_DIRECTORY`

{% end %}
