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
[overthewire.org](https://overthewire.org/). It's is aimed at beginners to
Linux and CTFs and provides an excellent introduction to the basics of the
Linux command line.

In this walkthrough I'm going to show the process for solving each challenge
while also providing some insight into what each challenge is trying to teach
and why it's useful, so keep an eye out for callouts like those below.

{% callout(type="note") %}

text="Access to each Bandit level is made over SSH. The username will
correspond to the index of the level starting at 0. Always make sure you're
logging into the correct Bandit level!")

{% end %}

{% callout(type="warning") %} 
If you're looking for the solution of a particular level, you are highly
encouraged to attempt it on your own before following this walkthrough. You
will learn some useful things regardless, but attempting it on your own first
will help the knowledge stick better. I promise.
{% end %}

I have a few recommendations before getting started. Most of the OverTheWire
Bandit levels provide links to (potentially) usefal manual pages. Unfortunately
the `man` pages are often somewhat cryptic, especially for beginners. For that
reason I recommend the following steps to try to get unstuck when slogging
through the `man` pages for any particular command.

1. Search for examples. Many `man` pages contain examples of their usage. These
   may be marked by all-caps `EXAMPLES` in some cases, but sometimes not.
2. If skimming through the man pages doesn't surface any apparent examples,
   then you may want to refer to [`cheat.sh`](https://cheat.sh). Cheat.sh is a
database of usage examples for thousands of command line programs. It can be
searched from the main page or by simply adding a `/` followed by the command.
For example [`https://cheat.sh/ssh`](https://cheat.sh/ssh) will yield examples
of `ssh` usage. This one can be helpful when trying to understand how a command
is commonly used and which command flags are most useful.
3. If you're still struggling to understand some of the examples then paste
   them into [`https://explainshell.com`](https://explainshell.com).
Explainshell to get a more readable breakdown of each command line flag and
argument. This tool can save you a lot of time flipping back and forth through
the `man` pages.

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

### Solution

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
{% end %}

## Level 0

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
|[`ls`](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html)|list the files in a directory|
|[`cd`](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html)|change your current directory|
|[`cat`](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html)|display the contents of a file|
|[`file`](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html)|display information about the content of a file|
|[`du`](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html)|display the disk space used by files|
|[`find`](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)|search the file system for files with various parameters such as filename, file type, file size, etc.|

You are _highly_ encouraged to review each of the commands. At the very least
check out the `cheat.sh` page for each. Regardless these are all pretty common,
so you'll get plenty of experience with these commands in later levels.

### Solution

For now, it should be apparent the one of the above commands should serve to
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
completed without completing each level again. Once you've done that it's time
to continue to level 1.

## Level 1

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

### Solution

There will be opportunities to demonstrate STDIO and IO redirection later, but
for this level, all you need to recognize is that `-` is a special character
that tells cat to read input from STDIN instead of a file as we saw before. So
to properly refer to the `-` file, it's necessary to reference it by some other
means than just the simple filename. There are several ways to accomplish that.

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

   The glob can be quite useful and is actually a part of a larger set of
   [filename
   expansion](https://www.gnu.org/software/bash/manual/bash.html#Filename-Expansion)
   features available in Bash. They're quite useful and we may see more of them
   in future levels.

## Level 2

## Level 3

## Level 4

## Level 5

## To be continued

{% callout(type="note") %}
I hope you enjoyed the walkthrough. When time permits, I intend to expand this
post to include every level of OverTheWire Bandit.
{% end %}
