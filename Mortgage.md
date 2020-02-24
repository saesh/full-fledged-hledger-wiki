> Associated directory:
> [08-mortgage](../tree/master/08-mortgage)

## Where is the complexity

When you take out a mortgage (or any other loan) you usually set up a periodic repayment plan.

Then you make payments (manually, or by setting up payment instructions with your bank) and they
usually could be seen in your bank account statement.

However, each payment consists of interest payment (this is your bank service fee, essentially) and capital payment (which offsets the body of your loan). In my experience, statements from mortgage provider are either unparseable, do not provide a breakdown, or are being supplied too infrequently (quarterly or yearly) or all of the above.

However, it is possible to compute breakdown yourself, using [hledger-interest](http://hackage.haskell.org/package/hledger-interest).

This way, you would import mortgage payments from your bank statement as going into `liabilities:mortgage`, and then `hledger-interest` will generate additional transactions which will move your interest payments out of `liabilities:mortgage` and into `expenses:mortgage interest`. 

## Taking out a mortgage

If you took a mortgage to buy a house costing `X`, putting down `Y` yourself, taking a loan for `X-Y` and paying various fees in the amount of `Z` (which get rolled into the mortgage amount), then this could be recorded as:
```
YYYY-MM-DD  Taking out the mortgage
  assets:bank           -Y
  liabilities:mortgage
  assets:house          X
  expenses:fees         Z
```

If fees are not included in the mortgage, you can either record them in a separate transaction or add more postings:
```
YYYY-MM-DD  Taking out the mortgage
  assets:bank           -Y ; downpayment
  assets:bank           -Z ; fees
  liabilities:mortgage
  assets:house          X
  expenses:fees         Z
```

## Computing capital payments

Now you need to run `hledger-interest` on `liabilities:mortgage` account, telling it to take interest payments out of `expenses:house:mortgage interest` and back into `liabilities:mortgage` (warning: `--source` and `--target` semantics seems a bit opposite to what one would expect):
```
hledger-interest liabilities:mortgage \
   --source='expenses:house:mortgage interest' \
   --target='liabilities:mortgage' \
   --annual=<rate, use 0.03 for 3% etc>
```

This will print out a document in the journal format, which you can include into the appropriate yearly file.

## Automation

There is a bit of Catch-22 here, though - if generated file is `include`d into your yearly journal, how can you feed it into `hledger-interest`, which is supposed to generate it in the first place?

Additionally, interest transactions will modify balance of the mortgage account. What would happen when we run `hledger-interest` second time - would it not be confused by its own transactions?

Fortunately, we can temporarily create a completely empty mortgage interest payments file so that yearly journal will not have any broken `include`s, run `hledger-interest`, and put generated data into the file.
And in the future, we can use `hledger print` to ignore mortgage interest payment transactions as we feed journal into `hledger-interest`. 

All of this could be encoded as a simple script and a rule in `export.hs`, which you can see in [08-mortgage](../tree/master/08-mortgage) or [diffs/07-to-08.diff](../tree/master/diffs/07-to-08.diff).

## Next steps

[Remortgage](Remortgage)
