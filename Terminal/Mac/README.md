# Mac terminal configuration guide

1. Install iTerm2, configure ZSH, Oh My Zsh and Powerlevel10k ([link](https://gist.github.com/kevin-smets/8568070))
    1. Set up of Oh My Zsh: `sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`
    1. Install Powerlevel10k: `git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k`
    1. Install ZSH auto complete: `git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`
1. Add to .zshrc:

```bash
cd ~
alias python=python3
alias pip=pip3
plugins=(
        git
        zsh-autosuggestions
)
```

## Git

### Configure SSH keys

Save the right key under ./ssh and set ssh-agent and execute these commands:

```bash
ssh-keygen #if new key is needed
eval $(ssh-agent)
ssh-add ~/.ssh/<private_key_file> 
```

Create a file ./ssh/config with the following content:

```bash
Host *
UseKeychain yes
```