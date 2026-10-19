# Current Bash on an Apple Silicon Mac with Homebrew

macOS only ships the old Bash 3.2 at `/bin/bash` (for licensing reasons: newer versions are licensed under GPLv3). Since macOS Catalina, the default shell has been zsh. With Homebrew you can install a current Bash (5.x) and use it as your login shell.

This guide covers Apple Silicon Macs (M1 and newer), where Homebrew lives under `/opt/homebrew`.

## 1. Installation

```bash
brew install bash
```

The new Bash is then located at:

```
/opt/homebrew/bin/bash
```

## 2. Set it as the login shell

Add the new Bash to the list of allowed shells:

```bash
echo /opt/homebrew/bin/bash | sudo tee -a /etc/shells
```

Set it as the login shell:

```bash
chsh -s /opt/homebrew/bin/bash
```

Then close the Terminal window and open a new one.

## 3. Test

```bash
echo $SHELL          # should point to /opt/homebrew/bin/bash
echo $BASH_VERSION   # should start with 5.
which bash           # should find /opt/homebrew/bin/bash before /bin/bash
```

## 4. Things to keep in mind

- **Configuration file:** The macOS Terminal starts login shells. Bash therefore reads `~/.bash_profile`, not `~/.bashrc`. If you keep your settings in `~/.bashrc`, add this to `~/.bash_profile`:

  ```bash
  [ -f ~/.bashrc ] && source ~/.bashrc
  ```

- **zsh settings no longer apply:** Everything in `~/.zshrc` (PATH additions, aliases, Homebrew setup) must be carried over to `~/.bash_profile` as needed, e.g.:

  ```bash
  eval "$(/opt/homebrew/bin/brew shellenv)"
  ```

- **Scripts:** A `#!/bin/bash` shebang still uses the old Bash 3.2. To use the new version, write instead:

  ```bash
  #!/usr/bin/env bash
  ```

- **Switch back before uninstalling:** If Homebrew or Bash is removed, the login shell points to nothing. Switch back to zsh first:

  ```bash
  chsh -s /bin/zsh
  ```

  As a fallback, enter a fixed command such as `/bin/zsh` in the Terminal settings under *General → Shells open with*.

- **Updates:** Bash is updated with `brew upgrade`. The path stays the same, so the login shell does not need to be set again.

## 5. Troubleshooting: `which bash` finds the old Bash

If `which bash` still shows `/bin/bash`, the Homebrew directory is not at the front of your `PATH`. Under zsh, this is usually handled by an entry in `~/.zprofile`, which Bash does not read.

### Check

```bash
echo $PATH
```

If `/opt/homebrew/bin` is missing or only appears after `/bin`, the Homebrew environment must be added to `~/.bash_profile`.

### Fix

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.bash_profile
```

The full path to `brew` is used deliberately here, because `brew` cannot be found without a correct `PATH`.

Then reload and test again:

```bash
source ~/.bash_profile
hash -r      # clear the shell's command cache
which bash
```

### If it still doesn't work

- **PATH is overwritten later:** Look in `~/.bash_profile` for lines that reset the `PATH` entirely, e.g. `export PATH=/usr/bin:/bin` without `$PATH`. Remove such lines or move them before the `brew shellenv` entry.
- **Wrong file:** As a login shell, Bash reads only the first existing file of: `~/.bash_profile`, `~/.bash_login`, `~/.profile`. If `~/.bash_profile` exists, the other two are ignored. Entries belong in `~/.bash_profile` in that case.
- **Wrong shell active:** Use `echo $BASH_VERSION` to check whether the new Bash is running at all. If not, repeat section 2 and open a new Terminal window.
