# 如何使用git clone一个指定文件或者目录

1.先创建一个空目录

```shell
mkdir -p /use/dir
```

2.进入创建的目录

```shell
cd /use/dir
```

3.执行git init 初始化git

   ```shell
   git init
   ```
4.和远程git 库进行关联

   ```shell
   git remote add -f origin git@git.xxx.com:xxx/xxx.git
   ```
5.开启稀疏检出

   ```shell
   git config core.sparsecheckout true
   ```
6.sparse-checkout文件里写入要拉取的文件或者文件夹,我们可以通过编辑.git/info/sparse-checkout文件来定义我们要克隆的文件或文件夹。例如，我们可以将以下内容添加到.git/info/sparse-checkout文件中

   ```shell
   echo "clone_file" >> .git/info/sparse-checkout
   ```
7.进行git checkout 指定分支

   ```shell
   git checkout master
   ```

8.完整的步骤
   mkdir -p /use/dir

```
cd /use/dir
  
  git init
  
  git remote add -f origin git@git.xxx.com:xxx/xxx.git
  
  git config core.sparsecheckout true
  
  echo "clone_file" >> .git/info/sparse-checkout
  
  git checkout master或者git pull origin <分支名称>
```
