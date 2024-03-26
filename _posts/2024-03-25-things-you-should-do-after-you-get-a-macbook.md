---
layout: post
title: Things you should do after you get a macbook
tags: macOS
date: 2024-03-25 14:35 +0800
---
## Essential Applications

Homebrew should be the first application you install on your new MacBook. Once installed, it functions similarly to APT in Ubuntu, helping you manage all your packages (applications) on your Mac, including GUI applications!

- **[Homebrew](https://brew.sh/)**: A package manager that simplifies the installation of software on macOS.

Install homebrew:

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Below applications can install through homebrew:

- **Maccy**: A clipboard manager that enhances your productivity by remembering and organizing your clipboard history.
- **Mac Mouse Fix**: A must-have if you're using a non-Apple mouse. It corrects the default scroll direction, which is reversed and can be frustrating.
- **JetBrains Toolbox**: This application allows you to install and manage all your JetBrains IDEs from a single app, streamlining your development workflow.
- **Typora**: A lightweight Markdown reader and writer tool, ideal for those who need a simple and efficient way to work with Markdown files.
- **[SDKMan](https://sdkman.io/)**: This CLI tool enables you to install and manage different JDK versions effortlessly. It's an essential tool for developers working with Java and other JVM languages.
- **Docker**:  Docker Desktop for macOS simplifies the management of Docker containers on Mac, integrating seamlessly with macOS. It includes the essential Docker tools like Docker Engine, Docker CLI client, Docker Compose, and more. For installation, avoid the non-cask version to prevent uninstallation issues.

> Tips: Remember, on macOS, you only need to install Docker Desktop. **Do not install** the non-cask version of Docker, or you will not be able to uninstall it completely. In such cases, you must remove related system files before you can install Docker Desktop.

To install Docker Desktop, use the command:

```shell
brew install --cask docker
```

These applications are essential for getting the most out of your MacBook, whether you're coding, writing, or just optimizing your workflow.

## Set your zsh terminal

The default style of terminal is bland. I like the default styles of Ubuntu terminal. Lets tweak the default terminal, make it look like Ubuntu terminal.

### download the ubuntu profile on github

Clone the whole repo or just download this file : https://github.com/lysyi3m/macos-terminal-themes/blob/master/themes/Ubuntu.terminal, and open it.

### set the zshrc & vimrc

add below lines into `~/.zshrc`

```shell
# Enable color support
autoload -U colors && colors

# Set prompt with color
PROMPT='%F{green}%n@%m %F{blue}%~ %f%# '

alias ll='ls -lG'
```

Then, execute `source ~/.zshrc` to reload the zsh configurations.

the default settings of vim is not colorful, add below lines into `~/.vimrc`

```shell
syntax on
```

### set zsh-completions

install

```shell
brew install zsh-completions
```

update `~/.zshrc`, add below lines into the file.

```shell

    if type brew &>/dev/null; then
     FPATH=$(brew --prefix)/share/zsh-completions:$FPATH

     autoload -Uz compinit
     compinit
    fi
```

more details , please read [this](https://formulae.brew.sh/formula/zsh-completions#default) 

