# ssh-askpass (for Mac OS X)

Author: Mark Carver  
Created: 2011-09-14  
Licensed under GPL 3.0

Based off script from author: Joseph Mocker, Sun Microsystems
[http://blogs.oracle.com/mock/entry/and_now_chicken_of_the](http://blogs.oracle.com/mock/entry/and_now_chicken_of_the)

If you've gotten this error:

        remote: ssh_askpass: exec(/usr/libexec/ssh-askpass): No such file or directory

Then you probably need this script. Mac OS X does not prompt for a password outside of Terminal when connecting via SSH (for security reasons). This script grants any application the ability to prompt the user for a password via an AppleScript dialog.

---

### Usage

>    1. You can install this script using the `INSTALL` file included with the download (easiest/automatic)
>       1. Open Terminal `/Applications/Utilities/Terminal.app`
>       2. Type `sudo`` ` (with the space at the end)
>       3. Drag the `INSTALL` file onto `Terminal` and press `return`
>       4. Enter your password

>    2. You can install this script manually:
>       1. Copy the source code located below
>       2. Open Terminal `/Applications/Utilities/Terminal.app`
>       3. Type `sudo vi /usr/libexec/ssh-askpass`
>       4. Enter your password
>       5. Type `i` to go into edit mode
>       6. Press `⌘v` to paste the code
>       7. Press `esc` to go out of edit mode
>       8. Type `:` then `w` and press `return` to write the file
>       9. Type `:` then `x` and press `return` to exit vi
>       10. Type `sudo chmod +rx /usr/libexec/ssh-askpass` to make the script executable
>       11. Test the script! Type `/usr/libexec/ssh-askpass`
>
>_NOTE: You must run as `root` or precede the commands with `sudo` when using `INSTALL` or `vi`. The directory `/usr/libexec` is owned by `root`._

---

### Notes
If you plan on manually installing this script, please note that you will have to set the following variable for SSH to recognize where the script is located:

> `export SSH_ASKPASS="/path/to/ssh-askpass"`

---

### Source

```
#!/bin/bash
# Script: ssh-askpass
# Author: Mark Carver
# Created: 2011-09-14
# Licensed under GPL 3.0

# A ssh-askpass command for Mac OS X
# Based from author: Joseph Mocker, Sun Microsystems
# http://blogs.oracle.com/mock/entry/and_now_chicken_of_the

# To use this script:
#   Install this script running INSTALL as root
#
# If you plan on manually installing this script, please note that you will have
# to set the following variable for SSH to recognize where the script is located:
#   export SSH_ASKPASS="/path/to/ssh-askpass"

TITLE="${SSH_ASKPASS_TITLE:-SSH}";
TEXT="$(whoami)'s password:";
IFS=$(printf "\n");
CODE=("on GetCurrentApp()");
CODE=(${CODE[*]} "tell application \"System Events\" to get short name of first process whose frontmost is true");
CODE=(${CODE[*]} "end GetCurrentApp");
CODE=(${CODE[*]} "tell application GetCurrentApp()");
CODE=(${CODE[*]} "activate");
CODE=(${CODE[*]} "display dialog \"${@:-$TEXT}\" default answer \"\" with title \"${TITLE}\" with icon caution with hidden answer");
CODE=(${CODE[*]} "text returned of result");
CODE=(${CODE[*]} "end tell");
SCRIPT="/usr/bin/osascript"
for LINE in ${CODE[*]}; do
    SCRIPT="${SCRIPT} -e $(printf "%q" "${LINE}")";
done;
eval "${SCRIPT}";
```
