> Associated branch: [02-getting-data-in](../tree/02-getting-data-in)

The easiest place to start is with your main account. Normally it would be the account that is used to fund most of your day-to-day expenses. It is probably a checking/debit/credit card account at the bank of your choice or
a combination of several of them.

You would want to get CSV statements for your account covering a reasonable period of time. You can start small: get a statement for the last month or a calendar/fiscal year-to-date. You can always import older statements later (this would be covered further on). For now, let's save that single statement somewhere
next to your journal. I use `./import/<institution name>/in` for this purpose. For example, my current account was once with Lloyds and so their statements will go into `./import/lloyds/in/{filename}.csv`

Quite often these CSV files would not be directly suitable for hledger's CSV import facility. You might need to do some data scrubbing. I've included a sample data file and conversion scripts in [02-getting-data-in](../tree/02-getting-data-in). If you run `./convert.sh` in `./import/lloyds`, it will take raw files from `in`, generate scrubbed CSV files in `csv` and convert them into `.journal` files saved in `lloyds/import/journal`:
```
import
└── lloyds
    ├── convert.sh                           - conversion script. This is what you will run
    ├── lloyds2csv.pl                        - helper script to clean up downloaded statements
    ├── in
    │   └── 99966633_20171223_1844.csv       - original downloaded file
    ├── csv
    │   └── 99966633_20171223_1844.csv       - cleaned up file, ready to be consumed by hledger
    ├── journal
    │   └── 99966633_20171223_1844.journal   - generated journal
    └── lloyds.rules                         - CSV conversion rules
```

Here is a crucial bit: instead of copying the contents of that file into your journal, lets just `!include` it into the yearly journal for appropriate year (in this case, 2017). Now you can re-run `./export.sh` and lo and behold: generated reports will now have data in them
and if you are keeping them under version control you should be able to diff them against the previous version and see exactly what has changed where at a glance.

You will notice that import rules put all expenses in the single 'expenses' account. That's fine, let's assume that you do not have time to sort them out just now, we will do this later.

Now that you can easily convert single statement, let's [save and convert all of them](Getting-full-history-of-the-account).

## Initial Balances

Most probably your account was not opened in 2017. If your account already had some balance on 1st Jan 2017, you will need to record this in your journal. As with all other manual transactions, you will put this in top-level yearly file, in this case it is `2017.journal`:

```
;; Opening balances
2017/01/01 opening balances
  assets:Lloyds:current    = £100
  equity:opening balances
```

Make sure you use `equity:opening balances` as this is an account that `hledger equity` uses to generate end-of-year transactions (we will cover them later).