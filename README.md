# 上架用的两个网页

App Store Connect 要求两个**必须能打开**的网址：

| 用途 | 文件 |
|---|---|
| App 支持 URL | `support.html` |
| 隐私政策 URL | `privacy.html` |

`index.html` 是落地页，两个都链着，顺便说明「非官方」。

## 内容来源

`privacy.html` 的正文**逐字取自** `Tanzi/Features/Settings/LegalScreens.swift` 里的
`LegalText.privacy`。审核员会拿网页和 App 内的文字对照，**改一边就得改另一边**。

比对方法：

```bash
bash Tools/check_legal.sh
```

## 怎么发布（GitHub Pages，免费）

1. 在 GitHub 建一个**公开**仓库，比如 `tanzi-site`
2. 把这三个 html 传上去（放根目录，不要放子目录）
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
