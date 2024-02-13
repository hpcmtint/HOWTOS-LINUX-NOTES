```bash
function trash() { mv "$@" ~/.Trash; }
function dsh() { docker exec -it "$@" sh; }
function dbash() { docker exec -it "$@" bash; }       

alias .='cd ..'
alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'
alias .....='cd ../../../..'
alias ......='cd ../../../../..'
alias ..1='cd ..'
alias ..2='cd ../..'
alias ..3='cd ../../..'
alias ..4='cd ../../../..'
alias ..5='cd ../../../../..'
alias .1='cd ..'
alias .2='cd ../..'
alias .3='cd ../../..'
alias .4='cd ../../../..'
alias .5='cd ../../../../..'
alias Vim='vim bash_aliases.sh'
alias amazonbackup='s3backup'
alias amzcdndel='/home/scripts/admin/cdn/purge_cdn_cache --profile amazon'
alias amzcdnmdel='/home/scripts/admin/cdn/purge_cdn_cache --profile amazon --stdin'
alias api='ssh api'
alias apt-get='sudo apt-get'
alias backup='sudo /home/scripts/admin/scripts/backup/wrapper.backup.sh --type local --taget /raid1/backups'
alias bc='bc -l'
alias browser='chrome'
alias c='clear'
alias cd..='cd ..'
alias cd...='cd ../..'
alias cd....='cd ../../..'
alias cd.....='cd ../../../..'
alias cd......='cd ../../../../..'
alias cd1='cd ..'
alias cd2='cd ../..'
alias cd3='cd ../../..'
alias cd4='cd ../../../..'
alias cd5='cd ../../../../..'
alias cdndel='/home/scripts/admin/cdn/purge_cdn_cache --profile akamai'
alias cdnmdel='/home/scripts/admin/cdn/purge_cdn_cache --profile akamai --stdin'
alias chgrp='chgrp --preserve-root'
alias chmod='chmod --preserve-root'
alias chown='chown --preserve-root'
alias chrome='/opt/google/chrome/chrome'
alias ckear='clear'
alias cl='clear'
alias clr='clear'
alias cls='clear;ls'
alias cp='cp -i'
alias cpuinfo='lscpu'
alias df='df -H'
alias diff='colordiff'
alias dnstop='dnstop -l 5  eth1'
alias du='du -ch'
alias dus='df -h'
alias edit='vim'
alias egrep='egrep --color=auto'
alias ethtool='ethtool eth1'
alias fastping='ping -c 100 -s.2'
alias ff='ff13'
alias ff13='/opt/firefox13/firefox'
alias ff4='/opt/firefox4/firefox'
alias fgrep='fgrep --color=auto'
alias findbig='find . -type f -exec ls -s {} \; | sort -n -r | head -5'
alias firewall='iptlist'
alias flushmcd='echo "flush_all" | nc 10.10.27.11 11211'
alias fs='hadoop fs'
alias g='git'
alias ga='git add -A'
alias gap='git add -p'
alias gb='git branch'
alias gc='git clone'
alias gcaa='git commit -a --amend -C HEAD'
alias gcm='git checkout master'
alias gco='git checkout'
alias gcount='git shortlog -sn'
alias gcp='git cherry-pick'
alias gd='git diff'
alias gfrb='git fetch && git rebase'
alias gl='git pull'
alias glum='git pull upstream master'
alias gm='git merge'
alias gmv='git mv'
alias gnew='git log HEAD@{1}..HEAD@{0}'
alias gp='git push'
alias gpd='git push origin development'
alias gpm='git push origin master'
alias gppd='git pull && git push origin develop'
alias gppm='git pull && git push origin master'
alias gpr='git pull --rebase'
alias gpumeminfo='grep -i --color memory /var/log/Xorg.0.log'
alias gr='git remote'
alias gra='git remote add'
alias grep='grep --color=auto'
alias grh='git reset HEAD'
alias grm='git rm'
alias gs='git status'
alias gsl='git shortlog -sn'
alias gss='git status -s'
alias gstd='git stash drop'
alias gt='git tag'
alias gwc='git whatchanged'
alias h='history'
alias halt='sudo /sbin/halt'
alias hcl='history -c; clear'
alias header='curl -I'
alias headerc='curl -I --compress'
alias hls='fs -ls'
alias httpdreload='sudo /usr/sbin/apachectl -k graceful'
alias httpdtest='sudo /usr/sbin/apachectl -t && /usr/sbin/apachectl -t -D DUMP_VHOSTS'
alias iftop='iftop -i eth1'
alias ipt='sudo /sbin/iptables'
alias iptlist='sudo /sbin/iptables -L -n -v --line-numbers'
alias iptlistfw='sudo /sbin/iptables -L FORWARD -n -v --line-numbers'
alias iptlistin='sudo /sbin/iptables -L INPUT -n -v --line-numbers'
alias iptlistout='sudo /sbin/iptables -L OUTPUT -n -v --line-numbers'
alias iwconfig='iwconfig wlan0'
alias j='jobs -l'
alias l.='ls -d .* --color=auto'
alias lightyload='sudo /etc/init.d/lighttpd reload'
alias lightytest='sudo /usr/sbin/lighttpd -f /etc/lighttpd/lighttpd.conf -t'
alias ll='ls -l'
alias ln='ln -i'
alias ls='ls -aF --color=always'
alias master='ssh teamie-lsyncd'
alias mcdshow='/usr/bin/memcached-tool 10.10.27.11:11211 display'
alias mcdstats='/usr/bin/memcached-tool 10.10.27.11:11211 stats'
alias meminfo='free -m -l -t'
alias mkdir='mkdir -pv'
alias mount='mount |column -t'
alias music='mplayer --shuffle *'
alias mv='mv -i'
alias nasbackup='sudo /home/scripts/admin/scripts/backup/wrapper.backup.sh --type nas --target nas01'
alias nfsrestart='sync && sleep 2 && /etc/init.d/httpd stop && umount netapp2:/exports/http && sleep 2 && mount -o rw,sync,rsize=32768,wsize=32768,intr,hard,proto=tcp,fsc natapp2:/exports /http/var/www/html &&  /etc/init.d/httpd start'
alias nginxreload='sudo /usr/local/nginx/sbin/nginx -s reload'
alias nginxtest='sudo /usr/local/nginx/sbin/nginx -t'
alias now='date +"%T"'
alias nowdate='date +"%d-%m-%Y"'
alias nowtime='now'
alias nplaymp3='for i in /nas/multimedia/mp3/*.mp3; do mplayer "$i"; done'
alias nplayogg='for i in /nas/multimedia/ogg/*.ogg; do mplayer "$i"; done'
alias nplaywave='for i in /nas/multimedia/wave/*.wav; do mplayer "$i"; done'
alias opera='/opt/opera/opera'
alias path='echo -e ${PATH//:/\\n}'
alias ping='ping -c 5'
alias playavi='mplayer *.avi'
alias playmp3='for i in *.mp3; do mplayer "$i"; done'
alias playogg='for i in *.ogg; do mplayer "$i"; done'
alias playwave='for i in *.wav; do mplayer "$i"; done'
alias ports='netstat -tulanp'
alias poweroff='sudo /sbin/poweroff'
alias prod='ssh prod'
alias pscpu='ps auxf | sort -nr -k 3'
alias pscpu10='ps auxf | sort -nr -k 3 | head -10'
alias psg='ps -aux ¦ grep bash'
alias psmem='ps auxf | sort -nr -k 4'
alias psmem10='ps auxf | sort -nr -k 4 | head -10'
alias reboot='sudo /sbin/reboot'
alias rebootlinksys='curl -u '\''admin:my-super-password'\'' '\''http://192.168.1.2/setup.cgi?todo=reboot'\'''
alias reboottomato='ssh admin@192.168.1.1 /sbin/reboot'
alias rm='rm -I --preserve-root'
alias root='sudo -i'
alias rsnapshotdaily='sudo  /home/scripts/admin/scripts/backup/wrapper.rsnapshot.sh --type remote --target nas03 --auth /home/scripts/admin/.authdata/ssh.keys  --config /home/scripts/admin/scripts/backup/config/adsl.conf'
alias rsnapshothourly='sudo /home/scripts/admin/scripts/backup/wrapper.rsnapshot.sh --type remote --target nas03 --auth /home/scripts/admin/.authdata/ssh.keys --config /home/scripts/admin/scripts/backup/config/adsl.conf'
alias rsnapshotmonthly='sudo /home/scripts/admin/scripts/backup/wrapper.rsnapshot.sh --type remote --target nas03 --auth /home/scripts/admin/.authdata/ssh.keys  --config /home/scripts/admin/scripts/backup/config/adsl.conf'
alias rsnapshotweekly='sudo /home/scripts/admin/scripts/backup/wrapper.rsnapshot.sh --type remote --target nas03 --auth /home/scripts/admin/.authdata/ssh.keys  --config /home/scripts/admin/scripts/backup/config/adsl.conf'
alias s1='ssh slave1'
alias s3backup='sudo /home/scripts/admin/scripts/backup/wrapper.backup.sh --type nas --target nas01 --auth /home/scripts/admin/.authdata/amazon.keys'
alias sales='ssh sales'
alias sha1='openssl sha1'
alias shutdown='sudo /sbin/shutdown'
alias staging='ssh staging'
alias su='sudo -i'
alias svi='sudo vi'
alias tcpdump='tcpdump -i eth1'
alias top='atop'
alias update='sudo apt-get update && sudo apt-get upgrade'
alias updatey='sudo apt-get --yes'
alias vi='vim'
alias vis='vim "+set si"'
alias vlc='vlc *.avi'
alias vnstat='vnstat -i eth1'
alias wakeupnas01='/usr/bin/wakeonlan 00:11:32:11:15:FC'
alias wakeupnas02='/usr/bin/wakeonlan 00:11:32:11:15:FD'
alias wakeupnas03='/usr/bin/wakeonlan 00:11:32:11:15:FE'
alias wget='wget -c'
alias x='exit'
alias gitupdate='git pull && git fetch; git add .; git status; git commit --author="hpcmtint@gmail.com" -m "added `echo $date`"; git push; git status'

alias d='docker' # Shorten the docker command.

alias dps='docker ps' # List running containers.

alias dall='docker ps -a' # List all containers.

alias dlogs='docker logs' # View container logs.

alias dexec='docker exec -it' # Execute command in running container.
alias drm='docker rm' # Remove a container.

alias drmi='docker rmi' # Remove an image.

alias dstop='docker stop' # Stop a container.

alias dstart='docker start' # Start a container.

alias drestart='docker restart' # Restart a container.

alias dbuild='docker build' # Build an image from a Dockerfile.

alias dpull='docker pull' # Pull an image.

alias dpush='docker push' # Push an image.

alias dnetworks='docker network ls' # List networks.

alias dvolumes='docker volume ls' # List volumes.

alias dclean='docker system prune -a' # Clean up unused Docker resources.

alias dinfo='docker info' # Display system-wide information.

alias dsearch='docker search' # Search Docker Hub for images.

alias dimages='docker images' # List images.

# Aliases for Docker Swarm:


alias ds='docker service' # Manage services.

alias dsnodes='docker node ls' # List nodes in a swarm.

alias dsservices='docker service ls' # List services in the swarm.

alias dscreate='docker service create' # Create a new service.

alias dsupdate='docker service update' # Update a service.

alias dslogs='docker service logs' # View logs from a service.

alias dsrm='docker service rm' # Remove a service.

alias dsinspect='docker service inspect' # Display detailed info of a service.

alias dscale='docker service scale' # Scale a service.

alias dspause='docker service pause' # Pause a service.

alias dsresume='docker service resume' # Resume a service.

# Install & Update utilties

alias sai="sudo apt install"
alias sai="sudo apt-get install"
alias sau="sudo apt update"
alias sau="sudo apt-get update"
alias update="sudo apt update"
alias update="yum update"
alias updatey="yum -y update"
# System state

alias reboot="sudo /sbin/reboot"
alias poweroff="sudo /sbin/poweroff"
alias halt="sudo /sbin/halt"
alias shutdown="sudo /sbin/shutdown"
alias flighton='sudo rfkill block all'
alias flightoff='sudo rfkill unblock all'
alias snr='sudo service network-manager restart'
# Show open ports

alias ports='sudo netstat -tulanp'
# Free and Used

alias meminfo="free -m -l -t"
# Get top process eating memory

alias psmem="ps auxf | sort -nr -k 4"
alias psmem10="ps auxf | sort -nr -k 4 | head -10"
# Get top process eating cpu

alias pscpu="ps auxf | sort -nr -k 3"
alias pscpu10="ps auxf | sort -nr -k 3 | head -10"
# Get details of a process

alias paux='ps aux | grep'
# Get server cpu info

alias cpuinfo="lscpu"
# Older system use /proc/cpuinfo

alias cpuinfo="less /proc/cpuinfo"
# Get GPU ram on desktop / laptop

alias gpumeminfo="grep -i --color memory /var/log/Xorg.0.log"
# Resume wget by default

alias wget="wget -c"
# Grabs the disk usage in the current directory

alias usage='du -ch | grep total'
# Gets the total disk usage on your machine

alias totalusage='df -hl --total | grep total'
# Shows the individual partition usages without the temporary memory values

alias partusage='df -hlT --exclude-type=tmpfs --exclude-type=devtmpfs'
# Gives you what is using the most space. Both directories and files. Varies on current directory

alias most='du -hsx * | sort -rh | head -10'
# MacOs commands

alias rp='. ~/.bash_profile'
alias myip='ifconfig en0 | grep inet | grep -v inet6 | cut -d ' ' -f2'

#Useful Bash Functions:



denter() { docker exec -it "$1" /bin/sh; } # Enter a container’s shell.

dlogsf() { docker logs -f "$1"; } # Follow the logs of a container.

drm_stopped() { docker rm $(docker ps -a -q); } # Remove all stopped containers.

drmi_dangling() { docker rmi $(docker images -q -f dangling=true); } # Remove all dangling images.

dsreplicas() { docker service ls | grep "$1" | awk '{print $3}'; } # Get the number of replicas for a service.

dsrollupdate() { docker service update --force "$1"; } # Force a rolling update for a service.



# Utility Functions for Cleanup and Information:



dclean_volumes() { docker volume rm $(docker volume ls -qf dangling=true); } # Remove unused volumes.

dclean_networks() { docker network prune -f; } # Remove unused networks.

dservice_stats() { docker service ps "$1" --format "{{.Name}}: {{.CurrentState}}"; } # View status of each task of a service.

dlast() { docker ps -l; } # Show the last container started.

dsize() { docker system df; } # Show Docker disk usage.



# Miscellaneous:



dstop_all() { docker stop $(docker ps -q); } # Stop all running containers.

drestart_all() { docker restart $(docker ps -q); } # Restart all running containers.

dscale_all() { for s in $(docker service ls -q); do docker service scale $s=$1; done; } # Scale all services to a specified number of replicas.

dip() { docker inspect --format '{{.NetworkSettings.IPAddress}}' "$1"; } # Get the IP address of a container.

dport() { docker port "$1"; } # List port mappings for a container.

dnames() { docker ps --format '{{.Names}}'; } # List names of all running containers.

dinspect_config() { docker inspect --format '{{.Config}}' "$1"; } # Inspect the configuration of a container.

dhistory() { docker history "$1"; } # Show the history of an image.

dversion() { docker --version; } # Get Docker version.

```
