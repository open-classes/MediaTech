# GIT命令使用指南

GIT命令用的好，可以事半功倍。对于项目管理、版本管理和代码审核尤其重要。磨刀不误砍柴工。多用才能妙用，才会产生神来之笔，加油同勉！

> 心即理，知行合一，事上磨练，致良知。

## 1 git分支管理

- 分支管理

``` bash
home $ git checkout origin/master -b master
home $ git branch -a
home $ git remote -v
```

- 重置分支

``` bash
home $ git checkout master
home $ git reset --hard HEAD
home $ git reset --hard HEAD~1
home $ git reset --hard HEAD~2
```

- 删除远程分支并更新本地远程分支

``` bash
home $ git push origin  :${name_to_delete}
home $ git remote update origin --prune
```

## 2 git用户管理

- 查看用户配置信息

``` bash
home $ git config --list
```

- 修改用户配置信息

``` bash
# 配置全局用户信息
home $ git config --global user.name  "Author"
home $ git config --global user.email "Email"

# 配置本地用户信息
home $ git config user.name  "Author"
home $ git config user.email "Email"
```

## 3 git工程统计

- 提交次数

``` bash
home $ git shortlog --numbered --summary
```

- 查看所有的commit数

``` bash
home $ git log --oneline | wc -l
home $ git log --stat
home $ git log --graph --pretty=oneline --abbrev-commit
```

## 3 git代码管理

- cherry-pick提交

``` bash
home $ git checkout second
home $ git cherry-pick master
home $ git cherry-pick master~1
home $ git cherry-pick master~2
home $ git push origin second:second
```

- git合并多个提交

``` bash
home $ git log --graph --pretty=oneline --abbrev-commit
home $ git rebase -i HEAD~2
```

## 4 git常见问题

## 参考文献

- [1 Git Community Book 中文版](http://gitbook.liuhui998.com/)
- [2 Git Documents](https://git-scm.com/docs)