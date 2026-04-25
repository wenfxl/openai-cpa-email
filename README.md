# openai-cpa-email

这是一个openai-cpa-email专属邮箱项目极简 Cloudflare Email Worker。

作用只有一个：

- 收到邮件后
- 读取原始邮件内容（`raw_content`）
- 本地项目收件

不依赖 D1、KV、R2，也不需要额外的域名环境变量。

---

## 一、需要的文件

你现在只需要这一个文件：

- `worker.js`

---

## 二、怎么部署到 Cloudflare Worker

### 1. 登录 Cloudflare
进入：

- `Cloudflare Dashboard`
- `Workers & Pages`

### 2. 新建 Worker
点击：

- `Create application`
- `Create Worker`

### 3. 粘贴代码
打开 Worker 在线编辑器后：

- 删除默认代码
- 把 `worker.js` 文件内容全部复制进去
- 点击保存 / 部署

### 4. 添加环境变量
进入：

- `Worker`
- `Settings`
- `Variables`

添加下面 3 个变量。

### 5. 绑定邮件路由
进入你的域名：

- `Email Routing`
- 配置 `Catch-all` 或某条收信规则
- 目标选择这个 Worker

不做这一步，Worker 收不到邮件。

---

## 三、3 个变量怎么填

### 1）`EMAIL_WEBHOOK_URL`

**推荐填写：**

```text
http://域名:8000 也就是openai-cpa的项目地址，必须要在公网能访问才行。不要用IP
```

### 2）`EMAIL_WEBHOOK_SECRET`

**作用：**
鉴权密钥。

请求项目时，会在请求头里带：


openai-cpa里要用同样的值校验。

**示例：**

```text
123456
```

---

### 3）`EMAIL_WEBHOOK_TIMEOUT_MS`

**作用：**
请求时的超时时间，单位是毫秒。

**示例：**

```text
10000
```

表示：

- 最多等待 10 秒
- 10 秒还没响应，就当这次请求失败

一般保持 `10000` 就够用了。

---

## 四、变量示例

你可以在 Cloudflare 里这样填：

```text
EMAIL_WEBHOOK_URL = http://IP:8000
EMAIL_WEBHOOK_SECRET = 123456
EMAIL_WEBHOOK_TIMEOUT_MS = 10000
```

---

## 一句话总结

这个 Worker 的工作流程就是：

```text
Cloudflare 收到邮件
-> Worker 读取原始邮件
-> 本地项目收件
```

你只需要配置这 3 个变量：

- `EMAIL_WEBHOOK_URL`
- `EMAIL_WEBHOOK_SECRET`
- `EMAIL_WEBHOOK_TIMEOUT_MS`

然后把 Email Routing 绑定到这个 Worker 就可以用了。
