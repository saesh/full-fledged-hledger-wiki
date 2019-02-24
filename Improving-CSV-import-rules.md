> Associated directory:
> [05-improving-csv-import-rules](../../tree/master/05-improving-csv-import-rules)

If you follow along with the evolution of the sample directory, you
know that currently all expenses are being sent in a sinlge top-level
account called `expenses:unknown`. This is obviously not good enough. 

Moreover, if you have just imported several years of transactions, the
prospect of sorting them all out does not look appealing (and looks
very time consuming).

But remember: the goal is *eventual* consistency. You can do as much
(or as little) as you want at any point in time, and continue to
improve them over time in small increments.

## Known unknowns

Lets start with creating a record of all unsorted transactions and
work on reducing this list to nothing.

This sounds like another report to generate, and it will take just a
couple of lines to add it to `export.sh`:
```
@@ -29,6 +29,7 @@ transactions      y = y++"-all.journal"
 income_expenses   y = y++"-income-expenses.txt"
 balance_sheet     y = y++"-balance-sheet.txt"
 cash_flow         y = y++"-cash-flow.txt"
+unknown           y = y++"-unknown.journal"
 -- which accounts to include in opening/closing reports
 open_close_account_query = "assets|liabilities|debts"
 closing_balances  y = y++"-closing.journal"
@@ -42,6 +43,7 @@ reports =
          , [ income_expenses      (show y) | y <- all_years ]
          , [ balance_sheet        (show y) | y <- all_years ]
          , [ cash_flow            (show y) | y <- all_years ]
+         , [ unknown              (show y) | y <- all_years ]
          , [ opening_balances     (show y) | y <- all_years, y/=first ]
          , [ closing_balances     (show y) | y <- all_years, y/=current ]
          ]
@@ -79,6 +81,8 @@ export_all = do
 
   (cash_flow "//*") %> hledger_process_year year_inputs ["cashflow","not:desc:(opening balances)"]
 
+  (unknown "//*") %> hledger_process_year year_inputs ["print", "unknown"]
+
   (closing_balances "//*") %> generate_closing_balances year_inputs
 
   (opening_balances "//*") %> generate_opening_balances year_inputs

```

Run `./export.sh`, and now you can start working through all the
`*-unknown.journal` files in the `export` directory, adding rules into
CSV rulefile (`lloyds.rules` in my example) to sort them into
appropirate accounts. You should also be able to detect when import of
new files creates new transactions in `expenses:unknown` or
`income:unknown`, at which point you might categorize them.

## Account registry

As time goes and your journal grows, you would create a whole
hierarchy of accounts under `expenses` to keep track of your
outgoings. It is very easy to make a typo either in the manual
transactions or your CSV import rules and accidentally create new
unwanted accounts this way. When you have 100+ accounts it might be
easy to miss that you now have `expenses:grocereis` next to
`expenses:groceries` all of a sudden in your end-of-year balance
report. 

It is even worse if the erroneous account has transactions during the
year but no balance at year-end. Then it would not be even included in
the final balance report and could evade scrutiny for a really long
time.

Fortunately, hledger provides us with `hledger accounts` command which
will print out a sorted list of accounts mentioned in the given
journal file. Lets add another rule to `export.hs` to
generate `export/<year>-accounts.txt` file - check out
[05-improving-csv-import-rules](../../tree/master/05-improving-csv-import-rules)
or [diffs/04-to-05.diff](../../tree/master/diffs/04-to-05.diff).

Once you put these new reports under version control you would be able to
spot account additions easily and fix any typos. 

Keeping account registry comes especially handy if you decide to
rename or split/consolidate accounts, helping you to make sure that
you did not leave any stray transactions behind.

## Next steps

To be continued
