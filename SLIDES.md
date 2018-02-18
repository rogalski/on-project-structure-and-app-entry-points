# On Python project structure and app entry points
### Łukasz Rogalski
#### PyGda #33 lightning talk
![](assets/pygda.png)

---

### Starting a project

- Pick up a catchy name!
- Our dummy project for this presentation is called `chewbacca`
- We typically start writing a code with single script
- Makes sense for simple cases, maybe a few dozens lines of code

Structure:
```
project-root/
    run_chewbacca.py
```

To launch:
```
$ python3 run_chewbacca.py
```

---

### Code grows

- Having everything in same file is just too messy
- Let's extract separate functionalities to separate modules

Structure:
```
project-root/
    core.py
    run_chewbacca.py
    utils.py
```

Few culprits:
- with growing file list, it may be not obvious which file in an actual entry point of application
- common problem of shadowing built-in / third party module with files / packages in local directory
- See [Importing installed package from script raises "AttributeError: module has no attribute" on Stack Overflow](https://stackoverflow.com/questions/36250353/importing-installed-package-from-script-raises-attributeerror-module-has-no-at) for details

---

### Refactor to build a _package_

```
project-root/
    chewbacca/
        __init__.py
        core.py
        utils.py
    run_chewbacca.py
```

---

### ... and add setup.py

```python
from setuptools import setup

setup(
  name="chewbacca",
    version="0.1.0",
    packages=["chewbacca"],
    py_modules=["run_chewbacca"],
    entry_points={
        'console_scripts': [
            'chewbacca = run_chewbacca:main',
        ],
  },
)
```

`entry_points` syntax:
- LHS: `entry_point_name`
- RHS: `some_module.some_module:main_func`

---

### entry points in setup.py
- on installation, it’ll prepare entry point in certain directory
    - Win: `{PYTHON INTERPRETER}\Scripts\chewbacca.bat`
    - Mac/Linux: `{PYTHON INTERPRETER}\bin\chewbacca`
- entry point is just an `*.sh` / `*.bat` script to launch project main code within interpreter (take a look yourself!)
- if `PATH` environment variable is configured correctly, we may launch as simply as:

```
$ chewbacca -p param -o option
```

---

### Are we done?

Structure:
```
project-root/
    chewbacca/
        __init__.py
        core.py
        utils.py
    run_chewbacca.py
    setup.py
```

Two concerns:

1. `run_chewbacca.py` is still a dangling file outside of package directory
2. "Nice-and-short" entry point is available only after installation. What about simple download-and-run?

---

### `__main__.py` to the rescue
- we actually can tell interpreter to run _package_ instead of file
- we just need to add a `__main__.py` file to package we want to be launchable

Structure:
```
project-root/
    chewbacca/
        __init__.py
        __main__.py
        core.py
        utils.py
    setup.py
```

To launch:
```
$ python3 -m chewbacca -p param -o option
$ chewbacca -p param -o option
```

---

### Advantages:
- we have 100% contained namespace for our software, none of project files does not leak outside of it
- intuitive entry point available for packages installed both via pip and simply downloaded and extracted
- Adresses two important use-cases:
    - Cmdline usage for end users
    - Importable programmatic API for power users

```python
import chewbacca
print(chewbacca.say_hello())
```

---

### Popular packages do it
```sh
$ python3 -m pip
$ python3 -m tox
$ python3 -m pytest
```

---

### Things to consider
- I’m not sure how well it’ll apply to your project...
- But if it does, take care of it **as early as possible**
    - Once released, you’ll have to keep backwards compatibility
    - You do not want to completely reorganise repository of advanced project
    - It effectively makes working working with code history much harder

---

# Thank you!

---

# Q&A
