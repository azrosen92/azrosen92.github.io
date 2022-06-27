---
layout: post
title: "Navigating repos with different \"main\" branches"
date: 2022-06-27 11:07:34
categories: development tools git
---

There's been a trend across the industry to start using a `main` branch instead of
`master`. There are a lot of reasons _to_ follow this trend having to do with inclusivity.
I'm not going to go into those reasons in this post, I'm hoping that by reading this, I
can assume you are aware of and generally in agreement with those reasons. What I'd like to
talk about, is how I made this transition super easy for myself, given that I'm not always
working in projects that use the `main` branch. For the immediate future, I expect to have
to switch back and forth between projects that use `master` and those that use `main`. I
recognize that a lot of organizations and open source projects are making a strong effort
to make this transition, but as with anything in technology and engineering, it will take time.
During this transition period how do I make it easy for myself to switch back and forth between
projects using the two different "main" branch names.

For the past 4 years or so, I've maintained a `config` repo. This repo follows me around from job
to job, laptop to laptop, and allows me to quickly set up a local dev environment in which I'm productive
on most projects I work on. I have a number of git aliases, bash aliases, convenience scripts, and other
configurations that I consider project-agnostic in this repo, and my goal is for everything to continue
working as usual, regardless of the "main" branch being used in a project. I thought I'd leverage my `config`
repo to create some simple tooling which would allow me to easily switch back and forth between repos with a
`main` branch and those with a `master` branch.

The first thing I had to do was to make sure that my machine knows which branch a repo is using. After a
bit or research, I settled on the following one-liner.

```
~$ git symbolic-ref refs/remotes/origin/HEAD | cut -d'/' -f4
```

I wanted this to run each time I `cd` into a directory that contains a git repo, which was easily set up
by overloading my machine's `cd` command as

```
set_main_branch() {
  if [ -d .git ]; then
    export GIT_MAIN=$(git symbolic-ref refs/remotes/origin/HEAD | cut -d'/' -f4)
  fi
}

cd() {
  builtin cd "$@"

  set_main_branch
}
```

And then using `$GIT_MAIN` in place of `master` in all of my git alias's.

For example

```
[alias]
  update = !"git co $GIT_MAIN && git pull && git co - && git rebase $GIT_MAIN"
  com = !"git checkout $GIT_MAIN"
```

This may seem trivial, but it took a couple of iterations before I got to a solution that I was happy with.
My first try at this, looked something like

```
~$ git remote show origin | grep "HEAD branch" | sed 's/.*: //'
```

I immediately noticed a disruptive lag each time I switched directories, sometimes more than 1 second long.
Without too much knowledge about what `git remote show` does, it's obvious that there's an http call happening
in order to get the information I need. Some further digging into the docs, reveals that there's an optional
`-n` flag that can be used to fetch the remotes from cache. `git symbolic-ref`, on the other hand, uses the local
file structure within the `.git/` directory to print out the available local branches on the machine, saving the
round trip http request. We pretty quickly get a result like this

```
~$ git symbolic-ref refs/remotes/origin/head
refs/remotes/origin/main
```

Now comparing the two side by side, we can see more than 100x improvement in speed!

```
~$ time git symbolic-ref refs/remotes/origin/HEAD | cut -d'/' -f4
master

real	0m0.012s
user	0m0.004s
sys	0m0.008s

~$ time git remote show origin | grep "HEAD branch" | sed 's/.*: //'
master

real	0m1.541s
user	0m0.212s
sys	0m0.131s
```

It's worth noting that I could have also used `git remote show` with the `-n` flag. But when timing this, it ended up being
about 3 times slower than `git symbolic-ref`.
```
~$ git remote show origin -n | grep "HEAD branch" | sed 's/.*: //'
```

With this setup, I hardly notice when changing directories. My environment is automatically set up to work with
whatever the maintainers of a git repo have chosen to use as their "main" branch. And all of my git aliases
and workflows continue to work as they did before, without any distracting context changes.
