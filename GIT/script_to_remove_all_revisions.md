# Bash script to remove all revisions from github or gist repository.

```bash
#!/bin/bash
#
# By Zibri (2019)
#
# Usage: gitclean username password giturl
#
gitclean () 
{ 
    odir=$PWD;
    if [ "$#" -ne 3 ]; then
        echo "Usage: gitclean username password giturl";
        return 1;
    fi;
    temp=$(mktemp -d 2>/dev/null /dev/shm/git.XXX || mktemp -d 2>/dev/null /tmp/git.XXX);
    cd "$temp";
    url=$(echo "$3" |sed -e "s/[^/]*\/\/\([^@]*@\)\?\.*/\1/");
    git clone "https://$1:$2@$url" && { 
        cd *;
        for BR in "$(git branch|tr " " "\n"|grep -v '*')";
        do
            echo working on branch $BR;
            git checkout $BR;
            git checkout --orphan $(basename "$temp"|tr -d .);
            git add -A;
            git commit -m "Initial Commit" && { 
                git branch -D $BR;
                git branch -m $BR;
                git push -f origin $BR;
                git gc --aggressive --prune=all
            };
        done
    };
    cd $odir;
    rm -rf "$temp"
}
```
