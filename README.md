# openseo-lzcapp

OpenSEO（Semrush / Ahrefs 的开源替代）的懒猫微服打包仓库。镜像模式：直接引用上游官方多架构镜像 `ghcr.io/every-app/open-seo-standalone` 与 RustFS 存储镜像。

## 与上游 compose 的差异

上游 `deploy/standalone/compose.yaml` 是 5 服务结构（storage / runtime / init / crawl-gateway / web），LazyCat 打包做了等价改造：

- **init sidecar**（一次性初始化：配置生成、存储就绪等待、celld 部署、数据库迁移）→ 合并为 runtime 服务的 `setup_script`，在应用启动前完成。逻辑与上游 `init.sh` 一致。
- **web（nginx 反代）** → 省略，LazyCat 网关直接反代 runtime:8080（上游 nginx 只加了安全头与少量路径拦截，不影响核心功能）。
- **命名卷** → `/lzcapp/var` 下的持久目录。
- **存储凭据** → `{{ stable_secret }}` 生成稳定随机值，与上游 `.env` 的 STORAGE_ACCESS_KEY/SECRET_KEY 等价。
- 上游镜像 digest 锁定的 nginx/rustfs 基础镜像沿用；RustFS `1.0.3` digest 与上游 compose 完全一致。

## 版本跟踪

跟踪上游 GHCR 镜像的 semver tag（`0.1.x`，同时兼容 `v` 前缀形状），上游发新版自动更新发布。

## 外部服务凭据

DataForSEO / OpenRouter / Google OAuth 等凭据通过部署参数注入（对应上游 `.env`），均为可选，可后续在应用设置里配置。

上游：[every-app/open-seo](https://github.com/every-app/open-seo)（MIT）
