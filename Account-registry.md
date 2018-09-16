As time goes and your journal grows, you would create a whole hierarchy of accounts under 'expenses' to keep track of your outgoings. It is very easy to make a typo either in the manual transactions or your CSV import rules and accidentally create new unwanted accounts this way. When you have 100+ accounts it might be easy to miss that you now have 'expenses:grocereis' next to 'expenses:groceries' all of a sudden in your end-of-year balance report.

It is even worse if the erroneous account has transactions during the year but no balance at year-end. Then it would not be even included in the final balance report and could evade scrutiny for a really long time.

Fortunately, hledger provides us with `hledger accounts` command which will print out a sorted list of accounts mentioned in the given journal file. It is very easy to add a rule to export script to generate 'export/<year>-accounts.txt' file - check out branch [05-account-registy](https://github.com/adept/full-fledged-hledger/tree/05-account-registry). Once you put these files under version control you would be able to spot account additions easily and fix any typos. 

Keeping account registry comes especially handy if you decide to rename or split/consolidate accounts, helping you to make sure that you did not leave any stray transactions behind.
