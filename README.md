# Python for Robotics

HBRC Presentation

* Wayne Gramlich
* November 20, 2019
* Talk URL:https://github.com/waynegramlich/python_for_robotics

## Abstract

Since Python 2 is "going away" soon, we explore the new features are
available for Python 3 that can improve our Python code for robotics.
This talk is heavy on Python 3 and pretty light on the robotics.  The
goal is show how to improve the quality and shareability of Python
code for robotics.

## Why Python?

* First language for many programmers
* Cross-platform (Windows, MacOS, Unix*)
* Huge set of libraries
* Robotics:
  * Robot Operating System (ROS)
  * MicroPython for low level control
  * Image processing (OpenCV)
  * AI Frameworks

## Goals

* We need way more ROS robots at HBRC
* Frankly, we need an alternative platform to Neato
  * Staring HBRC Ros Robot (HR<Sup>2</Sup>)
* HBRC/SV ROS needs to ROS classes
* We need to start sharing some ROS code

## What happened between Python 2 and Python 3?

* integers are infinite precision
* `print` is now a function and not a statement
* Unicode strings finally happened
* Formatted strings were added (e.g. `f"The value = {value}"`)
* `dict` `values()` and `keys()` methods return iterators (not lists)
* `file` type upgraded to `io` library
* Exceptions changed (but not that much)
* `pathlib` was added for cross platform file names
* Classes reworked:
  * `object` is the root super class
  * Use `super().__init__(...)` to initialize super class
* `asyncio` library for coroutines happened (not covered by this talk)
* Type hints started to happen (big topic covered by this talk)

## Broad Topics Outline

The following topics are covered in this talk:

* Modules/Projects/"Librares"
* Virtual environments
* Linting tools
* Debugging
* Testing

## Modules/Packages/"Libraries"

Some definitions first:

* Module => file ending in `.py`
* Package => directory of modules with `__init__.py` present
* "Library" => directory with `setup.py` present
* "Library" are installable with `pip` and `easy_install`
* "Library" are uploadable to `pypi.org`

## Example "Library" Directory Structure

A typical "library" directory is organized a follows:

        library_name/        # library directory
          README.md          # Put some documentation here
          LICENSE            # Pick one, it matters!
          MANIFEST.in        # File insert/remove
          develop.rec        # Packages required for development
          setup.py           # Described below
          package_name/      # Frequently matches the module_name
            __init__.py      # Can be empty
            *.py             # Modules that implement functionality
            package_name.rec # Packages required for package
            data/            # Private data directory (optional)
          tests/             # Unit tests directory (optional)
            test_*.py        # Unit tests

## Virtual Environments:

Virtual environments allow for experimentation:

* Allow local installation of projects via `pip`
* Allow different versions of Python
* Projects are uploadable to `PyPi.Org`
* Standard python virtual environments:
  * See `https://realpython.com/python-virtual-environments-a-primer/`
    * `sudo pip install virtualenvwrapper`
    * Edit `~/.bashrc` and then `source ~/.bashrc` .
    * `mkvirtualenv ... ENVNAME  # Create new Virt. Env.`
    * `workon ENVNAME            # Select Virt. Env.`
    * `deactivate                # Deactivate Virt Env.`
* Ananconda (`https://anaconda.com/`) is a common alternative
        
## Required packages file (`*.rec`):

It is useful to keep a list of packages needed for your code and
your code development.  For example `develop.rec` could look as follows:

        # Code requirements:
        flake8
        mypy
	pudb
        pydoc
        pydocstyle
	pytest

To install packages:

        pip install -r develop.rec

## `setup.py` Template

There is a lot going on the code snippet below:

        import setuptools
         
        setuptools.setup(
            author="YOUR_NAME",
            author_email="YOUR_EMAIL",
            classifiers=[
                "Programming Language :: Python :: 3",
                "License :: OSI Approved :: MIT License",
                "Operating System :: OS Independent",
            ],
            description="DESCRIPTION", # Frequently read from disk
            entry_points={"console_scripts"=[...]}, # Not in this talk
            include_package_data=True,
            install_requires=["project1", ..., "projectN"],
            license="MIT",
            long_description="""....""", # Frequently read from disk
            long_description_content_type="text/markdown",
            name="PACKAGE_NAME",
            packages=["PACKAGE1", ..., "PACKAGEn"],  # Usually 1 only
            python_requires=">=3.6"
            url="REPOSITORY_URL",
            version="#.#.#")

## Using `setup.py`:

After you have configured `setup.py`, use `pip` as follows:

        cd .../LIBRARY     # Run in the correct Library directory
        pip install .      # Install project into virtual env.
        python -m PACKAGE  # Run your a package (or use console script)

## Linting Tools

Linting tools help detect bugs and find issues:

* `flake8`: Generic problem finder
* `mypy`: Static type checker
* `pydocstyle`: Docstring checker
* `pytest`: Test suite engine (Huge!!!)
* `pylint`: Not covered

## `flake8`

`flake8` finds problems in your code:

* Install and Use:
  * `pip install flake8`
  * `flake8 --max-line-length=NN module.py`
* Generates picky formatting complaints (deal with it!)
* Catches unused variables, imports, etc.
  * Squishes the dreaded assign to misspelled variable bug.
* Many options -- 100+ page manual.

## Type Hints and `mypy`:

`mypy` is *attempt* to do static Python type checking:

* Definitely a work in progress...
* The type hinted code looks much more like C/C++/C#/Java/...
* When extensively used, it tends to find all sorts of bugs!
* Type hints are discussed in PEP 483/484/3107.
* `flake8` kind of chokes a bit on type hints:
  * Work around is to enclose TYPE in quotes (e.g. "TYPE")

To install and use `mypy`:

        pip install mypy
        mypy MODULE.py

## Type Hints Imports

Start with:

        from __future__ import annotations  # 3.7+
        from typing import Any, Callable, Dict, IO
	from typing import List, Optional, Tuple, Type, Union

Some comments about the types from `typing`:

* Simple types are lower case -- `bool`, `float`, `int`, `str`, etc...
* `None` is used instead of `NoneType` type.
* More complex type examples:
  * `Dict[KEY_TYPE, VALUE_TYPE]`
  * `List[TYPE]`
  * `Tuple[TYPE, ...] or Tuple[TYPE1, ..., TYPEn]`
  * `Union[TYPE1, ..., TYPEn]`
  * `Optional[TYPE] # i.e. Union[Type, None]`
  * `Callable` or `Callable[[ARG1,...ARGn],RET1,...,RETn]`

## Type Hinting Class the `__init__` method:

The `__init__` method is kind of special about attributes:

        class CLASS_NAME:
        
            def __init__(self, ...):
                self.attribute1: TYPE1 = VALUE1
                ...
                self.attributeN: TYPEn = VALUEn

`mypy` complains when when you:

* attempt to stuff a different type into a class attribute.
* attempt to stuff a value into new attribute.

## Type Hinting Code:

Example type hinting code snippets:

        # Function declaration:
        def FUNCTION(arg1: TYPE1, ... argn: TYPEn) -> RET_TYPE`:
            variable: TYPE                 # Declares variable` to be of type TYPE
            variable: TYPE = EXPRESSION    # Declares and assigns EXPRESSION to variable
            left: int, right: int = (1, 2) # Syntax error, not allowed.
                                           # Instead declare variables separately

Use `isinstance(variable, TYPE)` cast from a super-class to a sub-class:

        variable1: SUPER_CLASS = super_class_expression
        assert isinstance(SUB_CLASS, variable1)
        variable2: SUB_CLASS = variable1

## A Short Type Hinting Example:

A short type hinting code example is shown below:

        from typing import List

        def factorial(n: int) -> int:
            return 1 if n == 0 else n * factorial(n - 1)

        def main() -> None:
            n: int
            factorials: List[int] = [factorial(n) for n in range(10)]
            n_factorial: int
            for n, n_factorial enumerate(factorials):
                print(f"{n}! = {n_factorial}")

## Docstrings and `pydocstyle`

What are Docstrings?

        def func():
            """Doc String goes here...."""

Install and Use `pydocstyle`:

        pip install pytdocstyle
        pydocstyle YOURMODULE.py

`pydocstyle` will look over your Docstring's and gripe at you.

        pydoc YOURMODULE.py`  # Shows your final docstrings

Note: PEP 8 says to keep Docstring line lengths to 72 characters.
GvR (Guido van Rossum) won't budge on this.

## Python Docstring Styles:

There are multiple doc string formats.  The Google style is reasonable:

        def func(arg1: TYPE1, ..., ArgN: TYPEn) --> RETTYPE:
            """One line summary ending with period.
 
            Multi-line description of function that
            uses full sentences.

            Args:
                arg1 (TYPE1): Description of arg1.
                ...
                argN (TYPEn): Description of argN.

            Returns:
                (RETTYPE) Returns....

            """
            # `pydocstyle` mandates the blank lines.

## Debugging

There are many Python debugger user interfaces.
`pudb` is reasonable:
* `pudb` is a terminal mode debugger user interface
* `pudb` Supports remote debugging of ROS Python Nodes
  * Need to use `ssh` and some magic (hand wave here!)
  * Launch files can modified to automatically do the magic
* To Install and use:

        pip install pudb
        pudb3 your_module.py

* Use Control-X followed by `exit()` to leave.

## Tracing Decorator

Tracing is a useful development tool.

To define a `tracing` decorator:

        # See: https://cscheid.net/2017/12/11/minimal-tracing-decorator-python-3.html
        import inspect
        trace_indent = 0
        def tracing(f):
            sig = inspect.signature(f)
            def do_it(*args, **kwargs):
                global trace_indent
                ws = ' ' * (trace_indent * 2)
                print(f"{ws}ENTER {f.__name__}:")
                for ix, param in enumerate(sig.parameters.values()):
                    print(f"{ws}    {param.name}: {args[ix]}")
                trace_indent += 1
                result = f(*args, **kwargs)
                trace_indent -= 1
                print(f"{ws}EXIT {f.__name__} (returned {result})")
                return result
            return do_it

## Tracing Decorator Usage

Decorate each function to be traced:

        @tracing
        def fib(n):
            return n if n <= 1 else fib(n-1) + fib(n-2)

        print(fib(5))

## Testing:

Unit testing is a actually a pretty good idea:

* Put unit tests in `PROJECT/PACKAGE/tests/test_*.py`
* To install and run:

        pip install pytest   # Do only once
        cd LIBRARY/PACKAGE  # If needed
        pytest --cov=EXECUTABLENAME --cov-report=annote
        grep -h -n "^!" PACKAGE/*.py,cover

* Each unexecuted line starts with '!' and `grep` finds them
* Add `# pragma: no cover` to ignore debugging lines

## Makefile

The following `Makefile` shows how to automate running the tools:

        PY_FILES := File1.py ... FileN.py
	PYL_FILES := ${PY_FILES:%.py=%.pyl}

        .PHONY: all clean test
        
        all: ${PYL_FILES}
        
        clean:
	        rm -f $(PYL_FILES}

        test: all
                pytest --cov=EXECUTABLENAME --cov-report=annote
                grep -h -n "^!" PACKAGE/*.py,cover


        %.pyl: %.py
	    mypy $<
	    flake8 --max-line-length=100 $<
	    pydocstyle $<
            touch $@

## Python Programming Environments

There are some Python Programming environments out there:

* `vscode` (C/C++/C# based) # Really popular these days
* Eclipse (Java Based)
* sh, vim, emacs, etc. # The old standby

## Miscellaneous:

Some wrap up comments:

  * Source code control is a good idea.
    * `git` pretty much dominates right now.
  * Comments: They are a really, really good idea.
  * Markdown:
    * The `README.md` is written in Markdown.
    * Comments can be written in Markdown.  Think about it.
  * MicroPython exists and is open source for lower level processors
    * There are at least 3 forks (main, AdaFruit, Pycopy)
    * `uasyncio` is really important and is constantly changing

## Conclusion

Python 3 has lots of interesting goodies, so let's build some
interesting Python robots!!!

* Talk URL:https://github.com/waynegramlich/python_for_robotics

## Questions?



