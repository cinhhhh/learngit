# learn markdown
## git创建远程仓库
1.登录github创建仓库，仓库名为learngit<br>
2.创建本地仓库，初始化,执行init命令<br>
`$ git init`<br>
执行后，目录中会出现.git文件夹（为隐藏文件夹），目录变为受git控制版本，执行添加远程仓库命令<br>
`$ git romote add origin git@github.com:cinhhhh/learn.git`<br>
执行后，可以把本地内容传到远程仓库，也可以从远程仓库拉取文件<br>
##git提交失败
提交时提示 `failed to push some refs to git`<br>
原因是远程仓库上有readme.md,本地仓库没有，可以执行命令<br>
`$ git pull --rebase origin master`<br>
先合并，再使用push提交