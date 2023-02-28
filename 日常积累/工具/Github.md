

# Git

```

git remote add upstream git@github.com:EpicGames/UnrealEngine.git

git fetch upstream ue5-early-access

git add *

git commit -m "upload"

git checkout ue5-early-access
```



## 工作机制

- 工作区
  - 磁盘区
  - 文件状态
    - 未跟踪
    - 未修改
    - 已修改
    - 已暂存
- 暂存区
  - 位于`.git/index`
  - 命令
    - `git add`
    - `git stash save`
- 本地库
  - `git commit`
  - 本地保存的也是完整项目
    - 历史记录
- 远程库
  - 服务器仓库

## 命令

### 常用命令

```python
git config --global user.name ouczbs
git config --global user.email ouczbs@qq.com
git init
git status
git add .* #（文件名字）
git commit -m "日志信息" 文件名
git reflog
git reset --hard #版本号
```

## 网络慢

### 端口配置

```python
set http_proxy=http://127.0.0.1:7890
set https_proxy=http://127.0.0.1:7890
```

## 权限验证

### SSH

无需密码

Github 添加公钥

SourceTree 添加私钥

换 SSH 可能导致拒绝访问

### HTTP

需要输入用户名和密码

```python
git config --global credential.helper store
```



## 学习

https://learngitbranching.js.org/?locale=zh_CN



