# Windows Terminal configuration guide & WSL Ubuntu Configuration

The idea of this document is to collect all the configuration steps I use in the Windows terminal and in in WSL (Ubuntu 20.04).

1. Download Windows Terminal from the Windows 10 Store.
2. Apply the configuation specified [here](./settings/settings.json)


## Software to install

- python3
- python3-pip
- azcli ([https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest#install](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest#install))
- git
- netcore ([https://docs.microsoft.com/en-us/dotnet/core/install/linux-ubuntu#1804-](https://docs.microsoft.com/en-us/dotnet/core/install/linux-ubuntu#1804-))
- zsh

## Docker CLI

Install requirements and configure docker cli to talk with the Docker for Desktop instance installed on Windows 10.

```bash
sudo wget https://storage.googleapis.com/golang/go1.9.2.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.9.2.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
go get -d github.com/jstarks/npiperelay
GOOS=windows go build -o /mnt/c/Users/<your_user_name>/go/bin/npiperelay.exe github.com/jstarks/npiperelay
sudo ln -s /mnt/c/Users/<your_user_name>/go/bin/npiperelay.exe /usr/local/bin/npiperelay.exe
sudo apt-get install socat
sudo apt-get install docker.io
touch ~/docker-relay
chmod +x ~/docker-relay
printf '#!/bin/sh\nexec socat UNIX-LISTEN:/var/run/docker.sock,fork,group=docker,umask=007 EXEC:"npiperelay.exe -ep -s //./pipe/docker_engine",nofork' > ~/docker-relay
sudo adduser <your WSL user> docker
```

From: [https://devblogs.microsoft.com/commandline/cross-post-wsl-interoperability-with-docker/](https://devblogs.microsoft.com/commandline/cross-post-wsl-interoperability-with-docker/)

### Note on running docker-relay:

1. Start Docker for Windows
1. Make sure `sudo` has your credential cached by running something like `sudo ls`. Otherwhise the next command won't work as it will start docker-relay as a job and if it would need to prompt for the user password the job would be paused.
1. Right after that run `sudo ./docker-relay &`

## kubectl

Install kubectl:

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl \
&& chmod +x ./kubectl \
&& sudo mv ./kubectl /usr/local/bin/kubectl
```

## Install Helm

```bash
curl https://helm.baltorepo.com/organization/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

From: [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/)

## Add to .bashrc

Add the following configurations to the .bashrc file in the home directory:

```bash
#
# My Configs
#

# Execute
cd ~

# Aliases
alias python=python3
alias pip=pip3
alias kk=kubectl

# Prompt
parse_git_branch() {
     git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}
export PS1="\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\[\033[33m\]\$(parse_git_branch)\[\033[00m\]\$ "
```

## Create links

Create the following links in the home directory:

```bash
cd ~
ln -s /mnt/d/Git/ ./git
# For the next one kubectl must be installed on Windows first
ln -s /mnt/c/Users/hernan\ j.\ larrea/.kube/ ./.kube
```

## Git

### Configure SSH keys

Save the right key under ./ssh and set ssh-agent and execute these commands:

```bash
ssh-keygen #if new key is needed
eval $(ssh-agent)
ssh-add ~/.ssh/<private_key_file> 
```

### Set ~/.gitconfig file

Set ~/.gitconfig file with the following content:

```
[user]
        mail = hjlarrea@hotmail.com
        name = Hernan J. Larrea
        email = hjlarrea@hotmail.com
```

## ZSH

1. Install Meslo font for PowerLevel10k: https://github.com/romkatv/powerlevel10k/blob/master/font.md
1. Set up of Oh My Zsh: `sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`
1. Install Powerlevel10k: `git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k`

Read more on: https://platzi.com/tutoriales/1748-terminal/8505-configurando-wsl2-windows-terminal-y-oh-my-zsh/

- Add to .zshrc:

```bash
cd ~
alias python=python3
alias pip=pip3
plugins=(
        git
        zsh-autosuggestions
)
```

