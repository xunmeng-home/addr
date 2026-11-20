# 寻梦 AI 地址发布页

一个纯静态页面：列出寻梦 AI 当前能用的域名，在访客自己的浏览器里测速，把最快的一条放在最上面。主站域名打不开时，用户回到这一页拿新地址。

没有构建步骤，没有依赖，没有服务器。

## 文件

| 文件 | 作用 |
| --- | --- |
| `index.html` | 整个页面，样式和脚本都写在里面，不引用任何外部资源 |
| `sites.txt` | 线路清单，页面打开时读它 |
| `logo.webp`、`logo.png` | 页头的横向标志，由 Portal 的 `public/brand/xunmeng-ai-logo-v8-horizontal.png` 缩小而来（png 给不认 webp 的旧浏览器） |
| `favicon.ico`、`apple-touch-icon.png` | 图标，从 Portal 的 `public/` 复制来的 |
| `.nojekyll` | 让 GitHub Pages 原样发布，不跑 Jekyll |

## 加一条 / 撤一条线路

改 `sites.txt`，一行一条，推到 `main`：

```
xunmeng.ai 国内线路
seekdream.ai 海外线路
```

- 域名后面空一格写给用户看的说明，可以不写。
- 第一行是测速出结果之前的默认入口，放最稳的那条。
- 新域名必须能打开 `https://<域名>/favicon.ico`，否则会被判成“打不开”。接到 Portal 的 Worker 上的域名天然满足。

`index.html` 里也写了一份清单（`#lines` 里那两行和 `#enter` 的链接），只在 `sites.txt` 读不到时用。主域名换了才需要改它。

## 测速怎么做的

对每个域名同时加载 3 次 `/favicon.ico`（带随机参数防缓存），取中位数，6 秒没回来算打不开。用加载图片而不是 `fetch`，有两个原因：

- 主站不用为这一页开跨域。
- 只有真的拿到一张图才算通。被劫持到别的页面、返回错误页的域名，图片解不出来，不会被误判成能用。

测的是访客自己到各域名的速度，所以同一条线路在不同地方排名不一样。

## 本地看

```sh
python3 -m http.server 8791
# 打开 http://127.0.0.1:8791/
```

直接双击 `index.html` 也能用，只是读不到 `sites.txt`，走的是页面里的兜底清单。

## 发布

任何能放静态文件的地方都行，页面里没有写死自己的地址（“收藏这一页”显示的是当前打开的地址）。

- **GitHub Pages**：仓库设为公开，Settings → Pages → Deploy from a branch → `main` / 根目录。地址是 `https://<账号或组织>.github.io/<仓库名>/`。
- **Cloudflare**：`bunx wrangler@4 pages deploy . --project-name <名字>`。

两边可以同时放，互为备份。
