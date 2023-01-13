## 安装 Node
```bash
https://nodejs.org/en/download/

wget https://nodejs.org/dist/v16.14.2/node-v16.14.2-linux-x64.tar.xz

xz -d node-v16.14.2-linux-x64.tar.xz
tar xf node-v16.14.2-linux-x64.tar


mv node-v16.14.2-linux-x64 /usr/local/node-v16

%load /etc/profile

export NODEJS=/usr/local/node-v16.14.0
export PATH=$PATH:$NODEJS/bin

%%writefile /etc/profile

source /etc/profile

```

## Node配置

```bash

npm config set registry http://registry.npm.taobao.org/

npm config get registry

npm install -g cnpm --registry=https://registry.npm.taobao.org

npm install npm@latest -g 

```