# "Full-fledged Hledger" Tutorial

Full-Fledged Hledger is a tutorial on how to setup up hledger to get:
- data split into files by year
- multi-source CSV imports
- range of auto-generated reports
- single script to update all reports when any source file change
- full freedom to evolve and refactor your journals as you see fit
- and more

# What is included?

A set of sample journals and helper scripts that I use together with [hledger](http://hledger.org) for tracking personal finances
and budgeting. It should be easily adaptable to other command-line accounting tools (ledger, beancount, ...).

I went through several different approaches over the course of 10 years, and this is the end result of that journey, complete with "how", "why" and lessons learned. 

# Goals

I wanted a setup that would satisfy three major requirements:

- Tracking expenses should take as little time, effort and manual work as possible.

- It should be easy to work towards eventual consistency. Large and
  daunting tasks (like "I will process 10 years of paper mortgage
  statements" or "I want to import 5 years of paypal payments") should
  not require one big push and perfect planning to finish them.
  Instead I should be able to do them bit by little bit, leaving
  things half-done, and picking them up later with little (mental)
  effort. Eventually my records would be perfect and consistent.

- Ability to refactor is a must. This is the natural extension of the
  previous point. I want to be able to go back and change the way I am
  doing things, with as little effort as possible and without fear of
  irrevocably breaking things. 

I believe that have a setup that allows you to do all that, and more.

# Structure

It is relatively easy to describe *what* I am doing, but it is harder
to describe *why*. The easiest way to to answer all the *why* questions
is to illustrate how one can grow a setup from scratch, gradually
introducing problems and solutions.

This repository contains a number of directories
([01-getting-started](../tree/master/01-getting-started),
[02-getting-data-in](../tree/master/02-getting-data-in), ...) that
represent evolution of the setup, starting from the bare minimum and
adding one new feature at a time. This allows you to choose the
starting point that is more suitable for you or compare/diff various
setups and see what exactly has been changed at every step : diffs
between the steps are in the [diffs](../tree/master/diffs)
directory.

# Suggested reading order

If you are fairly experienced with ledger, hledger, beancount or any
other plain-text accounting tool, you might want to quickly check out
[key principles and practices](Key-principles-and-practices) to see
how things are laid out and then head directly to the most featureful
(most-highly-numbered) directory and take a look around to see how
everything is done. Run `./export.sh` to generate all reports and see
what is being processed and how. If things dont make sense, start at
the beginning, and proceed in the order displayed on the sidebar.
