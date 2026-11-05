# 上架用的两个网页

App Store Connect 要求两个**必须能打开**的网址：

| 用途 | 文件 |
|---|---|
| App 支持 URL | `support.html` |
| 隐私政策 URL | `privacy.html` |

`index.html` 是落地页，两个都链着，顺便说明「非官方」。

还有一个 `moderation.json`，**不是网页，是 App 每次回到前台都会去拉的移除名单**
（`Tanzi/Core/ContentModeration.swift` 里的 `ModerationList`，地址写死为
`https://tranjazz.github.io/tanzi-site/moderation.json`）。必须和 html 放在同一个仓库根目录。

## 内容来源

`privacy.html` 的正文**逐字取自** `Tanzi/Features/Settings/LegalScreens.swift` 里的
`LegalText.privacy`。审核员会拿网页和 App 内的文字对照，**改一边就得改另一边**。

比对方法：

```bash
bash Tools/check_legal.sh
```

## 怎么发布（GitHub Pages，免费）

1. 在 GitHub 建一个**公开**仓库，比如 `tanzi-site`
2. 把这三个 html 和 `moderation.json` 传上去（放根目录，不要放子目录）
3. 仓库 → Settings → Pages → Source 选 `Deploy from a branch` → 分支 `main`、目录 `/ (root)` → Save
4. 等一两分钟，地址是：

```
https://<你的用户名>.github.io/tanzi-site/
https://<你的用户名>.github.io/tanzi-site/support.html
https://<你的用户名>.github.io/tanzi-site/privacy.html
```

5. 在 App Store Connect 里填后两个

## 检查

发布后先在**手机 Safari** 里打开一遍，确认：

- 能打开，不是 404
- 中文不乱码
- 深色模式下文字看得清
- 邮件链接能点

审核员打不开这两个链接会直接拒。

另外在 Safari 里打开 `https://tranjazz.github.io/tanzi-site/moderation.json`，确认能看到 JSON 原文（不是 404）。

## 处理举报（24 小时内）

苹果 1.2 要求：收到举报 24 小时内**移除内容并逐出发布者**。App 没有服务器，靠的就是 `moderation.json`。

举报邮件长这样（App 自动生成，标题 `[举报] 论坛名 · 原因`）：

```
论坛：4D4Y（4d4y）
帖子：某某某（tid 3469094）
楼层：12 楼（pid 71234567）
发布者：someone
原因：辱骂、骚扰或人身攻击
链接：https://...
```

收到后：

1. 点链接核实
2. 改 `moderation.json`，按括号里的站点 id 找到对应那一段：
   - 逐出这个人：用户名加进 `users`（不分大小写）
   - 移除一楼：pid 加进 `posts`（V2EX 的主楼 pid 是负的帖子号，照抄邮件里的数字）
   - 移除整个帖子：tid 加进 `threads`
   - 某个词以后一律过滤：加进最外层的 `keywords`（所有论坛通用）
3. 推到 GitHub。Pages 一两分钟生效，App 回到前台时最多再等 30 分钟就会拉到
4. 回信告诉举报人已处理；论坛那边的帖子请同时向论坛版主反映

格式：

```json
{
  "keywords": ["某个词"],
  "sites": {
    "4d4y":     { "users": ["someone"], "threads": [3469094], "posts": [71234567] },
    "chiphell": { "users": [], "threads": [], "posts": [] },
    "v2ex":     { "users": [], "threads": [], "posts": [-1234567] }
  }
}
```

**JSON 写错了 App 会忽略整份新名单、继续用上一份**，推之前可以用 `python -m json.tool moderation.json` 检查一下。
