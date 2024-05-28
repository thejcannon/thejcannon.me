---
title: My thoughts on ruff (and now `uv`)
subtitle: I have thoughts, and they aren't what you think (I think).
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

Just as I was about to start a community call-to-action, [`ruff`](https://docs.astral.sh/ruff/) was released.

# Ruff

> An extremely fast Python linter, written in Rust.

Ruff took the community by storm. It's GitHub star chart is a vertical line compared to other maintstream
Python ecosystem tools. It's praises were sung far and wide. It did everything you wanted, but faster (MUCH faster)!

## Reception - Speed

The most common thing I hear about `ruff` is regarding it's speed. It shouldn't be surprising that if you take
code written in Python that processes a token stream or an AST and then hands the same stream/tree to other code,
also in Python, and write that in Ruff (with performance in mind) you'll end up with something that goes brr.

Compared to `pylint` its bonkers fast (although that's comparing apples to oranges since `pylint` does much deeper
introspection). Compared to `flake8` and friends its extremely fast.

## Reception - Community

Some claim that their gravitation towards `ruff` stems not from technical merits, but rather the demeanor of the authors
of the respective tools. That the author of `flake8` had been unhelpful at times, and by comparison the author of `ruff`
was genuinely cooperative.

There are also those in the community who felt that `ruff` unfairly ~stole~ borrowed the semantics of dozens of `flake8`
plugins without proper accredation (at the time of creation) and without giving back to the ecosystem of tools and 
plugins that made them popular in the first place.



