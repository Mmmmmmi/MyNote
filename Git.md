# **Git**

# **基础操作**

```c++
//查看历史commit
git log

//查看最近commit
git reflog

//撤回至某一次提交的结果
git reset --hard commit_id

//撤回commit，且保留更改
git reset --soft HEAD^

//只更改 commit 的注释，输入完后会进入vim 进行修改
git commit --amend

//修改commit 注释时提示 vi not find 修改默认编辑器即可
git config --global core.editor vim
```

