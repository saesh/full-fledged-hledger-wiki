> Associated directory:
> [04-adding-more-accounts](../../tree/master/04-adding-more-accounts)

You probably have more than one account at the same financial institution where you have your current account. It could be the current account of your significant other or a savings account. Hopefully statement for this account
would be in the same format, so it should be easy to grab those as well and convert them.

For example, let's say that you have additional account `12345678`
which you will now import, so eventually you want your `import`
directory to look like this:
```
import
└── lloyds
    ├── in
    │   ├── 12345678_20171225_0001.csv      - newly downloaded files
    │   ├── 12345678_20171225_0002.csv      / 
    │   ├── 99966633_20171223_1844.csv
    │   ├── 99966633_20171224_2041.csv
    │   ├── 99966633_20171224_2042.csv
    │   └── 99966633_20171224_2043.csv
    ├── csv
    │   ├── 12345678_20171225_0001.csv      - scrubbed and cleaned
    │   ├── 12345678_20171225_0002.csv      /
    │   ├── 99966633_20171223_1844.csv
    │   ├── 99966633_20171224_2041.csv
    │   ├── 99966633_20171224_2042.csv
    │   └── 99966633_20171224_2043.csv
    └── journal
        ├── 12345678_20171225_0001.journal  - journals that we expect to generate
        ├── 12345678_20171225_0002.journal  /
        ├── 99966633_20171223_1844.journal
        ├── 99966633_20171224_2041.journal
        ├── 99966633_20171224_2042.journal
        └── 99966633_20171224_2043.journal
```

You will need to modify the CSV conversion rules to make sure that
`account1` is properly set (or inferred from a column in the input
file). Apart from that, the rest is easy -- you drop statements in
`./in`, `!include` generated journals as necessary, run `./export.sh`
and it should just work.

However, it would not be a stretch to assume that you have moved funds
between these accounts, and each transfer would be present in _two_
statements - once for source account and once for destination account.
This would mean that account balances (and balance assertions) will
not be right.


## Let's make sure that transfers are not double-counted.

Current account statements that were dealt with in the
[previous chapter](Getting-full-history-of-the-account) included a
couple of transfers from current to the savings account. Now that
savings account statements are brought into play, they will contain
records of these transfers as well, and if we were to include them
as-is, balances for both current and savings accounts would be wrong,
like this:
```
; In current account statement
2017-12-02 Transfer to savings
   assets:Lloyds:current  $-500
   assets:Lloyds:savings

; In the savings account statement
2017-12-02 Transfer from current
   assets:Lloyds:savings  $500
   assets:Lloyds:current
```

The trick here is to make both transfers face imaginary `transfers`
account, so current account statement will contain a transaction
moving funds from `assets:Lloyds:current` to `transfers`, and line in
savings account statement will generate a transaction moving funds
from `transfers` to `assets:Lloyds:savings`.

From `transfers` perspective, money will move in and out, and account will be left flat:
```
; In current account statement
2017-12-02 Transfer to savings
   assets:Lloyds:current  $-500
   assets:Lloyds:transfers

; In the savings account statement
2017-12-02 Transfer from current
   assets:Lloyds:savings  $500
   assets:Lloyds:transfers
```

This requires small changes changes to the rules file and conversion scripts
that could be seen in the directory
[04-adding-more-accounts](../../tree/master/04-adding-more-accounts)
or in the [diffs/03-to-04.diff](../../tree/master/diffs/03-to-04.diff).

Now when you run
`./export.sh`, you can use version control system to verify
that end-of-year balances for the current account are unchanged, which
will mean that we are not double-counting transfers (you did put all generated reports under version control, didn't you?).

## Next steps

Now you can start getting your data in shape, for example you can
start to classify expenses by [tweaking your CSV import rules](Improving-CSV-import-rules).
