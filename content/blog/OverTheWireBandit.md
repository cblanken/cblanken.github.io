+++
title = "OverTheWire - Bandit"
date = "2024-10-16"
description = "Walkthrough of the OverTheWire Bandit wargame."

[extra]
show_only_description = true
toc = true

[taxonomies]
tags = ["ctf", "infosec", "wargame", "OverTheWire"]
+++

# Introduction
[Bandit](https://overthewire.org/wargames/bandit/) is just one of several
wargames available at [overthewire.org](https://overthewire.org/). Bandit is
aimed at beginners to Linux and CTFs and provides an excellent introduction to
the basics of the Linux command line.

In this walkthrough I'm going to show the process for solving each challenge
while also providing some insight into what each challenge is trying to teach
and why it's useful, so keep an eye out for callouts like those below.

{{ callout(type="note" text="Access to each Bandit level is made over SSH. The
username will correspond to the index of the level starting at 0. Always make
sure you're logging into the correct Bandit level!") }}

{{ callout(type="warning" text="If you're looking for the solution of a
particular level, you are highly encouraged to attempt it on your own before
following this walkthrough. You will learn some useful things regardless, but
attempting it on your own first will help the knowledge stick better. I
promise.") }}

I have a few recommendations before getting started. Most of the OverTheWire
Bandit levels will provide links to manual pages of commands that may be
useful. Unfortunately the `man` pages are often somewhat cryptic, especially
for beginners. For that reason I recommend the following steps to try to get
unstuck when slogging through the `man` pages for any particular command.

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

# Level 0
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

{{ callout(type="note", text="It's worth mentioning that SSH is usually (and
preferably) used with an SSH key. More specifically a key-pair. A public key
and a private key which are both needed to take advantage of the aforementioned
public-key cryptography. I won't go into detail here, but don't worry there are
a couple later levels that do utilize SSH keys, so I'll discuss them in more
detail when we reach them.") }}

## Level 0 - Solution

The prompt for level 0 tells us that both the `username` and `password` are
`bandit0`. 

Just as important though, is the network location we've been
provided, `bandit.labs.overthewire.org`, which we'll use to connect to level 0.
Actually, `bandit.labs.overthewire.org` is what's known as a
[hostname](https://en.wikipedia.org/wiki/Hostname), and more specifically it's
a [fully qualified domain name
(FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). You can
recognize an FQDN because it will include a [top-level domain
(TLD)](https://en.wikipedia.org/wiki/Top-level_domain) such as `.com`, `.net`,
or `.org`. If you've ever accessed a website before, you should be familiar
with some of these. This naming system for computers is called the [Domain Name
System (DNS)](https://en.wikipedia.org/wiki/Domain_Name_System). Luckily we
don't need to dig into the details here. Just know that one of these FQDNs will
resolve to an [IP address](https://en.wikipedia.org/wiki/IP_address) which can
(usually) be used to uniquely identify a computer system on the internet.

This unique name will tell the `ssh` program what system to connect to. The
`ssh` program expects the user credentials and hostname in the following format
`ssh://[user@]hostname[:port]`. This is explained in the first couple
paragraphs of the `ssh` man page. The elements wrapped in brackets `[` and `]`
are actually optional. Linux `man` pages will commonly express the available
flags and arguments for a command in this way.

With that knowledge we can now connect to the first level with this command.
```bash
$ ssh ssh://bandit0@bandit.labs.overthewire.org:2220
```

You should be greeted by a banner for the Bandit wargame and a prompt
requesting entry of the `bandit0` password. Enter `bandit0` and you're ready to
get started.

There is a `readme` file in the current directory. Ther are few ways you can read files from the terminal but the easiest will be the [`cat` command]

{{ callout(type="note", text="It isn't necessary to solve any of the Bandit
levels, but I highly recommend researching DNS. It's intrinsic to how the
internet operates and you'll likely encounter it again and again if you pursue
any area of IT, software, or cybersecurity.") }}


# Level 1
# Level 2
# Level 3
# Level 4
# Level 5


# To be continued
I hope you enjoyed the walkthrough. When time permits, I intend to expand this
it to include every level of OverTheWire Bandit.
