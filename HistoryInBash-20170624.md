<!--- History in Bash --->
<!--- 2017-06-24 --->

Unix shells like bash, csh, or ksh, record user input to a history file. This
keeps a running record of terminal history and is handy for reverse lookups or quickly reentering long commands. 

This also means we can edit entries in the history file or control what gets saved and when.

### Toggling history on and off 
```sh
set +o history
```  
**Turns off** recording any input in the shell for the remainder of your session.

```sh
set -o history
```
**Turns on** recording input. This lets us reactivate recording without ending our session after using the  `set +o history` command. 

### Commands that begin with a space are ommited from history 

By setting HISTIFLE in `.bash_profile` we can prepend a single space to our commands and they will be selectively ignored.

```sh
# in .bash_profile
# History wont record commands preceded by a space
HISTCONTROL="ignorespace"
```

Ex. 
```sh
$  echo no trace 
$ echo trace
```

Then if we run `history | tail` we get this output:
```sh
$  history | tail
  1  trace
$
```
Since we used a single space character before our first `echo no trace` command, it's discarded and not saved. By adding a leading space to `history | tail` it's also excluded from our log.


### Deleting commands from history

If we want to remove something specific from `history` we can use this:

`history [-d num]`  

This invocation will let us pass a line number from `history` and have it removed. For example if we wanted to delete the `echo no trace` entry from our history output below:
```sh
$ history | tail
  553  echo trace
  554  echo trace
  555  echo trace
  556  echo trace
  557  echo trace
  558  echo trace
  559  echo trace
  560  echo trace
  561  echo no trace
  562  history | tail
$
```
We pass the line number of the command we want removed to `history -d` (in this case 561) and if we look at `history | tail` one more time it's deleted.
```sh
$ history -d 561
$ history | tail
  554  echo trace
  555  echo trace
  556  echo trace
  557  echo trace
  558  echo trace
  559  echo trace
  560  echo trace
  561  history | tail
  562  history -d 561
  563  history | tail
$ 
```
Now we can see `echo no trace` is removed from history.  

### Deleting the last ran command

Expanding on `history [-d num]` it's also possible to delete the last ran
command from `history` after setting `HISTCONTROL`, like so:

```sh
$  history -d $((HISTCMD-1))
```
(Note the leading space!) If that space wasn't there, this command would simply
delete itself! This command works _because of_ `HISTFILE="ignorespace"`. 

### But what about a range? 
Can we delete multile entries with one line? Absolutely! 
```sh 
$  for i in {579..574}; do history -d $i; done
```
Again we're using a leading space here. This loops though `history` and deletes
entries in the specified range. Let's see what that looks like. 

Here is our history before the running the above command: 

```sh
$  history | tail
  570  ls
  571  cd berkley/
  572  ls
  573  vim package.json
  574  ls
  575  cd ..
  576  ls
  577  cd ~
  578  clear
  579  echo trace
$
```
And here's what we get after using our loop with `history [-d num]`

```sh
$  for i in {579..574}; do history -d $i; done
$ history | tail
  565  history | tail
  566  bash
  567  man history
  568  man bash
  569  cd src
  570  ls
  571  cd berkley/
  572  ls
  573  vim package.json
  574  history | tail
$
```
Notice how the range includes both line 579 and 574, so both `echo no trace` and
`ls` respectively are each removed. Awesome.

### Rewriting history and saving only the current session
There's also a command for that.
```sh
$  history -w
```
This is useful if you only want the commands ran during your current session to
be saved. It's key to understand that this overwrites _the entire history file_
with only the commands ran _during the current session_. 

### High security
In some cases you may want to prevent extremely sensitive information from ever
being written to disk at all. 

In which case we can force exit our current session without saving history.
```sh
$  kill -9 $$
```
In theory data deleted from non-volatile media can still be recovered through
forensic analysis (think commands that have been saved after a session into
`history` or a `HISTFILE` edited with `history [-d num]`). For extra security this discards all the commands from the
previous session, preventing them from ever being written to disk.

### Using key commands (yes really) 
<kbd>⬆ (up arrow)</kbd> Cycles through previous commands. We can use this to
find a particular command we want removed. When we've reached the entry we want
removed we can use a line editing keystroke like <kbd>Ctrl</kbd>+<kbd>w</kbd>
and then cycle back down using <kbd>⬇ (down arrow)</kbd> until we reach a new
line—Finally then we can hit <kbd>Enter</kbd>. A removed entry will then be
marked by an asterisk. 

```sh
$  history | tail
  491  man history
  492  man bash
  493  cd src
  494  ls
  495  cd berkley/
  496  ls
  497  vim package.json 
  498  history | tail
  499  bash
  500* 
$
``` 

This technique also works with <kbd>Ctrl</kbd>+<kbd>u</kbd>

### Clearing the entire command history
For when you need to start over. 
```sh
history -c
```
### Know any other tricks?
Feel free to shoot me an email or message me on twitter. I'll add whatever you
think I missed. 

Cheers!
