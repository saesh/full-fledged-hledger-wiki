So, you want to keep track of your finances with hledger. You read [hledger step-by-step guide](http://hledger.org/step-by-step.html#useful-accounting-concepts), and you
know the difference between "assets", "liabilities", "equity", "income" and "expenses".

Let's get started. Look into [01-getting-started](../../tree/01-getting-started), where you will find a completely empty journal for the current year (at the time of writing - 2017) and a couple of export scripts.
Check that current year is properly reflected at the top of  `./export/export.hs`.

Now run `./export.sh`, and a bunch of files would be generated in `./export`:
```
export
├── 2017-all.journal           - a list of all transactions for the year
├── 2017-balance-sheet.txt     - balance sheet for the end of the year
├── 2017-closing.journal       - closing balances of all assets and liabilities accounts
├── 2017-income-expenses.txt   - income and expense report for the year
└── export.hs                  - Shake build script that describes how these reports are generated
```

General idea is to keep this files under version control so that whenever you want to do sweeping changes it would be easy to see what exactly was affected by asking version control system for diffs versus previously saved version of these reports.

As you can see, your current journal is empty and report reflect this - they would show that various totals are zero and pretty much nothing else. Now it is time to [get some data in](Getting-data-in) 