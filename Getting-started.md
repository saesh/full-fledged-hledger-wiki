So, you want to keep track of your finances with hledger. You read [hledger step-by-step guide](http://hledger.org/step-by-step.html#useful-accounting-concepts), and you
know the difference between "assets", "liabilities", "equity", "income" and "expenses".

Let's get started. Look into [01_getting_started](../tree/master/01_getting_started), where you will find a completely empty journal for the current year (at the time of writing - 2017) and a couple of export scripts.
Make sure that year of your journal corresponds to the year at the top of  `./export/export.hs`.

Now run `./export.sh`, and a bunch of files would be generated in `./export`:
```
export
├── 2017-all.journal           - a list of all transactions for the year
├── 2017-balance-sheet.txt     - balance sheet for the end of the year
├── 2017-closing.journal       - closing balances of all assets and liabilities accounts
├── 2017-income-expenses.txt   - income and expense report for the year
└── export.hs                  - Shake build script that describes how these reports are generated
```

General idea is to keep this files under version control so that whenever you want to do sweeping changes it would be easy to see what exactly was affected.