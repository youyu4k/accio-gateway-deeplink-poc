# Accio gateway deeplink PoC — GitHub Pages

授权安全测试页面。页面只向本机 Accio Gateway `http://127.0.0.1:4097/desktop/deeplink` 发送一条 `no-cors` POST，用于验证“任意网页可静默向本地 Agent 注入消息”。

- `index.html`：非破坏性 canary，只让 Agent 回复 `ACCIO_GATEWAY_GITHUB_OK`。
- `id.html`：授权测试页，只让 Agent 执行 `id` 并回复 stdout。
- `.nojekyll`：关闭 Jekyll 处理，按静态文件原样提供。

不包含任何账号、Cookie、内网地址或用户数据；请勿把测试产生的会话内容提交到仓库。

## 部署

```bash
cd github-pages
git init
git add .
git commit -m "Accio gateway deeplink PoC"
gh repo create accio-gateway-deeplink-poc --public --source=. --remote=origin --push
```

然后在仓库 Settings → Pages 选择 `main` / `/ (root)`，或使用：

```bash
gh api -X POST repos/<owner>/accio-gateway-deeplink-poc/pages \
  -F 'source[branch]=main' -F 'source[path]=/'
```

## 验证

已发布地址：

```text
https://youyu4k.github.io/accio-gateway-deeplink-poc/
https://youyu4k.github.io/accio-gateway-deeplink-poc/id.html
```

1. 确认 Accio 正在运行，且 Agent 权限模式为 `Auto`；
2. 用 **Chrome** 打开 canary 页面；
3. 页面显示 `POST sent (opaque)`；
4. Accio 自动创建会话，Agent 回复 `ACCIO_GATEWAY_GITHUB_OK`；
5. 打开 `id.html`，Accio 执行 `id` 并回显 `uid=... gid=...`。

本地实测（2026-11-25）：Chrome 158.0.8127.38 两个页面均成功触发；Accio 分别回复 `ACCIO_GATEWAY_GITHUB_OK` 和执行 `id`。

## 浏览器差异

- **Chrome 158**：公网 HTTPS 页面直接成功，无本地网络权限提示；
- **Safari 26.5.2**：同一 HTTPS 页面报 `TypeError: Load failed`，浏览器拦截 HTTPS→`http://127.0.0.1`；
- Safari 场景可用任意 HTTP 页面投递（例如局域网 HTTP 页面，已实测成功）。
- 浏览器差异不改变服务端未鉴权的事实。
