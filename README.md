# dotfiles

These are my dotfiles. They can be added to `.bash_profile` like this:

```bash
if [ -f ~/.dotfiles/mac/mac_settings ]; then
    . ~/.dotfiles/mac/mac_settings
fi

if [ -f ~/.dotfiles/mac/mac_settings_priv ]; then
    . ~/.dotfiles/mac/mac_settings_priv
fi
```

## GIT Clone

Clone this with GIT like this:

```bash
git clone https://github.com/PhilipMay/dotfiles.git .dotfiles
```
