# Ascending Python Development Guidelines

This document contains Python development guidelines in the following respects:
self-documentation, linting, idiomatic code patterns and commit message format.

## Table of Contents

1. [Self Documentation](#self-documentation)
1. [Linting](#linting)
1. [Idiomatic Code Patterns](#idiomatic-code-patterns)
1. [Commit Message Format](#commit-message-format)

## Self Documentation

Each Python module should contain the following three types of self documentation.

### Module header

The module header should provide a brief description of the _public-facing_ classes and functions of the module,
together with the created and last modified dates and the developer's name.

```python
"""
Description:

Created: mm/dd/yyyy by {DEV_NAME}
Last Modified: mm/dd/yyyy by {DEV_NAME}
"""
```

### Class and function docstring

A docstring is a string literal that occurs as the _first_ statement in a module, function, class, or method definition.
Such a docstring becomes the `__doc__` special attribute of that object.

> All modules should normally have docstrings, and all functions and classes exported by a module should also have
> docstrings. Public methods (including the `__init__()` constructor) should also have docstrings. A package may be
> documented in the module docstring of the `__init__.py` file in the package directory. - PEP 257

- Function docstring

  Each function's body should begin with a triple-quoted docstring that briefly describes what the function does and
  the _important restrictions_ on the arguments if any. The parameters and return value should be type-annotated.
  The "type" of an object is usually its class. Annotation classes for container types such as `List`, `Dict` can be
  imported from the `typing` standard library package. _We only use type annotation for documentation purposes_.
  _The use of `mypy` is up to individual developers_. [Here](https://realpython.com/python-type-checking/) is a guide
  to Python type annotation.

  Most IDEs integrate this type of docstrings and type annotations into its IntelliSense or "Show Hover" feature,
  thus helping the developers that _call_ the function. It's also a good aid to maintainers of the code base.

  For example:

  ```python
  from typing import List

  def example_function(meaningfully_named_list: List[str], easily_understandable_int: int) -> str:
      """
      Brief description of the function.
      """
      ...
  ```

- Class docstring

  Each _method_ of a class should have its own "function docstring". Immediately after the line of the `class` keyword,
  there should be a "class docstring" that briefly describes what the class is for. Immediately after the class
  docstring, there should be type annotations for class attributes _and then_ instance attributes. Class attributes'
  types should be enclosed with `ClassVar`.

  For example:

  ```python
  from typing import ClassVar

  class SomeClass:
      """
      Brief description of the class.
      """
      class_attr: ClassVar[str]
      instance_attr: int

      def __init__(self, value: int) -> None:
          """
          Brief description of the method.
          """
          self.instance_attr = value

      @classmethod
      def set_class_attribute(cls, value: str) -> None:
          """
          Brief description of the method.
          """
          cls.class_attr = value
  ```

### Inline comments

> Use inline comments sparingly. - **PEP 8**

Python code should explain itself by meaningful variable names, idiomatic code patterns, docstrings, and
well-organized function/method that only does a single thing. When otherwise unavoidable, place an inline
comment starting with `#` _on its own line_ to explain the meaning of the _next_ block of code. There should
be one empty space following the `#` sign.

## Linting

Python code should follow a minimum set of styles and patterns widely adopted by the community. We enforce these
rules by using the linter `flake8`. Every source code file should be error and warning free according to `flake8`.

In Python, a static linter is usually implemented as an ordinary package with a CLI command. For example, after
installing `flake8` in the local virtual environment, `flake8` becomes an executable command and
`flake8 PATH/TO/THE/linted_file.py` lints the Python source code file and displays the result in shell.

### flake8 setups

To use `flake8`, conduct the following steps.

- Make sure the file [.flake8](./.flake8) is in the project root folder. This file configures the linting rules.

- `pip install flake8 pep8-naming flake8-import-order` if they are not already in the development virtualenv. The
  latter two are `flake8` plugins.

- Enable `flake8` integration with the IDE being used. In VS Code, put the following lines in the user-scoped
  `settings.json` file and it displays all errors caught by `flake8` while coding.

  ```
      "python.linting.enabled": true,
      "python.linting.flake8Enabled": true,
  ```

### Rules enforced

When linting rules are violated, it is generally easy to identify and correct them with the help of `flake8`
error messages. Below is a summary of some important rules to help reduce the occurrence of errors while coding.

- Naming conventions

  - Global variables should be `UPPERCASE_WITH_UNDERSCORE`.

  - Local variables, parameters/arguments, functions, methods, and package/module names should be `lowercase_with_underscore`.

  - Class names should be `PascalCase`.

- Imports

  - Order import statements by the alphabetical order of package names.

  - Group import statements into three sections `# Builtin`, `# External`, `# Own`, separated by one empty
    line between sections, in that order.

- Miscellaneous

  - Maximum line width is 100 characters.

  - Do not use backslash for line continuation except for
    [multi-line `with` statements prior to Python 3.10](https://peps.python.org/pep-0008/#maximum-line-length).
    Use "implied line continuation" instead.

## Idiomatic Code Patterns

### Python classes

- Use classes as "instance factories" instead of namespaces when possible

  Python type annotations generally assume classes to represent "types" of objects --- objects are of the same type
  if they are created by the same "factory" class. Therefore, do not use class as the namespace and getter of singleton
  object. The same goal can be achieved by using a module as namespace, a global object as the singleton and a function
  instead of `__new__()` as the getter of the singleton.

- If possible, expensive resources should be class attributes and thereby shared among class instances. Only use
  `classmethod`s to initialize, update and clean up class attributes.

- Typically, the `__init__()` method should only conduct lightweight operations of setting the instance "state",
  i.e. setting instance attributes that do not cost much to create or get.

- Use `with` statement with context manager class instances

  A "context manager" is any object whose class implements the `__enter__()` and `__exit__()` methods. Such an object
  can be use in a `with` statement and typically handles resources that need both initialization and clean-up (e.g.
  file handles, database connection). Use `with` statement whenever an object provided by a third-party package is a
  context manager.

### Exception handling

- Never use "bare except" `except:`

  Bare except will catch exceptions that almost never should be caught, including `KeyboardInterrupt`
  (the user hitting `Ctrl + C`) and Python-raised errors like `SystemExit`.

- At least two levels of exception handling

  Ideally, a low-level "granular" function that does a single thing should only handle _known_ types of exceptions.
  In this function, the `except` keyword should be followed by exception classes as specific as possible. Unexpected
  exceptions should be allowed to bubble up to higher level. In this situation, exception handling is not very
  different from control flow. Below are two typical patterns.

  ```python
  # Uncaught exceptions will bubble up
  try:
      ...
  except (KnownException1, KnownException2) as err:
      ...

  # Catch all and manually bubble up unknown exceptions
  try:
      ...
  except Exception as err:
      if isinstance(err, KnownException1):
          ...
      elif isinstance(err, KnownException2):
          ...
      else:
          raise err
  ```

  On the top level, there should be another layer that catches all exceptions by `except Exception as err:`, and
  handles and logs them according to business requirements.

### Logging

The `logging` standard library package offers a handy 3-step workflow.

- If necessary, create a custom log message "formatter" by subclassing `logging.Formatter` and override the `format`
  method.

- Set the formatter on a "handler". On cloud platforms, `logging.StreamHandler()` is usually the one to use, as it
  sends log messages to `stderr` by default.

- Add the handler to a "logger" and set the logging level according to an environment variable. Use different methods
  on the logger according to the level of severity of the information being logged. This makes it easy to change the
  amount of logs according to the environment.

- Each logger is identified by a unique name. `logging.getLogger(...)` is the function that retrieves a logger by name, or
  creates a new one if not already existing. Logger of a specific name is always a singleton instance.

- In simple scenarios, it is usually sufficient to use a single "root logger", i.e. the instance
  returned by the `logging.getLogger()` call with no argument. If logs need to be well organized and constantly
  analyzed, it is a good convention to use module-level loggers --- in each module which uses logging, create a logger
  as follows:

  ```python
  logger = logging.getLogger(__name__)
  ```

  This means that logger names track the package/module
  [hierarchy](https://docs.python.org/3/howto/logging.html#advanced-logging-tutorial), and it’s intuitively obvious
  where events are logged just from the logger name.

## Commit Message Format

### Commit message

Ideally, every commit should only consist of changes made for a single _specific_ purpose. The commit message consists
of a mandatory subject, an optional body and/or footer.

The commit message should use the following format.

```
abbrev: the purpose of change, brief but specific

optional body

optional footer
```

`abbrev` is the abbreviation of the category of the change, for example:

- `feat`: new feature

- `fix`: fix bugs or failed unit test

- `unit`: unit test

- `e2e`: e2e test

- `db`: database related

- `doc`: documentation update

- `refactor`: code refactor

- `ci/cd`: changes related to CI/CD or dependency management

If the changes and the message subject are not self-explanatory enough, add a body that summarizes the changes
to each file and the purpose. If there is an associated ticket, put the ticket number in the footer.

### Pull request

The pull request title does not include abbreviation as in a commit message. In a single sentence, it should describe
the purpose that all commits in the PR work together for. The PR body should contain a brief description of changes
to each file and their purpose. When more commits are pushed to a PR, the body should be updated accordingly.
