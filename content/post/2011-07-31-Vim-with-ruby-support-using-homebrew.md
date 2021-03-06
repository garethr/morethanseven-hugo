---
comments: true
created_at: 2011/07/31
date: 2011-07-31T00:00:00Z
excerpt: null
title: Vim With Ruby Support Using Homebrew
url: /2011/07/31/Vim-with-ruby-support-using-homebrew/
---

I've spend a bit of time this weekend cleaning, tidying and upgrading software on my mac. While doing that I got round to compiling my own Vim. I'd been meaning to do this for a while, I prefer using Vim in a terminal to using MacVim, and I like having access to things like [Command-T](http://www.vim.org/scripts/script.php?script_id=3025) which requires Ruby support which the inbuild version lacks.

Vim isn't in Homebrew, because Homebrew's policy is to not provide duplicates of already installed software. Enter [Homebrew Alt](https://github.com/adamv/homebrew-alt) which provides formulas for anything not allowed by the homebrew policy. As luck would have it a [Vim Formula](https://github.com/adamv/homebrew-alt/blob/master/duplicates/vim.rb) already exists. And installing from it couldn't be easier.

    brew install https://raw.github.com/adamv/homebrew-alt/master/duplicates/vim.rb

As it turns out this failed the first time I ran it because I had an rvm installed Ruby on my path. I reset this to the system version and everything compiled fine.

    rvm use system

Note also that it's really quite simple to use a different revision or different flags when compiling. Just download that file, modify it, serve it locally (say with a python one line web server) and point brew install at it. Next step, running off head for all the latest and greatest Vim features.
