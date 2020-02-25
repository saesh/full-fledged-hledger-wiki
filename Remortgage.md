> Associated directory:
> [09-remortgage](../tree/master/09-remortgage)

## UNDER CONSTRUCTION

This advise is buggy, and this page is waiting for hledger-interest 1.5.4 to be released

## How to handle changes in mortgage conditions

If at a certain point you decide to re-mortgage, chances are that your new mortgage conditions would be different.

Typically, your interest rate would change, which means that the script that computes interest payments should change as well.

If you are using `hledger-interest` version 1.5.3 or below you will need to pre-filter output of `hledger-interest` with `hledger print` so that only transactions up to the month when you re-mortgaged are emitted, and then use this output to compute final balance at the time of remortgage, and then send your journal to `hledger-interest` again, this time prefiltered so that only post-remortgage transactions are included, and also throw remortgage day balance in the mix. Nothing that cannot be achieved with 20-100 lines of bash or python :) And next time you would probably avoid remortgaging just so that you will not have to fix this script again.

With `hledger-interest` 1.5.4 or above, there is an easier way: you can specify rate schedule. If your old rate `r1` was effective from 2017-01-01, and your new rate `r2` is effective from 2019-04-05, then `hledger-interest` could be invoked with your rate schedulek like this:
```
hledger-interest --annual-schedule='[(2017-01-01, r1), (2019-04-05, r2)]' <rest of the arguments>
```

Initial date does not have to be precise, it just has to be on or before first mortgage payment. It is perfectly ok to set it way in the past.

Modified mortgage-interest script that demonstrates this could be found in [09-remortgage](../tree/master/09-remortgage) or [diffs/08-to-09.diff](../tree/master/diffs/08-to-09.diff).

## Next steps

To be continued
