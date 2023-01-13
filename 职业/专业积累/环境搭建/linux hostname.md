## 修改主机名

- linux

  - ```
    vi /etc/hostname #修改主机名
    
    vi /etc/sysconfig/network #修改网络名
    #-- HOSTNAME=ouczbs
    
    vi /etc/hosts #修改本地映射，可以在网页上快捷访问
    #号开头的行做说明，不被系统解释。
    #第一部份：网络IP地址。
    #第二部份：主机名.域名，注意主机名和域名之间有个半角的点。
    #第二部份：主机名(主机名别名） ，其实就是主机名。
    ```

    
    