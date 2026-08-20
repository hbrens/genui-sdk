# 内网离线部署（Playground）

本仓库已把 `tiny-schema-renderer`（原 git submodule）转为普通目录并提交，
克隆 / 下载 ZIP 后**无需再访问 GitHub**，所有代码已自包含。

## 1. 获取代码

- 方式一：GitHub 网页 `Code → Download ZIP`，解压即可。
- 方式二：内网 Git 服务同步本仓库（已含完整 renderer 代码）。

## 2. 配置 npm 镜像

```bash
pnpm config set registry https://registry.npmmirror.com
```

## 3. 配置 LLM 端点

复制 `.env.example` 为 `.env`：

```bash
cp sites/playground/server/.env.example sites/playground/server/.env
```

### 修改两处：

**`sites/playground/server/.env`** — 配置自定义 OpenAI 兼容端点的密钥：

```bash
OPENAI_API_KEY=<your-api-key>
```

**`sites/playground/server/maas-models.json`** — 配置内网 LLM 地址与模型名：

```json
{
  "openai": {
    "name": "openai",
    "baseUrl": "http://your-internal-llm:port/v1",
    "apiKeyEnvName": "OPENAI_API_KEY",
    "models": [
      { "name": "gpt-5.6-luna", "id": "gpt-5.6-luna" }
    ]
  }
}
```

> `baseUrl` 换成内网 LLM 服务地址，`models[].name` / `models[].id` 换成实际模型名。
> `providerModelsPath=maas-models.json` 已默认指向该文件（server 的 tsx watch 会监听它，改完自动重启）。

## 4. 安装依赖并启动

```bash
pnpm install

# 如提示 vite-commit-hash-plugin 无 dist，先执行：
pnpm --filter vite-commit-hash-plugin build

pnpm dev
```

- Web 前端：http://localhost:5173
- Server API：http://localhost:3008（验证：`curl http://localhost:3008/get-models`）

## 注意

- `.env` 含密钥，已被 `.gitignore` 忽略，不会随代码提交/下载；内网需自行创建。
- 依赖通过 npm 镜像安装，首次 `pnpm install` 较慢属正常。
