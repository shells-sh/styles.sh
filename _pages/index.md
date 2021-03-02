---
title: '🖌️ BASH Style Guide'
permalink: /
layout: single
sidebar:
  nav: 'styles'
---

- Some programming styles are **personal preferences**.
- Some programming styles are **company guidelines**.
- Some programming styles are **community conventions**.

_This document contains my **personal** preferences only._

### Please be kind.

_I am sharing this in the hopes that some may find this interesting or useful. ~ [@bex](https://github.com/beccasaurus)_

> _Note: there are no shout outs to any projects or tools, generic [BASH][] code only._

[bash]: https://en.wikipedia.org/wiki/Bash_(Unix_shell)

---

# ⌨️ Variables

## `MYAPP_PUBLIC_VARIABLE`

Name public global variables in `UPPERCASE`

Prefix your global variable with something to identify to your users that the variable is associated with your script or program, e.g. `MYAPP_CONFIG_FILE`.

This helps to **avoid global naming collisions** with variables in other libraries which your users may be using.

## `_myApp_privateVar`

> ℹ️ All private variables should be `local` variables inside functions ([see more](#local))

Name local and non-public variables in `camelCase` (_note:_ `snake_case` _is more popular_)

Prefix your private variables with something associated with your script or program, e.g. `_myApp_configFile`.

This helps to **avoid global naming collisions** with variables in other libraries which your users may be using.

> 💡 Reminder: variables you assign will be available to the scope of other functions that you call.
>
> This includes `local` variables.

### ❌ Don't do this

```sh
myFunction() {
  local count=5 # Set a local variable (unrelated to function called below)
  calculate totals # Call the 'calculate' function
  echo "The count is $count and the total is $TOTAL" # Print local variable
}

# ---------------------------
# Function in another library:
calculate() {
  echo "You called calculate. The 'count' is $count"

  # This other library is using the 'count' variable and they
  # forgot to add 'local' or, in their library, 'count' is global.
  #
  # Modifications made to this variable will modify the parent
  # function's local 'count' variable.
  count=0

  local item
  for item in "CALCULATION_TOTALS[@]"
  do
    : $(( count++ ))
  done
  TOTAL="$count"
}
CALCULATION_TOTALS=(42)
```

```sh
$ myFunction
# You called calculate. The 'count' is 5
# The count is 1 and the total is 1
```

The final printed count should be 5, but it is 1 due to a naming collision.  
Even though your variable is a `local`!

## `declare`

Use `declare` to define variables with dynamic names.

```sh
# This dynamic variable name is set in a variable
variableName=foo

# Declare the variable using its name from a variable
declare "$variableName=42"

echo "$foo"
# => "42"

# This can also be used to modify the value
declare "$variableName=4"

echo "$foo"
# => "4"
```

This also works for arrays:

```sh
# This dynamic variable name is set in a variable
variableName=foo

# 'foo' is undefined and has a zero length
echo "${#foo[@]}"
# => 0

# Declare the variable using its name from a variable
declare -a "$variableName=(hello world)"

# 'foo' is defined as an array with a count of 2
echo "${#foo[@]}"
# => 2

# The first array item is "hello"
echo "${foo[0]}"
# => "hello"

# This can also be used to push new values onto the array
declare -a "$variableName+=(goodnight moon)"

# Print all values of the array
echo "${foo[*]}"
# => "hello world goodnight moon"
```

> 💡 **Note:** Using `declare` in a function assigns the variable as a `local`.
>
> BASH 4.2 adds `declare -g` which assigns the variable in the global scope.

## `typeset -n`

Use `typeset -n` to get a reference to a variable by using _the variable name_.

> ℹ️ Note: this is only available in BASH 4.3 and above

```sh
hello="World"

# Modify the value of hello using a variable which contains the variable name "hello"
variableName=hello
typeset -n theVariable="$variableName"

echo "$theVariable"
# => "World"

theVariable="change me"

echo "$theVariable"
# => "change me"

echo "$hello"
# => "change me"
```

## `(set -o posix; set)`

This is the corrent way to get a definition of a variable:

```sh
foo=5
foo_list=(a b c)

(set -o posix; set) | grep ^foo
# foo=5
# foo_list=([0]="a" [1]="b" [2]="c")
```

> 💡 **Tip:** this is a great way to serialize variables including BASH arrays!
>
> The syntax provided by `(set -o posix; set)` can be safely `eval`'d to reload values.
>
> Consider using this for communicating variables across subshell boundaries.

<br>

# 💬 Strings

## `cmd` or `"value"`

## `grep` & `sed`

## `${var/foo/bar}`

## `[[ "$1" = *"foo"* ]]`

<br>
# 🗃️ Arrays

## `declare -a`

## `declare -A`

<br>
# 🏃‍♀️ Functions

## `local`

## `return`

## `declare -f`

## `$OUT` `--out`

<br>
# 💻 Commands

## `main()`

## `$*` or `$@`

## `[ while "$#" -gt 0 ]`

## `case ... esac`

## `- <<< "Foo"`

<br>
 🐚 Subshells

## `$(cat myFile.txt)`

## `$(<myFile.txt)`

## `$?`

## `STDOUT & STDERR`

<br>
# 📐 Math

## `$(( i + 1 ))`

## `bc -l`

<br>
# 🐶 Representing Objects

## `name:1;age:2;`

## `/dev/urandom`

## `^&,;+&|+`

<br>
# 📦 Defining Blocks

## `do ... end`

<br>
# 🔬 Testing

## `it.needs_tests()`

<br>
# 📖 Documentation

## `## # My Function`

## `>> "$apiDocs.md"`

<br>
# 🍏 Mac support

💡 **Recommendation:** Support Mac out-of-the-box, do not use newer BASH features.

> Many users of BASH use Mac and the easiest solution to supporting them is to author
> your BASH code to support `3.2.57` out of the box.
>
> (Optional) If so desired, create a branch of your code which branches on `$BASH_VERSION` and
> uses an alternate, optimized version of your program which supports more modern
> BASH features such as associative arrays and indirect variable references.
>
> See these alternate solutions to BASH 4.3+ features:
>
> - [Indirect Variable References](#typeset--n)
> - [Associative Arrays](#declare--a-1)

## `3.2.57(1)-release`

Mac ships with a version of BASH which was [released in 2002](<https://en.wikipedia.org/wiki/Bash_(Unix_shell)#Release_history>): BASH 3.2.57(1)-release.

**Why?**

This is the last version of BASH which was shipped under the [GPLv2][] license, future versions switched to [GPLv3][].

[gplv2]: https://en.wikipedia.org/wiki/GNU_General_Public_License#Version_2
[gplv3]: https://en.wikipedia.org/wiki/GNU_General_Public_License#Version_3

Apple decided to stick with the [GPLv2][] version, even though it is 15 years old at the time of writing.

## `zsh`

> ℹ️ Starting with macOS Catalina, Macs now use [`zsh`][] as the default shell.
>
> _This document is not relevant to other shells, e.g. `zsh`_

[`zsh`]: https://en.wikipedia.org/wiki/Z_shell

## `BASH 4` + `BASH 5`

BASH 4 and 5 added useful features for developers and script authors, e.g.

- Associative Arrays (_string key/value pairs_)
- Indirect Variable References (_reference a variable using a dynamic variable name_)

If you want your BASH script to support Mac, you will not be able to use these features.

## `$variableName`

If your BASH scripts use variables with dynamic names, you will need to use `eval`.

In BASH 4.3 (_unsupported on Mac_), you can use `typeset -n` to get an indirect reference
to a variable by name and modify that variable.

```sh
hello="World"

# Modify the value of hello using a variable which contains the variable name "hello"
variableName=hello
typeset -n theVariable="$variableName"

echo "$theVariable"
# => "World"

theVariable="change me"

echo "$theVariable"
# => "change me"

echo "$hello"
# => "change me"
```

BASH 3.2.57 required `eval` to work with variables with dynamic names.

```sh
hello="World"

# Modify the value of hello using a variable which contains the variable name "hello"
variableName=hello
eval "$variableName=\"change me\""

echo "$hello"
# => "change me"
```

## `Docker`

If you are not developing on a Mac, it is recommended that you test your
application against Mac's `3.2.57` version of BASH using Docker and/or
configure your cloud test provider to run your tests on a Mac device.

### Example

Create a `Dockerfile`:

```Dockerfile
FROM bash:3.2.57
```

Build the image:

```sh
docker build -t bash3257 .
# Sending build context to Docker daemon  972.3kB
# Step 1/1 : FROM bash:3.2.57
#  ---> 4d4010b2347d
# Successfully built 4d4010b2347d
# Successfully tagged bash3257:latest
```

Run a BASH script in the local folder by mounting the folder and running it in a container via `bash`:

```sh
# [foo.sh]
echo "hi from $BASH_VERSION"
```

```sh
$ docker run --rm -it -v "$PWD:/scripts" bash3257 bash scripts/foo.sh
# hi from 3.2.57(1)-release
```
