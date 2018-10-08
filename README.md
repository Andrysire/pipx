# pipx - Execute binaries from Python packages in isolated environments

<p align="center">
<a href="https://github.com/cs01/pipx/raw/master/pipx_demo.gif">
<img src="https://github.com/cs01/pipx/raw/master/pipx_demo.gif">
</a>

</p>

<a href="https://github.com/ambv/black"><img alt="Code style: black" src="https://img.shields.io/badge/code%20style-black-000000.svg"></a>


pipx makes trying a program as easy as
```
> pipx <binary>
```

and installing a program globally as easy as
```
> pipx install <package>
```

pipx has two modes of operation: running a binary or installing a package and making binaries available globally.

It eliminates the tedium of creating and removing virtualenvs, and eliminates the temptation to run `sudo pip install ...` (you aren't doing that, are you? 😄).

Notice that you **don't need execute any install commands to run the binary**. As a user, this makes trying out binaries really easy. As a developer, this means you can simplify your deployment instructions to users since they can try or install your program painlessly.

pipx lets you run Python programs with **no commitment** and no impact to your system, all while using best practices. For example, you can see help for black by running `pipx black --help`. pipx also has a feature to install Python packages in an isolated virtualenv and make their binaries globally available.

## usage
```
pipx binary
```

```
pipx [--package PACKAGE] [--python PYTHON] binary [binary-arg] ...
```

```
pipx [-h] {install,upgrade,upgrade-all,uninstall,uninstall-all,list} ...
```

## install pipx
```
curl https://raw.githubusercontent.com/cs01/pipx/master/get-pipx.py | python3
```
python 3.6+ is required to install pipx. Binaries can be run with Python 3.3+.

To uninstall
```
pipx uninstall pipx
```

## examples
I'll use the python package `black` as an example. The `black` package ships with a binary called black. You can run it with pipx just like this.
```
> pipx black --help
Usage: black [OPTIONS] [SRC]...

  The uncompromising code formatter.
  ...
```
Black just ran, but you didn't have to run `venv` or install commands. How easy was that!?

pipx makes safely installing the program to globally accessible, isolated environment as easy as
```
> pipx install black
```
so now `black` will be available globally, wherever you want to use it, but **not mixed in with your OS's Python packages**.
```
> black --help  # now available globally
Usage: black [OPTIONS] [SRC]...

  The uncompromising code formatter.
  ...
```

> Aside: I just want to take a second to note how different this is from using `sudo pip install ...` (which you should NEVER do). Using `sudo pip install ...` will mix Python packages installed and required by your OS with whatever you just installed. This can result in very bad things happening. And since all the dependencies were installed along with it (which you have no idea what they were), you can't easily uninstall them -- you have to know every single one and run `sudo pip uninstall ...` for them!

You can uninstall packages with
```
pipx uninstall black
```
This uninstalls the black package **and all of its dependencies**, but doesn't affect any other packages or binaries.

Oh one other thing. You can also run Python programs directly from a URL, such as github gists. Behold [this gist](https://gist.github.com/cs01/fa721a17a326e551ede048c5088f9e0f) run directly with pipx:
```
> pipx https://gist.githubusercontent.com/cs01/fa721a17a326e551ede048c5088f9e0f/raw/6bdfbb6e9c1132b1c38fdd2f195d4a24c540c324/pipx-demo.py
pipx is working!
```
## pipx run binary examples
```
pipx black
pipx --version black  # prints pipx version
pipx black  --version  # prints black version
pipx --python pythonX black
pipx --package black==18.4a1 black --version
pipx --package git+https://github.com/ambv/black.git black
pipx --package git+https://github.com/ambv/black.git@branch-name black
pipx --package git+https://github.com/ambv/black.git@git-hash black
pipx https://gist.githubusercontent.com/cs01/fa721a17a326e551ede048c5088f9e0f/raw/6bdfbb6e9c1132b1c38fdd2f195d4a24c540c324/pipx-demo.py
```

## pipx install examples
```
pipx install black
pipx --python pythonX install black  # black will be associated with pythonX
# package options work the same as in the run binary examples
```

## pipx commands
There are a handful of commands that can be run by pipx:
```
install, upgrade, upgrade-all, uninstall, uninstall-all, list
```
All of these are pretty self-explanatory. You can run `pipx --help <command>` for more.

## globally install binaries with pipx
If you liked the binary you tried with `pipx <binary>` and want to keep it around, you can install it and have it always available globally.
```
pipx install <package>
```
will create a virtualenv, install the package, and create symlinks to the package's binaries in a folder on your PATH.

## Programs to try with pipx
Here are some programs you can try out with no obligation. If you've never used the program before, make sure you add the `--help` flag so it doesn't do something you don't expect. If you decide you want to install, you can run `pipx install <package>` instead.
```
pipx black  # uncompromising Python code formatter
pipx --package babel pybabel  # internationalizing and localizing Python applications
pipx --package chardet chardetect  # detect file encoding
pipx cookiecutter  # creates projects from project templates
pipx flake8  # tool for style guide enforcement
pipx gdbgui  # browser-based gdb debugger
pipx ipython  # powerful interactive Python shell
pipx pipenv  # python dependency/environment management
pipx poetry  # python dependency/environment/packaging management
pipx pylint  # source code analyzer
pipx pyinstaller  # bundles a Python application and all its dependencies into a single package
pipx pyxtermjs  # fully functional terminal in the browser  
```

## How it works
When running a binary (`pipx <binary>`), pipx will
* create a temporary directory
* create a virtualenv inside it with `python -m venv`
* update pip to the latest version
* install the desired package in the virtualenv
* invoke the binary
* erase the temporary directory leaving your system untouched

When installing a package and its binaries (`pipx install package`) pipx will
* create directory ~/.local/pipx/venvs/<package>
* create a virtualenv in ~/.local/pipx/venvs/<package>
* update pip to the latest version
* install the desired package in the virtualenv
* create symlinks in ~/.local/bin that point to new binaries in ~/.local/pipx/venvs/<package>/bin (such as ~/.local/bin/black -> ~/.local/pipx/venvs/black/bin/black)
* As long as `~/.local/bin/` is on your PATH, you can now invoke the new binaries globally

These are all things you can do yourself, but pipx automates them for you. If you are curious as to what pipx is doing behind the scenes, you can always use `pipx --verbose ...`.

## API
```
pipx --help
usage:
    pipx [-h] [--package PACKAGE] [--python PYTHON] [-v] [--version] binary
    pipx [-h] {install,upgrade,upgrade-all,uninstall,uninstall-all,list} ...


Execute binaries from Python packages. Alternatively, safely install a package
in a virtualenv with its binaries available globally. pipx will install a
virtualenv for the package in /Users/cssmith/.local/pipx/venvs. Symlinks to
binaries are placed in /Users/cssmith/.local/bin. These locations can be
overridden with the environment variables PIPX_HOME and PIPX_BIN_DIR,
respectively.

optional arguments:
  -h, --help            show this help message and exit

subcommands:
  Get help for commands with pipx -h [command]

  {binary,install,upgrade,upgrade-all,uninstall,uninstall-all,list}
    binary              Run a named binary, such as `black`. Automatically
                        downloads a Python package to a temporary virtualenv.
                        The virtualenv is completely gone after the binary
                        exits. i.e. `pipx black --help` will download the
                        latest version of black from PyPI, run it in a
                        virtualenv with the --help flag, then exit and remove
                        the virtualenv.
    install             Install a package
    upgrade             Upgrade a package
    upgrade-all         Upgrade all packages
    uninstall           Uninstall a package
    uninstall-all       Uninstall all package, including pipx
    list                List installed packages
```

## Credits
pipx was inspired by [pipsi](https://github.com/mitsuhiko/pipsi) and [npx](https://github.com/zkat/npx).