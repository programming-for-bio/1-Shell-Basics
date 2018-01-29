

### Instructions for submitting your homework


```bash

## fork the upstream repo on GitHub
<click fork button at https://github.com/programming-for-bio/1-Shell-Basics>

## clone your copy of it
git clone https://github.com/<you>/1-Shell-Basics.git

## copy and RENAME your markdown assignment  
cp <path-to-assignment> ./<myname>.txt

## add your markdown assignment file
git add ./<myname>.txt

## commit it
git commit -m "added assignment-1 file"

## push to origin
git push 

## make pull request to merge origin/master to upstream/master
<go to your origin repo and click 'New pull request' button>

```

### Later, pull other's assignments into your repo
```bash
## add upstream remote
git remote add upstream https://github.com/programming-for-bio/1-Shell-Basics.git

## pull commits (fetch & merge)
git fetch upstream
git merge
```

