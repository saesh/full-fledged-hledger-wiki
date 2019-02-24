> Associated directory: [01-getting-started](../../tree/master/01-getting-started)

I assume that you want to keep track of your finances with
[hledger](http://hledger.org). You have read
[hledger step-by-step guide](http://hledger.org/step-by-step.html#useful-accounting-concepts),
and you know the difference between "assets", "liabilities", "equity",
"income" and "expenses".

However, it is a hard to begin: which accounts one should create and
maintain? How to deal with multiple years and multiple banks? What if
you later decide to change something?

Let's get started. Look into [01-getting-started](../../tree/master/01-getting-started), where you will find a completely empty journal for the current year (at the time of writing - 2017) and a couple of export scripts.
Check that current year is properly reflected at the top of
`./export/export.hs`:

```
--
-- Range of years to report. You would typically want all the years you have data for.
--
first   = 2017 :: Int
current = 2017
```

Now run `./export.sh`, and a bunch of files would be generated in `./export`:
```
export
├── 2017-all.journal           - a list of all transactions for the year, to simplify review and double-checking
├── 2017-balance-sheet.txt     - balance sheet for the end of the year, showing your assets and liabilities
├── 2017-closing.journal       - closing balances of all assets and liabilities accounts
├── 2017-cash-flow.txt         - cash flow report for the year
├── 2017-income-expenses.txt   - income and expense report for the year
├── 2017-unknown.journal       - journal of all transactions in "unknow" accounts, for further review.
└── export.hs                  - Shake build script that describes how these reports are generated
```

General idea is to keep these files under version control so that
whenever you want to change anything in your setup it would be easy to
see what exactly was affected by asking version control system for
diffs versus previously saved version of these reports.

As you can see, your current journal is empty and reports reflect
this - they would show that various totals are zero and pretty much
nothing else. Commit all of them into version control system of your
choice.

Now it is time to [get some data in](Getting-data-in). 
