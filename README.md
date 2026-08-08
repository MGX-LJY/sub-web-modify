# sub-web-modify

MGX 自用订阅转换服务，由精简版 Vue 前端和定制版 subconverter 后端组成。

当前版本面向 Clash 与 Surge 6，默认连接 `https://sub.mgxnet.com`，并针对 AnyTLS 节点补充证书 SHA-256 指纹的解析与输出支持。

## 功能

- Vue 2 + Element UI 单页订阅转换界面
- 支持生成 Clash 和 Surge 6 配置
- Surge 6 配置可直接下载
- 默认启用 UDP 和 TCP Fast Open
- 默认使用 MGX 远程配置 `MGX_list.ini`
- 自动生成 `MGX-Clash.yaml` 或 `MGX-Surge6.conf` 文件名
- Nginx 同源代理 `/sub` 和 `/version`，避免浏览器跨域问题
- 定制 subconverter 支持 AnyTLS 证书 SHA-256 指纹
- 前端与后端均通过 MGX 私有 Docker Registry 发布

## 项目结构

```text
.
├── src/                         Vue 前端源码
├── Dockerfile                  前端构建与 Nginx 运行镜像
├── nginx.conf                  SPA 路由及后端反向代理
├── Dockerfile.subconverter     定制 subconverter 构建镜像
├── subconverter-anytls.patch   AnyTLS 指纹支持补丁
├── docker-compose.yml          生产部署编排
└── .env.production             前端生产环境配置
```

## 生产镜像

当前最新版已发布到 MGX 私有 Docker Registry：

```text
registry.mgxnet.com/mgx/sub-web:latest
registry.mgxnet.com/mgx/subconverter-mgx:latest
```

后续生产构建统一推送到 `registry.mgxnet.com/mgx/` 命名空间，不再以 Docker Hub 镜像作为生产发布目标。

## 快速部署

仓库中的 `docker-compose.yml` 默认使用上述两个生产镜像：

```bash
docker login registry.mgxnet.com -u mgxnet
docker compose pull
docker compose up -d
```

默认端口映射为：

```text
192.168.31.4:8090 -> sub-web:80
```

如需在其他主机部署，请先修改 `docker-compose.yml` 中的宿主机监听地址。

前端通过 Nginx 将以下路径转发到 Compose 网络中的 `subconverter:25500`：

| 路径 | 用途 |
| --- | --- |
| `/version` | 查询 subconverter 版本 |
| `/sub` | 生成转换后的订阅配置 |

## 本地开发

要求 Node.js 18+ 和 Yarn 1.x。

```bash
yarn install --frozen-lockfile
yarn serve
```

生产构建：

```bash
yarn build
```

生产环境默认后端定义在 `.env.production`：

```dotenv
VUE_APP_SUBCONVERTER_DEFAULT_BACKEND=https://sub.mgxnet.com
```

## 构建镜像

构建前端：

```bash
docker build -t registry.mgxnet.com/mgx/sub-web:latest .
```

构建定制后端：

```bash
docker build \
  -f Dockerfile.subconverter \
  -t registry.mgxnet.com/mgx/subconverter-mgx:latest \
  .
```

推送两个镜像：

```bash
docker push registry.mgxnet.com/mgx/sub-web:latest
docker push registry.mgxnet.com/mgx/subconverter-mgx:latest
```

`Dockerfile.subconverter` 固定上游 subconverter 提交，构建时应用 `subconverter-anytls.patch`。升级上游版本时应先验证补丁仍可干净应用，再更新固定提交。

## AnyTLS 适配

定制补丁在后端完成以下处理：

- 从 Clash AnyTLS 节点读取 `fingerprint`
- 从 Surge AnyTLS 节点读取 `server-cert-fingerprint-sha256`
- 输出 Clash 配置时保留 `fingerprint`
- 输出 Surge 配置时生成 `server-cert-fingerprint-sha256`
- 将连续的 64 位 SHA-256 十六进制字符串转换为冒号分隔格式

前端的 Surge 6 选项使用 subconverter 的 `target=surge&ver=5` 参数，这是当前后端的 Surge 输出参数约定。

## Registry 凭据

Registry 密码和 HTTP Secret 保存在本机敏感文档：

```text
/Users/martinezdavid/docker/README.md
```

凭据禁止写入本仓库、镜像、Compose 文件或公开文档。

## 致谢与许可

本项目基于 `youshandefeiyang/sub-web-modify` 修改，后端基于 `tindy2013/subconverter` 定制。许可信息见 [LICENSE](LICENSE)。
