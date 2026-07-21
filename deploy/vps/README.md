# Paperless-ngx — VPS 部署

独立 Docker 栈：仅监听 `127.0.0.1:8000`，由 [vps_nginx](https://github.com/xiaolitongxue666/vps_nginx) 反代 **`/paperless/`**。

公网：将 `paperless` 加入 `VPS_NGINX_PUBLIC_EXPOSE`，访问  
`https://xiaolitongxue.com.cn/paperless/`（与 `/blog/`、`/freshrss/` 同模式，非独立子域名）。

## 路径

| 环境 | 路径 |
|------|------|
| VPS | `/home/ubuntu/Code/VPS/paperless-ngx` |
| Compose | `deploy/vps/` |
| 凭证（VPS 本地，勿提交） | `deploy/vps/admin-credentials.txt` / `docker-compose.env` |

## 启动

```bash
cd /home/ubuntu/Code/VPS/paperless-ngx/deploy/vps
cp docker-compose.env.example docker-compose.env
# PAPERLESS_URL=https://xiaolitongxue.com.cn  （origin only，无 path）
# PAPERLESS_FORCE_SCRIPT_NAME=/paperless
docker compose pull && docker compose up -d
```

vps_nginx：`PUBLIC_EXPOSE` 含 `paperless` 后 `sudo -E ./scripts/deploy.sh`。

## 快速阅读（v2.20 镜像）

```bash
PAPERLESS_OCR_MODE=skip
PAPERLESS_OCR_SKIP_ARCHIVE_FILE=always
PAPERLESS_OCR_OUTPUT_TYPE=pdf
```

大文件可丢进 `consume/` 自动入库。内置 PDF 阅读器≈pdf.js，**沉浸式翻译通常无法译正文**；精读双语请用 [Kavita](https://github.com/xiaolitongxue666/Kavita) + **EPUB**（`/kavita/`）。

## 约束

- 勿并入 RSS 栈；公网暴露须强密码（个人文档）。
- 探测：`curl --noproxy '*'`。
- 与 Kavita：本栈归档/搜索；阅读进度与 EPUB 双语见 `/home/ubuntu/Code/VPS/kavita/deploy/vps`。
