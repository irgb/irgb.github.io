---
title: Git 常用命令
date: 2017-04-04
tags: [git]
---

### push tag:
1. git push \<remote\> tag_name # push a single tag
2. git push \<origin\> \--tags # push all tags

### undo all changes:
1. git clean -fd # delete all untracked files and directories
2. git reset \--hard # removes staged and working directory changes
3. git checkout /path/to/your/file # checkout a file from HEAD, and overwrite your change.

### pring history graph
在`.gitconfig` 中添加：

```shell
git log --graph --abbrev-commit --decorate --format=format:'%C(cyan)%h%C(reset) - %C(white)%s%C(reset) %C(yellow)%d%C(reset) %C(dim white) - %anC(reset) %C(dim green)(%ci)%C(reset)' --all
```

Reference：
- []()
- [Git pretty format colors
](https://stackoverflow.com/a/15458378/5432806)
### save HTTPS account
1. **(Recommended)** attach credential to remote repository url: `https://username:password@git.coding.net/username/repository.git`  
1. store temporarily: execute command `git config credential.helper 'cache --timeout=60'`, then credential will expire after 60 seconds since last use.
2. store permanently: execute command `git config --global credential.helper store`, then credential will be stored in file `~/.git-credentials` and `~/.gitconfig` permanently.

**Reference**:

- [git: revert (reset) a single file](http://www.norbauer.com/rails-consulting/notes/git-revert-reset-a-single-file.html)

