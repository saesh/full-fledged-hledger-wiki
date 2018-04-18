The easiest place to start is with the account that is used to fund most of your day-to-day expenses. It is probably a checking/debit/credit card account at the bank of your choice or
a combination of several of them.

You would want to get CSV statements for your account for a reasonable period of time. You can start small: get a statement for the last month or a calendar/fiscal year-to-date, and save somewhere
next to your journal. I use `./import/<institution name>/in` for this purpose. For example, at some point, my current account was with Lloyds and their statements
will go into `./import/lloyds/in/{filename}.csv`

Quite often these CSV files would not be directly usable with hledger's CSV import facility. You might need to do some data scrubbing. I've included a sample data file and conversion scripts in [02_getting_data_in](../tree/master/02_getting_data_in). If you run `./convert.sh` in `./import/lloyds`, you will get yourself a nice converted journal in `lloyds/import/journal`:
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

Here is a crucial bit: instead of copying that file into your journal, lets just `!include` it there. Now you can re-run `./export.sh` and lo and behold: generated reports will now have data in them
and if you are keeping them under version control you should be able to see exactly what has changed there.

You will notice that import rules put all expenses in the 'expenses' account. That's fine, suppose we do not have time to sort them out just now.
