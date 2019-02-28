> Associated directory: [02-getting-data-in](../tree/master/02-getting-data-in)

## Downloading files

The easiest place to start is with your main account. Normally it would be the account that you use to fund most of your day-to-day expenses. It is probably a checking/debit/credit card account at the bank of your choice. If you have several of them -- just pick one for now, we will deal with the rest shortly.

You would want to get CSV statements for your account covering a
reasonable period of time. You can start small: get a statement for
the last month or a calendar/fiscal year-to-date. You can always
import older statements later (this would be covered in later
chapters). For now, let's save that single statement in
`./import/<institution name>/in`. For example, my current account was
once with Lloyds and so their statements will go into
`./import/lloyds/in/{filename}.csv`. From now on I will keep using Lloyds as an example.

## Conversion scripts

Quite often these CSV files would not be directly suitable for
hledger's CSV import facility. You might want or need to do some data
scrubbing. Let's say that in our example I want to remove extra
spaces from the description field and replace numeric account names
with human-readable ones -- even though it is not necessary to import
my sample file, I want to demonstrate how clean-up script could be
written and ran. Lets place the clean-up script in `./import/lloyds/in/in2csv`:
```
#!/bin/bash
sed -e 's/  +/ /g; s/,99966633,/,assets:Lloyds:current,/' < "$1"
```

Then you need to write conversion rules for hledger's CSV import
command (place them in `./import/lloyds/lloyds.rules`) and a script
that can use these rules to print out converted `journal` file. Place
it in `./import/lloyds/in/csv2journal`. Usually, it is as simple as
this:
```
#!/bin/bash
hledger print --rules-file lloyds.rules -f "$1"
```

## Automating the conversion

Build rules (from `./export/export.hs`) will automatically invoke
conversion scripts if you call them `in2csv` and `csv2journal` and
indicate that you want conversion for this particular directory --
this is better than automatically try to discover and convert all the
`in/*.csv` files in the `import` directory.

Let's enroll our `lloyds` directory into automatic conversion by
editing `export/export.hs`:
```
   -- Enumerate directories with auto-generated cleaned csv files
   [ "//import/lloyds/csv/*.csv" ] |%> in2csv
 
   -- Enumerate directories with auto-generated journals
   [ "//import/lloyds/journal/*.journal" ] |%> csv2journal

```

`Shake` operate with absolute pathnames, so the path to `import` in the
example above starts with `//` to indicate that `import` could be anywhere
on your disk.

Your conversion scripts will run from the directory where they
reside, so you could refer to other files (like `lloyds.rules`) by
their relative names.

## Initial Balances

Most probably your account was not opened in 2017. If your account
already had some balance on 1st Jan 2017, you will need to record this
in your journal. As with all other manual transactions, you will put
this in the top-level yearly file, in this case, it is `2017.journal`: 
```
;; Opening balances
2017/01/01 opening balances
  assets:Lloyds:current    = £100
  equity:opening balances
```

Make sure you use `equity:opening balances` as this is an account that
`hledger equity` uses to generate end-of-year equity carry-over
transactions (we will cover them later). 


## Tying it all in

Finally, everything is in place to convert the downloaded file
and to `.journal`.

Here is a crucial bit: instead of copying the contents of that file
into your journal for the current year, lets just `!include` it, so now your
 `2017.journal` will look like this:
```
;; Opening balances
2017/01/01 opening balances
  assets:Lloyds:current    = £100
  equity:opening balances
  
!include ./import/lloyds/journal/99966633_20171223_1844.journal
```

Now you can run `./export.sh`. This will automatically run `in2csv`
and `csv2journal` scripts with the right set of arguments and
regenerate all your reports. If you are keeping reports under version
control you should be able to diff them against the previous version
and see exactly what has changed where at a glance.

You will notice that import rules put all expenses in the single
`expenses:unknown` account. That's fine, let's assume that you do not have
time to sort them out just now, we will do this later.

## Recap

I've included a sample data file and conversion scripts in
[02-getting-data-in](../tree/master/02-getting-data-in):
```
import
└── lloyds
    ├── in
    │   └── 99966633_20171223_1844.csv       - sample of the original downloaded file
    ├── csv
    │   └── 99966633_20171223_1844.csv       - cleaned up file, ready to be consumed by
    │                                          'hledger print'
    ├── journal
    │   └── 99966633_20171223_1844.journal   - generated journal
    ├── in2csv                               - conversion script to produce ./csv/*
    ├── csv2journal                          - conversion script to produce ./journal/*
    └── lloyds.rules                         - CSV conversion rules
```

The full list of changes done from the previous step could be seen in
[diffs/01-to-02.diff](../tree/master/diffs/01-to-02.diff). There is a
single bit of change there not covered so far: CSV conversion rules in
`lloyds.rules` are added as a dependency for autogenerated Lloyds
journals so that changes in the rules files will cause all journals to
be regenerated:
```
extraDeps file
  | "//lloyds//*.journal" ?== file   = ["lloyds.rules"]
  | otherwise = []

```

Build rules automatically add `in2csv` and `csv2journal` as
dependencies to the files they produce, so any changes in those
scripts will cause all relevant outputs to be regenerated.

## Next steps

Now that you have converted a single statement, let's get the full history
of a single account, [save and convert all of them](Getting-full-history-of-the-account).

