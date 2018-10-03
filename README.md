# lazy-load.sh - load `rvm`, `nvm`, `pyenv` on-demand on `macOS`

On a system with all of `rvm`, `nvm`, `pyenv` & `jenv`, time to create a
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

You can reduce your `iTerm2` or `Terminal` startup time by **10X**.

## Installation
Clone the repository:
```bash
$ git clone https://github.com/gowda/lazy-load.sh
```

Rest of the document assumes that you cloned the repository to `$HOME/lazy-load.sh`,
you might to change file or directory references according to your clone directory.

Add the following line to your `$HOME/.bash_profile`:
```bash
source $HOME/lazy-load.sh/loader
```

Now it is safe to remove any configuration you have for setting up `rvm`, `nvm`,
`pyenv` & `jenv` from your `.bash_profile`.

## Disable an environment manager
You can disable `pyenv` lazy loading support by adding `.disabled` suffix to
the `pyenv` lazy loading script:
```bash
$ mv $HOME/lazy-load.sh/scripts/03-pyenv $HOME/lazy-load.sh/script/03-pyenv.disabled
```

To enable the lazy loading again for `pyenv`, do the reverse:
```bash
$ mv $HOME/lazy-load.sh/scripts/03-pyenv.disabled $HOME/lazy-load.sh/script/03-pyenv
```
