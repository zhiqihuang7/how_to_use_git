## Git & GitHub 实操全指南 (Linux/Ubuntu)

> 本笔记记录了从零开始构建项目、同步云端以及 Git 核心逻辑的操作流程。

---
## 第零部分：环境搭建与 SSH 钥匙配置（新电脑仅需做一次）

在开始使用 Git 前，必须先安装软件并打通本地与 GitHub 的免密通道。

### 1. 安装与身份登记
在 Ubuntu 终端执行：
```bash
sudo apt update
sudo apt install git

# 告诉 Git 你是谁（每次存档都会记录这个名字）
git config --global user.name "你的英文名" #最好和Github一样
git config --global user.email "你的GitHub注册邮箱"
```
### 2. 生成并配置 SSH 公钥 (Ed25519)
原理：在本地生成一把私钥（留在电脑里）和一把公钥（放到 GitHub 上）。以后推送代码时，两把钥匙匹配即可免密通行。
```bash
# 生成密钥（一路回车即可）
ssh-keygen -t ed25519 -C "你的邮箱"

# 查看并复制公钥内容
cat ~/.ssh/id_ed25519.pub
```
配置到 GitHub：

1. 复制终端里以 ssh-ed25519 开头的一整行内容。

2. 登录 GitHub -> 右上角头像 -> Settings -> SSH and GPG keys -> New SSH key。

3. 随便填个 Title，Key type 选 Authentication Key，粘贴内容并保存。


## 第一部分：从 0 开始新建项目并关联 GitHub

当你有一个新点子，想要开始写代码并同步到 GitHub 时，请遵循以下流程：

### 1. 本地初始化
在 Ubuntu 终端执行：
```bash
mkdir my_project          # 新建项目文件夹
cd my_project             # 进入文件夹
git init                  # 初始化 Git 仓库，生成隐藏的 .git 文件夹
```
### 2. 配置“防火墙” (极其重要)
在项目根目录新建 .gitignore 文件，屏蔽 IDE 产生的杂乱文件：
```bash
.idea/
__pycache__/
venv/
*.pyc
#这几个必须有
#之后还有文件要忽略，遵守格式 .file_name/
```
### 3. 关联 GitHub 远程仓库
1在 GitHub 网页新建一个同名仓库（不要勾选初始化 README）。

2获取 SSH 地址（格式如：git@github.com:用户名/仓库名.git）。

3在本地终端输入：
```bash
git remote add origin git@github.com:用户名/仓库名.git
git branch -M main       # 将默认分支更名为 main
```

#### ATTENTION : 也可直接复制粘贴Github上给出的代码到Pycharm终端，更方便，但是要做完第一次
```bash
git add .
```
#### 以及
```bash
git commit -m "巴拉巴拉巴拉"
```
#### 才可以成功上传


### 4. 第一次存档与发射
```bash
git add .                # 追踪所有文件
git commit -m "initial commit: 开启新项目" 
git push -u origin main  # 第一次推送需加 -u，以后直接 git push
```


## 第二部分：日常写代码的循环流程
### 1. 在 PyCharm 中编写、调试你的代码。
### 2. 状态探测：
```bash
git status            # 看看哪些文件变红了（改动了）
```
### 3. 暂存变动：
```bash
git add . #把所有变动放入暂存区
```

### 4. 存档并同步
```bash
git commit -m "feat:增加了什么功能"
git push        #推送到GitHub
```
#### 4.1. 写commit内容的时候，可以遵守以下约定俗成的前缀
```bash
feat: #加了新功能
fix: #修复了代码或逻辑错误
docs: #只改了REARMED，注释，笔记等
style: #不影响代码的改动，格式方面，空格什么的
refactor: #代码重写
chore: #杂事，构建过程或者辅助工具的改变，比如改.gitignore
```

## 第三部分：回溯
根据后悔程度选择对应指令
### 级别 1：代码改乱了，但还没 add (清空工作区改动)
你刚写了几行代码，发现思路全错，想让文件瞬间回到上一次存档的样子：
```bash
git restore <文件名>      # 撤销单个文件的修改
git restore .            # 撤销当前目录下所有文件的修改
```

### 级别 2：不小心 add 进去了，但还没 commit (移出暂存区)
比如你不小心 git add . 把测试用的大文件也放进去了：
```bash
git restore --staged <文件名>   # 把文件从暂存区拿出来，但你写的代码还在！
```

### 级别 3：已经 commit 了，但发现漏了文件或写错了注释 (软回溯)
想撤销刚刚的那次 commit 动作，但保留你在文件里敲的代码：
```bash
git reset --soft HEAD~1   # HEAD~1 代表往回退 1 步。代码会回到刚 add 完的状态。
```

### 级别 4：彻底搞砸，需要回到历史某次存档 (强制回溯)
当代码写炸了，需要强制回到过去某个哈希值（Commit Hash）对应的状态时：
### 1. 获取“时空坐标”
```bash
git log --oneline        # 查看简洁的历史记录
```
你会看到类似：7a2b3c4 fix: 删除了错误的函数。其中 7a2b3c4 就是哈希值。

### 2. 强制回溯
```bash
git reset --hard 7a2b3c4
```
原理： --hard 会重置你的工作区、暂存区和本地仓库。执行后，你文件夹里的代码会瞬间变回那个时刻的样子。谨慎使用！


