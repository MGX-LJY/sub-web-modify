# sub-web-modify · 订阅转换前端 (MGX 精简版)

![Docker Pulls](https://img.shields.io/docker/pulls/doctor/sub-web-modify)
![Image Size](https://img.shields.io/docker/image-size/doctor/sub-web-modify/latest)

> **基于 [youshandefeiyang/sub-web-modify] 魔改**  
> - 去广告、去推广  
> - 仅保留本地后端 `http://localhost:25500`  
> - 远程配置精简为 `MGX.ini`  
> - 默认暗黑 / 亮色自动跟随系统  
> - 支持生成 Clash / Surge / Sing-box / v2ray / Trojan 等多格式订阅  
>
> 后端使用 **[tindy2013/subconverter](https://github.com/tindy2013/subconverter)** 官方版本。

---

## 目录

- [镜像标签](#镜像标签)
- [快速开始](#快速开始)
- [环境变量](#环境变量)
- [版本更新](#版本更新)
- [许可协议](#许可协议)

---

## 镜像标签

| 标签 | 基础镜像 | 说明 |
|------|----------|------|
| `latest` | `nginx:1.24-alpine` | 滚动更新 |
| `2025.07.20` | 同上 | 对应 Git commit `abcdefg` |

---

## 快速开始

```bash
# ① 拉取前端
docker pull doctor/sub-web-modify:latest

# ② 拉取官方后端
docker pull tindy2013/subconverter:latest

# ③ 单机 docker-compose
cat > docker-compose.yml <<'EOF'
version: '3.9'
services:
  subconverter:
    image: tindy2013/subconverter:latest
    container_name: subconverter
    ports:
      - "25500:25500"
  sub-web:
    image: doctor/sub-web-modify:latest
    container_name: sub-web
    ports:
      - "8090:80"
    environment:
      - VUE_APP_BACKEND_URL=http://subconverter:25500
EOF

docker compose up -d
