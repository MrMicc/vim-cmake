# Vim-CMake

Vim-CMake is a plugin for building CMake projects inside of Vim/Neovim, with a
nice visual feedback.

![tests](https://img.shields.io/github/actions/workflow/status/cdelledonne/vim-cmake/lint.yaml?label=tests)
![language](https://img.shields.io/github/languages/top/cdelledonne/vim-cmake)
![version](https://img.shields.io/github/v/tag/cdelledonne/vim-cmake?label=version&sort=semver)
![license](https://img.shields.io/github/license/cdelledonne/vim-cmake)

![screencast][screencast]

**Features**

* Visual experience, shows CMake output in a console-like window
* Slick management of build configurations
* Autocompletion for build targets, configurations, executables and tests
* Quickfix list population after each build
* Airline/statusline status information, including current build configuration
* Plug-and-play, but configurable
* Written in Vimscript

**Requirements**

* Vim with `+terminal`, or Neovim >= 0.5
* Under Windows, only Neovim is supported at the moment
* Target completion uses the `cmake-file-api(7)`, which requires CMake 3.14 or
  later
* Running executables also requires the `cmake-file-api(7)`, and thus CMake 3.14
  or later

<!--=========================================================================-->

## Installation

Use a package manager like [vim-plug][vim-plug]:

```vim
Plug 'cdelledonne/vim-cmake'
```

or Vim's native package manager:

```sh
mkdir -p ~/.vim/pack/plug/start
cd ~/.vim/pack/plug/start
git clone --recurse-submodules https://github.com/cdelledonne/vim-cmake.git
```

or Lazy, create and added at `vim-cmake.lua`
```
return {
    'cdelledonne/vim-cmake',
    cmd = {
        'CMakeGenerate',
        'CMakeClean',
        'CMakeBuild',
        'CMakeInstall',
        'CMakeRun',
        'CMakeTest',
        'CMakeSwitch',
        'CMakeOpen',
        'CMakeClose',
        'CMakeToggle',
        'CMakeCloseOverlay',
        'CMakeStop',
    },
    keys = {
        { "\\g", "<cmd>CMakeGenerate<cr>",     desc = "CMakeGenerate" },
        { "\\c", "<cmd>CMakeClean<cr>",        desc = "CMakeClean" },
        { "\\b", "<cmd>CMakeBuild<cr>",        desc = "CMakeBuild" },
        { "\\i", "<cmd>CMakeInstall<cr>",      desc = "CMakeInstall" },
        { "\\t", "<cmd>CMakeTest<cr>",         desc = "CMakeTest" },
        { "\\s", "<cmd>CMakeSwitch<cr>",       desc = "CMakeSwitch" },
        { "\\o", "<cmd>CMakeOpen<cr>",         desc = "CMakeOpen" },
        { "\\c", "<cmd>CMakeClose<cr>",        desc = "CMakeClose" },
        { "\\T", "<cmd>CMakeToggle<cr>",       desc = "CMakeToggle" },
        { "\\C", "<cmd>CMakeCloseOverlay<cr>", desc = "CMakeCloseOverlay" },
        { "\\S", "<cmd>CMakeStop<cr>",         desc = "CMakeStop" },
    },

    init = function()
        vim.g.cmake_link_compile_commands = 1
    end,

}
```

<!--=========================================================================-->

## Usage

Run `:CMakeGenerate` from the top-level CMake source directory to generate a
build system for the project.  Then, run `:CMakeBuild` to build the project.
The built files will end up in the binary directory ([out-of-source
build][oos]).  To switch between build configurations, run `:CMakeSwitch
<config>`.  To run executable targets in an overlay window, run `:CMakeRun
<target>`.  To run CMake-generated tests with CTest, run `:CMakeTest`.

With Vim-CMake, you can easily manage build configurations (Debug, Release,
etc.), build specific targets and control build options, and fix errors using
Vim's quickfix feature.  For a detailed explanation of commands, mappings and
functionalities run `:help cmake`.  A quick overview follows.

<!--=========================================================================-->

## Commands and `<Plug>` mappings

| Command                   | `<Plug>` mapping      | Description                           |
|:--------------------------|:----------------------|:--------------------------------------|
| `:CMakeGenerate[!]`       | `(CMakeGenerate)`     | Generate build system                 |
| `:CMakeClean`             | `(CMakeClean)`        | Remove build system and build files   |
| `:CMakeBuild[!] [target]` | `(CMakeBuild)`        | Build a project                       |
| `:CMakeInstall`           | `(CMakeInstall)`      | Install build output                  |
| `:CMakeRun <target>`      | `(CMakeRun)`          | Run executable target                 |
| `:CMakeTest`              | `(CMakeTest)`         | Run CMake-generated tests with CTest  |
| `:CMakeSwitch <config>`   | `(CMakeSwitch)`       | Switch to another build configuration |
| `:CMakeOpen`              | `(CMakeOpen)`         | Open CMake console window             |
| `:CMakeClose[!]`          | `(CMakeClose)`        | Close CMake console window            |
| `:CMakeToggle`            | `(CMakeToggle)`       | Toggle CMake console window           |
| `:CMakeCloseOverlay`      | `(CMakeCloseOverlay)` | Close overlay window                  |
| `:CMakeStop`              | `(CMakeStop)`         | Stop running command                  |

<!--=========================================================================-->

## Additional `<Plug>` mappings

| `<Plug>` mapping     | Behaves as                                            |
|:---------------------|:------------------------------------------------------|
| `(CMakeBuildTarget)` | `(CMakeBuild)`, but leaves cursor in the command line |

<!--=========================================================================-->

## Key mappings

Mappings in the CMake console window:

| Key mapping | Description                |
|:------------|:---------------------------|
| `cg`        | Run `:CMakeGenerate`       |
| `cb`        | Run `:CMakeBuild`          |
| `ci`        | Run `:CMakeInstall`        |
| `ct`        | Run `:CMakeTest`           |
| `cq`        | Run `:CMakeClose`          |
| `<C-C>`     | Stop running command       |

Mappings in the overlay window:

| Key mapping | Description                |
|:------------|:---------------------------|
| `cq`        | Run `:CMakeCloseOverlay`   |

<!--=========================================================================-->

## Events

Vim-CMake provides a set of custom events to trigger further actions.  Run
`:help cmake` for full documentation on all configuration options and examples.

| Event                      | Description                                |
|:---------------------------|:-------------------------------------------|
| `User CMakeGeneratePre`    | Triggered before running `:CMakeGenerate`  |
| `User CMakeBuildPre`       | Triggered before running `:CMakeBuild`     |
| `User CMakeBuildSucceeded` | Triggered after a successful `:CMakeBuild` |
| `User CMakeBuildFailed`    | Triggered after a failed `:CMakeBuild`     |

<!--=========================================================================-->

## Quickfix list

After each build (e.g. run with `:CMakeBuild`), Vim-CMake populates a quickfix
list to speedup the edit-compile-run cycle, similarly to when running `:make` in
Vim/Neovim.  Upon an unsuccessful build, just use the standard quickfix commands
to open the list of errors (e.g. `:copen`) and jump between errors (e.g.
`:cfirst`, `:cnext`).

<!--=========================================================================-->

## Configuration

Vim-CMake has sensible defaults.  Again, run `:help cmake` for full
documentation on all the configuration options.  A list of default values
follows.

| Options                         | Default            |
|:--------------------------------|:-------------------|
| `g:cmake_command`               | `'cmake'`          |
| `g:cmake_test_command`          | `'ctest'`          |
| `g:cmake_default_config`        | `'Debug'`          |
| `g:cmake_build_dir_location`    | `'.'`              |
| `g:cmake_generate_options`      | `[]`               |
| `g:cmake_build_options`         | `[]`               |
| `g:cmake_native_build_options`  | `[]`               |
| `g:cmake_test_options`          | `[]`               |
| `g:cmake_console_size`          | `15`               |
| `g:cmake_console_position`      | `'botright'`       |
| `g:cmake_console_echo_cmd`      | `1`                |
| `g:cmake_jump`                  | `0`                |
| `g:cmake_jump_on_completion`    | `0`                |
| `g:cmake_jump_on_error`         | `1`                |
| `g:cmake_link_compile_commands` | `0`                |
| `g:cmake_root_markers`          | `['.git', '.svn']` |
| `g:cmake_log_file`              | `''`               |
| `g:cmake_log_level`             | `'INFO'`           |
| `g:cmake_statusline`            | `0`                |
| `g:cmake_restore_state`         | `1`                |
| `g:cmake_reinit_on_dir_changed` | `1`                |

<!--=========================================================================-->

## Public API

Vim-CMake provides a public API to query information about the CMake environment
as well as plugin state.  The public API consists of a single function
`cmake#GetInfo()` which returns a dictionary containing the information.  For
extensive information run `:help cmake-api`.

To show the CMake version in your statusline you could do:
```vim
set statusline=%{cmake#GetInfo().cmake_version.string}
```
or integrate this as a component in your preferred statusline plugin.

<!--=========================================================================-->

## Contributing

Feedback and feature requests are appreciated.  Bug reports and pull requests
are very welcome.  Check the [Contributing Guidelines][contributing] for how to
write a feature request, post an issue or submit a pull request.

<!--=========================================================================-->

## License

Vim-CMake is licensed under the [MIT license][license].  Copyright (c)
2020&ndash;2024 Carlo Delle Donne.

<!--=========================================================================-->

[screencast]: https://user-images.githubusercontent.com/24732205/88468329-18aad100-cee2-11ea-94f4-f2ac59a2e6b9.gif
[vim-cmake]: https://github.com/vhdirk/vim-cmake
[cmake4vim]: https://github.com/ilyachur/cmake4vim
[cmake.vim]: https://github.com/jalcine/cmake.vim
[vim-cmake-project]: https://github.com/sigidagi/vim-cmake-project
[LucHermitte/vim-build-tools-wrapper]: https://github.com/LucHermitte/vim-build-tools-wrapper
[neoterm]: https://github.com/kassio/neoterm
[vim-plug]: https://github.com/junegunn/vim-plug
[oos]: https://cprieto.com/posts/2016/10/cmake-out-of-source-build.html
[contributing]: ./CONTRIBUTING.md
[license]: ./LICENSE
