# FATE单机部署指南

## 1. 说明

服务器配置：

| **数量**     | 1                                    |
| ------------ | ------------------------------------ |
| **配置**     | 8 core / 16G memory / 500G hard disk |
| **操作系统** | Version: CentOS Linux release 7 或者 Ubuntu 18.04     |
| **用户**     | User: app owner:apps                 |

## 2. 使用Docker镜像安装FATE

注意，如下示例中的${version}，请用实际的版本号替换，参考[fate.env](../../fate.env)文件中的FATE版本！

### 2.1 部署前环境检查

- 主机需要能够访问外部网络，从公共网络中拉取安装包和docker镜像。
- 依赖[docker](https://download.docker.com/linux/), docker建议版本为18.09，您可以使用以下命令验证docker环境：docker --version,docker的起停和其他操作请参考docker --help
- 执行之前，请检查**8080**是否已被占用。 如果要再次执行，请使用docker命令删除以前的容器和镜像

设置部署所需环境变量(注意, 通过以下方式设置的环境变量仅在当前终端会话有效, 若打开新的终端会话, 如重新登录或者新窗口, 请重新设置)

```bash
export version={本次部署的FATE版本号, 如1.11.2}
```

样例:

```bash
export version=1.11.2
```

### 2.2 拉取镜像

#### 2.2.1通过镜像包

   ```bash
wget https://webank-ai-1251170195.cos.ap-guangzhou.myqcloud.com/fate/${version}/release/standalone_fate_docker_image_${version}_release.tar.gz

docker load -i standalone_fate_docker_image_${version}_release.tar.gz

docker images | grep federatedai/standalone_fate
   ```

   能看到对应${version}的镜像则镜像下载成功

### 2.3 启动

   ```bash
docker run -it --name standalone_fate -p 8080:8080 federatedai/standalone_fate:${version}
   ```

### 2.4 测试（这个每次登入docker镜像时都需要执行此命令进入env）

   ```bash
source bin/init_env.sh
   ```

   - [测试项](#3-测试项)

## 3. 测试项

### 3.1 Toy测试

   ```bash
flow test toy -gid 10000 -hid 10000
   ```

   如果成功，屏幕显示类似下方的语句:

   ```bash
success to calculate secure_sum, it is 2000.0
   ```

### 3.2 单元测试

   ```bash
fate_test unittest federatedml --yes
   ```

   如果成功，屏幕显示类似下方的语句:

   ```bash
there are 0 failed test
   ```

有些用例算法在 [examples](../../examples/dsl/v2) 文件夹下, 请尝试使用。

您还可以通过浏览器体验算法过程看板，访问：http://${ip}:8080, ip为`127.0.0.1`或本机实际ip。这个看板是查看程序日志的地方，跟踪算法运行过程也可以在这里实时查看。