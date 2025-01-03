+++
title = "OverTheWire - Bandit"
date = "2024-10-20"
description = "Learn Linux with OverTheWire Bandit."

[extra]
show_only_description = true
toc = true

[taxonomies]
tags = ["ctf", "infosec", "wargame", "OverTheWire", "Linux"]
+++

## Introduction

[Bandit](https://overthewire.org/wargames/bandit/) is just one of several
[wargames](https://en.wikipedia.org/wiki/Wargame_(hacking)) available at
[overthewire.org](https://overthewire.org/). It's aimed at beginners to Linux
and CTFs and provides an excellent introduction to the basics of the Linux
command line.

In this walkthrough I'm going to show the process for solving each challenge
while also providing some insight into what each challenge is trying to teach
and why it's useful, so keep an eye out for callouts like those below.

{% callout(type="note") %}
Access to each Bandit level is made over SSH. The username will correspond to
the index of the level starting at 0. Always make sure you're logging into the
correct Bandit level!
{% end %}

{% callout(type="warning") %} 
If you're looking for the solution of a particular level, you are highly
encouraged to attempt it on your own before following this walkthrough. You
will learn some useful things regardless, but attempting it on your own first
will help the knowledge stick better. I promise.
{% end %}

I have a few recommendations before getting started. Most of the OverTheWire
Bandit levels provide links to potentially useful manual pages. Unfortunately
the `man` pages can be somewhat cryptic, especially for beginners. For that
reason I recommend the following steps to try to get unstuck when slogging
through the `man` pages for any particular command.

1. Search for examples. Many `man` pages contain examples of their usage. These
   may be marked by all-caps `EXAMPLES` in some cases, but sometimes not.
2. If skimming through the man pages doesn't surface any apparent examples,
   then you may want to refer to [cheat.sh](https://cheat.sh). Cheat.sh is a
database of usage examples for thousands of command line programs. It can be
searched from the main page or by simply adding a `/` followed by the command.
For example [https://cheat.sh/ssh](https://cheat.sh/ssh) will return examples
of `ssh` usage. This one can be helpful when trying to understand how a command
is commonly used and which command flags are most useful.
3. If you're struggling to understand some of the examples then paste them into
   [https://explainshell.com](https://explainshell.com). Explainshell
provides a more readable breakdown of each command line flag and argument. This
tool can save you a lot of time flipping back and forth through the `man`
pages.

Please also note that for any terminal output, the command prompt will be
truncated to a single `$` for brevity. Just remember that you'll need to be
logged in to the appropriate level before executing any of the commands.

For example:

```terminal
$ cat hello.txt
Hello there!
```


## Log in

> The goal of this level is for you to log into the game using SSH. The host to
> which you need to connect is bandit.labs.overthewire.org, on port 2220. The
> username is bandit0 and the password is bandit0. Once logged in, go to the
> Level 1 page to find out how to beat Level 1.

[Bandit 0](https://overthewire.org/wargames/bandit/bandit0.html) provides an
introduction to SSH and provides a couple useful links for further research.

- [ssh manual](https://manpages.ubuntu.com/manpages/noble/man1/ssh.1.html)
- [SSH - Wikipedia](https://en.wikipedia.org/wiki/Secure_Shell)

Firstly, let's discuss what SSH is and how it's used day-to-day. SSH is a
networking protocol that allows secure communication between networked computer
systems. This is distinct from the `ssh` command line program linked above. The
program implements the protocol and allows regular users to utilize the SSH
protocol for secure communication. The security of SSH is based on [public-key
cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) which
you're welcome to read more about, but many of the details are out of scope for
this walkthrough.

The most important thing you need to know about SSH is that it can be used to
login to a computer system with a `username` and `password` just as if you were
physically present. Except in this case it's another system accessible over the
network. This is how it will be used over the course of the Bandit wargame as
well as some of the other wargames by OverTheWire.

{% callout(type="note") %}
It's worth mentioning that SSH is usually (and preferably) used with an SSH
key. More specifically a key-pair. A public key and a private key which are
both needed to take advantage of the aforementioned public-key cryptography. I
won't go into detail here, but don't worry there are a couple later levels that
do utilize SSH keys, so I'll discuss them in more detail when we reach them.
{% end %}

The prompt tells us that both the `username` and `password` are `bandit0`.

Just as important though, is the network location we've been provided,
`bandit.labs.overthewire.org`, which we'll use to connect to level 0. Actually,
`bandit.labs.overthewire.org` is what's known as a
[hostname](https://en.wikipedia.org/wiki/Hostname) or [domain
name](https://en.wikipedia.org/wiki/Domain_name). More specifically, it's a
[fully qualified domain name
(FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). You can
recognize an FQDN because it will include a [top-level domain
(TLD)](https://en.wikipedia.org/wiki/Top-level_domain) such as `.com`, `.net`,
or `.org`. This naming system for computers is called the [Domain Name System
(DNS)](https://en.wikipedia.org/wiki/Domain_Name_System). Luckily we don't need
to dig into the details here. Just know that one of these FQDNs will resolve to
an [IP address](https://en.wikipedia.org/wiki/IP_address) which can (usually)
be used to uniquely identify a computer system on the internet.

This unique name will tell the `ssh` program what system to connect to. The
`ssh` program expects the user credentials and hostname in the following format
`ssh://[user@]hostname[:port]`. This is explained in the first couple
paragraphs of the
[ssh&nbsp;man&nbsp;page](https://www.man7.org/linux/man-pages/man1/ssh.1.html).

> ssh connects and logs into the specified destination, which may be  specified
> as  either  `[user@]hostname`  or  a  URI  of  the  form
`ssh://[user@]hostname[:port]`.

The elements wrapped in brackets `[` and `]`
are actually optional. Linux `man` pages will commonly express the available
flags and arguments for a command in this way.

With that knowledge we can now connect to the first level with this command.

```bash
ssh ssh://bandit0@bandit.labs.overthewire.org:2220
```

You should be greeted by a banner for the Bandit wargame and a prompt
requesting entry of the `bandit0` password. Enter `bandit0` and you're ready to
get started.

{% callout(type="note") %}
It isn't necessary to solve any of the Bandit levels, but I highly recommend
researching DNS. It's intrinsic to how the internet operates and you'll likely
encounter it again and again if you pursue any area of IT, software, or
cybersecurity.

Resources:

- [What is DNS?](https://www.freecodecamp.org/news/what-is-dns-for-beginners/):
an excellent intro to the basics of DNS by freeCodeCamp
- [Implement DNS in a weekend](https://implement-dns.wizardzines.com): if you
have some programming experience, I highly recommend following this walkthrough
by Julia Evans

{% end %}

## Walkthrough

### Level 0

Now that we're logged in, it's time to solve the first level.

> The password for the next level is stored in a file called readme located in
> the home directory. Use this password to login to `bandit1` using SSH. Whenever
> you find a password for a level, use SSH (on port 2220) to log into that
> level and continue the game.

The prompt also provides links to the man pages for several commands.

With perhaps the exception of `du`. Each of these commands is fundamental for
any Linux user who wants to effectively use the command line.

|Command|Description|
|-------|-----------|
|[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html)|list the files in a directory|
|[cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html)|change your current directory|
|[cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html)|display the contents of a file|
|[file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html)|display information about the content of a file|
|[du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html)|display the disk space used by files|
|[find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)|search the file system for files with various parameters such as filename, file type, file size, etc.|

You are _highly_ encouraged to review each of the commands. At the very least
check out the `cheat.sh` page for each. Regardless these are all pretty common,
so you'll get plenty of experience with these commands in later levels.

#### Solution

For now, it should be apparent that one of the above commands should serve to
show us the contents of the `readme` file mentioned in the prompt. The humble
`cat` command.

It takes a filename as a parameter. So providing it with the filename `readme`
should print it's contents.

```terminal
$ cat readme
Congratulations on your first steps into the bandit game!!
Please make sure you have read the rules at https://overthewire.org/rules/
If you are following a course, workshop, walkthrough or other educational activity,
please inform the instructor about the rules as well and encourage them to
contribute to the OverTheWire community so we can keep these games free!

The password you are looking for is: [REDACTED PASSWORD]
```

I won't be providing any of the passwords throughout this walkthrough per the
OverTheWire [rules](https://overthewire.org/rules/). So get out your notepad
and copy those passwords. You'll need them to return to the last level you've
completed without completing each level again. Once you've done that, you're
ready to continue to Level 1.

### Level 1

> The password for the next level is stored in a file called `-` located in the
> home directory

The prompt also provides links to the man pages for the same commands as level 0.

Naturally, you might think to try the `cat` command again. Afterall, we're told
the password in in the file called `-`. Let's try it and see what happens.

```terminal
$ cat -
█
```

Strange, you're left with a blank line on the terminal now. And, if you type
some text and press the `Enter` key, the text is repeated back to the
terminal like so.

```terminal, hl_lines=3
$ cat -
hello there
hello there
```

The reason for this will become clear if you read the description from the
`cat` man page.

```hl_lines=7-9
NAME
   cat - concatenate files and print on the standard output

SYNOPSIS
   cat [OPTION]... [FILE]...

DESCRIPTION
   Concatenate FILE(s) to standard output.
   With no FILE, or when FILE is -, read standard input.
```

So, according to the description, the parameter `-`, makes `cat` read from
standard input. You may have guessed this already, but standard input or STDIN
is usually what's entered by the user in the terminal; however, STDIN doesn't
explicitly refer to input from a user. Rather, it refers to a _stream_ of data
that is being sent to a program, so it may also refer to files or even the
output of other programs that is being passed to other programs.

You'll most likely hear of this concept of input and output referred to as
STDIO or standard input and output. If you'd like to read more, there is an
[excellent article by
freeCodeCamp](https://www.freecodecamp.org/news/introduction-to-linux/#heading-standard-file-streams)
explaining more about it and many other useful concepts.

#### Solution

There will be opportunities to demonstrate STDIO and IO redirection later, but
for this level, all you need to recognize is that `-` is a special character
that tells cat to read input from STDIN instead of a file as we saw before. So
to properly refer to the `-` file, it must be referenced by some other means
than the simple filename. There are several ways to accomplish that.

1. Use `./<filename>` where `<filename>` would be `-` for this example. The `.`
   is a special character that is interpreted as the current directory. This is
usually implied when we just enter a file by it's name. However, stating it
explicitly allows us to circumvent the special case of using `-` as an argument
to `cat`.

   ```terminal
   $ cat ./-
   [REDACTED PASSWORD]
   ```

2. Use the full path. On Linux and other Unix-based systems the [root of the
   file system](https://en.wikipedia.org/wiki/Root_directory) can be specified
with a `/`. To use this method though we'll need to know the full path of the
`-` file. To get that we can use the `pwd` command, which is short for "print
working directory".

   ```terminal
   $ pwd
   /home/bandit1
   ```

   To complete the full path for `-` we just need to append `/<filename>`.

   ```terminimal
   $ cat /home/bandit1/-
   [REDACTED PASSWORD]
   ```

3. We can also use what is called a glob (`*`). The glob can be used to execute
   commands over multiple files at once. For example using the following
command will print all files in the current working directory.

   ```terminal
   $ cat ./*
   [REDACTED PASSWORD]
   ```

   In this example the only file in our current directory is the password file
   `-`. But just like the other examples it circumvents the special `-`
   argument to `cat`.

   The glob is actually a part of a larger set of [filename
   expansion](https://www.gnu.org/software/bash/manual/bash.html#Filename-Expansion)
   features available in Bash. They're quite useful and we may see more of them
   in future levels.

{% callout(type="tip") %}

The main takeway from this level is that there are special characters that may
change how commands are interpreted on the command line. Some of them will be
built-in to whatever shell you're using, but some may just be conventions like
the `-` character for STDIN and won't apply to every program. Here is a
breakdown of the special characters used in the Bash shell that you might want
to watch out for.
[https://mywiki.wooledge.org/BashGuide/SpecialCharacters](https://mywiki.wooledge.org/BashGuide/SpecialCharacters)

{% end %}

### Level 2

> The password for the next level is stored in a file called `spaces in this filename`
> located in the `home` directory

This level is similar to Level 1, except that the file is not a special
character. Instead it contains special characters, the space █. The space
character is essential for the shell to interpret the input text.

```terminal
$ cat spaces in this filename
cat: spaces: No such file or directory
cat: in: No such file or directory
cat: this: No such file or directory
cat: filename: No such file or directory
```

As you can see from above, entering the name as it's written will cause the
`cat` command to interpret each word in the file as a separate filename.

Just as before, there are a few ways to get around this.

#### Solution
1. Escape the space characters. In Bash, the backslash `\` is used as an
   [escape
character](https://www.gnu.org/software/bash/manual/bash.html#Escape-Character). When the `\` is used, the following character is interpreted literally. This allows the space characters of the filename to be "escaped".

   ```terminal
   $ cat spaces\ in\ this\ filename
   [REDACTED PASSWORD]
   ```

2. Instead of escaping the spaces individually, the filename can also be
   surrounded by [single
quotes](https://www.gnu.org/software/bash/manual/bash.html#Single-Quotes).
Every character between two single quotes is interpreted literally.

   ```terminal
   $ cat 'spaces in this filename'
   [REDACTED PASSWORD]
   ```

3. Similarly, [double
   quotes](https://www.gnu.org/software/bash/manual/bash.html#Double-Quotes)
can also be used to interpret the surrounded characters literally. However,
there are some exceptions, and double quotes allow some special characters to
be interpreted. But the space character is not one those, so it is functionally
the same as the above example in this case.

   ```terminal
   $ cat "spaces in this filename"
   [REDACTED PASSWORD]
   ```

{% callout(type="tip") %}

Recognize that it's sometimes necessary to escape characters within filenames
and other arguments.

Most Linux users will avoid naming files with any special characters, but
sometimes you'll still run into them. This is especially true with files
created on Windows where spaces inside file and directory names are much more
common.

{% end %}

### Level 3

> The password for the next level is stored in a hidden file in the `inhere`
> directory.

```terminal
$ ls ./inhere
```

According to the `ls` command, there don't appear to be any files in the
`inhere` directory. That's because `ls` doesn't display hidden files by
default.

#### Solution

To show hidden files with `ls`, it's necessary to use the `--all` or `-a` flag.

```terminal
$ ls -a ./inhere
.  ..  ...Hiding-From-You
```

With the `--all` flag enabled, `ls` now shows all the files in the `inhere`
directory. Including the hidden files. In Linux and other Unix-based systems, a
leading dot `.` in a filename is used to indicate a hidden file. As such, you
may here them referred to as dotfiles.

Now that we know the name of the hidden file, `cat` can be used to print the
contents just as we've seen before.

```terminal
$ cat inhere/...Hiding-From-You
[REDACTED PASSWORD]
```

{% callout(type="note") %}

The term "dotfile" is also frequently used to refer to a user's personal
configuration files. This is because many configuration files follow the
leading dot `.` convention, so they don't clutter up directory listings.

{% end %}

{% callout(type="tip") %}
Some systems will have aliases for the `ls` command as well, with various flags
enabled. Here a few common ones.
- `ll` for `ls -AlhF` to list all files in a human-readable, long format
- `la` for `ls -A` to list all files excluding `.` and `..`
- `l` for `ls -CF` to list files in a column format
- `lsd` for `ls --group-directories-first`

See [this DigitalOcean
article](https://www.digitalocean.com/community/tutorials/an-introduction-to-useful-bash-aliases-and-functions)
to learn more about common aliases and how to configure your own.

{% end %}

### Level 4

> The password for the next level is stored in the only human-readable file in
> the `inhere` directory. Tip: if your terminal is messed up, try the `reset`
> command.

If you haven't done so already, now would be a good time to read through the
manual for each of the commands that were already mentioned in [Level
0](#level-0).

If you've read through the description for each, you should have a pretty good
idea of which command you'll need to solve this one.

#### Solution

This level is asking us to identify which files in `inhere` are human readable.
The `file` command can get the information we need.

From the `file` manual page we know that we can expect a response of `text` for
any files that only contain typical [ASCII
characters](https://en.wikipedia.org/wiki/ASCII#Character_set).

```
The  type  printed  will  usually  contain one of the words `text` (the file
contains only printing characters and a few common control characters and is
probably safe to read on an ASCII terminal), `executable` (the file contains
the result of compiling a program  in  a form  understandable to some UNIX
kernel or another), or `data` meaning anything else (data is usually “binary”
or non-printable).
```

One option is to execute `file` on each file in `inhere` one-by-one like so.

```terminal
$ file inhere/-file00
inhere/-file00: data
```

A better way is to use the glob `*` that we've seen previously. This allows us
to run the file command on all the files at once.

```terminal, hl_lines=9
$ file ./inhere/*
./inhere/-file00: data
./inhere/-file01: data
./inhere/-file02: data
./inhere/-file03: data
./inhere/-file04: data
./inhere/-file05: data
./inhere/-file06: data
./inhere/-file07: ASCII text
./inhere/-file08: data
./inhere/-file09: data
```

From here it's obvious that we want the file with the text data at `./inhere/-file07`.

```terminal
$ cat ./inhere/-file07
[REDACTED PASSWORD]
```

{% callout(type="tip") %}

Always keep an eye out for opportunities to use globbing. Especially if you're
running commands over a bunch of files.

{% end %}

### Level 5

> The password for the next level is stored in a file somewhere under the
> `inhere` directory and has all of the following properties:
> - human-readable
> - 1033 bytes in size
> - not executable

This level requires a precise approach to locate the correct file. While it's
technically possible to solve with just the `ls` and `file` commands. It would
require some tedious manual searching to find the files that match each of the
criteria. Luckily the `find` command is capable of locating files with all the
above criteria. You just need to know the right flags.

#### Solution

The `find` command is essential to efficiently locating files on Linux systems.
It has several flags that can be used to refine its search. Most importantly
for this level are `-readable`, `-size` and `-executable`.

```terminal
$ find -readable -size 1033c -not -executable
./inhere/maybehere07/.file2
```

Note `-size` and `-not` flags. The `c` suffix for the `-size` argument is used
to indicate a size in bytes. The other available suffixes are all available in
the [find man
page](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html).
Additionally, the `-not` flag negates the next expression, thus locating any
files that aren't executable in this example.

{% callout(type="note") %}

In this case the content and size of the file are sufficient to uniquely
identify the file and the `-not -executable` isn't strictly necessary.

```terminal
$ find -readable -size 1033c
./inhere/maybehere07/.file2
```

{% end %}

Once again, `cat` the file to get the password.

```terminal
$ cat ./inhere/maybehere07/.file2
[REDACTED PASSWORD]
```

### Level 6

> The password for the next level is stored somewhere on the server and has all
> of the following properties:
>
> - owned by user bandit7
> - owned by group bandit6
> - 33 bytes in size

This level is very similar to [Level 5](#level-5) with a couple minor
differences. First, the file is "stored somewhere on the server" instead of in
the `inhere` directory. That just means we'll need to run the `find` command
from the root of the file system to ensure the file isn't missed. Secondly, the
file is specified by two new parameters. The [user and
group](https://wiki.archlinux.org/title/Users_and_groups) that own the file.

#### Solution

Searching the `find` manpage you can find the two flags `-user` and `-group` to
filter for files owned by the `bandit7` user and the `bandit6` group as
specified by the prompt. The final parameter is for a `-size` of 33 bytes which
we already saw in level 5.

Here's an example of the output from `find` with all the required arguments.

```terminal
$ find / -user bandit7 -group bandit6 -size 33c
find: ‘/drifter/drifter14_src/axTLS’: Permission denied
find: ‘/root’: Permission denied
find: ‘/snap’: Permission denied
find: ‘/tmp’: Permission denied
find: ‘/proc/tty/driver’: Permission denied
find: ‘/proc/250118/task/250118/fd/6’: No such file or directory
find: ‘/proc/250118/task/250118/fdinfo/6’: No such file or directory
find: ‘/proc/250118/fd/5’: No such file or directory
...
[TRUNCATED OUTPUT]
```

Unfortunately searching with `find` from `/` has a side effect. Any files or
directories that the current user is not allowed to read will print an error to
the terminal. This makes it pretty difficult to parse the output for any
resulting files that match our search. To avoid this flood of errors, a common
solution is to redirect the standard error stream to
[`/dev/null`](https://www.digitalocean.com/community/tutorials/dev-null-in-linux)
This is actually just a file that discards anything written to it.

To redirect a data stream in Bash we must specify its [file
descriptor](https://en.wikipedia.org/wiki/File_descriptor), which is an
integer. The file descriptor is followed by a greater than sign `>` which
indacates that the stream should be redirected to a target file. Here is the
same command as above with all errors redirect to `/dev/null`.

```terminal, hl_lines=2
$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
```

As you can see, the output is considerably easier to understand. Read the file
at `/var/lib/dpkg/info/bandit7.password` to get the next password.

### Level 7

> The password for the next level is stored in the file `data.txt` next to the
> word "millionth".

This is the first level that OverTheWire introduces some new recommended
commands since [level 0](#level-0).

|Command|Description|
|-------|-----------|
|[man](https://manpages.ubuntu.com/manpages/noble/man1/man.1.html)|access the system reference manuals|
|[grep](https://manpages.ubuntu.com/manpages/noble/man1/grep.1.html)|print lines that match patterns|
|[sort](https://manpages.ubuntu.com/manpages/noble/man1/sort.1.html)|sort lines in text files|
|[uniq](https://manpages.ubuntu.com/manpages/noble/man1/uniq.1.html)|remove duplicate lines from a file|
|[strings](https://manpages.ubuntu.com/manpages/noble/man1/strings.1.html)|print readable strings from arbitrary files (even binary)|
|[base64](https://manpages.ubuntu.com/manpages/noble/man1/base64.1.html)|encode data into [Base64](https://en.wikipedia.org/wiki/Base64)|
|[tr](https://manpages.ubuntu.com/manpages/noble/man1/tr.1.html)|translate and replace characters|
|[tar](https://manpages.ubuntu.com/manpages/noble/man1/tar.1.html)|a utility for archive files|
|[gzip](https://manpages.ubuntu.com/manpages/noble/man1/gzip.1.html)|a utility for compressing files|
|[bzip2](https://manpages.ubuntu.com/manpages/noble/man1/bzip2.1.html)|a utility for compressing files|
|[xxd](https://manpages.ubuntu.com/manpages/noble/man1/xxd.1.html)|a tool for creating a hex dump of a file|

Once again, I highly recommend at least reading through the introduction for
each of these commands and checking out the examples at
[cheat.sh](https://cheat.sh).

#### Solution

If you've followed the above advice, there should really only be one contender
to solve this level. The `grep` command.

```terminal
NAME
   grep - print lines that match patterns

SYNOPSIS
   grep [OPTION...] PATTERNS [FILE...]
   grep [OPTION...] -e PATTERNS ... [FILE...]
   grep [OPTION...] -f PATTERN_FILE ... [FILE...]

DESCRIPTION
   grep  searches for PATTERNS in each FILE. PATTERNS is one or more
   patterns separated by newline characters, and grep prints each line that
   matches a pattern. Typically PATTERNS should be quoted when grep is used in a
   shell command.

```

According to the syntax description we should be able to search for patterns in
a file with the following syntax.

```terminal
grep <PATTERN> <FILE>
```

Swapping in the values mentioned in the prompt will return the line we're
looking for.

```terminal, hl_lines=2
$ grep "millionth" data.txt
millionth       [REDACTED PASSWORD]
```

#### Regex

Grep and more generally
[regular expressions](https://en.wikipedia.org/wiki/Regular_expression) (regex)
are extremely useful. You'll find many applications have integrated support for
text search via regex. In particular text editors, word processors, and
programming IDEs.

{% callout(type="tip") %}

To explore regex more I highly recommend reading through the [Regex Quick Start
Guide](https://www.regular-expressions.info/quickstart.html) from
[regular-expression.info](https://www.regular-expressions.info) to get a feel
for what's possible with regex and then follow that up with some
experimentation on [regex101.com](https://regex101.com). This is a tool that
visualizes regex matches. Drop any text you want into it and try out all kinds
of search patterns. Try to match words, letters, various combinations of upper
and lowercase letters, punctuation, etc. Seeing regex in action and observing
precisely what matches with different patterns will give you a much better
intuition for what's possible than anything I could write here.

Regex can get very complicated very quickly, so regex101 is also a great
tool for debugging your regex. Definitely give it a bookmark, it'll be a life
saver. Trust me.

{% end %}

### Level 8

> The password for the next level is stored in the file `data.txt` and is the
> only line of text that occurs only once

This level is the first that seriously benefits from chaining two commands
together. In Bash this is done with the pipe `|` character. The pipe, when
placed after a command will pass all of the output (stdout) into the input
(stdin) of the command that follows it.

For example, we can combine the `ls` and `grep` commands to list only those
files that contain "bash" in the name.

```terminal
$ ls -a | grep bash
.bash_logout
.bashrc
```

Remember that the `-a` flag is necessary to list hidden or dot files.

Read through the [Piping and
Redirection](https://ryanstutorials.net/linuxtutorial/piping.php) article
provided under the helpful reading material section to learn more about
piping.

#### Solution

Reviewing the recommended commands, one should stick out.

```
NAME
   uniq - report or omit repeated lines

SYNOPSIS
   uniq [OPTION]... [INPUT [OUTPUT]]

DESCRIPTION
   Filter adjacent matching lines from INPUT (or standard input), writing
to OUTPUT (or standard output).

   With no options, matching lines are merged to the first occurrence.
```

The `uniq` command is able to filter matching or repeated lines. Since we're
looking for a unique line in a file, this will be helpful. However, there is
one caveat when using `uniq` that you must be aware of. The `uniq` command
filters _adjacent matching_ lines. This means that any matching lines that
aren't directly adjacent, won't be filtered. So the first step must be to
organize the file such that matching lines are adjacent. In other words the
file should be sorted. The `sort` program is designed precisely for this use
case. 

For example, here's the first 20 lines of `data.txt` when sorted.

```terminal
$ sort data.txt | head -n20
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
```

As mentioned before, the pipe `|` is useful here to send the output of the
`sort` command to `uniq`.

```terminal
$ sort data.txt | uniq | head -n20
0BKVRLEJQcpNx8wnSPxDLFnFKlQafKK6
0eJPctF8gK96ykGBBaKydhJgxSpTlJtz
0kJ7XHD4gVtNSZIpqyP1V45sfz9OBLFo
0lPOvKhpHZebxji0gdjtGCd5GWiZnNBj
0REUhKk0yMqQOwei6NK9ZqIpE5dVlWWM
1jfUH1m4XCjr7eWAeleGdaNSxFXRtX0l
1VKPEkd0bCtIRwMFVQfY7InulwOFyDsn
2u8fvAzvnaFlvQG3iPt4Wc1TFhPcGxhH
35l6mr3f6TvlJyDwU6aUgJX07cLhr6t9
3FIgajXBiaQAiTMVGo1gxRDSiACNyvvJ
3mNA2le0gfURQKNHVIhGkMNLqLwjyyLN
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
4P8FsHcdr7d5WKnPtAaXY5SslKICd2gL
5EmwMKZHwF6Lwq5jHUaDlfFJBeHbcX0b
5hYz0028e1Q2TrtPVz5GZbpMzZNjebhh
5I2jWpqjtVp576xXI2TLh1UCyXJtGQ78
6Boy6esAjnIxCYn8uI6KZ7VD7zysDM8i
7cP8ssLElERHXqOJc9T84bxsmJBjNXk2
7qHmEo1FEbzthgyNpKc38YofXjYKZv18
8FCtUQlFXsJnNeyiDY5KfE3vRy6sZFEJ
```

Well that's strange. Why has `uniq`  returned all those lines? If you read the
description for `uniq` carefully, then one line explains this.

```
With no options, matching lines are merged to the first occurrence.
```

So essentially what we have here is a file with each unique line of the file
where adjacent duplicates have been compressed into a single line. However, we
want to list _only_ the unique lines from the input. For that, the `-u` flag
will meet our needs. The `-u` flag tells sort to only print _unique_ lines. In
other words, only lines without any duplicates in the input.

So finally, we have a solution.

```terminal
$ sort data.txt | uniq -u
[REDACTED PASSWORD]
```

### Level 9

> The password for the next level is stored in the file `data.txt` in one of the
> few human-readable strings, preceded by several ‘=’ characters.

This level 

#### Solution

```terminal
$ grep '=== \w*' data.txt
grep: data.txt: binary file matches
```

The `\w` is a shortcut for any word characters which equivalent to
`[a-zA-Z0-9_]`.

We can see here, that grep found some matches, but `data.txt` was interpreted
as a binary file. We can force grep to process the file as if it were text with
the `-a` flag.

```terminal, hl_lines=4 8 12 16
$ grep -a '=== \w*' data.txt
D]
  h#!QJsVzl7POl%Y]Ha^UvToD|@T^N8g}b}?
Q#gm1x}========== theѦ+idW^)F1>)٘SK3PZt&xs肉WB/2ÜB       Ź/Bjɢ<7<u/d|
                                                                    -n
#iu=
    7֣n)Uջش5bBKK}x>}:4Rl_7gHD:274CFy
6!&zB$l_GphqI.02H$Twm⧫o3mt0p~L3JprD========== passwordi L ~ˏ<@Ȅh$%Q5Dk |3
~Tf;o9sP#t+Pe΢쵟
OqDf.8Czmnf&vl:FXKbM
                 CIBi>Y
Еk      $nXT=~}*4a2?TO"'&J~fDV3========== isd5z(#&s!10&poq
nR F
    z|!(if+A64+'FTb5A}
éT:kAU2Qcɐ%#g+;YA_ekrX53|f8+e~&Oiu?VhM}^Qp^G==6!sT:     "uVa-t\fg
]󈍅(.ۍg:7nnp CD`voSQ-<]`@#H UumBiAj堵!O&D9========== [REDACTED PASSWORD]

```

On the last line of the above output, you'll find the password to the next
level.

{% callout(type="tip") %}

It's possible for `grep` to output the precise matching text instead of each
entire line. The `-o` flag is needed to do this. It tells grep to only output
the matched pattern.

```terminal, linenos, hl_lines=5
$ grep -a -o '=== \w*' data.txt
=== the
=== passwordi
=== is
=== [REDACTED PASSWORD]
```

This gives a much clearer picture of the password without all the surrounding
binary data.

{% end %}

### Level 10

> The password for the next level is stored in the file `data.txt`, which
> contains __base64__ encoded data



#### Solution

This one is pretty straight forward. The prompt gives it away by mentioning
that the data is Base64 encoded.

Check the manual for the `base64` command and you'll find one of the first
flags is `-d` for `--decode`.

```terminal, hl_lines=2
$ base64 -d data.txt
The password is [REDACTED PASSWORD]
```

{% callout(type="note") %}

Be sure you understand what _encoding_ is in this context.

[Base64](https://en.wikipedia.org/wiki/Base64) is just one scheme of many to
convert binary data into printable characters i.e. alphanumeric characters with
the addition of the equal sign `=`. In fact, that equal sign `=` is used for
padding the end of encoded text, so it's often a dead giveaway that some text
was Base64 encoded. For example, the word "password" when Base64 encoded is
`cGFzc3dvcmQ=`.

Base64 encoded text is extremely common on the web and you're likely to come
across it at some point, so it may behoove you to read up on it. FreeCodeCamp
has an [excellent
article](https://www.freecodecamp.org/news/what-is-base64-encoding/) that gives
a good overview of how Base64 works and what it's used for.

You're less likely see other forms of binary to text encoding, but feel free to
[read more](https://en.wikipedia.org/wiki/Binary-to-text_encoding).

{% end %}

### Level 11

> The password for the next level is stored in the file data.txt, where all
> lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

This challenge is describing what's commonly known as a shift cipher or [Caesar
cipher](https://en.wikipedia.org/wiki/Caesar_cipher). It's not actually used in
modern times for any meaningful attempts at securing messages, but it's
somewhat popular in CTFs and wargames.

To keep within the spirit of the wargame, let's first go over how you might
solve this challenge in the terminal. One of the recommended commands is `tr`
which can "[t]ranslate, squeeze, and/or delete characters" according to the
description. Here's a few different ways to go about it.

```bash
# Rotate the alphabet by 13 letters where both input and output are explicit
cat data.txt | tr "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ" "nopqrstuvwxyzabcdefghijklmNOPQRSTUVWXYZABCDEFGHIJKLM"

# Similar to above except the input is defined by a regular expression
cat data.txt | tr "a-zA-Z" "nopqrstuvwxyzabcdefghijklmNOPQRSTUVWXYZABCDEFGHIJKLM"

# Similar to above except both the input and output are defined by a regular expression
# Note: the odd arrangement for the output is the necessary since Regex doesn't allow
# the letter ranges to wrap around
cat data.txt | tr "a-zA-Z" "n-za-mN-ZA-M"
```

{% callout(type="tip") %}

While the `tr` command is cool and all, there's an even cooler tool you should
be using when investigating any challenge related to cryptography. And that's
[CyberChef](https://gchq.github.io/CyberChef). CyberChef has a huge number of
useful features for transforming data and supports hundreds of data formats and
encoding schemes.

Check out the [CyberChef ROT13 cipher
solver](https://gchq.github.io/CyberChef/#recipe=ROT13(true,true,false,13)).

{% end %}

## To be continued

{% callout(type="note") %}
I hope you enjoyed the walkthrough. When time permits, I intend to expand this
post to include every level of OverTheWire Bandit.

Happy hacking!
{% end %}
