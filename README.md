# Supervisor

> Effortlessly run scripts or commands when certain files change.

Supervisor is a package that watches directories for changes. When specific changes take place, it invokes commands or scripts you specify.

For example, you could watch your Downloads folder and then automatically remove image metadata.

## Installation

We highly recommend you use [`pipx`](https://github.com/pypa/pipx) to install Supervisor (and all CLI packages, really). To install pipx, visit the [relevant portion of their docs.](https://github.com/pypa/pipx#install-pipx)

`pipx install ssupervisor`

The command will be available at `supervisor`. To check that it worked, run `supervisor --help`

> Why "ssupervisor"?

Naming conflicts on PyPi.

## Features

- Watch directories for specific changes: `any`, `modified`, `created`, `moved`, or `deleted`
- Using the `.supervisor` configuration file, you can watch any number of directories at the same time.
- Invoke inline commands or provide a path to a script which will be invoked on each change in the directory.

## Usage

Once you've installed Supervisor, run `supervisor -c create` to create the `~/.supervisor` configuration file. It'll be populated with a comment (starting with `#`) telling you to read the README. This is the part it's referring to.

Supervisor uses lines in the configuration file to start the observer(s). Below is the syntax for a configuration line (without square brackets):

```
[event]:::[directory]:::[command/file]
```

In practice, you might watch your `Downloads/` folder for new files:

```
created:::/home/me/Downloads/:::echo "New file added"
```

**To start watching, simply run `supervisor`.** To modify the execution behavior, you can add configuration options. These are detailed below.

### Watching multiple directories

To watch multiple directories, simply add a new line:

```
created:::/home/me/Downloads/:::echo "New file added"
delete:::/home/me/Pictures/:::echo "Picture deleted"
```

### Event Types

- `any`: Invoked on any of the following events.
- `modified`: When a file within a directory is modified.
- `created`: When a new file is added to the directory.
- `deleted`: When a file is removed from the directory.
- `moved`: When a file is moved to another place outside of the directory.

### Configuration options

There are some helper commands to make it easier to work with the `.supervisor` configuration file.

To access these arguments, pass the `-c` or `--conf` flag before. For instance, `supervisor -c create` to create a config file.

- `create`: Creates a new `~/.supervisor` file.
- `reset`: Deletes the config file and offers an option to create a new one.
- `edit`: Prompts for an editor name (e.g. `vim`) and opens it for editing.
- `delete`: Deletes the config file.
- `echo`: Prints the contents of the config file to the console.

#### Argument passing

Invoked with: `-p, --pass-argument`

This will pass the changed file as a command-line argument to your script. For instance, consider the following `script.py`:

```
import sys

print(sys.argv)
```

Invoking Supervisor with `supervisor -p` will add the file to the list of arguments.

#### Logging

Invoked with: `-l, --log`

Although every change is printed to the console, you may want a log of all the events Supervisor executes. To do so, enable the `-l` flag.

Note: You cannot watch your home directory AND enable logging. This would create an infinite loop.

The log file is created at: `$HOME/supervisor.log`

#### Recursive walking

Invoked with: `-r, --recursive`

**BEWARE!!**

This enables recursive checks of the filesystem. This means that watching your home directory, for instance, will watch all directories within it. If you don't use this option carefully, your script could be invoked thousands of times without you doing anything. Some operating systems modify certain directories at arbitrary points in time. If you need recursive observation, make sure your script can be invoked hundreds or potentially thousands of times, depending on how far up in the directory tree you watch.

**Do not use this option unless you know what you're doing.** Almost all uses of Supervisor do not need to use this option. It can be useful,  however, when you want to watch any change within a subdirectory.

For instance, we may want to know if any file within the `supervisor` project directory was changed. This includes, for instance, the `src/lib` directory.

We can add the configuration line:

```
any:::/path/to/Supervisor/:::script.sh
```

and invoke Supervisor with: `supervisor --recursive`. Then, any change within this directory or any subdirectories is reported.

## Developing

If you're looking for information on how to build and develop Supervisor locally, you've found the right part of the README.

Supervisor uses the painless (nearly) [Poetry](https://python-poetry.org/) library to manage the dependencies and distribution of Supervisor.

### Installing locally

- `poetry install` to install the dependencies and `supervisor` within the current environment. (Activate it with `poetry shell`)
- `poetry build` to build the package and prepare it for distribution. If you're just making a PR, this step isn't necessary.

For more info on Poetry commands, see [Poetry: Commands](https://python-poetry.org/docs/cli/)