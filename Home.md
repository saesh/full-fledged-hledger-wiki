## What is this?

This is a set of sample journals and helper scripts that I use together with hledger for tracking personal finances
and budgeting. It should be easily adaptable to other command-line accounting tools (ledger, beancount, ...).

I went through several different approaches over the course of 10 years, and in a sense this is a short story of
how that journey went and where it led.

## Goals

I wanted setup that would enable me to have:

- Avoid spending too much time, effort and manual work on tracking expenses

- Eventual consistency: even if you can't record something precisely right now, maybe I would be able to do it later

- Ability to refactor. I want to be able to go back and change how I am doing things, with as little effort as possible and without fear of irrevocably breaking things.

## What is in here, what do you have to have installed and how are you supposed to use these files

Scripts and files here assume Linux-like environment with Haskell (in particular, you will need `runhaskell` and `stack`) and textutils/shellutils available. I have not tested them on Mac OS or Windows.

You will need to have `shake` build system installed (which you can get via `stack install shake`). Scripts used to convert CSV files assume that you will end up having many of them and use GNU `parallel` to run as many conversion jobs as you have CPU cores. If `parallel` is not available, scripts will process files sequentially.

Repository contains a number of subdirectories (`01_getting_started`, `02_getting_data_in`, ...) that represent gradual evolution of the setup, starting from the smallest
possible and gradually adding more and more features in. This way you can either choose the starting point that is more suitable for you or compare/diff various setups and see
what exactly have been changed to add each particular feature.

The bigger the number, the more features/examples are incorporated.

## Directory structure

At top level, there would be a number of yearly journals, one per year (`2014.journal`, `2015.journal`, etc).

Each of them will `!include` a bunch of files from `./import/{statement source}/journal/` subdirectories and will also contain all manual transactions for the given year.

Script `export.sh` is used to generate a bunch of reports in the `export` subdirectory, which contains [Shake](http://shakebuild.com/) script `./export/export.sh` that drives whole process.

All source statements go into `./import/{statement source}/in`, then they are converted to proper CSV files and put into `./import/{statement source}/csv` and generated journal files go into `./import/{statement source}/journal`. I usually have `./import/{statement source}/convert.sh` that allows you to re-generate all files in `./journal` from the source files held in `./in`.

Typical filesystem tree will look like this:
```
├── 2014.journal
├── 2015.journal
├── 2016.journal
├── 2017.journal
├── export
│   ├── 2014-all.journal
│   ├── 2014-balance-sheet.txt
│   ├── 2014-closing.journal
│   ├── 2014-income-expenses.txt
│   ├── 2015-all.journal
│   ├── 2015-balance-sheet.txt
│   ├── 2015-closing.journal
│   ├── 2015-income-expenses.txt
│   ├── 2015-opening.journal
│   ├── 2016-all.journal
│   ├── 2016-balance-sheet.txt
│   ├── 2016-closing.journal
│   ├── 2016-income-expenses.txt
│   ├── 2016-opening.journal
│   ├── 2017-all.journal
│   ├── 2017-balance-sheet.txt
│   ├── 2017-closing.journal
│   ├── 2017-income-expenses.txt
│   ├── 2017-opening.journal
│   └── export.hs
├── export.sh
└── import
    └── lloyds
        ├── convert.sh
        ├── csv
        │   ├── 12345678_20171225_0001.csv
        │   ├── 12345678_20171225_0002.csv
        │   ├── 99966633_20171223_1844.csv
        │   ├── 99966633_20171224_2041.csv
        │   ├── 99966633_20171224_2042.csv
        │   └── 99966633_20171224_2043.csv
        ├── in
        │   ├── 12345678_20171225_0001.csv
        │   ├── 12345678_20171225_0002.csv
        │   ├── 99966633_20171223_1844.csv
        │   ├── 99966633_20171224_2041.csv
        │   ├── 99966633_20171224_2042.csv
        │   └── 99966633_20171224_2043.csv
        ├── journal
        │   ├── 12345678_20171225_0001.journal
        │   ├── 12345678_20171225_0002.journal
        │   ├── 99966633_20171223_1844.journal
        │   ├── 99966633_20171224_2041.journal
        │   ├── 99966633_20171224_2042.journal
        │   └── 99966633_20171224_2043.journal
        ├── lloyds2csv.pl
        └── lloyds.rules
```

# How to build a setup like this

## Getting started

So, you want to keep track of your finances with hledger. You read [hledger step-by-step guide](http://hledger.org/step-by-step.html#useful-accounting-concepts), and you
know the difference between "assets", "liabilities", "equity", "income" and "expenses".

Lets get started. Look into `01_getting_started`, where you will find a completely empty journal for the current year (at the time of writing - 2017) and a couple of export scripts.
Make sure that year of your journal corresponds to the year at the top of  `./export/export.hs`.

If you run `./export.sh`, a bunch of files would be generated in `./export`:
```
export
├── 2017-all.journal           - a list of all transactions for the year
├── 2017-balance-sheet.txt     - balance sheet for the end of the year
├── 2017-closing.journal       - closing balances of all assets and liabilities accounts
├── 2017-income-expenses.txt   - income and expense report for the year
└── export.hs                  - Shake build script that describes how these reports are generated
```

General idea is to keep this files under version control so that whenever you want to do sweeping changes it would be easy to see what exactly was affected.