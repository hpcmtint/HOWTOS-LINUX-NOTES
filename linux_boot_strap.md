PROXY:

For all users of the system via the /etc/wgetrc or for the user only with the ~/.wgetrc file:

cat ~/.wgetrc

use_proxy=yes
http_proxy=172.30.115.1:3128
https_proxy=172.30.115.1:3128

cat ~/.gitconfig 
[http]
        proxy = http://172.30.115.1:3128
[https "https://github.com"]
        proxy = https://172.30.115.1:3128
[https]
        proxy = https://172.30.115.1:3128
[user]
        email = hpcmtint@gmail.com
        name = Michael Tint

[alias]
    lg = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''%C(white)%s%C(reset) %C(dim white)- %an%C(reset)' --all


