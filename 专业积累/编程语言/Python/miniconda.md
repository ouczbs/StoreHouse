## 安装

- linux

  ```shell
  #下载 安装
  wget -c https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh #下载最新版本
  chmod 777 Miniconda3-latest-Linux-x86_64.sh #给执行权限
  bash Miniconda3-latest-Linux-x86_64.sh #运行
  
  #配置使用
  #conda config --set auto_activate_base false #取消默认激活conda环境
  reboot # 重启可自动激活 bash
  
  ```

	- [[jupyter|安装 jupyter]]