You probably have more than one account at the same place where you have your current account. It could be the current account of your significant other or a savings account. Statement for this account
would be in the same format, so it should be easy to grab those as well and convert them.

You will need to modify the rules to make sure that `account1` is properly set (or inferred from a column in the input file). Apart from that, the rest is easy -- you drop statements in `./in`, run `convert.sh` and `!include` generated journals as necessary:
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

You might also need to make sure that transfers between accounts are not double-counted.

Current account statements that were dealt with in `03_getting_full_history/` included a couple of transfers from current to the savings account. Now that savings account statements are brought into play, they will contain records of these transfers as well, and if we were to include them as-is, balances for both current and savings accounts would be wrong:
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

The trick here is to make transfers face non-existent 'transfers' account, such that line in the current account statement is converted to be a transaction from `assets:Lloyds:current` account to `transfers`, and line in savings account statement is converted to be a transaction from `transfers` to `assets:Lloyds:savings` (after which `transfers` account is flat):
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

This requires further changes to the rules file and conversion scripts that could be seen in `04_adding_more_accounts/`.

As newly-converted journals are `!include`-ed into yearly files and
`./export.sh` is re-ran, you can use version control system to verify
that yearly balance statements for the current account are unchanged (you
did put all generated reports under version control to simplify this,
didn't you?).
