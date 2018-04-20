Now that a single statement is successfully imported, grab all of them -- as many as you can get your hands on. Let's assume that you can get 4 years worth of statements. Save them all in the same `in` directory
as your already-converted statement, run `./convert.sh` and hey presto -- you now have the full history of your main day-to-day account in a set of nice journal files in `./import/lloyds/journal`:
```
import
└── lloyds
    ├── convert.sh
    ├── in                                   - original yearly statements
    │   ├── 99966633_20171223_1844.csv
    │   ├── 99966633_20171224_2041.csv
    │   ├── 99966633_20171224_2042.csv
    │   └── 99966633_20171224_2043.csv
    ├── csv                                  - cleaned up files, ready to be consumed by hledger
    │   ├── 99966633_20171223_1844.csv
    │   ├── 99966633_20171224_2041.csv
    │   ├── 99966633_20171224_2042.csv
    │   └── 99966633_20171224_2043.csv
    ├── journal                              - generated journals
    │   ├── 99966633_20171223_1844.journal
    │   ├── 99966633_20171224_2041.journal
    │   ├── 99966633_20171224_2042.journal
    │   └── 99966633_20171224_2043.journal
    ├── lloyds2csv.pl
    └── lloyds.rules
```

Generated journals span 4 years, so you would need more yearly journals: let's create `2014.journal`, `2015.journal` and `2016.journal` and `!include` all newly-converted journal files in there.

When these files are created, a bit of housekeeping is required. As a rule, people usually don't want to retain the detailed record of expenses once the year is over and start expense tracking for the current year from scratch.

On the other hand, assets and liabilities cannot be left behind and
should be carried over into the next year. To achieve this carry-over,
you 
need to make sure that your yearly files look like this:

```
!include ./export/{year}-opening.journal

;; !include your converted statements here

!include ./export/{year}-closing.journal
```

File for the earliest year should not have an `!include` for opening balances, and file for the current year should not have `!include` for closing balances.

Note that we are `!include`ing files from `./export` - they would be auto-generated, you will not have to generate them manually.

Now, if you edit `./export/export.hs` and change `first` year to be 2014 and re-run `./export.sh`, you will see all the "opening" and "closing" reports generated, along with balance sheets, income-expense statements, and other reports for all years from 2014 to 2017:
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
├── 2017-opening.journal
└── export.hs
```

Remember to put them all under version control. Check out branch [03-getting-full-history](../../tree/03-getting-full-history) to explore these changes.

Now that you have imported your main account, it is time to [add more accounts](Adding-more-accounts) to your setup.