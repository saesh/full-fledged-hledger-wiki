> Associated directory:
> [07-investments-easy-approach](../tree/master/07-investments-easy-approach)

## Let's start easy

Tracking investments could be as easy/simple or as hard/complex as you
want it to be. But in the spirit of "eventual consistency" let's start
simple and add more details later.

At this point, exact nature of your investment is not that important.
It could be a savings account, brockerage account with some shares in
it, your pension, etc. We are going to assume a couple of basic
things:

1. Your investment is represented by a separate account (presumably
   under `assets`)

2. You (periodically?) invest/pay more money **into** your investment.
At the very least there is one initial payment.

3. Value of your investment changes over time and you record this in
your journal. You might receive period valuation document or emails
from the financial institution where you keep your savings. Or you might
go online and check the value of your investment. Either way, you
record the numbers in your journal, and if my experience is any
indication, you probably do not have a way to automatically import
them. I will assume that you **enter manual valuation transactions**
into your yearly journals

4. Completely optionally, you might withdraw money from your
   investment. I am mentioning this purely to say that this should not
   influence calculations I am about to describe.

This way you record and track cash value of your investment, without
bothering to record exact nature or structure of it (which shares, how
many, ...).

## Recording valuations

Recording valuations could be a conundrum. After all, value of
investment could go either up or down -- what would be suitable
account to balance this against? Should it be income or expense account?

My suggestion is to record valuations versus special "unrealized
profit and loss" account, which sits outside of your `income` and
`expense` hierarchy.

Accountant will say that it should be part of your equity, but I found
it easier when it does not mix with all the opening/closing balance
transactions that occur in `equity`. For my examples, I will be using
`virtual:unrealized pnl` as the account that balances your valuation
transactions, like this:
```
2017/04/01 Current pension valuation
    assets:pension:aviva        = £1023
    virtual:unrealized pnl
```

## Computing return on investment

Hledger includes a "return on investment" command accessible as `hledger
roi`. You need to point it to the account that tracks your investments
and account that balances your valuation transactions, and it will
calculate for you two numbers: internal (or money-weighted) rate of return and
time-weighted rate of return.

What are they?

It is quite possible that value of your investments (and therefore
rate of return) changes on a daily basis. However, this is really hard
to think about (and tedious to work with), so for simplicity we could
instead assume that your investment could be approximated with one
that grows at a constant rate R.

We need to choose R in such a way that all your cash contributions to
your investment, grown at this rate R for the respective periods
during which they were invested, add up to the current value of this
portfolio.

This rate is called money-weighted or dollar-weighter rate of return
or **internal rate of return -- IRR for short**.

**Time weighted returns (TWR)** on the other hand, ignores the effect
of cash inflows/outflows, but recognizes that different periods of
time have different rates of return and attempts to tell you what is
the performance of your investment portfolio over time that you held
it. This is also commonly called "return on the first dollar
invested".

Good description of differences between IRR and TWR can be found in
[this article](http://www.merriman.com/investing-101/performance-time-weighted-return-vs-internal-rate-of-return/),
and in Investopedia (check references below).

## Choosing a period

I found it most helpful to have rate of return reported for the whole
period of investment and (if you invest for sufficiently long period)
also have yearly numbers to be able to see whether you had "good year"
or "bad year".

Luckily, simply adding `-Y` to your `hledger roi` gives you yearly breakdown.

## Let automate this

Assuming that you record your valuations manually, you will only need
to recompute investment report when your top-level yearly report files
change (but not necessary when files included from them change). This
could be encoded in `export.hs`, with actual report generation
delegated into a separate script.

I refer you to
[07-investments-easy-approach](../tree/master/07-investments-easy-approach)
or [diffs/06-to-07.diff](../tree/master/diffs/06-to-07.diff), where
I've added a couple of valuation transactions and a script to compute
investment report.

## References

* TWR computations used here are described in a lot of detail on
  http://monevator.com/how-to-unitize-your-portfolio/ 

* Good set of articles on IRR, TWR, value of money, disconting, EAR
  etc on Investopedia:
  https://www.investopedia.com/exam-guide/cfa-level-1/quantitative-methods/default.asp

## Next step

To be continues. Probably mortgages.
