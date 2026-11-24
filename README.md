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

1. 确认 Accio 正在运行，且 Agent 权限模式为 `Auto`；
2. 用 Safari 打开：

```text
https://<owner>.github.io/accio-gateway-deeplink-poc/
```

3. 页面显示 `POST sent (opaque)`；
4. Accio 自动创建会话，Agent 回复 `ACCIO_GATEWAY_GITHUB_OK`；
5. 如需验证命令执行，打开：

```text
https://<owner>.github.io/accio-gateway-deeplink-poc/id.html
```

Accio 会执行 `id` 并回显 `uid=... gid=...`。

## 浏览器差异

- Safari：页面加载即发送，无本地网络权限提示，已实测；
- Chrome 新版本的 Private Network Access / Local Network Access 可能弹出“访问本地网络”授权，或直接拦截 public → local 请求；这是浏览器侧限制，不改变服务端未鉴权的事实。
