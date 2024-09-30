+++
title = "The Wandering Inn (Statistics)"
date = 2024-10-01
description = "A web application to display interesting TWI stats."

[extra]
toc = true

[taxonomies]
tags = ["TWI", "statistics", "data-viz", "web-novel", "Django", "Tailwind"]
+++

# Project
[The Wandering Inn (TWI)](https://wanderinginn.com) is a webnovel or web serial
if you prefer. In fact it's one of the longest you'll find. At the time of this
writing, the word count clocks in at over 13 million. For the record, that's
over 27 times the length of [The Lord of the
Rings](https://en.wikipedia.org/wiki/The_Lord_of_the_Rings) and almost 3 times
the length of [The Wheel of
Time](https://en.wikipedia.org/wiki/The_Wheel_of_Time). Personally I love the
story, but I'm not hear to talk about that today. Instead we're going to take a
look at a data visualization project I've been working on.

Due to its length, TWI has a vast collection of characters, locations, magical
items etc. that are mentioned by name throughout the story. Not only that, the
story also includes references to [Classes], [Skills], [Spells] and some other
things in brackets. To understand why would require that we delve into the lore
a bit, but suffice to say, all these references make up some interesting data
points to analyze in terms of their frequency, location on the timeline, and
relationships with one another.

TWI Stats is a project that aims to let anyone explore interesting stats
related to all the items above via various tables and interactive charts and
graphs.

The code can be found at [https://github.com/cblanken/wandering-inn-stats](https://github.com/cblanken/wandering-inn-stats)

Check out the live deployment at [https://twi-stats.cblanken.dev](https://twi-stats.cblanken.dev)

{{ callout(type="warning", text="At the time of this writing, the application
only includes statistics up through volume 9. No chapters of volume 10 have
been analyzed yet. During development, the wiki underwent significant changes
and development which broke much of the web scraping required to keep the
database current with all the characters, [Classes], and [Skills] etc. The wiki
data collection modules are currently being refactored to accommodate the new
wiki and extract data via the MediaWiki API to be more robust in the future.")
}}


# Word counts
Of course, we have word counts. Even if they aren't particularly interesting,
they're fun to keep track of. That way we can at least compare the length to
other works.

## Word count by chapter
![word count by chapter](/images/twi-stats/word-counts-by-chapter.svg)
[`Current interactive version`](https://twi-stats.cblanken.dev/overview/charts/word-counts-by-chapter)

## Cumulative word count over time
Amazingly, the author's pace hasn't flagged at any point over the last several
years of writing.

![word count over time](/images/twi-stats/total-word-counts-over-time.svg)
[`Current interactive version`](https://twi-stats.cblanken.dev/overview/charts/total-word-counts-over-time)

## Word counts by book
These books actually refer to the audiobook releases of the serial.

![word count by book](/images/twi-stats/word-counts-by-book.svg)
[`Current interactive version`](https://twi-stats.cblanken.dev/overview/charts/word-counts-by-book)

## Word counts by volume
Volumes are the original demarcation of major plot points of the web serial. As
you can see, they keep getting longer 👀.

![word count by volume](/images/twi-stats/word-counts-by-volume.svg)
[`Current interactive version`](https://twi-stats.cblanken.dev/overview/charts/word-counts-by-volume)

# Mentions
There are several important reference types that warrant tracking mentions. These include:
1. Characters
2. [Classes]
3. [Skills]
4. [Spells]
5. [Locations]


{{ callout(type="note", text="There are some other types that would be
interesting to track as well, but they're still under development. These will
include things like named magical items and monsters.") }}


Here's an example of the chart for the most referenced [Classes].

![class mentions](/images/twi-stats/class-mentions.svg)
[`Current interactive version`](https://twi-stats.cblanken.dev/overview/charts/class-mentions)

Additionally each of these reference types has a breakdown with a few more
charts describing how often they're mentioned across chapters and the chapters
with the most references for each type.

For example, the [Mage] class has the following charts.

### [Mage] mention count (cumulative)
![mage cumulative mention count](/images/twi-stats/mage-total-mentions.svg)
[`Current interactive version`](https://twi-stats.cblanken.dev/classes/mage/charts/total-mentions)

### [Mage] mention count (chronological)
![mage chronological mention count](/images/twi-stats/mage-mentions.svg)
[`Current interactive version`](https://twi-stats.cblanken.dev/classes/mage/charts/mentions)

### [Mage] most mentioned chapters
![mage most mentioned chapters](/images/twi-stats/mage-most-mentioned-chapters.svg)
[`Current interactive version`](https://twi-stats.cblanken.dev/classes/mage/charts/most-mentioned-chapters)

There are few more stats that are tracked as well, but they're specific to each
individual reference type and I won't go into detail here. You can check them
out on their respective pages of the application.

# Technology
The TWI Stats project is built with the following open source software:
- The [Django](https://www.djangoproject.com) web framework
- The [Tailwind](https://tailwindcss.com) CSS framework
- The [Plotly](https://plotly.com/python/) graphing library
- A [PostgreSQL](https://www.postgresql.org) database

# Attributions
## Author
A huge thanks to [Pirateaba](https://www.patreon.com/pirateaba/posts) for
writing such a wonderful web serial and keeping all the ducks fed for so many
years.

## Wiki
This project wouldn't be possible without all the awesome folks maintaining the [TWI Wiki](https://wiki.wanderinginn.com/The_Wandering_Inn_Wiki) and tracking the thousands of various characters, [Classes], [Skills] etc.