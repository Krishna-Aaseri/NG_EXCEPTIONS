# ng-exceptions [![Travis](https://img.shields.io/travis/Qix-/ng-exceptions.svg?style=flat-square)](https://travis-ci.org/Qix-/ng-exceptions)

Pretty and more helpful exceptions in Python, automatically.

![Example screenshot of exceptions](screenshot.png)

## Usage

Install `ng_exceptions` via pip:

```console
$ pip install ng_exceptions
```

And set the `NG_EXCEPTIONS` environment variable to any value:

```bash
export NG_EXCEPTIONS=1  # Linux / OSX
setx NG_EXCEPTIONS 1    # Windows
```

That's it!

### Python REPL (Interactive Shell)

In order to use `ng_exceptions` in the Python REPL, first install the package (as instructed above) and run:

```console
$ python -m ng_exceptions
Type "help", "copyright", "credits" or "license" for more information.
(ngExceptionsConsole)
>>>
```

in order to drop into a `ng_exceptions`-enabled Python interactive shell.

### Advanced Usage

If you want to allow the entirety of values to be outputted instead of being truncated to a certain amount of characters:

```python
import ng_exceptions
ng_exceptions.MAX_LENGTH = None
```

While using `ng_exceptions` in production, do not forget to unset the `NG_EXCEPTIONS` variable to avoid leaking sensitive data in your logs.

### Django Usage

_Tested with Django 1.11_

Create a middleware exception handler in a file like `myapp/middleware.py`:

```python
import sys
from ng_exceptions import excepthook


class ngExceptionsMiddleware(object):
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        return self.get_response(request)

    def process_exception(self, request, exception):
        excepthook(exception.__class__, exception, sys.exc_info()[2])
        return None
```

In `settings.py`, add your new class to the `MIDDLEWARE` list:

```python
MIDDLEWARE = [
...
    'myapp.middleware.ngExceptionsMiddleware',
]
```

example output:

![image](https://user-images.githubusercontent.com/157132/56871937-5a07b480-69f1-11e9-9fd5-fac12382ebb7.png)



## Troubleshooting

If you do not see beautiful exceptions, first make sure that the environment variable does exist. You can try `echo $NG_EXCEPTIONS` (Linux / OSX) or `echo %NG_EXCEPTIONS%` (Windows). On Linux and OSX, the `export` command does not add the variable permanently, you will probably need to edit the `~/.profile` file to make it persistent. On Windows, you need to open a new terminal after the `setx` command.

Check that there is no conflict with another library, and that the `sys.excepthook` function has been correctly replaced with the `ng_exceptions`'s one. Sometimes other components can set up their own exception handlers, such as the `python3-apport` Ubuntu package that you may need to uninstall.

Make sure that you have not inadvertently deleted the `ng_exceptions_hook.pth` file that should be in the same place as the `ng_exceptions` folder where all of your Python packages are installed. Otherwise, try re-installing `ng_exceptions`.

You can also try to manually activate the hook by adding `import ng_exceptions; ng_exceptions.hook()` at the beginning of your script.

Finally, if you still can not get this module to work, [open a new issue](https://github.com/ng-exceptions) by describing your problem precisely and detailing your configuration (Python and `ng_exceptions` versions, OS, code snippet, interpeter, etc.) so that we can reproduce the bug you are experiencing.

# License
Copyright &copy; 2017, Josh Junon. Licensed under the [MIT license](LICENSE.txt).
