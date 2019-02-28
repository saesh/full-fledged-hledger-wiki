## Where is the complexity

When you take out a morgage (or any other loan) you usually set up a periodic repayment plan.

Then you make payments (manually, or by setting up payment instructions with your bank) and they
usually could be seen in your bank account statement.

However, each payment cosists of interest payment (this is your bank service fee, essentially) and capital payment (which offsets the body of your loan). In my experience, statements from mortgage provider are either unparseable, do not provide breakdown, or are being supplied too infrequently (quarterly or yearly) or all of the above.

However, it is possible to compute breakdown yourself, using [hledger-interest](http://hackage.haskell.org/package/hledger-interest).

This way, you would import mortgage payments from your bank statement as going into `liabilities:mortgage`, and then `hledger-interest` will generate  additional transactions which will move your interest payments out of `liabilites:mortgage` and into `expenses:mortgage interest`. 

## Taking out a mortgage

If you took a mortgage to buy a house costing `X`, putting down `Y` yourself, taking a loan for `X-Y` and paying various fees in the amount of `Z` (which get rolled into the mortgage amount), then this could be recorded as:
```
YYYY-MM-DD  Taking out mortgage
  assets:bank           -Y
  liabilities:mortgage
  assets:house          X
  expenses:fees         Z
```

If fees are not included in mortgage, you can either record them in a separate transaction or add more postings:
```
YYYY-MM-DD  Taking out mortgage
  assets:bank           -Y ; downpayment
  assets:bank           -Z ; fees
  liabilities:mortgage
  assets:house          X
  expenses:fees         Z
```

## Computing captital payments

Now you need to run `hledger-interest` on `liabilities:mortgage` account, telling it to take interest payments out of `expenses:house:mortgage interest` and back into `liabilies:mortgage` (warning: `--source` and `--targe` semantics seems a bit opposite to what one would expect):
```
hledger-interest liabilities:mortgage \
   --source='expenses:house:mortgage interest' \
   --target='liabilities:mortgage' \
   --annual=<rate, use 0.03 for 3% etc>
```

This will print out document in the journal format, which you can include into appropriate yearly file.

## Automation

There is a bit of Catch-22 here, though - if generated file is `!include`d into your yearly journal, how can you feed it into `hledger-interest`, which is supposed to generate it in the first place?

Fortunately, `hledger-interest` does not need any of the transactions it is about to generate, so we can temprorary create a completely empty mortgage interest payments file, run `hledger-interest`, and put generated data into the file.

Unfortunately, as mortgage payments file(s) are being generated or regenerated, your balance assertions (opening and closing balances) might temporary be out of whack, and `hledger-interest` cannot be instructed to ignore them.
Also, `hledger-interest` is built against slighly dated `hledger-lib`, so it might get confused about more advanced features which appeared in the later versions of `hledger` (if you use them in your journals).

Fortunately, we can use `hledger` to extract necessary data while ignoring balance assertions, and then feed them into `hledger-interest`. 

All of this could be encoded as a simple script and a rule in `export.hs`, which you can see in [08-mortgage](../tree/master/08-mortgage) or [diffs/07-to-08.diff](../tree/master/diffs/07-to-08.diff).

## Next steps

To be continued
