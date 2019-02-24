> Associated directory: [03-getting-full-history](../tree/master/03-getting-full-history)

Now that a single statement is successfully imported, grab all of them
-- as many as you can get your hands on. Let's assume that you can get
4 years worth of statements, from 2014 to 2017, even though account
was opened earlier than that.

Save them all in the same `import/lloyds/in` directory
as your already-converted statement. Even though `import/lloyds/csv`
and `import/lloyds/journal` is not fully populated yet, eventually we
will expect them to look like this:

```
import
└── lloyds
    ├── in                                 - these are the original yearly statements:
    │                                        one that we had before and three new ones
    │   ├── 99966633_20171223_1844.csv
    │   ├── 99966633_20171224_2041.csv
    │   ├── 99966633_20171224_2042.csv
    │   └── 99966633_20171224_2043.csv
    ├── csv                                - cleaned up files that we expect to be produced
    │   ├── 99966633_20171223_1844.csv
    │   ├── 99966633_20171224_2041.csv
    │   ├── 99966633_20171224_2042.csv
    │   └── 99966633_20171224_2043.csv
    └── journal                            - generated journals that we will use
        ├── 99966633_20171223_1844.journal
        ├── 99966633_20171224_2041.journal
        ├── 99966633_20171224_2042.journal
        └── 99966633_20171224_2043.journal
```

I deliberately chose file names that do not reflect which year's data
they contain - in my experience, financial institutions are often
unhelpful this way.

Now we just need to `!include` theses journals into our yearly files
and run `./export.sh` ... however, we do not have journal files for years
2014, 2015 and 2016 yet.


## Adding new years

Let's start with the obvious: create `2014.journal`, `2015.journal`
and `2016.journal`, each of which will contain a single `!include`
line for one of the `.journal` files we are about to produce. For
example, `2015.journal` will look like this:
```
!include ./import/lloyds/journal/99966633_20171224_2042.journal
```

Include new years in `all.journal`:
```
!include 2014.journal
!include 2015.journal
!include 2016.journal
!include 2017.journal
```

## On the opening balances

Now a bit of housekeeping is required.

First of all, opening balances previously recorded in `2017.journal`
are not relevant anymore and need to be removed and new, different
opening balances record needs to be added to `2014.journal` instead.

But what about opening balances for year 2015, 2016 and 2017?

As a rule, people usually don't want to retain the detailed record of
expenses once the year is over and start expense tracking for the
current year from scratch. On the other hand, assets and liabilities cannot be left behind and
should be carried over into the next year. And this is what opening
balances transaction tries to achieve - it brings into existence your
assets and liabilities at the beginning of the year.

You could, of course, compute and maintain opening balances
transactions for all years manually, but this is tedious and
error-prone.

Alternative is to observe that opening balances for 2015 should be
exactly the final balances in all assets and liabilities accounts at
the end of 2014. Same for the 2016: it should open with the balances
at the end of 2015 and so on.

Luckily, set of reports that `export.hs` will generate for the year
2014 will include `export/2014-closing.journal` and
`export/2015-opening.journal`, which we can now use.

Lets modify `2015.journal` to include opening balances from the end of
2014:
```
!include ./export/2015-opening.journal
!include ./import/lloyds/journal/99966633_20171224_2042.journal
```

Do the same for `2016.journal` and `2017.journal`.

Wonderful, now you get correct opening balances each year, for free.

However, if you try to run hledger across two or more years at once
(by using `hledger -f all.journal`) you will quickly realize that your
assets and liabilities amounts are no longer correct. After all, `2015-opening.journal`
assumes that there are no previous balances in all assets and equities
accounts, but `all.journal` includes `2015.journal` right after
`2014.journal`, and they are not empty... You are effectively doubling
your assets and equities at the beginning of each year!

This is where `export/2014-closing.journal` would come into play -- it
contains a transaction which zeroes out all assets and liabilities (vs
`equity:closing balances` account) which could be included right
before the start of year 2015. Lets modify `all.journal`:
```
!include 2014.journal
!include ./export/2014-closing.journal
!include 2015.journal
!include ./export/2015-closing.journal
!include 2016.journal
!include ./export/2016-closing.journal
!include 2017.journal
```

## CSV import rules might need to change

New statement files might require new conversion rules. In this
example, there would be a couple of transactions that send money to
the savings account, which we will cover later. For now, lets add a
rule to `lloyds.rules` to make sure it converts properly:
```
if
TRANSFER TO 12345678
  account2   assets:Lloyds:savings
```

## Power of automation

The final change is to make sure that automated conversion rules cover
newly created year files. Modify `export.hs` to read:
```
first   = 2014 :: Int
current = 2017
```

Now, if you run `export.sh`, all new statements will be converted and
report files for years 2014 - 2016 will be generated:
```
export
├── 2014-all.journal
├── 2014-balance-sheet.txt
├── 2014-closing.journal
├── 2014-income-expenses.txt
├── 2015-all.journal
├── 2015-balance-sheet.txt
├── 2015-closing.journal
├── 2015-income-expenses.txt
├── 2015-opening.journal
├── 2016-all.journal
├── 2016-balance-sheet.txt
├── 2016-closing.journal
├── 2016-income-expenses.txt
├── 2016-opening.journal
├── 2017-all.journal
├── 2017-balance-sheet.txt
├── 2017-closing.journal
├── 2017-income-expenses.txt
└── 2017-opening.journal
```

Remember to put them all under version control!

Check out supplied directory
[03-getting-full-history](../tree/master/03-getting-full-history)
to explore these changes or see
[diffs/02-to-03.diff](../tree/master/diffs/02-to-03.diff) to see
the diff of changes done since the previous chapter.

## Pace yourself

To state the obvious, you do not need to try and import all the
previous years in one go. Do as many (or as few) as you want, just
make sure that journal for the earliest year (in our case -
`2014.journal`) has a manual transaction for opening balance to ensure that all the numbers line up.

## Recap

This is how everything should work at this point:

```
                                import/loyds/in/*
                                           +
                                           |  in2csv
                                           v
                                import/loyds/csv/*
                                           +
                                           |  csv2journal
                                           v
                                import/loyds/journal/*
                                           +
      +--------------------------------------------------------------------------+
      |                       |                        |                         |
      |                       |                        |                         |
      |                       |                        |                         |
+-----v---------+        +----v-------+           +----v-------+            +----v-------+
| 2014.journal  |     +-->2015.journal|        +-->2016.journal|         +-->2017.journal|
|               |     |  +------------+        |  +------------+         |  +------------+
|[manual opening|     |   | |                  |   | |                   |   | |
| of balances]  |     |   | |                  |   | |                   |   | |
+---------------+     |   | |                  |   | |                   |   | |
 | |                  |   | |                  |   | |                   |   | |
 | v                  |   | v                  |   | v                   |   | v
 | reports            |   | reports            |   | reports             |   | reports
 | closing of 2014    |   | closing of 2015    |   | closing of 2016     |   |
 |   + +              |   |   + +              |   |   + +               |   |
 |   | |              +   |   | |              +   |   | |               +   |
 |   | +->opening of 2015 |   | +->opening of 2016 |   | +->opening of 2017  |
 |   |                    |   |                    |   |                     |
+v---v--------------------v---v--------------------v---v---------------------v-----------+
|all.journal                                                                             |
+----------------------------------------------------------------------------------------+
```

Files in `import` are being cleaned up and converted from CSV to
journal format. Converted journal files are included into your yearly
journals. Build rules in `export.hs` produce summary reports and
`export/<year>-opening.journal` and `export/<year>-closing.journal`.
Those opening/closing balance journals are, in turn, included into
journal files for subsequent years and into `all.journal`.

Now, regardless of how you access your data - either all at once via
`hledger -f all.journal` or via `hledger -f <year>.journal`, your
balances should look right.

## Why bother with closing and (re)opening and balances?

This whole setup with `opening` and `closing` files sounds awfully
complicated -- why does it seem like a good idea? After all, you can
just `!include` all the converted files into `all.journal` and not
split things by year. Then there would be no issues with closing and re-opening balances,
and things would get simpler -- or so it seems.

I am convinced that per-year files make a lot of sense, especially if
you have 10+ years of financial data. Firstly, you rarely want to see
how much you have spent on groceries over the last 10 years -- I
usually operate with year-to-date and it is my experience that I am
probably the norm and not the exception. If all you have is one huge
journal file, then any hledger invocation gets slower (believe me, 10
years get you noticeable startup time, even on a modern machine) but
most importantly you almost always need to supply `--begin` and
`--end` to *any* hledger invocation you do, and this gets old really
fast.

With yearly files, it is easy to do `hledger -f <year>.journal` and
enjoy the speed and convenience it gets you.

But even more importantly, yearly files allow for efficient scripting.
Build rules in `export.hs` will always rebuild the smallest set of
files and reports necessary. With single journal any change in any of
the included files will force you to re-generate all reports for all
years, and as you accumulate data, this starts taking quite a lot of
time.

To summarize, I think that yearly files give you the best mix of speed
and convenience based on my experience so far. Maybe one of the
readers would convince me to change my ways to something different :)

## Next steps

Now that you have imported your main account, it is time to [add more accounts](Adding-more-accounts) to your setup.
