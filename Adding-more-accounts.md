> Associated directory:
> [04-adding-more-accounts](../tree/master/04-adding-more-accounts)

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
between these accounts and each transfer would be present in _two_
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
the savings account statement will generate a transaction moving funds
from `transfers` to `assets:Lloyds:savings`.

From the `transfers` perspective, money will move in and out, and the account will be left flat:
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
[04-adding-more-accounts](../tree/master/04-adding-more-accounts)
or in the [diffs/03-to-04.diff](../tree/master/diffs/03-to-04.diff).

Now when you run
`./export.sh`, you can use the version control system to verify
that end-of-year balances for the current account are unchanged, which
will mean that we are not double-counting transfers (you did put all generated reports under version control, didn't you?).

## More on transfers and double-counting

You could come across the double-counting issue in different cases, but most of them would be similar to the one just described: 
- the problem will be introduced when you add a new data source
- the problem could be solved by introducing a new intermediate account.

For example:

**Problem**: you used to direct PayPal transactions in your main bank account to `expenses:PayPal`, but now you want to start parsing PayPal statements. How should you parse "money in" lines from PayPal files?

**Solution**: change rules for you bank account to send PayPal transactions to `assets:PayPay:top-up`. Create rules for PayPal files to set `account1` on money-in transactions to `assets:PayPal:top-up` as well.

Same applies to Amazon purhases (once you start importing Amazon statements), cryptocurrency exchanges, various debit card providers (Revolut, Monzo, N26, Starling, ...) and pretty much any other acccount that you would fund from your primary account and which could provide its own reports/statements.

## Next steps

Now you can start getting your data in shape, for example, you can
start to classify expenses by [creating your CSV import rules](Creating-CSV-import-rules).
