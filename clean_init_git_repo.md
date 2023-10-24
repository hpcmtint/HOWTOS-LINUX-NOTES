# cleaning traces of Git commit history and pushing back the report to GitHUB

~~~bash
rm -fR .git
git init
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:hpcmtint/keycloak.git
git push -u --force origin master
git log -G"awe.co.uk" --patch
~~~
