# Glados自动签到

## 食用方式：

### 注册一个GLaDOS的账号([注册地址](https://glados.space/landing/0A58E-NV28S-6U3QV-33VMG))

#### 我的邀请码：([0A58E-NV28S-6U3QV-33VMG](https://0a58e-nv28s-6u3qv-33vmg.glados.space)) 

#### 我的优惠码（9折）：([DEVILSTORE](https://0a58e-nv28s-6u3qv-33vmg.glados.space)) 

### **Fork**本仓库

![图片加载失败](imgs/1.png)

### 添加**secret**

1. 跳转至自己的仓库的`Settings`->`Secrets and variables`->`Action`

2. 添加1个`repository secret`，命名为`GLADOS_COOKIES`，其值对应GLaDOS账号的cookie值中的有效部分（获取方式如下）

- 在GLaDOS的签到页面按`F12`

- 切换到`Network`页面下，刷新

![图片加载失败](imgs/2.png)

- 点击第一个选项卡后在`Request Headers`下找到`Cookie`，右键复制cookie的值即可

  > 参考格式：koa:sess=eyJ1c2xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxAwMH0=; koa:sess.sig=xJkOxxxxxxxxxxxxxxxtnM;

![图片加载失败](imgs/3.png)

- 多账号请在 `COOKIES` 中 添加多个 `cookies` 中间使用 `&`连接即可。（例如： `c1&c3&c3...`）

3. 配置积分兑换策略（非必须）

- 添加1个`repository secret`，命名为`GLADOS_EXCHANGE_PLAN`，配置自动兑换积分策略：

| 值 | 积分要求 | 兑换天数 |
|---|---------|---------|
| `plan100` | 100 积分 | 10 天 |
| `plan200` | 200 积分 | 30 天 |
| `plan500` | 500 积分 | 100 天 |

> 不配置时默认不进行自动兑换；如需兑换，设置 `GLADOS_EXCHANGE_PLAN` 为 `plan100` / `plan200` / `plan500`。

4. 手机推送（非必须，两种渠道可任选其一，也可同时开启）

**方式一：PushMe（推荐，支持自建服务）**

[PushMe](https://github.com/yafoo/pushme) 是轻量安卓消息客户端，可自建服务端（[pushme-server](https://github.com/yafoo/pushme-server)）。签到结果会带主题色推送到手机：全成功绿色、部分失败橙色、全失败红色。

- 添加 `repository secret`，命名为 `PUSHME_HOST`，值为你的服务地址（**必须带协议，不带结尾斜杠**），例如 `https://push.i-i.me` 或 `https://push.example.com`
- 添加 `repository secret`，命名为 `PUSHME_KEY`，值为 APP 内显示的 `push_key`
- （可选）添加 `PUSHME_TYPE` 指定消息类型，支持 `text` / `markdown` / `html` / `url`，默认 `markdown`

> 自建服务需对外开放 HTTP 接口（默认 3010 端口）并做好鉴权；`PUSHME_HOST` 与 `PUSHME_KEY` 同时配置才会启用，只配一个会在日志中提示并跳过。

**方式二：PushDeer**

- 添加1个`repository secret`，命名为`PUSHDEER_SENDKEY`，其值对应 PushDeer key: ([获取地址](https://www.pushdeer.com/product.html))。

### **star**自己的仓库

![图片加载失败](imgs/4.png)

## 文件结构

```shell
│  checkin.py	# 签到脚本
│
├─.github
│  └─workflows
│          gladosCheck.yml	# Actions 配置文件
```

## 更新日志

- **2026-01**: 重构代码，添加log输出方便定位，支持新版网址，支持配置积分兑换策略。
- **2026-04**: 优化代码逻辑，优化日志输出，支持[新版域名](https://railgun.info) ，在 GLADOS_COOKIES 中添加新版域名下的 cookies 即可使用。
- **2026-09**: 新增设备平台自适应重试（code 4 无需手动配置 UA 即可自动恢复）；默认关闭自动兑换，需显式配置 `GLADOS_EXCHANGE_PLAN` 才启用。
- **2026-09**: 新增网络层重试/退避：`requests.Session` 挂载 `HTTPAdapter` + `urllib3.Retry`（total=3、指数退避 1/2/4s、对 429/5xx 自动重试），降低 CI 网络抖动导致的误报失败；业务 200 响应不受影响。
- **2026-10**: 新增 PushMe 推送渠道（支持**自建服务端**，配置 `PUSHME_HOST` + `PUSHME_KEY`），标题自动带主题标记（成功/警告/失败），推送内容按 markdown 渲染换行；PushDeer 保留为可选渠道，两者可同时启用。


## 问题排查与定位
- 大家可以通过查询 actions 中的 running checkin 日志快速定位问题，有其他问题提交issue。
- 若日志出现 `code : 4, message : Automated check-in detected`：这是 GLaDOS 的**设备平台校验**（`reason=device-mismatch`，即签到请求 UA 平台与登录设备平台不一致）。脚本已内置**设备平台自适应**：首次失败时自动读取服务端返回的 `loginDevice` 并切换对应平台 UA 重试，**无需手动配置 User-Agent**；若长期仍失败，请重新登录 glados.cloud 手动签到一次并刷新 cookie。

  <img width="1684" height="844" alt="image" src="https://github.com/user-attachments/assets/45348a5f-43e4-45f5-8fdf-ce84d343b30d" />

## 声明

本项目不保证稳定运行与更新, 因GitHub相关规定可能会删库, 请注意备份







