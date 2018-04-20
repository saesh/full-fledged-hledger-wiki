You probably have more than one account at the same financial institution where you have your current account. It could be the current account of your significant other or a savings account. Hopefully statement for this account
would be in the same format, so it should be easy to grab those as well and convert them.

You will need to modify the CSV conversion rules to make sure that `account1` is properly set (or inferred from a column in the input file). Apart from that, the rest is easy -- you drop statements in `./in`, run `convert.sh` and `!include` generated journals as necessary. For example, let's say that you have additional account `12345678` which you will now import:
```
import
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

It would not be a stretch to assume that you have moved funds between these accounts, and each transfer would be present in two statements - once for source account and once for destination account.

Let's make sure that these transfers are not double-counted.

Current account statements that were dealt with in the [previous chapter[(Getting-full-history) included a couple of transfers from current to the savings account. Now that savings account statements are brought into play, they will contain records of these transfers as well, and if we were to include them as-is, balances for both current and savings accounts would be wrong, like this:
```
; In current account
2017-12-02 Transfer to savings
   assets:Lloyds:current  $-500
   assets:Lloyds:savings

; In the savings account
2017-12-02 Transfer from current
   assets:Lloyds:savings  $500
   assets:Lloyds:current
```

The trick here is to make transfers face imaginary 'transfers' account, so current account will contain a transaction from `assets:Lloyds:current` account to `transfers`, and line in savings account statement will contain transaction from `transfers` to `assets:Lloyds:savings`. From `transfers` perspective, money will move in and out, and account will be left flat:
```
; In current account
2017-12-02 Transfer to savings
   assets:Lloyds:current  $-500
   assets:Lloyds:transfers

; In the savings account
2017-12-02 Transfer from current
   assets:Lloyds:savings  $500
   assets:Lloyds:transfers
```

This requires further changes to the rules file and conversion scripts that could be seen in the branch [04-adding-more-accounts](../../tree/04-adding-more-accounts).

As newly-converted journals are `!include`-ed into yearly files and
`./export.sh` is re-ran, you can use version control system to verify
that yearly balance statements for the current account are unchanged, which will mean that we are not double-counting transfers (you
did put all generated reports under version control to simplify this,
didn't you?).
