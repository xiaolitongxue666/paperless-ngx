# Paperless-ngx — VPS 部署

独立 Docker 栈：仅监听 `127.0.0.1:8000`，由 [vps_nginx](https://github.com/xiaolitongxue666/vps_nginx) 反代 **`/paperless/`**。生产 compose 用 `paperless-ngx:latest`（约 2.5G）。可用 &lt; 2.5G 时不要 `pull`。勿把运行中的 latest 换成 `3.1.3`。

## 部署（生产唯一入口）

```bash
cd deploy/vps
COMPOSE_IGNORE_ORPHANS=true docker compose up -d --no-build
# 回滚：上一 latest digest + named volume；不要 bootstrap、不要 volume rm
```

**single**：`docker compose config` + `curl --noproxy '*' http://127.0.0.1:8000/`。**related**：`/paperless/`（要 vps_nginx）。

公网：将 `paperless` 加入 `VPS_NGINX_PUBLIC_EXPOSE`，访问  
`https://xiaolitongxue.com.cn/paperless/`（与 `/blog/`、`/freshrss/` 同模式，非独立子域名）。

## 路径

| 环境 | 路径 |
|------|------|
| 本机 | `Code/VPS/paperless-ngx` |
| VPS | `/home/ubuntu/Code/VPS/paperless-ngx` |
| Compose | `deploy/vps/` |
| 凭证（勿提交） | `deploy/vps/admin-credentials.txt` / `docker-compose.env` |

## 启动

```bash
cd deploy/vps   # 本机 Code/VPS/paperless-ngx；VPS 历史树同上
cp docker-compose.env.example docker-compose.env   # 已 gitignore
# PAPERLESS_URL=https://xiaolitongxue.com.cn  （origin only，无 path）
# PAPERLESS_FORCE_SCRIPT_NAME=/paperless
docker compose pull && docker compose up -d
```

vps_nginx：`PUBLIC_EXPOSE` 含 `paperless` 后 `sudo -E ./scripts/deploy.sh`。

## 快速阅读（3.1.3 镜像）

```bash
PAPERLESS_OCR_MODE=skip
PAPERLESS_OCR_SKIP_ARCHIVE_FILE=always
PAPERLESS_OCR_OUTPUT_TYPE=pdf
```

大文件可丢进 `consume/` 自动入库。内置 PDF 阅读器≈pdf.js，**沉浸式翻译通常无法译正文**；精读双语请用 [Kavita](https://github.com/xiaolitongxue666/Kavita) + **EPUB**（`/kavita/`）。

## 约束

- 生产镜像 `ghcr.io/paperless-ngx/paperless-ngx:latest`。OCR 出站走 **17890**。
- overlay 只留在 `deploy/vps/`。上游：`https://github.com/paperless-ngx/paperless-ngx`。
- 勿并入 RSS 栈；公网暴露须强密码（个人文档）。
- 探测：`curl --noproxy '*'`。
- 与 Kavita：本栈归档/搜索；阅读进度与 EPUB 双语见 `/home/ubuntu/Code/VPS/Kavita/deploy/vps`。勿把 `originals/` 当 Kavita library。
