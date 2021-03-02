🖌️ BASH Style Guide

- Some programming styles are **personal preferences**.
- Some programming styles are **company guidelines**.
- Some programming styles are **community conventions**.

_This document contains my **personal** preferences only._

_These style guidelines are intended for BASH libraries and applications._  
_This is less relevant for simple shell scripts, although perhaps useful._

### Please be kind.

_I am sharing this in the hopes that some may find this interesting or useful. ~ [@bex](https://github.com/beccasaurus)_

> _Note: there are no shout outs to any projects or tools, generic [BASH][] code only._

[bash]: https://en.wikipedia.org/wiki/Bash_(Unix_shell)

---

View the full style guide at https://styles.sh

---

 - [my 2¢](#page-title)
   - [» Style Guide](#page-title)
 - [⌨️ Variables](#%EF%B8%8F-variables)
   - [<code>MYAPP_PUBLIC_VARIABLE</code>](#myapp_public_variable)
   - [<code>_myApp_privateVar</code>](#_myapp_privatevar)
   - [<code>declare</code>](#declare)
   - [<code>typeset -n</code>](#typeset--n)
   - [<code>(set -o posix; set)</code>](#set--o-posix-set)
 - [💬 Strings](#strings)
   - [<code>cmd</code> or <code>"value"</code>](#cmd-or-value)
   - [<code>grep</code> & <code>sed</code> & <code>awk</code>](#grep--sed--awk)
   - [<code>${cheat%%\*sheet}</code>](#)
   - [<code>shopt -s extglob</code>](#shopt--s-extglob)
 - [🗃️ Arrays](#arrays)
   - [<code>declare -a</code>](#declare--a)
   - [<code>IFS=$'\n'</code>](#ifs)
   - [<code>find -print0</code>](#find-print0)
   - [<code>declare -A</code>](#declare--a-1)
 - [🏃‍♀️ Functions](#functions)
   - [<code>local</code>](#)
   - [<code>return</code>](#)
   - [<code>declare -f</code>](#)
   - [<code>$OUT</code> <code>--out</code>](#)
 - [💻 Commands](#commands)
   - [<code>main()</code>](#)
   - [<code>$*</code> or <code>$@</code>](#)
   - [<code>${1:-default}</code>](#)
   - [<code>[ while "$#" -gt 0 ]</code>](#)
   - [<code>case ... esac</code>](#)
   - [<code>- <<< "Foo"</code>](#)
 - [🐚 Subshells](#subshells)
   - [<code>$(cat myFile.txt)</code>](#)
   - [<code>$(&lt;myFile.txt)</code>](#)
   - [<code>$?</code>](#)
   - [<code>STDOUT</code> & <code>STDERR</code>](#)
 - [📐 Math](#math)
   - [<code>$(( i + 1 ))</code>](#)
   - [<code>bc -l</code>](#)
 - [🐶 Representing Objects](#representing-objects)
   - [<code>name:1;age:2;</code>](#)
   - [<code>/dev/urandom</code>](#)
   - [<code>^&,;+&|+</code>](#)
 - [📦 Defining Blocks](#defining-blocks)
   - [<code>cmd { ... }</code>](#)
   - [<code>do ... end</code>](#)
 - [🔬 Testing](#testing)
   - [<code>it.needs_tests()</code>](#)
 - [📖 Documentation](#documentation)
   - [<code>## # My Function</code>](#)
   - [<code>>> "$apiDocs.md"</code>](#)
 - [🍏 Mac support](#-mac-support)
   - [<code>3.2.57(1)-release</code>](#32571-release)
   - [<code>zsh</code>](#zsh)
   - [<code>BASH 4</code> + <code>BASH 5</code>](#bash-4--bash-5)
   - [<code>$variableName</code>](#variableName)
   - [<code>Docker</code>](#docker)


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

#### Example

```sh
myFunction() {
  local privateValue=42 # <-- locals are in scope for called functions
  anotherFunction
}

anotherFunction() {
  echo "The private value is $privateValue" # <-- 'local' value available
}

myFunction
# => "The private value is 42"
```

## `declare`

Use `declare` to define variables with dynamic names.

#### Declare Dynamic Name Variable

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

#### Declare Dynamic Name Array

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
```

#### Modify Dynamic Name Array

```sh
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

#### Example

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

This is the correct way to get a definition of a variable:

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

If text represents a "value", use `"double quotes"`

If text represents a command-line argument, use `no quotes`

#### Example

```sh
dogs setName "Rover"

# In the above example:
# - 'setName' represents a command
# - 'Rover' represents a value

# There's no *technical* reason for the above not to be written as:
dogs setName Rover
dogs "setName" Rover
"dogs" "setName" Rover
"dogs" "setName" "Rover"
# ^ These are all technically equivalent
```

## `grep` & `sed` & `awk`

When possible, use built-in BASH string manipulation and pattern matching over `grep`, `sed`, `awk`, et al.
However, keep your code's maintainability in mind and use these tools when it results in simpler code.

### `tl;dr`

- Do not "blindly" reach for familiar tools such as `grep` and `sed` when BASH functionality would work just as well, if not better.

### String Matching

For simple values, prefer BASH string matching over `grep`.

#### String Contains Example

```sh
var="Hello World"

# Goal: Determine if the value contains the text 'World'

# ❌ Don't do this
if [ echo "$var" | grep World ]; then # ...

# ✅ Do this
if [[ "$var" = *"World"* ]]; then # ...
```

#### String Matches Pattern Example

```sh
var=""

# Goal: Determine is the value starts with 'Hello'

# ❌ Don't do this
if [ echo "$var" | grep ^Hello ]; then # ...

# ✅ Do this
if [[ "$var" =~ ^Hello ]]; then # ...
```

### String Manipulation

Prefer BASH string manipulation over `sed`.

#### String Replacement Example

```sh
var="Hello World"

# Goal: Replace 'Hello' with 'HELLO'

# ❌ Don't do this
var="$( echo "$var" | sed 's/Hello/HELLO/' )"

# ✅ Do this
var="${var/Hello/HELLO}"
```

### String Extraction

Prefer BASH string manipulation over `awk` _depending on the need for performance._

#### String Extraction Example

```sh
var="Hello World Goodnight Moon"

# Goal: Get the second space-delimited value

# ❌ Don't do this
var="$( echo "$var" | awk '{print $2}' )"

# ✅ Do this
var="${var*# }"
var="${var%% *}"
```

> ☝️ Caveat: whereas `'{print $2}'` is easy to understand, the following is not:
>
> ```sh
> var="${var*# }"
> var="${var%% *}"
> ```
>
> Keep in mind the performance requirements for your program and choose
> what is right for you.

### `${cheat%%\*sheet}`

### `shopt -s extglob`

<br>
# 🗃️ Arrays

## `declare -a`

The BASH array is the most powerful tool in anyone's BASH arsenal.

It's a very simple single-dimensional array of text values.

Because BASH `3.2.57` doesn't support Associative Arrays (see [Mac Support](#-mac-support)),
this is the primary data structure upon which all BASH libraries and applications are built!

> 💕 Learn to love the single-dimensional BASH array

To declare a new array, use `declare -a` ([see example](#declare-dynamic-name-array) above)

> 💡 **Reminder:** `declare -a` assigns the array as a `local` variable in functions.

For recommendations on storing complex data, see [Representing Objects](#-representing-objects) below.

## `IFS=$'\n'`

## `declare -A`

I have nothing to say about BASH Associative Arrays 🤷‍♀️

I almost never use them because I try to natively support BASH `3.2.57`.

<br>
# 🏃‍♀️ Functions

## `local`

It is so super critical that every variable you assign in a function be `local`.

#### Example

```sh
myFunction() {
  local varOne # <-- ✅ ALWAYS define variables as local
  varTwo=2     # <-- ❌ NEVER set globals unless you intend to
  declare -a varThree=() # declare defines vars as 'local' by default
}
```

> ℹ️ When perfoming `for` loops, the variable name will become assigned.
> This will be global unless you define it as `local` before your `for` loop.
>
> ```sh
> myFunction() {
>   local arg # <-- define your 'for' loop variables as local
>   for arg in "$@"
>   do
>     : # do something
>   done
> }
>
> myFunction "hello" "world"
> echo "$arg"
> # => "" # <-- if you don't use `local arg`, this will be "world"
> ```

## `return`

BASH uses implicit returns, meaning the return code will be the `$?` return code of the last command run in your function - unless you explicitly `return`.

Recommend you check for error cases, e.g. wrong number of arguments or invalid arguments, and explicitly `return 1`.

Do not explicitly `return 0` unless you are sure the previous commands did not fail (_or if you do not care_).

#### Example

```sh
## # `myFunction`
##
## |      | Parameters |
## |------|------------|
## | `$1` | The one and only argument this function expects |
##
myFunction() {
  [ $# -ne 0 ] && { echo "Wrong number of arguments" >&2; return 1; }
  # do things
}
```

> 💡 **Tip:** Always write tests for the return values of your functions.

## `declare -f`

If you need to view the source code of a function: `declare -f functionName`

> 💡 **Tip:** If you need to copy a function, you can get the source code from `declare -f functionName`,
> replace the name at the start of the source code, and `eval` the source code.

## `$OUT` `--out`

`TODO`

<br>
# 💻 Commands

## `main()`

## `$*` or `$@`

## `${1:-default}`

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

## `cmd { ... }`

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
