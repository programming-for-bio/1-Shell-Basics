

### Instructions for submitting your homework

```bash

## fork the upstream repo on GitHub
<click fork button at https://github.com/programming-for-bio/1-Shell-Basics>

## clone your copy of it
git clone https://github.com/<you>/1-Shell-Basics.git

## cd into the repo directory
cd 1-Shell-Basics/

## copy and RENAME your Markdown assignment file to here
cp <path-to-assignment> ./assignment/<myname>.md

## add your markdown assignment file
git add ./assignment/<myname>.md

## commit it
git commit -m "added Markdown file to assignment/ dir"

## push to origin
git push 

## make pull request to merge origin/master to upstream/master
<go to your origin repo on GitHub and click 'New pull request' button>
```


### Later, pull other's assignments into your repo
```bash
## add upstream remote
git remote add upstream https://github.com/programming-for-bio/1-Shell-Basics.git

## pull commits (fetch & merge)
git fetch upstream
git merge
```