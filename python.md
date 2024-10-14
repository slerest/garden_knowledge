## Tools

inspired by this [source](https://stackoverflow.com/a/41573588) 

### PyPI packages not in the standard library:

[virtualenv](https://pypi.org/project/virtualenv/) is a very popular tool that creates isolated Python environments for Python libraries. If you're not familiar with this tool, I highly recommend learning it, as it is a very useful tool.

It works by installing a bunch of files in a directory (eg: `env/`), and then modifying the `PATH` environment variable to prefix it with a custom bin directory (eg: `env/bin/`). An exact copy of the `python` or `python3` binary is placed in this directory, but Python is programmed to look for libraries relative to its path first, in the environment directory. It's not part of Python's standard library, but is officially blessed by the PyPA (Python Packaging Authority). Once activated, you can install packages in the virtual environment using `pip`.

[pyenv](https://pypi.org/project/virtualenv/) is used to isolate Python versions. For example, you may want to test your code against Python 2.7, 3.6, 3.7 and 3.8, so you'll need a way to switch between them. Once activated, it prefixes the `PATH` environment variable with `~/.pyenv/shims`, where there are special files matching the Python commands (`python`, `pip`). These are not copies of the Python-shipped commands; they are special scripts that decide on the fly which version of Python to run based on the `PYENV_VERSION` environment variable, or the `.python-version` file, or the `~/.pyenv/version` file. pyenv also makes the process of downloading and installing multiple Python versions easier, using the command `pyenv install`.

[pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv) is a plugin for `pyenv` by the same author as pyenv, to allow you to use `pyenv` and `virtualenv` at the same time conveniently. However, if you're using Python 3.3 or later, `pyenv-virtualenv` will try to run `python -m venv` if it is available, instead of `virtualenv`. You can use `virtualenv` and `pyenv` together without `pyenv-virtualenv`, if you don't want the convenience features.

[virtualenvwrapper](https://pypi.org/project/virtualenvwrapper/) is a set of extensions to virtualenv (see [docs](https://virtualenvwrapper.readthedocs.io/en/latest/)). It gives you commands like `mkvirtualenv`, `lssitepackages`, and especially `workon` for switching between different `virtualenv` directories. This tool is especially useful if you want multiple `virtualenv` directories.

[pyenv-virtualenvwrapper](https://pypi.org/project/virtualenvwrapper/) is a plugin for pyenv by the same author as `pyenv`, to conveniently integrate `virtualenvwrapper` into `pyenv`.

[pipenv](https://pypi.org/project/pipenv/) aims to combine `Pipfile`, `pip` and `virtualenv` into one command on the command-line. The `virtualenv` directory typically gets placed in `~/.local/share/virtualenvs/XXX`, with `XXX` being a hash of the path of the project directory. This is different from `virtualenv`, where the directory is typically in the current working directory. `pipenv` is meant to be used when developing Python applications (as opposed to libraries). There are alternatives to `pipenv`, such as [poetry](https://python-poetry.org/).

### Standard library:

`pyvenv` (not to be confused with [pyenv](https://virtualenv.pypa.io/en/latest/) in the previous section) is a script shipped with Python 3.3 to 3.7. It was **removed** from Python 3.8 as it had problems (not to mention the confusing name). Running `python3 -m venv` has exactly the same effect as pyvenv.

[venv](https://docs.python.org/3/library/venv.html) is a package shipped with Python 3, which you can run using python3 -m venv (although for some reason some distros separate it out into a separate distro package, such as python3-venv on Ubuntu/Debian). It serves the same purpose as virtualenv, but only has a subset of its features (see a comparison [here](https://virtualenv.pypa.io/en/latest/)). `virtualenv` continues to be more popular than `venv`, especially since the former supports both Python 2 and 3.
