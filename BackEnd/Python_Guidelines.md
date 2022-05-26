# Ascending Python Development Guidelines

This document contains Python development guidelines in the following respects:
self-documentation, PEP 8 style guide, static linters, idiomatic code patterns and commit message format.
The appendix contains sample VS Code configurations related to some of the guidelines.

## Table of Contents

1. [Self Documentation](#self-documentation)
1. [PEP 8 Style Guide](#pep-8-style-guide)
1. [Static Linters](#static-linters)
1. [Idiomatic Code Patterns](#idiomatic-code-patterns)
1. [Commit Message Format](#commit-message-format)
1. [Appendix](#appendix)

## Self Documentation

Each Python module should contain the following three types of self documentation. In VS Code, these doc templates
can be inserted quickly by the `User Snippets` feature. See the [Appendix](#appendix) section for details.

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
Such a docstring becomes the `__doc__` special attribute of that object. According to
[PEP 257](https://peps.python.org/pep-0257/):

> All modules should normally have docstrings, and all functions and classes exported by a module should also have
> docstrings. Public methods (including the `__init__()` constructor) should also have docstrings. A package may be
> documented in the module docstring of the `__init__.py` file in the package directory.

- function docstring

  Each function's body should begin with a triple-quoted docstring that briefly describes what the function does and
  the _important restrictions_ on the arguments if any. Most IDEs integrate this type of docstring into its
  IntelliSense or "Show Hover" feature, thus helping the developers that _call_ the function. It's also a good aid
  to maintainers of the code base.

  Optionally, the docstring can provide a description of each parameter's data type and meaning, in the following
  format.

  ```
  """
  desc

  Parameters:
      name (type): desc

  Returns:
      name (type): desc
  """
  ```

  Alternatively, the same effect can be achieved by using meaningful names and type annotation for parameters. Type
  annotation helps IDEs provide better auto-completion inside the function body, and enables linting with `mypy`.
  For example,

  ```python
  from typing import List  # type annotation classes are imported from the standard library "typing"

  def example_function(meaningfully_named_list: List[str], easily_understandable_int: int) -> str:
      """
      Only description in the docstring.
      """
      ...
  ```

- class docstring

  Each method of a class should have its own "function docstring". Immediately after the line of the `class` keyword,
  there should be a "class docstring" that describes what the class provides and meanings and data types of important
  attributes in the following format.

  ```python
  class SomeClass:
      """
      Class docstring describes the class.

      Attributes:
          name (type): desc
      """
      def some_method(self):
          """
          Function docstring describes the method.
          """
  ```

  Similary, the "Attributes" portion of the class docstring can be replaced with meaningful attribute names and
  [class and instance variable annotations](https://peps.python.org/pep-0526/#class-and-instance-variable-annotations).

### Inline comments

> Use inline comments sparingly.  - **PEP 8**

In general, Python code should explain itself by meaningful variable names, idiomatic code patterns, docstrings, and
well-organized function/method that only does a single thing. When otherwise unavoidable, place an inline
comment starting with `#` _on its own line_ to explain the meaning of the next block of code. Inline comments on the
same line as functional code are reserved for linter directives. There should be one empty space following the `#` sign.

## PEP 8 Style Guide

[PEP 8](https://peps.python.org/pep-0008) provides a comprehensive style guide for improving code readability.
In general, there's no need to go through PEP 8 carefully. The style conventions can be picked up by correcting all
errors and warnings reported by a static linter such as `flake8` (see the next section for more details).
Alternatively, a formatter such as `autopep8` or `black` can be integrated with an IDE so that it automatically formats
the code on each file save.

Nevertheless, it is helpful to keep the following important PEP rules/suggestions in mind while coding.

### Imports

- Do not import more than one _package/module_ on the same line.

- Never use `from package_name import *`.

- Order import statements by the alphabetical order of package names.

- Separate import statements into three sections, "Builtin", "External", "Own".

For example:

```python
# Builtin
import datetime
import json

# External
import boto3
from sqlalchemy import create_engine, select, delete, update, Table, MetaData
from sqlalchemy.orm import Session

# Own
from dao.mapping_statuses_dao import MappingStatusesDao
from wrappers import db_connection, logger
```

### Naming conventions

- Global variables should be `UPPERCASE_WITH_UNDERSCORE`.

- Local variables, parameters/arguments, functions, methods, and package/module names should be `lowercase_with_underscore`.

- Class names should be `PascalCase`.

### Line continuation

- Do not use backslash for line continuation except for
  [multi-line `with` statements prior to Python 3.10](https://peps.python.org/pep-0008/#maximum-line-length).

- The preferred way of wrapping long lines is to use Python's "implied line continuation" inside parentheses,
  brackets and braces. The following are examples of implied line continuation with the "hanging indent" style.
  This helps with following the 79-character max line length rule as much as possible.

    ```python
    # method/function call with many arguments
    mapping = self._object(
        request_id=mapping_json.get('request_id'),
        transaction_id=mapping_json.get('transaction_id'),
        source_system_id=mapping_json.get('source_system_id'),
        mapping_status_id=mapping_json.get('mapping_status_id'),
        created_date=mapping_json.get('created_date')
    )
    ```

    ```python
    # long method chaining
    stmt = (
        update(self._object)
            .where(self._object.provisioning_id == provisioning_id)
            .values(
                start_date=subscription['startsAtLocal'],
                end_date=subscription['endsAtLocal']
            )
    )
    ```

    ```python
    # long formula
    income = (
        gross_wages + taxable_interest + dividends - qualified_dividends
        - ira_deduction - student_loan_interest
    )
    ```

## Static Linters

In Python, a static linter is usually implemented as an ordinary package with a CLI command. For example, after
`pip install flake8` in the local virtual environment, `flake8` becomes an executable command and
`flake8 PATH/TO/THE/linted_file.py` lints the Python source code file and displays the result in shell.

However, linters are most useful when integrated with an IDE. In VS Code, if `flake8` is enabled and properly
configured, it lints the file on every save, shows the error and warning counts at the bottom left corner, and
places red and yellow wavy lines in the editing area for all errors and warnings respectively. Linters help to
catch bugs before code execution and enforce coding style. It is a good practice to make sure every source code
file gets no error report from linters.

### flake8

`flake8` is a basic linter that catches errors such as mismatch between the numbers of required parameters and
arguments passed in, "variable never used" error, and PEP violations. Correcting PEP 8 related errors reported by
`flake8` is the most convenient way of enforcing the code style. To use `flake8`, install it in the development
virtualenv and put the settings from [Appendix](#appendix) into VS Code's user-scoped `settings.json` file.

### mypy

> Mypy is an optional static type checker for Python that aims to combine the benefits of dynamic (or "duck") typing
> and static typing. Mypy combines the expressive power and convenience of Python with a powerful type system and
> compile-time type checking. Mypy type checks standard Python programs; run them using any Python VM with basically
> no runtime overhead.  - mypy front page

`Mypy` works by analyzing the _optional_ type annotations that developers choose to put in the source code. It can be
adopted gradually into a code base --- not every module has to be fully annotated at the same time for `mypy` to start
working. When used _consistently and with a properly configured IDE_, it provides the following benefits, which are
illustrated through examples.

- Enhanced self documentation and expressiveness

    Python type annotations are builtin or custom _classes_ that follow a variable after a colon or follow a
    function/method definition after `->`, such as

    ```
    some_variable: <Type1> = <value>

    def some_function(param1: <Type2>) -> <Type3>:
        ...
    ```

    where `<Type1>`, `<Type2>`, `<Type3>` are identifiers for builtin or custom _classes_ and serve as the "types" of
    the variables or return values being annotated.

    For the sample annotated function below,

    ```python
    from typing import Iterable, Optional

    def get_greeting_message(names: Iterable[str], count: int) -> Optional[str]:
        if count <= 0:
            return None
        return 'Hello ' + ', '.join(names[:count]) + '.'
    ```

    it should be clear to the reader what types of arguments are allowed and whether it is necessary to check
    for `None` with the return value. This removes the need of documenting parameter types in the function docstring,
    or even the need of docstring if the code is self-explanatory. 

- Better auto-completion while coding

    In the sample code below, `mypy` deduces from the type of `excel_path` that `excel_path.name` is of type `str`.
    The IDE thereby provides auto-completion for the method call `excel_path.name.endswith()` and eliminates the
    possibility of typo. On Line 7, `mypy` gets a hint from the developer that `df` is a pandas `DataFrame` object,
    and all subsequent method calls on `df` get IntelliSense support from the IDE, including reading the pandas
    documentation right from the hover window. These are not possible on objects without type information.

    ```python
    from pathlib import Path
    import pandas as pd

    def extract_data(excel_path: Path) -> None:
        if not excel_path.name.endswith('.xlsx'):
            raise Exception('Expecting the path to an excel file')
        df: pd.DataFrame = pd.read_excel(excel_path)
        ...
    ```

- Enforcing function/method invocation contract

    The most important use case of `mypy` is to make sure functions and methods are called with the right types of 
    arguments. For example, if a function parameter is annotated as of type `MyCustomClass`, only instances of
    `MyCustomClass` or its subclasses are acceptable as the actual argument. When mistakes are made, the IDE
    would quickly detect them using `mypy` and display suggestions right in the editor window. If type checking is done
    consistently and properly, it mostly removes the need of testing call signatures in unit test.

    On the other hand, although Python type checking seems close to certain aspects of a statically typed compiled
    language, it remains completely optional and does not affect the runtime. It is possible for
    Python code with `mypy` errors to run well, and this helps preserve clever code that makes use of runtime
    duck typing. In addition, `mypy` linting can always be easily disabled per line.

To start using `mypy`, install it in the development virtualenv and put the settings from [Appendix](#appendix)
into VS Code's user-scoped `settings.json` file. _Some_ third-party packages' type annotations (called "stubs", which
are similar to C header files) need to be installed separately from the package. These are usually suggested by `mypy`
in the IDE. For example, `pip install pandas-stubs` install the stub files for `pandas` into the same
package folder. There will be one `.pyi` file for each same-named module (`.py` file or C extension module).

## Idiomatic Code Patterns

### Python classes

- Use classes as "instance factories" instead of namespaces when possible

    There is nothing wrong with using Python class as namespace, and it is idiomatic in certain situations (e.g. 
    `unittest` and `pytest` both use class to group related test functions). The only thing is that type annotation
    users generally expect classes to represent "types" of objects --- objects are of the same type if they are created
    by the same "factory" class. Therefore, do not use class as the namespace and getter of singleton object. The
    same goal can be achieved by using a module as namespace, a global object as the singleton and a function instead
    of the class constructor as getter function of the singleton.

- The `__init__()` method should be lightweight

    When using classes as instance factories, the general design pattern is:

    - Expensive resources should be _class_ attributes and thereby _shared_ among class instances if possible. Only use
      `classmethod`s to initialize, update and clean up class attributes. Class attributes are the variables _assigned_
      in the class body but not inside any method.

    - Typically, the `__init__()` method should only conduct lightweight operations of setting the instance "state",
      i.e. setting instance attributes that do not cost much to create or get.

    - Operations that implement business logic should be encapsulated in ordinary methods.

    - Utility functions that do not rely on instance state or class attributes can either be a `staticmethod` or
      a stand-alone function in the same module. This is only a matter of style.

    - [Descriptor](https://docs.python.org/3/howto/descriptor.html) is an exception to this pattern, but usually
      they only need to be recognized from a third-party package in use. 

    In the example below, `resource` and `runtime_config` are class attributes and are type-annotated as such with
    `ClassVar`. `mypy` will therefore make sure they are never _set_ from any instance, but only during class
    creation or from a `classmethod`. `resource` is known at compile time and does not change, so its assignment
    operation (Ln 6) is placed in the class body and executed only once together with the module, thus saving time
    with this expensive operation. `runtime_config` is a class attribute that is set at runtime by
    `set_runtime_config` (Ln 25). Ln 7 and 8 only _declare_ the types of a class and of an instance attribute for
    readability and linting. `runtime_config` and `instance_state` do not yet exist after the execution of the class
    body (Ln 22).

    ```python
    import json
    from typing import ClassVar, Dict
    from third_party import ExpensiveResource

    class BusinessLogicWorker:
        resource: ClassVar[ExpensiveResource] = ExpensiveResource(...)
        runtime_config: ClassVar[Dict[str, str]]
        instance_state: int

        def __init__(self, value: int) -> None:
            self.instance_state = value

        @classmethod
        def set_runtime_config(cls, config_json: str) -> None:
            cls.runtime_config = json.loads(config_json)

        def do_work(self) -> None:
            self.resource.work(
                config1=self.instance_state,
                config2=self.runtime_config['config2'],
                config3=self.runtime_config['config3']
            )

    # imagine event as the event object passed to an AWS Lambda
    BusinessLogicWorker.set_runtime_config(event['config'])
    agent = BusinessLogicWorker(event['value'])
    agent.do_work()
    ```

- Use `with` statement with context manager class instances

    A "context manager" is any object whose class implements the `__enter__()` and `__exit__()` methods. Such an object
    can be use in a `with` statement and typically handles resources that need both initialization and clean-up (e.g.
    file handles, database connection). `with` statement is a very idiomatic Python pattern. Use it whenever an object
    provided by a third-party package is a context manager. Typical code looks like below.

    ```python
    with ThirdPartyContextManager(...) as meaningful_name:
        meaningful_name.some_method_call(...)
        ...
    ```

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

- Add the handler to a "logger" and set its logging level. The logger is the "user interface" for logging messages
  of various levels.

- Each logger is identified by a unique name. `logging.getLogger()` is the function that retrieves a logger by name, or
  creates a new one if not already existing. Logger of a specific name is always a singleton instance.

- If logs are only needed for debugging, it is usually sufficient to use a single "root logger", i.e. the instance
  returned by the `logging.getLogger()` call with no argument. If logs need to be well organized and constantly
  analyzed, it is a good convention to use module-level loggers --- in each module which uses logging, create a logger
  as follows:

    ```python
    logger = logging.getLogger(__name__)
    ```

  This means that logger names track the package/module
  [hierarchy](https://docs.python.org/3/howto/logging.html#advanced-logging-tutorial), and it’s intuitively obvious
  where events are logged just from the logger name.

### Miscellaneous

> There should be one-- and preferably only one --obvious way to do it. - The Zen of Python

The Python community puts more efforts in standardizing features and promoting best practices than those of many other
programming languages. These standards are usually published in the official documentation and
[Python Enhancement Proposals](https://peps.python.org/pep-0001/).  This section collects some of the standards so that
the team can adopt, and is to be continuously expanded.

- Use `pathlib` from the standard library for path manipulation

    Traditionally, Python has represented file paths using ordinary text strings. With support from the `os.path`,
    this has been adequate but a bit cumbersome, and could lead to subtle errors sometimes.
    `pathlib` treats each path as a immutable and hashable object and deals with the challenges.

    To get started, `from pathlib import Path` and simply use `Path(__file__)` everywhere `os.path.realpath(__file__)`
    used to be. For every `os.path` feature, there is a corresponding method on `Path` that does the same thing.
    [Cheatsheet](https://github.com/chris1610/pbpython/blob/master/extras/Pathlib-Cheatsheet.pdf).

## Commit Message Format

### Commit message

Ideally, every commit should only consist of changes made for a single _specific_ purpose. The commit message consists
of a mandatory subject, and optional body and/or footer.

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

- `doc`: documentation update

- `refactor`: code refactor

- `ci/cd`: changes related to CI/CD or dependency management

If the changes and the message subject are not self-explanatory enough, add a body that summarizes the changes
to each file and the purpose. If there is an associated ticket, put the ticket number in the footer.

### Pull request

The pull request title does not include abbreviation as in a commit message. In a single sentence, it should describe
the purpose that all commits in the PR work together for. The PR body should contain a brief description of changes
to each file and the purpose. When more commits are pushed to a PR, the body should be updated accordingly.

## Appendix

### VS Code snippets for docstring

In VS Code, click the gear icon at the bottom left corner, choose `User Snippets` and then `python.json (Python)`.
Copy-paste the following contents into `python.json`. Afterwards, in any `.py` file, the snippets can be triggered
by typing the capital-letter combination in the `prefix` field and hit `Tab`. After typing out at one place,
`Tab` takes the cursor to the next placeholder.

```json
    "Function Docstring": {
        "prefix": "FDC",
        "body": [
            "\"\"\"",
            "${1:desc}\n",
            "Parameters:",
            "    ${2:name} (${3:type}): ${4:desc}\n",
            "Returns:",
            "    ${5:name} (${6:type}): ${7:desc}",
            "\"\"\""
        ],
        "description": "docstring for functions/methods"
    },
    "Class Docstring": {
        "prefix": "CDC",
        "body": [
            "\"\"\"",
            "${1:desc}\n",
            "Attributes:",
            "    ${2:name} (${3:type}): ${4:desc}",
            "\"\"\""
        ],
        "description": "docstring for classes"
    },
    "Just Docstring": {
        "prefix": "DOC",
        "body": ["\"\"\"", "$1", "\"\"\"$0"],
        "description": "simplest docstring --- six double quotes"
    },
    "Standard Header": {
        "prefix": "HEADER",
        "body": [
            "\"\"\"",
            "Description: $1\n",
            "Created: ${2:mm/dd/yyyy} by ${4}",
            "Last Modified: ${3:mm/dd/yyyy} by ${4}",
            "\"\"\""
        ],
        "description": "Standard header template"
    },
```

### VS Code settings for flake8 and mypy

- flake8

    ```json
        "python.languageServer": "Pylance",
        "python.linting.enabled": true,
        "python.linting.flake8Enabled": true,
        "python.linting.flake8Args": [
            "--exclude=.svn,CVS,.bzr,.hg,.git,__pycache__,.tox,.eggs,*.egg",
            "--ignore=W503,E501"
        ],
    ```

- mypy

    ```json
        "python.linting.mypyEnabled": true,
        "python.linting.mypyArgs": [
            "--show-column-numbers",
            "--show-error-codes"
        ],
    ```

### Other useful VS Code settings

- bracket pair colorization

    ```json
        "editor.bracketPairColorization.enabled": true,
    ```

- Integrated terminal

    ```json
        "terminal.integrated.inheritEnv": false,
        "terminal.integrated.defaultProfile.osx": "zsh",
    ```

- Python Indent plugin

    ```json
        "pythonIndent.trimLinesWithOnlyWhitespace": true,
    ```

- language specific setting

    ```json
        "[python]": {
            "editor.rulers": [80, 120],
            "editor.tabSize": 4
        },
        "[markdown]": {
            "editor.rulers": [120]
        },
    ```