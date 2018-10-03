# lazy-load.sh - load `rvm` on-demand on `macOS`

If your `.bash_profile` loads `rvm`, creating an instance of `iTerm2` or
`Terminal` takes a while. On a `macOS`:
```bash
$ time echo 'exit' | bash -li
$ exit
logout

real  0m0.382s
user  0m0.174s
sys   0m0.163s
```

Same without `rvm` is **10X faster**:
```bash
$ time echo 'exit' | bash -li
$ exit
logout

real  0m0.039s
user  0m0.009s
sys   0m0.020s
```

You may not use `rvm` or `ruby` every time you create a new `iTerm2` or
`Terminal` window.

## Lazy loading `rvm`
When `rvm` is the only tool configured by your `.bash_profile`, then the load
time for login shell:

Define a bash function `rvm` & load real `rvm` when custom function is called
for the first time.

```bash
function rvm() {
  unset -f "rvm";

  local SCRIPT="$HOME/.rvm/scripts/rvm"
  [[ -s ${SCRIPT} ]] && source ${SCRIPT};

  rvm "$@";
}
```

Load time for creating a new `iTerm2` or `Terminal` remains the same, invoking
`rvm` for the first time has additional cost. It takes **2X** normal time:
```bash
$ time rvm --version # first invocation of rvm
rvm 1.29.4 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]

real 0m0.677s
user 0m0.333s
sys  0m0.280s

$ time rvm --version # not first invocation of rvm
rvm 1.29.4 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]

real  0m0.332s
user  0m0.175s
sys   0m0.149s
```

### `macOS` bundled `ruby`
`macOS` comes with `ruby` installed by the system. If you invoke `ruby` without
first invoking `rvm`, you will end up using `/usr/bin/ruby` which is not what
you intended to use. To ensure invoking `ruby` always uses `rvm` provided program,
define another function `ruby` with exact same functionality as `rvm` function:
```bash
function ruby() {
  unset -f "ruby";

  local SCRIPT="$HOME/.rvm/scripts/rvm"
  [[ -s ${SCRIPT} ]] && source ${SCRIPT};

  ruby "$@";
}
```

`macOS`'s `ruby` comes with many other programs like `irb`, `gem`, etc.. Define
a function for every tool with exact same functionality as `rvm` function:
```bash
MACOS_RUBY_DIR="/System/Library/Frameworks/Ruby.framework/Versions/Current"
binaries=$(ls ${MACOS_RUBY_DIR}/usr/bin)

for binary in $binaries; do
  source /dev/stdin <<EOF
function ${binary}() {
  for t in ${binaries}; do
    unset -f "\$t";
  done

  local SCRIPT="\$HOME/.rvm/scripts/rvm"
  [[ -s "\${SCRIPT}" ]] && source "\${SCRIPT}";

  $binary "\$@";
}
EOF
done
```

## Extending for `nvm`, `pyenv` & `jenv`
Same process can be used to lazy load `nvm`, `pyenv` & `jenv`. Scripts
demonstrate an example implementation for `macOS`.

On a system with all of `rvm`, `nvm`, `pyenv` & `jenv` running, time to create a
an `iTerm2` or `Terminal` window without lazy loading:
```bash
$ time echo 'exit' | bash -li
$ exit
logout

real  0m3.076s
user  0m1.287s
sys   0m1.107s
```

With lazy loading:
```bash
$ time echo 'exit' | bash -li
$ exit
logout

real  0m0.370s
user  0m0.180s
sys   0m0.268s
```

## Optimisations
This implementation calculates the programs to tap into by using `ls` & few
other programs. Since the programs don't change often, you can use a static
list of programs & save more on `iTerm2` creation time.
