+++
title = "4 ways to put the version number into setup.py for your Python package"
date = "2014-06-04T08:19:00+08:00"
tags = ["python"]
+++

Assume that we're developing a Python package **pypkg** with a directory structure like this:
```
pypkg
  |+++- pypkg/
  |       |+++- __init__.py
  |
  |+++- setup.py
```

<!--more-->

And we have a statement `__version__ = '5.5.6.6'` in `pypkg/__init__.py`.

So the first way will be just import the module `pypkg` and then call `pypkg.__version__` in the *setup* block in `setup.py`:

```python
# In setup.py
import pypkg
... (skipped)
setup(
	version = pypkg.__version__
)
```

But sometimes we don't want to import the module, so we can setup a regex to match version statement in `__init__.py`, and just search that file for a match:

```python
# In setup.py
def get_version():
    VERSIONFILE = os.path.join('pypkg', '__init__.py')
    initfile_lines = open(VERSIONFILE, 'rt').readlines()
    VSRE = r"^__version__ = ['\"]([^'\"]*)['\"]"
    for line in initfile_lines:
        mo = re.search(VSRE, line, re.M)
        if mo:
            return mo.group(1)
    raise RuntimeError('Unable to find version string in %s.' % (VERSIONFILE,))
# ... (skipped)
setup(
	version = get_version()
)
```

If we want to get all metadatas (e.g. author) not just the version number, we can do it in the same way:

```python
# In setup.py
module_file = open("pypkg/__init__.py").read()
metadata = dict(re.findall("__([a-z]+)__\s*=\s*'([^']+)'", module_file))
# ... (skipped)
setup(
	author = metadata['author']
	version = metadata['version']
)
```

Now we may want to separates the metadata from `pypkg/__init__.py`, say we put the statement `__version__ = '5.5.6.6'` into another file `pypkg/version.py`:

```python
# In setup.py
exec(compile(open('pypkg/version.py').read(), 'pypkg/version.py', 'exec'))
# ... (skipped)
setup(
	version = __version__
)
```
