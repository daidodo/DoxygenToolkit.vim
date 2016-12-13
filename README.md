# Introduction
This project is developed based on [vim-scripts/DoxygenToolkit.vim](https://github.com/vim-scripts/DoxygenToolkit.vim), which have defined five purposes:

* Generates a *doxygen* license comment.  The tag text is configurable.
* Generates a *doxygen* author skeleton.  The tag text is configurable.
* Generates a *doxygen* comment skeleton for a C, C++ or Python function or class,
including `@brief`, `@param` (for each named argument), and `@return`.  The tag
text as well as a comment block header and footer are configurable.
(Consequently, you can have `\brief`, etc. if you wish, with little effort.)
* Ignore code fragment placed in a block defined by `#ifdef ... #endif` (C/C++).  The
block name must be given to the function.  All of the corresponding blocks
in all the file will be treated and placed in a new block `DOX_SKIP_BLOCK` (or
any other name that you have configured).  Then you have to update
`PREDEFINED` value in your *doxygen* configuration file with correct block name.
You also have to set `ENABLE_PREPROCESSING` to YES.
* Generate a *doxygen* group (beginning and ending). The tag text is
configurable.

And what this project has improved is:
* Searching for an existing license file to produce license comment, so you won't need to define a long text in `.vimrc`. Further more, the searching path is from current file's directory (which is probably inside your project), then to its parent directory, and so on to the root directory (`/`). So if you have defined a license file for your project, it will be first encountered and used. Otherwise, you could put a default license statement file on top of all your projects.
* Adding several predefined macros that could be substituted in your license statement text, such as `<AUTHOR>`, `<MAIL>` and `<YEAR>`.

# Installation
This plugin follows the standard runtime path structure, and as such it can be installed with a variety of plugin managers:

*  [Pathogen](https://github.com/tpope/vim-pathogen)
  *  `git clone https://github.com/daidodo/DoxygenToolkit.vim ~/.vim/bundle/DoxygenToolkit.vim`
*  [NeoBundle](https://github.com/Shougo/neobundle.vim)
  *  `NeoBundle 'daidodo/DoxygenToolkit.vim'`
*  [Vundle](https://github.com/gmarik/vundle)
  *  `Plugin 'daidodo/DoxygenToolkit.vim'`
*  [Plug](https://github.com/junegunn/vim-plug)
  *  `Plug 'daidodo/DoxygenToolkit.vim'`
*  manual
  *  copy all of the files into your `~/.vim` directory

# Usage

## `:Dox`
Generate *doxygen* comment skeleton for a class or function in the current line or after.
* Function:
```{.cpp}
/**
 * @brief
 * @param key
 * @param value
 * @param len
 * @return
 */
bool insert(const key_type & key, const char * value, size_t len){
```
* Class
```{.cpp}
/**
 * @brief
 * @tparam Key
 * @tparam HashKey
 * @tparam EqualKey
 */
template<
    typename Key,
    class HashKey = CHashFn,
    class EqualKey = std::equal_to
>class CAtomicHashTable
{
```
* Struct, Namespace, Variable, etc.
```{.cpp}
/**
 * @brief
 */
struct Object {
```
```{.cpp}
/**
 * @brief
 */
namespace marine {
```
```{.cpp}
/**
 * @brief
 */
std::vector<char> v;
```

## `:DoxLic`
Place a license statement in the place of cursor.

### Statement text
The statement text is from a license file. The file, with its name defined by `g:DoxygenToolkit_licenseFile`, is located in current editing file's directory, or its parent directory, or any directory along to the root (`/`), .

If `g:DoxygenToolkit_licenseFile` is not defined, or no such file found, text defined by `g:DoxygenToolkit_licenseTag` will be used.

If `g:DoxygenToolkit_licenseTag` is not defined either, an internal statement will be placed.

### Predefined macros
There are several predefined macros that could show up in the statement text:
* `<YEAR>`: will be replaced by current year, e.g. `2016`.
* `<AUTHOR>`: will be replaced by author's name if `g:DoxygenToolkit_authorName` is defined.
* `<MAIL>`: will be replaced by "`<` author's mail address `>`" if `g:DoxygenToolkit_authorMail` is defined.
* `<N>`: will be replaced by a newline (`\n`) in the text.

## `:DoxAuthor`
Generate `@file` and `@author` comment skeleton in current line.

Example:
```{.cpp}
/**
 * @file
 * @brief
 * @author Daidodo
 * @version 1.0
 * @date 2016-12-10
 */
```
If you want to add current file name after `@file` (which *doxygen* can deduce by itself), you could set `DoxygenToolkit_putFilename = 1`.

If `g:DoxygenToolkit_authorName` is not defined, you will see `@author <AUTHOR>` instead.

Version string could be defined by `g:DoxygenToolkit_versionString`. If not set, `@version` information will not be placed.

## `:DoxBlock`
Insert a *doxygen* block on the current line.

Example:
```{.cpp}
/**
 * @name
 * @{ */
/**  @} */
```
# Configuration
There are a bunch of variables you can customize.

### `g:DoxygenToolkit_authorName=""`
Defines author's name.

If not set, `<AUTHOR>` will appear in the output comments wherever needed.

Example:
```{.vim}
:let g:DoxygenToolkit_authorName="Daidodo"
```

### `g:DoxygenToolkit_authorMail=""`
Defines author's email address.

If not set, `<MAIL>` macro in license text will not be substituted.

Example:
```{.vim}
:let g:DoxygenToolkit_authorMail="daidodo@gmail.com"
```
then `"Text with <MAIL>."` with be replaced by `"Text with <daidodo@gmail.com>."`.

### `g:DoxygenToolkit_licenseFile=""`
Defines license file name.

When executing command `:DoxLic`, *DoxygenToolkit* will try to locate a license file in current editing file's directory, then in its parent directory, and so on to the root (`/`). If any file is found, the content will be placed as license statement comment into the editing file.

Example:
```{.vim}
:let g:DoxygenToolkit_licenseFile="COPYING"
```

### `g:DoxygenToolkit_licenseTag=""`
Defines license statement text.

When executing command `:DoxLic`, if no license file was found, the content of this variable will be placed as license statement comment into the editing file.

If not set, an internal license statement will appear instead.

Example:
```{.vim}
let g:DoxygenToolkit_licenseTag="Copyright (c) <YEAR> <AUTHOR> <MAIL><N><N>"
let g:DoxygenToolkit_licenseTag.="This program is free software: you can redistribute it and/or modify<N>"
let g:DoxygenToolkit_licenseTag.="it under the terms of the GNU General Public License as published by<N>"
...
```

### `g:DoxygenToolkit_versionString=""`
Defines version string.

If not set, `@version` will not appear in `:DoxAuthor` output.

Example:
```{.vim}
:let g:DoxygenToolkit_versionString="1.0"
```

### `g:DoxygenToolkit_putFilename=0`
Whether to put current file name after `@file`.

Basically it's better to let *doxygen* figure out what the file name is by not putting it there.

Example:
```{.vim}
:let g:DoxygenToolkit_putFilename=1
```

### `g:DoxygenToolkit_commentType="C"`
This variable only has two valid values: `C` or `C++`.

If it equals to `C`, C-style comments will be used, like `/** ... */`. This is the default.

If it equals to `C++`, C++-style comments will be used, like `///`.

Example:
```{.vim}
:let g:DoxygenToolkit_commentType="C++"
```
C style comments:
```{.c}
/**
 * @brief
 * @param count
 * @param val
 */
void resize(size_type count, value_type val = 0){
```
C++ style comments:
```{.cpp}
/// @brief
/// @param count
/// @param val
void resize(size_type count, value_type val = 0){
```

To find more configurations, see the `g:DoxygenToolkit_XXX` variables in the script's source. These variables can be set in `.vimrc`.
