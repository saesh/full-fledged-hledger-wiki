> Associated directory:
> [06-maintaining-lots-of-csv-rules](../tree/master/06-maintaining-lots-of-csv-rules)

## Lots and lots of rules

Eventually you will reach a point where your rulefile is so big that
every time you add a new rule, you are faced with a choice: either
search for an existing rule to add new pattern to, or create a new
rule, creating boilerplate of:
```
if
PATTERN
  account2 ...

```
, which is rather wasteful and quite a lot to type. 

My solution to this was to write rules in a simple pipe-separated
file, and have a rule that preprocesses them into format suitable for
hledger.

File would have the following format:
```
PATTERN|account2|comment
```
, where comment could be empty and tailing separator could be omitted. Lines that begin with `#` are
ignored. Pipe was chosen as a separator because it is very unlikely to appear in either pattern, account name or comment.

For example:
```
EMPLOYER INC|imcome:employer
TRANSFER|assets:Lloyds:transfers
WAITROSE|expenses:groceries|weekly shopping
```

Account could also be left blank, in which case generated rule will just add a comment. This could be used to
augment more general rule, for example you can annotate a transaction on particular date:
```
2017-10-11.*EMPLOYER INC||per diem for trip to New York
```

File like that could be easily edited or manipulated in Excel/OpenOffice.

If you adopt a convention of calling this file `import/<source>/rules.psv`, and calling generated file `import/<source>/generated.rules` then it is possible to write a build rule which would automatically conver this file to hledger syntax whenever necessary and redo CSV-to-journal conversion when you add or modify rules.

See the changes in [06-maintaining-lots-of-csv-rules](../tree/master/06-maintaining-lots-of-csv-rules).

## Next steps

To be continued
