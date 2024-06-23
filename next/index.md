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

You install `requests` and import `requests`. You install `typing-extensions` and import
`typing_extensions`. You install `python-dateutil` you import `dateutil` (I guess it isn't
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

So, of the 7994 packages (6 of which failed to scrape due to one weird reason or another) scraped...

### Common prefixes

The most common prefixes[^1] are:

```
setup|629
tests|221
test|86
docs|56
examples|37
src|14
doc|13
docs/source|12
scripts|11
azureml|10
```

However, this is slightly misleading since `setup` would be the prefix of _most_ source distributions.
So if we re-ran the query sticking to just wheels:

```
tests|114
test|35
docs|15
examples|11
azureml|10
opencensus|9
sphinxcontrib|6
backports|5
benchmarks|5
scripts|5
```

Which is slightly annoying that installation of 200-ish packages might take "place" of the top-level `tests` or `scripts` module name,
depending on how I structure my project.

For the rest of the statstics, we'll pretend the following prefixes don't exist: `setup`, `test`, `tests`, `doc`, `docs`, `examples`, `benchmarks`, `scripts`. And
any sdists with `src/` will have the prefix stripped.

### Conventions

- 3330 packages are identified by a prefix which is the normalized package name with hyphens replaced with a dot.
   - E.g. (`requests` -> `requests`, or `zope-browser` -> `zope.browser`)
   - 3089 of those packages are _uniquely_ identified by that single prefix. (Meaning they have no other prefix).
     - E.g. `pytest`-the-package is identified by `pytest`-the-module, so it meets criteria one. But `pytest`-the-package
       also has the prefix of `_pytest` (anf `py`), so it doesn't meet criteria two.
- Similarly, if we replaced hyphens with path separators we'd add another 381, and hyphens with underscores adds 2166.
  (if you were wondering which was more popular).

So that leaves us with 2119 packages that don't follow the simple convention.



---

[^1]: Query
   ```sql
   SELECT pp.prefix, COUNT(*) as count
   FROM package_prefixes pp
   JOIN packages p ON pp.package_name = p.package_name
   WHERE p.url LIKE '%.whl'
   GROUP BY pp.prefix
   ORDER BY count DESC
   LIMIT 10;
   ```

[^2]: Query
   ```sql
   SELECT pp.prefix, COUNT(*) as count
   FROM package_prefixes pp
   JOIN packages p ON pp.package_name = p.package_name
   WHERE p.url LIKE '%.whl'
   GROUP BY pp.prefix
   ORDER BY count DESC
   LIMIT 10;
   ```

[^3]: Query
   ```sql
   SELECT COUNT(*) as matching_packages
   FROM packages p
   JOIN package_prefixes pp ON p.package_name = pp.package_name
   WHERE pp.prefix = p.package_name;
   ```

[^4]: Query
   ```sql
   WITH matching_packages AS (
       SELECT p.package_name
       FROM packages p
       JOIN package_prefixes pp ON p.package_name = pp.package_name
       WHERE pp.prefix = p.package_name
   ),
   prefix_counts AS (
       SELECT package_name, COUNT(*) as prefix_count
       FROM package_prefixes
       GROUP BY package_name
   )
   SELECT COUNT(*) as single_prefix_packages
   FROM matching_packages mp
   JOIN prefix_counts pc ON mp.package_name = pc.package_name
   WHERE pc.prefix_count = 1;
   ```
