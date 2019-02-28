# Motivation

I know that reading time is a precious commodity. You should probably skim this page to see whether my
principles and practices resonate with you. 

If you do like them - [keep reading](Getting-started). Even if you would not end up using my setup, maybe you will find some useful ideas to adopt.

NB: If you are just starting with hledger, this section could
overwhelm you. If it happens -- skip it entirely for now. Everything contained
here will be introduced in subsequent sections, and you can revisit this page at a later time.

However, if you are experienced (and potentially impatient) hledger user who does
not want to read through the whole wiki or just wants a quick summary, then this section might be all you need.


# Version control everything

Put your journal under version control - this would save you from
yourself, you would be able to make sweeping edits of your journals
(renaming accounts, etc) without fear of messing things up or losing
everything. 

Put all the electronic statements that you've incorporated into your
journal under version control as well. If your bank changes the format
of the statements and new statements happen to be better than the old
ones (more information is available, etc), you would be able to
regenerate all past statements and see what was changed and convince
yourself that numbers will stay the same.

If you generate any reports on a regular basis (balance sheet,
income-expenses, etc) - make scripts to generate them and put them
under version control. Now you can change your journal files however
you like and you would be able to check changes in the reports to
convince yourself that your journal changes are good and complete. 

# Keep source files separate and use !include a lot

When you convert electronic statements, produce one .journal file per
statement converted, put them under version control and !include them
in your journal. Now, if your statements ever change, you would be
able to re-generate corresponding .journal file and aggregate reports
(like balance sheet) and see the changes that happened at every step
of the way. Example use-case would be: you made a mistake collecting
statements and exported same month twice, and now you have re-acquired
one of the statements or manually edited it to remove duplicated rows. 

# Generate a lot of reports automatically after every significant change

Whenever you are done reconciling your accounts (or part of them),
generate a lot of reports. Have a script that does that in "one
click". Keep reports under version control. This way you would be able
to spot weird or out-of-place things that happened after any change to
your journals. 

# Minimize manual entries

Manual entries take time. They would probably take more time and
effort than the rest of the stuff related to keeping your finances in
check, combined. Think about whether you want to track all those cash
expenses. Do you need to? 

For example, all ATM transactions in my bank accounts are done versus
`expenses:misc:cash`, and unless monthly amount there gets out of
check, I never bother detailing them, and I strongly suggest you do
the same -- unless you handle a lot of cash. 

# Split your journals by year

This makes things faster and also allows you to reset your expenses at
a reasonable interval. Hledger and ledger have a helpful `equity`
command to simplify the process, but you can simplify it even further
with some scripting. 

# Refactor and change aggressively

If you have an ability to easily re-generate journal files from all of
your electronic statements, you can tweak CSV import rules to improve
classification of your transactions or change it if current
classification does not suit your need. 

Changing CSV import rules and re-generating .journal files from all
the source statements allows you to be more confident in the result as
compared to mass search and replace over report files. 

# Add more details over time

It is relatively easy to find yourself with a setup where you drop
fresh CSV statements in a directory from time to time, run a couple of
scripts, review changes in a version control system, and you are done
with next batch of reconciliation. 

Freed-up time can be used to get more statements from more sources
that will provide you with a more fine-grained picture. For example,
you might pay off your credit card from your current account every
month and these lump-sum expenses went into `expenses:credit card`,
but now you can consider getting detailed statements from credit card
provider. Now transactions that pay off your outstanding balance can
go to `liabilities:credit card:balance payments` and the credit card
statement could be processed so that credit card spending goes from
`liabilities:credit card` to various `expenses:...` accounts, and
credit card balance payments go from `liabilities:credit card:balance
payments` to `liabilities:credit card`. 

Once you have things in place to process credit card statements, you
can change one CSV conversion rule for the current account that
influences how credit card balance payments are processed, regenerate
all current account .journals and have detailed information about
credit card spendings. 

The same thing could be done with Amazon, Paypal, your pension
account, savings and brokerage accounts, etc - you can start with
treating them as black boxes or expense categories, and then with time
procure statements for them and incorporate them. If everything is
under version control, it would be easy to do that and check that all
aggregate reports react accordingly. 

# Separate source files, manual entries, and reports

* Put all journal files in a single directory with two subdirectories: one
  for the source files from financial institutions (`./import`), and
  one for generated summaries and reports (`./export`).
  
* At the top level there is `all.journal` which is the main entry
  point for all of your financial history. It contains just a bunch
  of `!include` lines

* Next to it are a bunch of `{year}.journal` files that will have: 
  1. `!include` of the opening balances file carrying over your assets/liabilities from the previous year; 
  2. a bunch of `!include`s for all the converted CSV files; 
  3. **all manual transactions that you have for that year**

* Raw CSV files go into `./import/{source}/in`

* Converted CSV files go into `./import/{source}/journal`

* Run `./export.sh` after each change, check the changes to the
  reports generated in `./export`, commit everything religiously.

* When you run `hledger` manually, point it to `all.journal` most of the time.

# But why...?

Why is `all.journal` structured the way it is?

Why per-year files at all?

Do they have to start in January?

What do I do to start a new year?

How do I start from scratch?

What if my current setup is completely different - how do I convert?

I love single wiki page that contains all the answers as much as the
next man. Unfortunately, my attempts to write a single page that fully
describes my setup had failed miserably, and the best I can offer you
is to head to [Getting started](Getting-started) and read the rest
of the wiki - it is not too large, and hopefully, you would be able to
skip over large sections that explain things you already know.
