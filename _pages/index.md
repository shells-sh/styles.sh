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

> Note: there are no shout outs to any projects or tools, generic BASH code only.

---

## 🍏 Mac support

### `3.2.57(1)-release`

### `eval`

## ⌨️ Variables

### `PUBLIC_VARIABLE`

### `__myApp__privateVar`

### `(set -o posix; set)`

### `typeset -n`

## 💬 Strings

### `cmd` or `"value"`

### `grep` & `sed`

### `${var/foo/bar}`

### `[[ "$1" = *"foo"* ]]`

## 🗃️ Arrays

### `declare -a`

### `declare -A`

## 🏃‍♀️ Functions

### `local`

### `return`

### `$OUT`

## 💻 Commands

### `main()`

### `$*` or `$@`

### `[ while "$#" -gt 0 ]`

### `case ... esac`

### `- <<< "Foo"`

## 🐚 Subshells

### `$(cat myFile.txt)`

### `$(<myFile.txt)`

### `$?`

### `STDOUT & STDERR`

## 📐 Math

### `$(( i + 1 ))`

### `bc -l`

## 🐶 Representing Objects

### `name:1;age:2;`

### `/dev/urandom`

### `^&,;+&|+`

## 📦 Defining Blocks

### `do ... end`

## 🔬 Testing

### `it.needs_tests()`

## 📖 Documentation

### `## # My Function`

### `>> "$apiDocs.md"`
