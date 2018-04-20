> NB: If you are just starting with hledger, this section could overwhelm you. Skip it and return later after you've read the first couple of sections. 

I prefer to keep all hledger files in a single directory, with frequently-used files at top level and less important files hidden out of sight in a bunch of subdirectories.

At the top level, you would have a number of yearly journal files, one file per year (`2014.journal`, `2015.journal`, etc).

Each of them will `!include` a bunch of files from import subdirectories (`./import/{statement source}/journal/`) and will also contain all manually-entered transactions for the given year. Import subdirectories will contain raw CSV/PDF/QIF statements exported from financial institutions that you have accounts with.

Script `export.sh` is used to generate a bunch of reports in the `./export` subdirectory. Actual generation is driven by `./export/export.hs` which is a [Shake](http://shakebuild.com/) build system script. It is entirely possible that you would not normally look at any of those, but they would come in handy when/if you will refactor your financial records -- they would quickly show you which aggregate balances change and how.

All source statements go into `./import/{statement source}/in`. Each source directory will have a small shell scripts `./import/{statement source}/convert.sh` that will converted raw input files proper CSV files and put them into `./import/{statement source}/csv`. These will be converted to journal files that will go into `./import/{statement source}/journal`. If you need to change something in the generated report files, you never do this manually - instead, you will change CSV conversion rules and re-generate all .journal files from source files.

After a couple of years typical filesystem tree will look like this (showing single source of statements called `lloyds`):
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
