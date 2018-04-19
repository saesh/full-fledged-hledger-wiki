# Version control everything

Put your journal under version control - this would save you from yourself, you would be able to make sweeping edits of your journals (renaming accounts, etc) without fear of messing things up or losing everything.

Put all the electronic statements that you've incorporated into your journal under version control as well. If your bank changes the format of the statements and new statements happen to be better than the old ones (more information is available, etc), you would be able to regenerate all past statements and see what was changed and convince yourself that numbers will stay the same. 

If you generate any reports on the regular basis (balance sheet, income-expenses, etc) - make scripts to generate them and put them under version control. Now you can change your journal files however you like and you would be able to check changes in the reports to convince yourself that your journal changes are good and complete.

# Keep source files separate and use !include a lot

When you convert electronic statements, produce one .journal file per statement converted, put them under version control and !include them in your journal. Now, if your statements ever change, you would be able to re-generate corresponding .journal file and aggregate reports (like balance sheet) and see the changes that happened at every step of the way. Example use-case would be: you made a mistake collecting statements and exported same month twice, and now you have re-acquired one of the statements or manually edited it to remove duplicated rows.

# Generate a lot of reports automatically after every significant change

Whenever you are done reconciling your accounts (or part of them), generate a lot of reports. Have a script that does that in "one click". Keep reports under version control. This way you would be able to spot weird or out-of-place things that happened after any change to your journals.

# Minimize manual entries

Manual entries take time. They would probably take more time and effort than the rest of the stuff related to keeping your finances in check, combined. Think whether you want to track all those cash expenses. Do you need to?

Any ATM transactions in my bank accounts are done versus `expenses:misc:cash`, and unless monthly amount there gets out of check, I never bother detailing them, and I strongly suggest you do the same -- unless you handle a lot of cash.

# Split your journals by year

This makes things faster and also allows you to reset your expenses at a reasonable interval. Hledger and ledger have helpful `equity` command to simplify the process, but you can simplify it even further with some scripting.

# Refactor and change aggressively

If you have an ability to easily re-generate journal files from all of your electronic statements, you can tweak CSV import rules to improve classification of your transactions or change it if current classification does not suit your need.

Changing CSV import rules and re-generating .journal files from all the source statements allows you to be more confident in the result as compared to mass search and replace over report files.

# Add more details over time

It is relatively easy to find yourself with a setup where you drop fresh CSV statements in a directory from time to time, run a couple of scripts, review changes in version control system, and you are done with next batch of reconciliation.

Freed-up time can be used to get more statements from more sources that will provide you with a more fine-grained picture. For example, you might pay off your credit card from your current account every month, and these lump-sump expenses went into `expenses:credit card`, but now you can consider getting detailed statements from credit card provider. Now transactions that pay off your outstanding balance can go to `liabilities:credit card:balance payments` and credit card statement could be processed so that credit card spending goes from `liabilities:credit card` to various `expenses:...` accounts, and credit card balance payments go from `liabilities:credit card:balance payments` to `liabilities:credit card`.

Once you have things in place to process credit card statements, you can change one CSV conversion rule for the current account that influences how credit card balance payments are processed, regenerate all current account .journals and have detailed information about credit card spendings.

The same thing could be done with Amazon, Paypal, your pension account, savings and brokerage accounts, etc - you can start with treating them as black boxes or expense categories, and then with time procure statements for them and incorporate them. If everything is under version control, it would be easy to do that and check that all aggregate reports react accordingly.