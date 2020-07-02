# Clone repo and run setup.py develop
Use this installation method if you plan to modify `pygs`. This
way any changes to your local repository are picked up without
having to reinstall `pygs`.

**install from your repository clone** by installing
`setuptools`:

```bash
$ pip install setuptools
```

And running the `setup.py` script with the `develop` command:

```bash
$ python setup.py develop
```

This is like `pip install`, but it places a symbolic link in the
Python install folder instead of placing the actual project files
in the Python install folder.

# Clone repo and add foo/pygs to PYTHONPATH

If cloning into directory `foo`, add `foo/pygs` to the
PYTHONPATH.

## Edit PYTHONPATH on Windows

On Windows, add to the PYTHONPATH for the rest of the PowerShell
session:

```powershell
> $env:PYTHONPATH += "path_to_foo\pygs;"
```

Apply this change to every PowerShell session by putting the
above line in the PowerShell Profile.

## Editing PYTHONPATH on Linux

In a pure POSIX environment (Linux), add to the PYTHONPATH with:

```bash
$ PYTHONPATH+="path_to_foo/pygs:"
```

Or apply to every `bash` session by editing the ~/.bashrc:

```bash
PYTHONPATH+="path_to_foo/pygs:"
export PYTHONPATH
```

## On Cygwin just use the Windows Python Installation

On Cygwin (a POSIX layer on top of Windows) I recommend editing
the PYTHONPATH for Windows only and then running the Windows
Python installation when running Python from Cygwin.

The reason is that Cygwin cannot access the USB ports with the
`pyserial` package. So I end up running the Windows Python
(`python.exe`) from within Cygwin, as opposed to the Python
installed for Cygwin (Cygwin is perfectly happy running any
Windows executable). PYTHONPATH is formatted differently on POSIX
and Windows (path splitter ; vs : and slash direction \ vs /), so
editing PYTHONPATH in the Cygwin `.bashrc` just mangles PYTHONPATH
and makes it unusable by either Python installation.

## Why the path is foo/pygs

The way this works is that Python looks in the folders in
PYTHONPATH for a folder containing an `__init__.py`. The
`__init__.py` is in `pygs/pygs`. The first `pygs` is the PyPI
project name, the second `pygs` is the package name. So adding
`foo/pygs` to the PYTHONPATH, Python looks in the `pygs` project
folder and finds the `pygs` package folder.

## Clone into USERSITE and add USERSITE/pygs to PYTHONPATH

Add `USERSITE/pygs` to the PYTHONPATH variable (replacing
USERSITE with the actual USERSITE path).

`USERSITE` is different for every Python installation.

Find the `USERSITE` path with:

```bash
$ python -m site --user-site
```

(Create the `USERSITE` path if it does not exist.)

I like developing packages directly in `USERSITE`. This way I
don't need to edit PYTHONPATH and my packages are visible to
Python.

Unfortunately, because of how PyPI projects are structured,
cloning a *project* to `USERSITE` does not work the same way.

The package is one level deeper: project/package. So Python looks
in USERSITE and does not detect a package because the `project/`
folder does not contain the `__init__.py` file.

On many projects, the package is two levels deeper because the
project is structured `project/src/package/`. In this case, add
`foo/project/src` to PYTHONPATH.
