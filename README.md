
# iBL3ND - Telegram 管理员转发与防骚扰机器人

iBL3ND 是一个基于 Cloudflare Workers 构建的 Telegram 中转机器人，
支持自动消息转发、管理员回复、屏蔽机制、人机验证、关键词拦截、以及远程拦截词表同步。




---

## 🚀 功能简介

- **管理员中转**：用户消息自动转发给管理员，管理员可直接回复。
- **人机验证**：首次或超过 3 小时未验证的用户需点击按钮验证。
- **屏蔽与解封**：管理员可 `/block` `/unblock` 用户。
- **关键词过滤**：支持本地和远程拦截词（自动定时刷新）。
- **多语言提示**：支持中英文自动识别。
- **节流提醒**：用户等待提示每小时最多出现一次。
- **Webhook 自动注册**：支持一键注册、注销、查看。

---

## ⚙️ 环境变量

| 变量名 | 示例值 | 说明 |
|--------|---------|------|
| `ENV_BOT_TOKEN` | `123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11` | Telegram Bot Token |
| `ENV_BOT_SECRET` | `secret_value` | Webhook Secret Token |
| `ENV_ADMIN_UID` | `"123456789"` | 管理员的 Telegram UID（纯数字字符串） |

---

## 🛠️ 部署步骤

1. 打开 [Cloudflare Dashboard](https://dash.cloudflare.com) → **Workers & Pages** → 新建 Worker  
2. 将 `worker.js` 全部代码复制进去  
3. 在 “Settings → Variables → Environment Variables” 添加上述三个环境变量  
4. 绑定 KV：  
   - 命名空间名称任意  
   - 绑定变量名固定为：`nfd`  
5. 部署后访问：  
   - `https://你的域名/registerWebhook` 注册 Webhook  
   - `https://你的域名/debugWebhook` 查看绑定状态  
6. 使用 `/setMenu` 更新命令菜单（管理员 & 用户菜单分别设置）  

---

## ⚙️ 注意事项：替换默认远程资源

部署前，请根据自己的仓库或存储路径，修改以下三个远程文件 URL：

| 变量 | 说明 | 默认地址 |
|------|------|-----------|
| **START_MSG_ZH_URL** | 机器人 `/start` 指令时加载的中文欢迎内容 | [startMessage.zh.md](https://raw.githubusercontent.com/IBL3ND/iBL3ND-bot/refs/heads/main/iBL3ND.ZH.MD) |
| **START_MSG_EN_URL** | 机器人 `/start` 指令时加载的英文欢迎内容 | [startMessage.en.md](https://raw.githubusercontent.com/IBL3ND/iBL3ND-bot/refs/heads/main/iBL3ND-US.MD) |
| **DEFAULT_BLOCKLIST_URL** | 自动拉取的远程拦截词表地址 | [blocklist.txt](https://raw.githubusercontent.com/IBL3ND/iBL3ND-bot/refs/heads/main/iBL3ND-AD.TXT) |

### ✅ 修改方法

在 Worker 代码顶部找到以下三行：

```js
const START_MSG_ZH_URL = 'https://raw.githubusercontent.com/IBL3ND/iBL3ND-bot/refs/heads/main/iBL3ND.ZH.MD';
const START_MSG_EN_URL = 'https://raw.githubusercontent.com/IBL3ND/iBL3ND-bot/refs/heads/main/iBL3ND-US.MD';
const DEFAULT_BLOCKLIST_URL = 'https://raw.githubusercontent.com/IBL3ND/iBL3ND-bot/refs/heads/main/iBL3ND-AD.TXT';
```

替换为你自己仓库或其他托管平台上的文件地址。例如：

```js
const START_MSG_ZH_URL = 'https://raw.githubusercontent.com/<你的GitHub用户名>/<你的仓库>/refs/heads/main/iBL3ND.ZH.MD';
const START_MSG_EN_URL = 'https://raw.githubusercontent.com/<你的GitHub用户名>/<你的仓库>/refs/heads/main/iBL3ND-US.MD';
const DEFAULT_BLOCKLIST_URL = 'https://raw.githubusercontent.com/<你的GitHub用户名>/<你的仓库>/refs/heads/main/iBL3ND-AD.TXT;
```

---

## 🧩 管理员命令

| 命令 | 说明 |
|------|------|
| `/block` | 屏蔽用户（需回复用户消息） |
| `/unblock` | 解除屏蔽（需回复用户消息） |
| `/checkblock` | 查看屏蔽状态 |
| `/addkw <关键词>` | 添加屏蔽关键词 |
| `/rmkw <关键词>` | 移除屏蔽关键词 |
| `/listkw` | 查看本地关键词 |
| `/reloadblock` | 强制刷新远程拦截词 |
| `/listkw_remote` | 查看远程词表前 100 条 |
| `/listkw_all` | 查看本地 + 远程合并词表 |
| `/version` | 查看当前提示文案版本 |
| `/notifytest` | 测试用户等待提醒内容 |
| `/resetnotify <userId>` | 手动清理某个用户节流键 |

---

## 🧠 用户验证机制

- 用户第一次发消息前会要求验证（点击“✅ 我是人类”）。  
- 验证结果保存 3 小时，超时会自动要求重新验证。

---

## 🔔 用户提示节流

- “消息已转发给管理员，请耐心等待回复。如长时间未收到答复，可适当再次留言。”  
- 每个用户 **1 小时** 最多提示一次，可通过修改：

```js
const NOTIFY_INTERVAL = 3600 * 1000; // 1 小时
```

来调整。

---

## ⚡ 调试命令

| 路径 | 功能 |
|------|------|
| `/registerWebhook` | 重新注册 Webhook |
| `/unRegisterWebhook` | 注销 Webhook |
| `/debugWebhook` | 查看当前绑定状态 |
| `/setMenu` | 更新命令菜单 |

---

 