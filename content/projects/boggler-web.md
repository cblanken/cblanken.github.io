+++
title = "Boggler"
date = 2024-09-30
description = "A web application to solve the Boggle word game."

[extra]
toc = true
project_image = "boggler/boggler-cover.png"
project_src_link = "https://github.com/cblanken/boggler-flask"
project_demo_link = "https://boggler.cblanken.dev/"

[taxonomies]
tags = ["Boggle", "word-games", "Flask", "python"]
+++

# Project
[Boggle](https://en.wikipedia.org/wiki/Boggle) is an excellent little word
game. If you haven't played before, here are some details on [how to
play](https://en.wikipedia.org/wiki/Boggle#Rules).

Each round is played on a board of dice arranged in a square. Each player
writes down as many words as they can composed of the connected letters on the
board. Longer words are worth more points. Play lasts until a player reaches an
agreed upon score or time limit is reached.

A board would look something like this.
![boggle board example](/images/boggler/boggler_board.png)

Anyway, I enjoyed playing the game a lot with my family growing up and it was
always fun finding new or absurdly long words. Unfortunately after a round
ends, the board is re-rolled never to be seen again leaving all those potential
words lost to time. This is where the Boggler comes in.

If you don't care too much about the details, you can check out the live
application at [boggler.cblanken.dev](https://boggler.cblanken.dev). Have fun!

# Features
The Boggler is first and foremost an application for solving Boggle boards.
Just enter all the letters from your game board and click "solve". The
application will check several dictionaries to provide you with a
searchable table of all the available words from the board. This is great for
finding the longest words or words that used a particular set of letters that
you're interested in.


To follow along with the examples below, you can find the interactive board at
[https://boggler.cblanken.dev/board/solved/6dc8aaa7cb8bc0b12be3e8aeafcede64](https://boggler.cblanken.dev/board/solved/6dc8aaa7cb8bc0b12be3e8aeafcede64)

## Solved board table
Each solved board has an interactive board page where you can peruse all the
available words in a table and filter the results however you see fit.

For example the word table for the example board above looks like this.

![boggler table](/images/boggler/boggler_table.png)

You can see in this example that the table is sorted by word length. A great
way to find some of the cool words you might have missed.

## Dictionaries
You'll notice there is also a series of dictionaries available to select from
just above the table. If there are any you'd prefer not to use for your games,
simply uncheck them from here.

![solved board dictionaries](/images/boggler/dicts.png)

Personally, I've found them all to be relatively reliable with the exception of
the _dwyl_ dictionary. It's an open source English dictionary which you can
read more about [here](https://github.com/dwyl/english-words). Unfortunately,
it also contains many words that are invalid for Boggle. To read more about the
other dictionaries, please see the _Dictionary_ section on the
[homepage](https://boggler.cblanken.dev/board).


## Heatmap
One of my favorite features is the heatmap. The heatmap displays how many
times a letter was used by all the available words making it possible to pick
out hotspots and identify some of the more useful letter combinations and
patterns.

![boggler board heatmap](/images/boggler/boggler_heatmap.png)

## Filtering
There are a few methods for filtering the results of the solved board. The
first being the aforementioned dictionary list. Words are only shown if at
least one of the enabled dictionaries contains the word.

Another is the search bar available above the word table.
![table search](/images/boggler/table_search_rod.png)

It isn't necessary to search for full words, you might have noticed searching
for "rod" also shows words like "trod" and "trode". Words with the target
letter sequence will be shown regardless of where the letters appear in the
word.

And finally, the board itself also functions as a filter. To enable this, click
on any letter in the board to filter for words that pass through that letter.
![board letter filter](/images/boggler/letter_filter.png)

Clicking the red "filter" button below the board will remove the filter.

In this example, the letter "I" in the first row is selected, which is
indicated by the red background color and blue border around the selected
letter. The word table reflects all the words that pass through that "I".

{% callout(type="note") %}
Only a single letter can be filtered with this
method. To find more complex letter combinations, you'll need to resort to
using the table's search bar. However, they can be used in tandem.
{% end %}

## Path tracing
Each word has a unique path on the board and __clicking a word in the table__ will
render that path. For example one of the longest words for this board is
"indirect". The first letter of the word is indicated by a darker background
color. ![word path](/images/boggler/word_path.png)

## Saved boards

Solved boards are all saved in a database and can be viewed at
[https://boggler.cblanken.dev/solved](https://boggler.cblanken.dev/solved).
This means you can easily share any solved boards. Each solved board has a
unique URL and you can either copy the URL directly or click the "Share" button
above the board to automatically copy it to your clipboard.

The example we've been looking at is available at
[https://boggler.cblanken.dev/board/solved/6dc8aaa7cb8bc0b12be3e8aeafcede64](https://boggler.cblanken.dev/board/solved/6dc8aaa7cb8bc0b12be3e8aeafcede64)

# Technology
Boggler uses HTML and Flask's
[Jinja](https://flask.palletsprojects.com/en/2.3.x/templating/) templating
engine for composing content.

The board and dictionary data are backed by a [SQLite](https://www.sqlite.org) database.

The styling uses custom CSS and some [Bootstrap](https://getbootstrap.com) for a
couple components. 

Vanilla JavaScript is used in conjunction with the following JavaScript
libraries for dynamic interaction with the board and tables.
1. [DataTables](https://datatables.net) to display word and board tables
2. [LeaderLine](https://anseki.github.io/leader-line/) to render the word path overlays

