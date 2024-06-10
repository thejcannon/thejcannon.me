---
title: \`ruff\` (and Astral)
subtitle: My thoughts, and others.
---

A lot of people have a lot of opinions about [Astral](https://astral.sh/) (and their accomplishments).
I haven't heard my personal thoughts echoed strongly in anybody else's opinion, so I thought I'd write them
out. Let me know if you feel the same or differently.

# Background

For background, I LOVE Python and I LOVE tools. However, I wasn't the greatest fan of the Python tooling ecosystem.
Like many others, I was puzzled how such a well-loved and popular language accumulated such a community of technology,
but robust, capable, auto-fixing linting wasn't seen at the party.

I dreamed of a unified tool that I could use that not only found mistakes, but fixed them too. At the time I had only
dipped my toe into other ecosystems (like Rust or Javascript). I started the [NI Python Styleguide](https://ni.github.io/python-styleguide/)
with the idea that I'd evaluate every flake8 plugin on Earth and see what rules were worth using. Eventually,
I'd hope to take that list and find ways to auto-fix almost every entry so developers were free to develop code.

I started a personal project which I'd hope would be that tool (I called it `convendjinn`. Aren't I clever)
but never quite found the spark to want to make it a reality.

Just as I was about to start a community call-to-action, [`ruff`](https://docs.astral.sh/ruff/) was released.

# Ruff

> An extremely fast Python linter, written in Rust.

Ruff took the community by storm. It's GitHub star chart is a vertical line compared to other maintstream
Python ecosystem tools. It's praises were sung far and wide. It did everything you wanted, but faster (MUCH faster)!

##  Speed

The most common thing I hear about `ruff` is regarding it's speed. It shouldn't be surprising that if you take
code written in Python that processes a token stream or an AST and then hands the same stream/tree to other code,
also in Python, and write that in Ruff (with performance in mind) you'll end up with something that goes brr.

Compared to `pylint` its bonkers fast (although that's comparing apples to oranges since `pylint` does much deeper
introspection). Compared to `flake8` and friends its extremely fast.

\[Inline thoughts\]: I love that it's fast. I think it needed to be to break through to folks, and it especially
needed to be to make realtime IDE-feedback possible. I don't think it needed to be _this fast_ though to be successful.
(Put another way, I wonder if it could've still been succuessful having been written in Python. More on that later).

## Community

### Author(s)

Some claim that their gravitation towards `ruff` stems not from technical merits, but rather the demeanor of the authors
of the respective tools. That the author of `flake8` had been unhelpful at times, and by comparison the author of `ruff`
was genuinely cooperative.

\[Inline thoughts\]: I think it's fair to say there's more to Software Engineering than just the "physical" code itself. 
Although, I doubt many people switched _solely_ because they felt a certain way about the author, 
but it likely didn't make the decision to switch any harder for themselves.

### IP

There are also those in the community who felt that `ruff` unfairly ~stole~ borrowed the semantics of dozens of `flake8`
plugins without proper accredation (at the time of creation) and without giving back to the ecosystem of tools and 
plugins that made them popular in the first place.

\[Inline thoughts\]: I don't have much here. Part of putting software out in the open source world is you don't control
how it gets used (if it gets used at all).

### Technology

`ruff` is written in Rust, which some lament is not Python. The argument goes that it's harder to contribute to Python
tooling when it isn't in Python.

\[Inline thoughts\]: This one's tough, because its multi-faceted. I don't think this levels of speed would be possible
in Python. I, and others, also lament the lack of a plugin system which I don't think would be 
a problem if implemented in Python. But the bar for entry in certain areas of Python has always been gated by learning
(an)other language: C. It's the C in CPython. So, I don't feel too strongly about it being implemented in Rust. I'm happy
with the benefits, and hope the plugin siuation works itself out (kinda).

### Extra thoughts - Fixing

`ruff` tries very hard not only to report errors, but to automatically fix them as well. This has long been a dream of mine,
since the _only_ thing hard about fixing most problems is formatting, and formatting is solved problem :tm:.

### Extra thoughts - Maturity

I haven't seen this echoed anywhere else, but honestly one of the things I love _most_ about `ruff` has nothing to do with
the above. It's that `ruff` feels like a well-rounded, complete, "ecosystem"-focused tooling. It's the whole package.
I don't have to hunt for "awesome" plugins, which may-or-may-not be maintained, or support my version of Python, or have
good configuration. `ruff` has everything in one place. _This_ is the killer feature of `ruff`.

### Extra thoughts - Plugins

Because of the above, I'm torn about plugins. I want my own home-grown plugins for my personal usage, but don't want
"community" plugins because then `ruff` wouldn't be the one-stop-shop. So, I'm both happy and sad we don't have them.

### Extra extra thoughts - Typechecking?

It's no secret that at this time Astral (more on them later) is working on typechecking implemented in Rust. 
I think that writing your own typechecker is reserved for people or companies who are either crazy or insanely motivated.
As is the case for Microsoft (Pytright, and VS Code by extension), Google (pytype), Meta (Pyre), or the community (mypy).

Normally I would bomoan yet-another-typechecker entering the scene, muddying compatibility and accumulating bug reports.
_However_, I am eager for this particular typechecker, because it solves the very last item on my Python linting checklist.

You see, there are tons of patterns I would like my linter to find (and fix) that can _only_ be found with the amount of context
found in a typechecker. These aren't type errors (and therefore typecheckers aren't in the game of reporting on them), but they
are bugs (or smells). Today's linters can't find them because they lack the rich type-driven context. But a linter/typechecker combo?
_That_ would be the dream.

## Astral itself

"VC" is a three-letter word to some. `ruff` was originally made in a person's free(-ish) time, and the company (Astral)
came later. I think it's disengenuous to paint Astral as some evil, VC-backed plot to take all of our... our... erm...
stuff. The reality is the technology it replaced was maintained by less than a handful of people, volunteering their time
when possible, and who had no real "skin" in the game. Contrast to Astral whose company, right now, kind of relies on
"capturing" the market and producing good tools to keep it that way, I think it's a net positive. Is it ideal? Nah.
But also is the downfall of Python? Also no. I quite like the current state of things and hope it continues.

# Conclusion

I'm very optimistic about `ruff` and Astral. I'm already very pleased that most of the items (fast linting, auto-fixing, mature tooling)
on my Python tooling wishlist have been satisfied, and am eagerly awaiting the last(-ish) item (type-based-linting) to also be
accomplished. I'm not terribly upset or worried about any of the "drama" behind `ruff` or Astral, and I think, as a community
if we didn't want all our eggs in one VC-backed basket, maybe we should've done better beforehand 
(and if was so easy for one tool/company to "take over" what does that say about the state of how things were?).

So, I welcome our new overlords, and wish them all the best in continuing to churn out delicious features.
