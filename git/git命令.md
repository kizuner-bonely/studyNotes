# 初始化 git

**设置用户名**

```
git config --global user.name 'xxx'
```

**设置用户名邮箱**

```
git config --global user.email 'xxx'
```

## 添加文件到git仓库

**创建文件夹**

```
mkdir 项目名
```

**在文件内初始化 git**

```
git init
```

**通过创建文件向仓库添加文件**

```
touch 文件名
```

# git 命令

**查看工作区文件**

```
git status
```

**将工作区文件提交到暂存区**

```
git add 文件名
```

**将暂存区文件提交到仓库**

```
git commit -m "提交描述"
```

**修改 git 仓库文件**

```
vi 文件名
```

**将项目克隆到本地**

```
git clone 仓库地址
```

**将代码托管到 GitHub**

```
git push
```

**查看当前 git 配置**

```
git config --list
```

# 分支

现有如下场景：

1. 开发某个网站
2. 为实现某个新用户的需求创建一个分支
3. 在这个分支上开展工作

**然而**，此时突然接到一个电话说有一个bug需要紧急修复，这时我需要

1. 切换到我的线上分支
2. 为这个紧急任务新建一个分支并在其中修复它
3. 在测试通过之后切换到线上分支然后合并修复好的分支
4. 将改动推送到线上分支
5. 回到最初工作的分支上

**1. 创建一个解决bug的分支**

假设要解决的问题名字叫 #53，则要运行下列命令

```
$ git checkout -b iss53

or

$ git branch iss53
$ git checkout iss53
```

**2.修改好bug后向本地仓库提交新代码**

```
$ git commit -a -m 'fixed a bug [issue 53]'
```

**3.将新建分支合并到主分支上**

```
$ git merge iss53
```

**4.删除新建的分支**

```
$ git branch -d iss53
```

**5. 提交完代码后要回到原来的主分支继续工作**

```
$ git checkout master
```



> ## 创建独立分支

```shell
$cd repo
$git checkout --orphan 新分支名 <start_point>

#删除此分支中的索引及索引中的所有文件
$git rm -rf .

#创建git索引
$git add .
#initial commit
$git commit -a -m "initial commit"
#push到远端仓库
$git push origin 新分支名
```



# 源

```bash
git remote set-url origin NewGitURL
```

`fatal: refusing to merge unrelated histories`

```bash
git pull origin main --allow-unrelated-histories
```

