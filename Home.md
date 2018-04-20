# Goals

I wanted a setup that would satisfy three major requirements:

- Tracking expenses should take as little time, effort and manual work as possible

- Eventual consistency should be achievable: even if I can't record something precisely right now, maybe I would be able to do it later, so I should be able to leave things half-done and pick them up later 

- Ability to refactor is a must. I want to be able to go back and change the way I am doing things, with as little effort as possible and without fear of irrevocably breaking things.

You could grow a setup that would allow you to have all of this and more step by step. This repository contains a number of branches ([01-getting-started](../../tree/01-getting-started), [02-getting-data-in](02-getting-data-in), ...) that represent a gradual evolution of the setup, starting from the bare minimum and adding one new feature at a time. This allows you to choose the starting point that is more suitable for you or compare/diff various setups and see what exactly has been changed at every step.

# Reading order

If you are fairly experienced with ledger, hledger, beancount or any other plain-text accounting tool, you might want to quickly check out [Directory structure](Directory-structure) to see how things are laid out and then head directly to the most featureful branch and look around to see how everything is done. Run `./export.sh` to generate all reports.

Alternatively, read the full guide:
0. [Directory structure](Directory-structure)
1. [Getting started](Getting-started)
2. [Getting data in](Getting-data-in)
3. [Getting full history of the account](Getting-full-history-of-the-account)
4. [Adding more accounts](Adding-more-accounts)