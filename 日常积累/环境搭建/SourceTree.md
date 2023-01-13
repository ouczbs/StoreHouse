- SSH
  - 修改SSH客户端配置
  - 选择OPEN SSH 与密钥
  - https://zhuanlan.zhihu.com/p/212302462
```
//github服务器上的SSL证书未经过第三方机构认证，git就会报错
git config --global http.sslverify false

ssh-keygen -t rsa -C "ouczbs@qq.com"

git config --global user.name "ouczbs"
git config --global user.email "ouczbs@qq.com"
```