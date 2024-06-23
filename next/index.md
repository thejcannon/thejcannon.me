---
title: "What's in a name?"
excerpt: "Inspecting filenames of PyPI packages."
---

# Background

Naming is important (as well as hard). Good names are good. Bad names are... bad.
And we name a lot of things. In Python (a name) code is budled and uploaded as a package 
(another name), usually to the Python Package Index (PyPI, a third name). Inside of these
packages is code. Most commonly, Python code (but they truly can contain anything).
The only relationship between the package name and the module name(s) is the same as the 
relationship as between a cat's name and its behavior. _Usually_ "fluffy" is a fluffy cat,
but it's a convention. "Cupcake" could be the name of a very very naughty kitten.

You install `requests` and import `requests`. You install `typing-extensions` and install
`typing_extensions`. You install `python-dateutil` you install `dateutil` (I guess it isn't
`python_dateutil` because that would imply you could import `go_dateutil` and that's a can
of worms the author(s) didn't want to open). But, again, this is merely a convention.

So let's say you wanted to map module names to package names? How well would you be doing
so only via conventions? How unique are modules? What are the conventions, and who isn't following them?

Well, that's exactly what I needed to find out for my hobby project, so hop in! Come along!

# The plan

## Step 1: We need data

Hugo van Kemenade, has [this list](https://hugovk.github.io/top-pypi-packages/) 
of the top 8000 most downloaded packages on PyPI, updated monthly. That was easy (Thanks Hugo!).

PyPI has [a nice simple API](https://wiki.python.org/moin/PyPISimple) (no quite literally) for
gettings links to downloadables for a package.

`pip` wants to extract METADATA out of wheels (which are just zips) without downloading the 
entire thing, so it has some [clever code](https://github.com/pypa/pip/blob/main/src/pip/_internal/network/lazy_wheel.py)
for doing "range requests" to only fetch a fraction of the bytes. (For tarballs, we're out of luck).

Swirl all that in a big pot, and voila! You can quickly scrape PyPI to get each package's filenames.

## Step 2: That was too easy, let's add some complexity

Since getting data was kinda easy, the universe has evened things out by making analyzing that data
in a useful way kinda hard. That's because of two reasons:

1. Source distributions (sdists, as opposed to binary ones, bdists) go through a build process. That means there is
   only a loose relationship between the files inside them and the files that would be inside a built
   distribution (part of that that build process could be moving, moving, or creating files).
2. Namespace packages. Namespaces might've been "one honking great idea" but namespace _packages_
   are usually misunderstood and a honking painful thing to have to remember.

The solution to 1. is easy, just assume the files in the sdist exist in the bdsit as-is.

The solution to 2. is annoyingly complex. Namespace packages come in two forms:

1. Implicit namespace packages. These are the reason you can `mkdir foo`, then `import foo` even though
   there's no `__init__.py` in it. Any directory can be imported without a `__init__.py` and is treated as
   an _implicit_ namespace packages. That's a daily annoyance for me, but in this case its actually preferable.
2. Explicit namespace packages. These have a `__init__.py` with one or two magic incantations that basically say
   "I'm a namespace". And they can't/shouldn't have much more.

Because of 2., if I was to try and find what common "prefixes" a package has by simplying looking at filenames,
both `opencensus` and `opencensus-context` and `opencensus-ext-azure` would all claim `opencensus`.

So, for any `__init__.py` whose path shows up in more than one package, we need to see if it contains one of the
magic incantations.

## Step 3: Let's have fun





(Enable tooltips for the SQL?)

- 665 of them don't have wheels
  - 108 of which released this year
  - 5 of which had a latest sdist of not tarball or zip (1 `.msi`, 3 `.exe`, and 1 `.tgz`)
- 1 did have wheels, but they ended with `.exe` and `.egg`, so not wheels. 
- of the wheels:

```
SELECT COUNT(DISTINCT(p.url))
FROM packages p
JOIN filenames f ON p.package_name = f.package_name
WHERE p.url LIKE '%.whl'
  AND f.filename LIKE 'src%'
```

  - 2 packages have `src/` files (1 unintentionally, 1 intentionally)
  - 222 packages have `test/` files
  - 153 packages have `tests/` files
  - 49 packages have `doc/` files
  - 25 packages have `docs/` files
  - 24 packages have `python/` files


- 3396 have duplicate `__init__.py` filepaths

- Of the not-a-namespace-duplicates
  - `tests/__init__.py` (112) and `test/__init__.py` (34) are the most common
  - `exmaples/__init__.py` comes with 10 hits

