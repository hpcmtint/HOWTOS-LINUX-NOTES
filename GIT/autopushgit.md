# Here is a Bash script for Git to automatically push to a remote repository

1. Automatically check ssh-agent

2. Automatically send passphrase using expect script

Usage is simply: cd /path/to/your/repository and then push

Add this script to a file, for example, $HOME/.ssh/push:

```bash
#!/bin/bash

# Check connection
ssh-add -l &>/dev/null
[[ "$?" == 2 ]] && eval `ssh-agent` > /dev/null

# Check if Git config is configured
if [ ! $(git config user.name) ]
then
    git config --global user.name <user_name>
    git config --global user.email <user_email>
fi

# Check if expect is installed
if [[ ! $(dpkg -l | grep expect) ]]
then
    apt-get update > /dev/null
    apt-get install --assume-yes --no-install-recommends apt-utils expect > /dev/null
fi

# Check identity
ssh-add -l &>/dev/null
[[ "$?" == 1 ]] && expect $HOME/.ssh/agent > /dev/null

# Clean and push the repository
REMOTE=$(git remote get-url origin)
URL=git@github.com:${REMOTE##*github.com/}
[[ $REMOTE == "http"* ]] && git remote set-url origin $URL
git add . && git commit -m "test automatically push to a remote repo"
git status && git push origin $(git rev-parse --abbrev-ref HEAD) --force
```

Link it to the /bin directory, so it can be called by just the push command:

```bash
sudo ln -s $HOME/.ssh/push /bin/push
chmod +x /bin/push
```
