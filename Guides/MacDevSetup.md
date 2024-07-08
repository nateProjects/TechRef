## Launch the Terminal

/Applications/Utilities/Terminal

Or install another one such as [https://iterm2.com/downloads.html](https://iterm2.com/downloads.html)

## Install XCode Commandline Tools

Copy and paste the following command in the Terminal -

```
xcode-select --install 
```

## M1 Mac Rosetta

If you are on an M1 ARM-based Mac -

```
/usr/sbin/softwareupdate --install-rosetta --agree-to-license
```

## Instal Brew

This will allow you to install common Unix apps and utilities (as well as native MacOS ones) -

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Note: run brew upgrade periodically to keep packages up to date or install the following -

```
brew tap homebrew/autoupdate
brew autoupdate start
```

## Install Git

_Most versions of MacOS will already have Git installed, however it may be a much earlier version._

To install the latest version of Git using homebrew -

```
brew install git
```

& if you would like to install the Git GUI -

```
brew install git-gui
```

Alternatively use the [official Git installer](https://sourceforge.net/projects/git-osx-installer/files/git-2.23.0-intel-universal-mavericks.dmg/download?use_mirror=autoselect)

### Git Config

Add **.DS_Store** (hidden OS X system file) to your .gitignore files -

```
git config --global core.excludesfile ~/.gitignore
echo .DS_Store >> ~/.gitignore
```

## Install Python

_Most versions of MacOS will already have Python installed, however it may be a much earlier version._

To install a newer version of Python (simple version) -

```
brew install python
```

### **Advanced - Multiple Versions**

If you may be using / supporting multiple versions of Python -

```
brew install pyenv
pyenv install LATEST-VERSION-NUMBER
pyenv global LATEST-VERSION-NUMBER
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
```

Add the following to **.zshrc** for zsh or **.bash_profile** for Bash -

```
if command -v pyenv 1>/dev/null 2>&1; then
  eval "$(pyenv init -)"
fi
```

See [here](https://www.python.org/downloads/) to find the latest version number or download the [official Python installer](https://www.python.org/downloads/macos/)

## Install Ansible

If you intend to work on any Global Watchtower scripts etc. -

```
brew install ansible
```

After installation if you’d like support for Mac package management -

```
ansible-galaxy collection install community.general
```

## Install an IDE

Either one of these two will do -

VS Codium -

```
brew install --cask vscodium
```

Sublime -

```
brew install --cask sublime-text
```

Or the [official VSCodium Installer](https://vscodium.com/#install) or the [official Sublime Installer](https://www.sublimetext.com/download)

## SSH / TMUX

SSH is installed by default on MacOS  
But you may wish to install TMUX for saving project state and switching between sessions -

```
brew install tmux
```

## ZSH

If you wish to install the ZSH shell & OhMyZsh -

```
brew install zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## AWS CLI

If you will be using AWS from the commandline -

```
brew install awscli
```

Add the following to ~/.bashrc / and/or ~/.zshrc to enable bash completion for aws command -

```
echo 'complete -C aws_completer aws' >> ~/.bashrc
echo 'source /usr/local/share/zsh/site-functions/_aws' >> ~/.zshrc
```

Official [AWS-CLI Installer](https://awscli.amazonaws.com/AWSCLIV2.pkg) - See [Manual Instructions](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

## Sudo Touch

If you want to enable touch authorisation for Sudo -

```
sudo nano /etc/pam.d/sudo
```

Add the following line below pam_smartcard.so -

```
auth sufficient pam_tid.so
```

## Fonts For Code

Github's [Mona Sans](https://github.com/mona-sans)

iA Writer font https://ia.net/downloads#Fonts

## Misc

Commandline - https://archive.ph/PXzE7
