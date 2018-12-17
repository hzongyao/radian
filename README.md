# rtichoke: A 21 century R console

[![CircleCI](https://circleci.com/gh/randy3k/rtichoke/tree/master.svg?style=shield)](https://circleci.com/gh/randy3k/rtichoke/tree/master)
[![Build status](https://ci.appveyor.com/api/projects/status/2ftxvwy66kxx8u9o/branch/master?svg=true)](https://ci.appveyor.com/project/randy3k/rtichoke/branch/master)
[![](https://img.shields.io/pypi/v/rtichoke.svg)](https://pypi.org/project/rtichoke/)
<a href="https://www.paypal.me/randy3k/5usd" title="Donate to this project using Paypal"><img src="https://img.shields.io/badge/paypal-donate-blue.svg" /></a>
<a href="https://liberapay.com/randy3k/donate"><img src="http://img.shields.io/liberapay/receives/randy3k.svg?logo=liberapay"></a>

<img src="rtichoke.png"></img>

_rtichoke_ is an improved console for the R program with multiline editing and rich syntax highlight and more ...


Under the hood, _rtichoke_ is built on top of the python library `prompt-toolkit`. One would consider _rtichoke_ as a [ipython](https://github.com/ipython/ipython) clone for R, though its layout and keybinds are actually more similar to those of [julia](https://julialang.org).

_rtichoke_ is still under active developement, users should use it at their own risks.

<img width="600px" src="https://user-images.githubusercontent.com/1690993/30728530-b5e9eb5c-9f26-11e7-8453-73a2e880c9de.png"></img>


## Features

- shell mode: hit `;` to enter and `<backspace>` to leave
- reticulate python repl mode: hit `~` to enter
- lightweight, no compilation is required
- cross platform, runs on Windows, macOS and Linux
- improved R prompt and reticulate python prompt
    - multiline editing
    - syntax highlight
    - auto completion (reticulate autocompletion depends on `jedi`)
- unicode support
- auto matching parens/quotes.
- brackated paste mode
- emacs/vi editing mode
- automiatically adjust to terminal width
- read more than 4096 bytes per line


## Installation

Requirements:

- An installation of R (version 3.4.0 or above) is required to use _rtichoke_, an R installation binary for your system can be downloaded from https://cran.r-project.org.
- `python` is also required to install _rtichoke_. If your system doesn't come with a python distribution, it can be downloaded from https://conda.io/miniconda.html. Both version 2 and version 3 should work, though python 3 is recommended.
- `pip` is optional but it makes the installation a bit easier.

```sh
# install released version
pip install -U rtichoke
# to run rtichoke
rtichoke
```

```sh
# or the development version
pip install -U git+https://github.com/randy3k/rchitect
pip install -U git+https://github.com/randy3k/lineedit
pip install -U git+https://github.com/randy3k/rtichoke
```

## Alias on unix system

You could alias `r` to _rtichoke_ by putting

```bash
alias r="rtichoke"
```
in `~/.bash_profile` such that `r` would open _rtichoke_ and `R` would still open the tranditional R console.
(`R` is still useful, e.g, running `R CMD BUILD`.)


## Settings

_rtichoke_ can be customized via `options` in `.Rprofile` file. This file is usually located in your user home directory.

```r
options(
    # see https://help.farbox.com/pygments.html
    # for a list of supported color schemes, default scheme is "native"
    rtichoke.color_scheme = "native",

    # either  `"emacs"` (default) or `"vi"`.
    rtichoke.editing_mode = "emacs",

    # indent continuation lines
    # turn this off if you want to copy code without the extra indentation;
    # but it leads to less elegent layout
    rtichoke.indent_lines = TRUE,

    # auto match brackets and quotes
    rtichoke.auto_match = FALSE,

    # auto indentation for new line and curly braces
    rtichoke.auto_indentation = TRUE,
    rtichoke.tab_size = 4,

    # pop up completion while typing
    rtichoke.complete_while_typing = TRUE,
    # timeout in seconds to cancel completion if it takes too long
    # set it to 0 to disable it
    rtichoke.completion_timeout = 0.05,

    # automatically adjust R buffer size based on terminal width
    rtichoke.auto_width = TRUE,

    # insert new line between prompts
    rtichoke.insert_new_line = TRUE,

    # when using history search (ctrl-r/ctrl-s in emacs mode), do not show duplicate results
    rtichoke.history_search_no_duplicates = FALSE,

    # custom prompt for different modes
    rtichoke.prompt = "\033[0;34mr$>\033[0m ",
    rtichoke.shell_prompt = "\033[0;31m#!>\033[0m ",
    rtichoke.browse_prompt = "\033[0;33mBrowse[{}]>\033[0m ",

    # supress the loading message for reticulate
    rtichoke.suppress_reticulate_message = FALSE,
    # enable reticulate prompt and trigger `~`
    rtichoke.enable_reticulate_prompt = TRUE
)
```

## FAQ

#### How to specify R_HOME location

If _rtichoke_ cannot locate the installation of R automatically. The best option is to expose the R binary to the system `PATH` variable. 

On Linux/macOS, you could also export the environment variable `R_HOME`. For example,
```sh
$ export R_HOME=/usr/local/lib/R
$ rtichoke
```
Note that it should be the path to `R_HOME`, not the path to the R binary. The
folder should contain a file called `COPYING`. In some cases, you may need to
futher specify `LD_LIBRARY_PATH`,
```sh
$ export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:`R RHOME`/lib"
$ rtichoke
```

If the shared library cannot be found, please make sure your R was installed with the shared library `libR.so` or `libR.dylib` or `libR.dll`. On Linux, the flag `--enable-R-shlib` may be needed when R is complied from the source.



#### `reticulate` prompt does not autocomplete

Make sure that `jedi` is installed

```
pip install jedi
```

#### Fail to load library

Some packages may not be loaded properly with `rtichoke` but they work well with the bare R. The issue could be caused by several reasons, check
[#38](https://github.com/randy3k/rtichoke/issues/38) and [#46](https://github.com/randy3k/rtichoke/issues/46). Open an issue if it still doesn't work.

#### how to use local history file

_rtichoke_ maintains its own history file `.rtichoke_history` and doesn't use the `.Rhistory` file. A local `.rtichoke_history` is used if it is found in the launching directory. Otherwise, the global history file `~/.rtichoke_history` would be used. To override the default behavior, you could launch _rtichoke_ with the options: `rtichoke --local-history`, `rtichoke --global-history` or `rtichoke --no-history`.


#### Does it slow down my R program?

_rtichoke_ only provides a frontend to the R program, the actual running eventloop is the same as that of the traditional R console. There is no performance sacrifice (or gain) while using this modern command line interface. 

#### Nvim-R support

Put
```vim
let R_app = "rtichoke"
let R_cmd = "R"
let R_hl_term = 0
let R_args = []  " if you had set any
let R_bracketed_paste = 1
```
in your vim config. 


#### `reticulate` intialization error

```
SystemError: initialization of _heapq did not return an extension module
```

You would need to install [this](https://github.com/rstudio/reticulate/pull/279) particular version of reticulate for now.

```r
devtools::install_github("rstudio/reticulate#279")
```


#### Readline Error

```
libreadline.so.6: undefined symbol: PC
```

If you are using conda and encounter this error, it is likely because the `readline` from conda is bugged. Install it again via `conda-forge`.
```python
conda install -c conda-forge readline=6.2
# or
conda upgrade -c conda-forge readline
```

#### `setTimeLimit` not working

_rtichoke_ utilizes the function `setTimeLimit` to set timeout for long completion. Users may notice that `setTimeLimit` is not working under the
global environment. A workaround is to put the code inside a block or a function,

```r
{
    setTimeLimit(1)
    while(1) {}
    setTimeLimit()
}
```

#### Prompt not shown inside a docker container

It maybe caused by the invalid terminal size, try running `stty size` in your terminal
to see if it returns a correct size. You could change the values of it from the environmental variables
`$COLUMNS` and `$LINES` when you log-in the docker container.

```
docker exec -it <container> bash -c "stty cols $COLUMNS rows $LINES && bash"
```


## Credits

_rtichoke_ wouldn't be possible witout the creative work [prompt_toolkit](https://github.com/jonathanslenders/python-prompt-toolkit/) by Jonathan Slenders.

The name _rtichoke_ was suggested by [thefringthing](https://www.reddit.com/r/rstats/comments/7zibhj/any_suggestions_of_a_new_name_of_rice/).

<div>Icons made by <a href="http://www.freepik.com" title="Freepik">Freepik</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a> is licensed by <a href="http://creativecommons.org/licenses/by/3.0/" title="Creative Commons BY 3.0" target="_blank">CC 3.0 BY</a></div>