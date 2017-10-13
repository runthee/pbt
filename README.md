Python Bullet Train
===================

Prompt decoration for ZSH and Bash.


Installation
------------

```shell
git clone https://github.com/jtyr/python_bullet_train.git ~/python_bullet_train
# For ZSH
PROMPT='$(~/python_bullet_train/python_bullet_train.py $?)'
# For Bash
PS1='$(~/python_bullet_train/python_bullet_train.py $?)'
```

The `python_bullet_train.py` requires Python v3 to be installed.

In order to display all characters of the prompt correctly, the shell should
support UTF-8 and [Nerd](https://github.com/ryanoasis/nerd-fonts) (or at least
[Powerline](https://github.com/ryanoasis/powerline-extra-symbols) and
[Devicons](https://vorillaz.github.io/devicons/)) fonts should be installed and
set in the terminal application.


Usage
-----

```shell
### Test the Status car
false
true
### Test Sign car
sudo ./python_bullet_train.py
### Test the Dir car
cd /
cd ~
cd /usr/bin
### Test Time car
# Set 12h format
export PBT_CAR_TIME_TIME_FORMAT="%I:%M:%S %p"
# Change background color of the all car
export PBT_CAR_TIME_BG=yellow
# Change color of Date part
export PBT_CAR_TIME_DATE_FG=black
# Reset the color of the Date part
unset PBT_CAR_TIME_DATE_FG
# Reset the background color of all Time car
unset export PBT_CAR_TIME_BG
# Remove the Date part from the car
export PBT_CAR_TIME_FORMAT=" {{ Time }} "
# Reset the format of the car
unset PBT_CAR_TIME_FORMAT
### Train
# Remove the Time car from the train
export PBT_CARS="Status, Hostname, Dir, Sign"
# Reset the original train
unset PBT_CARS
```

In order to use the `ExecTime` car, the following must be set for the given
shell:

```shell
# For ZSH
function pbt_exectime_pre() {
    export PBT_CAR_EXECTIME_SECS=$(date '+%s.%N')
    PBT_CAR_EXECTIME__TMP=1
}
function pbt_exectime_post() {
    if [ -z $PBT_CAR_EXECTIME__TMP ]; then
        export PBT_CAR_EXECTIME_SECS=$(date '+%s.%N')
    fi

    unset PBT_CAR_EXECTIME__TMP
}
preexec_functions+=(pbt_exectime_pre)
precmd_functions+=(pbt_exectime_post)

# For Bash
function pbt_exectime() {
    if [ -z $PBT_CAR_EXECTIME__TMP ]; then
      return
    fi

    unset PBT_CAR_EXECTIME__TMP

    export PBT_CAR_EXECTIME_SECS=$(date '+%s.%N')
}
PROMPT_COMMAND='PBT_CAR_EXECTIME__TMP=1'
trap 'pbt_exectime' DEBUG
```


Compilation
-----------

It's also possible to compile the script into a binary executable file which
should make it run about 10-20% faster. For that we will need a tool called
[`freeze`](https://wiki.python.org/moin/Freeze) which we can find in the Python
source tree:

```shell
git clone https://github.com/python/cpython.git -b "v$(python3 --version | grep -Po '.* \K\d.*')" --depth 1 /tmp/cpython
```

Then we can compile it:

```shell
python3 /tmp/cpython/Tools/freeze/freeze.py python_bullet_train.py
make
```

That will create a binary file `python_bullet_train` which can be used in the
`PROMPT` environment variable instead of the Python script as shown above.

The conpilation process sometimes incorrectly recognizes where Python's libraries
are exactly installed. Then we can get errors like this:

```
Error: needed directory /usr/lib/python3.6/config-3.6m not found
```

and

```
gcc: error: /usr/lib/python3.6/config-3.6m/libpython3.6m.so: No such file or directory
```

That can be solved by symlinks:

```shell
ln -s /usr/lib/python3.6/config-3.6m{-x86_64-linux-gnu,}
```

and

```shell
ln -s /usr/lib/{libpython3.6m.so,python3.6/config-3.6m/}
```


Author
------

Jiri Tyr


License
-------

MIT
