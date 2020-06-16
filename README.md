# YNAB Sankey Diagrams

Ever wanted to visualize flows between budget categories from month to month? No? Well, I have.
So I wrote this application to generate [Sankey diagrams](https://en.wikipedia.org/wiki/Sankey_diagram)
based on YNAB budget data.

## Why Though?

Two reasons. One: YNAB doesn't keep a record of how budgeted amounts are moved around, and some
especially obsessive folks (namely me) dearly wish they could review that information.
Two: I want to play around with a light frontend project and learn some JS, and this
seemed like as good a project as any.

## Status

This is a super early-stage project. It's based on the
[starter kit](https://github.com/ynab/ynab-api-starter-kit)
provided by YNAB.

My experience is very much not in webdev. My coding experience is in C++/C#/Python/Prolog/etc.
My last Javascript project was cobbled together in the early days of AJAX (remember that?)
So don't expect quick progress; this is a let's-learn-Javascript project first and foremost.

## Goals

The goal is to have a simple webapp which authenticates a user via OAuth, retrieves budget
data for some time period, and generates a Sankey diagram showing budget flows from month to month.
Each node is a category and each stage (i.e. column of nodes) is a month.
(Categories are duplicated between months.)

Although it's not possible to recreate a user's movements of budgeted amounts between categories,
it is possible to construct a set of flows which is in some sense minimal.
In particular, it should be possible to create a Sankey diagram with a minimum number of links
between nodes representing different categories.
To do this, I expect to prioritize links between same-category nodes, and then to prioritize
links between matching inter-month increases/decreases, and finally to create links from the
categories with the largest decreases to those with the largest increases.

Optionally, I might prioritize links between categories within a category group.
Perhaps this can be exposed as an option to the user.

Overall, the application logic is not tremendously complicated. And the tricky UI work
can mostly be handled by YNAB's OAuth interface and Google's
[Sankey library](https://developers.google.com/chart/interactive/docs/gallery/sankey).
I just need to stitch these things together.

## Running the App
I intend to deploy this repo to my personal domain, but during initial development
I'll follow YNAB's guidance: modify the code, run `npm run build`, commit, push.
The app will be live on `https://ChrisCScott.github.io/ynab-sankey/`.

## License

Copyright (c) 2020 Christopher Scott. All rights reserved.
