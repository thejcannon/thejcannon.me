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
so only via conventions? What are the conventions, and who isn't following them?

Well, that's exactly what I needed to find out for my hobby project, so hop in! Come along!

# The plan

## Step 1: We need data

Hugo van Kemenade, of Python fame, has [this list](https://hugovk.github.io/top-pypi-packages/) 
of the top 8000 most downloaded packages on PyPI, updated monthly. That was easy (Thanks Hugo!).

PyPI has [a nice simple API](https://wiki.python.org/moin/PyPISimple) (no quite literally) for
gettings links to downloadables for a package.

`pip` wants to extract METADATA out of wheels (which are just zips) without downloading the 
entire thing, so it has some [clever code](https://github.com/pypa/pip/blob/main/src/pip/_internal/network/lazy_wheel.py)
for doing "range requests" to only fetch a fraction of the bytes. (For tarballs, we're out of luck).

Swirl all that in a big pot, and voila! You can quickly scrape PyPI to get each package's filenames.

## Step 2: That was too easy, let's add some complexity

Since getting data was kinda easy, the universe has evened things out by making analyzing that data
in a useful way kinda hard.



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

