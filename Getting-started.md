> Associated directory: [01-getting-started](../tree/master/01-getting-started)

I assume that you want to keep track of your finances with
[hledger](http://hledger.org). You have read
[hledger basics tutorial](https://github.com/simonmichael/hledger/wiki/hledger-basics-tutorial) and [hledger accounting concepts](https://github.com/simonmichael/hledger/wiki/hledger-accounting-concepts),
and you know the difference between "assets", "liabilities", "equity",
"income" and "expenses".

However, it is hard to begin: which accounts one should create and
maintain? How to deal with multiple years and multiple banks? What if
you later decide to change something?

Let's get started with an empty journal.

Look into [01-getting-started](../tree/master/01-getting-started), where you will find a completely empty journal for the current year (at the time of writing - 2017) and a couple of export scripts.
Check that the current year is properly reflected at the top of
`./export/export.hs`:

```
--
-- Range of years to report. You would typically want all the years you have data for.
--
first   = 2017 :: Int
current = 2017
```

## What is this export.sh thing?

Script `export.sh` is a one-liner that invokes `export/export.hs`, where the real magic lies. That file
uses `shake` - build system written in Haskell - to automate all conversion and report generation tasks.
Entire contents of `export/export.hs` is build rules with small amount of customizeable information at the top -- like
current year and list of reports you want to generate.

`Shake` was chosen mainly for its ability to handle complex dependencies easily.

## Lets run it!

Now run `./export.sh`, and a bunch of files would be generated in `./export`:
```
export
├── 2017-all.journal           - a list of all transactions for the year, to simplify review
│                                and double-checking
├── 2017-balance-sheet.txt     - balance sheet for the end of the year, showing your assets
│                                and liabilities
├── 2017-closing.journal       - closing balances of all assets and liabilities accounts
├── 2017-cash-flow.txt         - cash flow report for the year
├── 2017-income-expenses.txt   - income and expense report for the year
├── 2017-unknown.journal       - journal of all transactions in "unknown" accounts,
│                                for further review.
└── export.hs                  - Shake build script that describes how these reports
                                 are generated
```

General idea is to keep these files under version control so that
whenever you want to change anything in your setup it would be easy to
see what exactly was affected by asking version control system for
diffs versus the previously saved version of these reports.

As you can see, your current journal is empty and reports reflect
this - they would show that various totals are zero and pretty much
nothing else. Commit all of them into the version control system of your
choice.


## Next steps

Now it is time to [get some data in](Getting-data-in). 
