# Goals

I wanted a setup that would enable me to have:

- Avoid spending too much time, effort and manual work on tracking expenses

- Eventual consistency: even if you can't record something precisely right now, maybe I would be able to do it later

- Ability to refactor. I want to be able to go back and change how I am doing things, with as little effort as possible and without fear of irrevocably breaking things.

You could grow a setup that would allow you to have all of this and more step by step. This repository contains a number of branches (`01-getting-started`, `02-getting-data-in`, ...) that represent a gradual evolution of the setup, starting from the bare minimum and adding one new feature at a time. This allows you to choose the starting point that is more suitable for you or compare/diff various setups and see what exactly has been changed at every step.

# Reading order

If you are fairly experienced with ledger, hledger, beancount or any other plain-text accounting tool, you might want to quickly check out [Directory structure](Directory-structure) to see how things are laid out and then head directly to the most featureful branch and look around to see how everything is done. Run `./export.sh` to generate all reports.

Alternatively, read the full guide:
1. [Directory structure](Directory-structure)
2. [Getting started](Getting-started)
3. [Getting data in](Getting-data-in)
4. [Getting full history of the account](Getting-full-history-of-the-account)
5. [Adding more accounts](Adding-more-accounts)