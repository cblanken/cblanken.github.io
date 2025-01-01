+++
title = "RustCON"
date = 2024-12-29
description = "An RCON client written in Rust."

[extra]
toc = true
project_image = "rustcon/rustcon_cover.svg"
project_src_link = "https://github.com/cblanken/rustcon"

[taxonomies]
tags = ["Rust", "RCON", "game-server"]
+++

RustCON is an
[RCON](https://developer.valvesoftware.com/wiki/Source_RCON_Protocol) client
written in Rust. RCON is a protocol primarily used for game server
administration. Some examples of games that use RCON are
[Minecraft](https://www.minecraft.net), [Left 4
Dead](https://store.steampowered.com/app/500/Left_4_Dead/), [Left 4 Dead
2](https://store.steampowered.com/app/550/Left_4_Dead_2/), [Garry's
Mod](https://gmod.facepunch.com), and [Project
Zomboid](https://projectzomboid.com/blog/), but there are many others.

This RCON client is quite simple and was my first foray into writing software
with the Rust programming language. The source code can be found at
[https://github.com/cblanken/rustcon](https://github.com/cblanken/rustcon).

## Usage

```
rustcon 0.1.0
A simple RCON client written in Rust

USAGE:
    rustcon [OPTIONS]

OPTIONS:
    -h, --help           Print help information
    -i, --ip <IP>        RCON server IPv4 address [default: 127.0.0.1]
    -p, --port <PORT>    RCON server PORT number [default: 27015]
    -V, --version        Print version information
```

## Demo

<script src="https://asciinema.org/a/lLmhAWmNQuXpPFXqkGeVpt2xa.js" id="asciicast-lLmhAWmNQuXpPFXqkGeVpt2xa" async="true"></script>


If you're looking for other RCON libraries and clients, some can be found on
the [Valve
wiki](https://developer.valvesoftware.com/wiki/Source_RCON_Protocol#Source_RCON_Libraries).
