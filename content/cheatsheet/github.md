+++
date = '2026-10-28T00:44:46+05:30'
draft = false
title = 'Github'
+++

### One click publish
```
git config alias.publish '!f() { git add . && git commit -m "${1:-Update site}" && git push; }; f'
```

`git publish "Add project page"`


### making a new repo
```
git init
git add .
git commit -m "Initial commit"
gh repo create my-repo --public --source=. --remote=origin --push
```

