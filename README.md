# local_git
 ***个人学习仓库***
- 第一次修改
1. 本地修改，尝试上传到origin
2. git修改，尝试本地获取更新

### **日常操作代码记录**
- 获取远程仓库最新代码：```git fetch origin main```
- 比较版本不同：```git log -p main ..origin/main```
- 与本地合并：```git merge origin/main```
- 拉取：```git pull```
- 放入暂存区：```git add [file1] [file2]```
- 添加备注：```git commit -m 'message'```
- 查看暂存区文件：```git ls-files```
- 本地代码推送到远程仓库：```git push origin main```
  - ```git push <远程主机名> <本地分支名>:<远程分支名>```
  - 分支名一致可省略冒号，并只写一个 *例上*
- 强制推送：```git push --force origin main```
- 删除主机分支：```git push origin --delete master```
- 放弃所有缓存：```git reset HEAD .```
- 放弃掉所有还没有加入到缓存区的修改：```git checkout .```

### **配置记录**
- 设置ssl验证：```git config --global http.sslVerify “false”```
- 更改局部用户名：```git config user.name "xxxx"```
- 更改局部邮箱：```git config user.email "xxxx"```
- 更改全局(也就是用户目录下.gitconfig文件)：```git config --global user.name "xxxx"```
- 访问超时可尝试此操作
  - 设置代理：```git config --global https.proxy```
  - 取消代理：```git config --global --unset https.proxy```
