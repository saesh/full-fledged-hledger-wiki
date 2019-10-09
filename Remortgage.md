> Associated directory:
> [09-remortgage](../tree/master/09-remortgage)

## How to handle changes in mortgage conditions

If at a certain point you decide to re-mortgage, chances are that your new mortgage conditions would be different.

Typically, your interest rate would change, which means that the script that computes interest payments should change as well.

The easiest way to handle the change would be to pre-filter input to `hledger-interest` with `hledger print` so that it will see transactions only up to the month when you re-mortgaged and then post-filter the output to the same perior (to exclude an entry for `today` that `hledger-interest` will generate).

Once you've done that, you could duplicate your `hledger-interest` invocation, but change the filters so that
they limit input and output to the period after remortgage, and change interest rate accordingly.

Since mortgage interest script works on a year by year basis, both `hledger-interest` invocations will essentially work on intersection of appropriate mortgage period and requested year. If the interesection is empty, nothing will be computed (as expected).

Modified script could be found in [09-remortgage](../tree/master/09-remortgage) or [diffs/08-to-09.diff](../tree/master/diffs/08-to-09.diff).

## Next steps

To be continued
