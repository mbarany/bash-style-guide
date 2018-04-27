# bash-style-guide
My personal style guide for Bash.

## When to use Shell
Shell should only be used for small utilities or simple wrapper scripts. While shell scripting isn't a development language, it is used for writing various utility scripts.
Some guidelines:
- If you are mostly calling other utilities and are doing relatively little data manipulation, shell is an acceptable choice for the task.
- If performance matters, use something other than shell.
- If you find you need to use arrays for anything more than assignment of ${PIPESTATUS}, you should **NOT** use shell.
- If you are writing a script that is more than 100 lines long, you should probably **NOT** be writing it in shell. Bear in mind that scripts grow. Rewrite your script in another language early to avoid a time-consuming rewrite at a later date.

## Syntax
- Shebang
  - Use POSIX compatible shebang.
    ```bash
    # Bad
    #!/bin/bash

    # Good
    #!/usr/bin/env bash
    ```
  - Depend on bash (`#!/usr/bin/env bash`). Don't be afraid to use bash features like arrays. Using `#!/bin/sh` for maximum portability is commendable, but in reality most users have bash.
- Use spaces instead of tabs. Preferably 2 spaces, but be consistent.
- Use LF Unix-style line endings. AKA `\n`.
- Use `$(command)` instead of backticks.
- Don't use `;` to separate statements and expressions. As a corollary - use one expression per line.
- Always use curly braces for variable expansion so that there's no guessing the variable name.
  ```bash
  # Bad
  echo $my_var
  echo "$my_foo"
  echo "using file: my_$a_var_file"

  # Good
  echo "${my_var}"
  echo "using file: my_${a_var}_file"
  ```

## Environment Settings
- Script settings
  - Use `set -o errexit` (a.k.a. `set -e`) to make your script exit when a command fails. Then add `|| true` to commands that you allow to fail.
  - Use `set -o nounset` (a.k.a. `set -u`) to exit when your script tries to use undeclared variables.
  - Use `set -o pipefail` in scripts to catch `mysqldump` fails in e.g. `mysqldump | gzip`. The exit status of the last command that threw a non-zero exit code is returned.
  - Use `set -o xtrace` (a.k.a `set -x`) to trace what gets executed. Useful for debugging.
- Normal output should go to **STDOUT** and any errors should go to **STDERR**.

## Naming
- Functions: Lower-case, with underscores to separate words. Separate libraries with ::. Parentheses are required after the function name. The keyword function is optional, but must be used consistently throughout a project. Braces must be on the same line as the function name and no space between the function name and the parenthesis.
  ```bash
  # Single function
  my_func() {
    ...
  }

  # Part of a package
  mypackage::my_func() {
    ...
  }
  ```
- Variables
  - Same as functions
  - Constants and environment variables should be all caps, separated with underscores, declared at the top of the file.
    ```bash
    # Constant
    readonly PATH_TO_FILES='/some/path'

    # Both constant and environment
    declare -xr ORACLE_SID='PROD'
    ```
- Filenames should be lowercase, with hyphens to separate words if desired.
- File Extensions
  - Executables should have no extension (strongly preferred) or a .sh extension. Libraries must have a .sh extension and should not be executable.
  - It is not necessary to know what language a program is written in when executing it and shell doesn't require an extension so we prefer not to use one for executables. However, for libraries it's important to know what language it is and sometimes there's a need to have similar libraries in different languages. This allows library files with identical purposes but different languages to be identically named except for the language-specific suffix.

## Comments
- Avoid superfluous comments. Prefer descriptive variable names.
- Use one space between the leading `#` character of the comment and the text of the comment.

## Conditionals
- Use double brackets ([[ ]]) where possible over single brackets.

## Best Practices
- Variables
  - Use `readonly` or `declare -r` to ensure they're read only.
  - Declare function-specific variables with `local`. This avoids polluting the global name space and inadvertently setting variables that may have significance outside the function.
- Functions
  - Put all functions together at the top of the file just below constants. Don't hide executable code between functions.
  - Never nest functions.
- `main` function
  - In order to easily find the start of the program, put the `main` program in a function called `main` as the bottom most function. This provides consistency with the rest of the code base as well as allowing you to define more variables as `local`. The last non-comment line in the file should be a call to `main`: `main "$@"`. Obviously, for short scripts where it's just a linear flow, `main` is overkill and so is not required.
- `eval` should be avoided
- Use a sensible line length. Preferably 80 characters, but no longer than 120.
- If you're writing a script that basically forwards all of its arguments to another one, then use `exec` to avoid spawning a subshell. For example, in some systems `egrep` is just a wrapper script for `grep` that looks like this:
  ```bash
  #!/bin/sh

  exec grep -E "$@"
  ```

## Conclusion
- Use common sense and most importantly **BE CONSISTENT**.
