---
title: Git 常用命令
date: 2017-04-04
categories: linux
tags: [git]
---

### push tag:
1. git push \<remote\> tag_name # push a single tag
2. git push \<origin\> \--tags # push all tags

### undo all changes:
1. git clean -fd # delete all untracked files and directories
2. git reset \--hard # removes staged and working directory changes
3. git checkout /path/to/your/file # checkout a file from HEAD, and overwrite your change.

### save HTTPS account
1. edit `~/.git/https-credentials`,  
2. 
**Reference**:

- [git: revert (reset) a single file](http://www.norbauer.com/rails-consulting/notes/git-revert-reset-a-single-file.html)

