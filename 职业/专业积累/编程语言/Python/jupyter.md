## 安装

- conda

  - ```shell
    #安装
    conda install jupyter # 安装jupyter
    
    #配置
    jupyter notebook --generate-config # 生成配置文件
    #c.NotebookApp.notebook_dir 配置根目录
    #c.NotebookApp.ip = '*' 允许所有ip访问
    
    #后台运行
    nohup jupyter notebook --allow-root > jupyter.log 2>&1 &
    ```
    
   - 后台运行
  
      - 用&让命令后台运行, 并把标准输出写入jupyter.log中
      - nohup表示no hang up, 就是不挂起, 于是这个命令执行后即使终端退出, 也不会停止运行.
  
    - 终止进程
    
      - ```shell
        ps -a 显示进程pid
        kill -9 pid 终止进程	
        ```