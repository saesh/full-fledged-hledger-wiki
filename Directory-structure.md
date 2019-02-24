> NB: If you are just starting with hledger, this section would definitely
> overwhelm you. Skip it entirely - everything contained here will be introduced in subsequent
> sections.
>
> However, if you are experienced and impatient hledger user who does not want to read through the whole wiki
> or just want a refresher of what goes where, then this section might be for you.

# TL;DR - what goes where

* All files reside in a single directory with two subdirectories: one
  for the source files from financial institutions (`./import`), and
  one for generated summaries and reports (`./export`).
  
* At the top level there is `all.journal` which is the main entry
  point for all of your financial history. It contains just a bunch
  of `!include` lines

* Next to it are a bunch of `{year}.journal` files that will have: 
  1. `!include` of the opening balances file carrying over your assets/liabilities from previous year; 
  2. a bunch of `!include`s for all the converted csv files; 
  3. **all manual transactions that you have for that year**

* Raw CSV files go into `./import/{source}/in`

* Converted CSV files go into `./import/{source}/journal`

* Run `./export.sh` after each change, check the changes to the
  reports generated in `./export`, commit everything religiously.

* When you run `hledger` manually, point it to `all.journal` most of the time.

* It is ok to stop reading here and head on to
  [Getting started](Getting-started) and read the rest of the
  section later.

# But why ... ?

Why is `all.journal` structured the way it is?

Why per-year files at all?

Do they have to start on January?

What do I do to start a new year?

How do I start from scratch?

What if my current setup is completely different - how do I convert?

I love single wiki page that contain all the answers as much as the
next man. Unfortunately, my attempts to write a single page that fully
describes my setup had failed miserably, and the best I can offer you
is to head to [Getting started](Getting-started) and read the rest
of the wiki - it is not too large, and hopefully you would be able to
skip over large sections that explain things you aleady know.
