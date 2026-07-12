# blog

Blog built with [Hugo](https://gohugo.io/), served at <https://achi-kyw.github.io/blog/>.

## Build

```bash
git submodule update --init   # 第一次 clone 後抓主題
hugo server                   # 本地預覽 http://localhost:1313
hugo                          # 建置到 public/(需 Hugo extended)
```

Push 到 `master` 後由 GitHub Actions 自動建置部署。
