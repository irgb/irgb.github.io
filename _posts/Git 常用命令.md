---
title: Git 常用命令
date: 2017-04-04
tags: [git]

---

## tag:
```
# create a new tag 
git tag -a "tag_name" -m "comment"
# list all tags
git tag
# push a single tag: 
git push origin tag_name
# push all tags
git push origin --tags
```

## undo all uncommit changes:
```
1. git clean -fd # delete all untracked files and directories
2. git reset --hard # removes staged and working directory changes
3. git checkout /path/to/your/file # checkout a file from HEAD, and overwrite your change.
```

## pring branch tree
在`.gitconfig` 中添加：

```shell
[alias]
lg = log --graph --abbrev-commit --decorate --format=format:'%C(cyan)%h%C(reset) - %C(white)%s%C(reset) %C(yellow)%d%C(reset) %C(dim white) - %anC(reset) %C(dim green)(%ci)%C(reset)' --all
```

使用命令`git lg`就可以在命令行中看到branch tree 了

**Reference**：

 - [Pretty git branch graphs
](https://stackoverflow.com/a/9074343/5432806)
 - [Git pretty format colors
](https://stackoverflow.com/a/15458378/5432806)

## 回滚到指定 commit id
```shell
git reset --hard 0682c06
git reset --soft HEAD@{1}
git commit -m "Reverting to the state of the project at 0682c06"
```
有时需要**撤销以前的单个或多个 commit**, 可用以下命令:
```shell
git revert -n <commit id> # -n means --no-commit, 否则 git 在 revert 之后会自动 commit.
git revert -n <commit id> # 可以连续执行多次, 以 revert 多个 commit.
git commit -m "revert commit_id1 and commit_id2"
# 也可以用 git revert --continue, 来提交. 但默认的注释只包含第一次 revert 的 commit id.
```

**Reference**
- https://stackoverflow.com/a/1895095/5432806
- http://serebrov.github.io/html/2014-01-04-git-revert-multiple-recent-comments.html
- https://stackoverflow.com/a/4114122/5432806


## save HTTPS account
1. **(Recommended)** attach credential to remote repository url: `https://username:password@git.coding.net/username/repository.git`
1. store temporarily: execute command `git config credential.helper 'cache --timeout=60'`, then credential will expire after 60 seconds since last use.
2. store permanently: execute command `git config --global credential.helper store`, then credential will be stored in file `~/.git-credentials` and `~/.gitconfig` permanently.

**Reference**:

- [git: revert (reset) a single file](http://www.norbauer.com/rails-consulting/notes/git-revert-reset-a-single-file.html)
