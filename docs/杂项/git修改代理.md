---
tags:
  - 备忘
date: 2023-08-29
dg-publish: true
dg-permalink: git修改代理
---


```
git config --global http.proxy socks5://127.0.0.1:20170
git config --global https.proxy socks5://127.0.0.1:1080

git config --global http.proxy http://127.0.0.1:20171
git config --global https.proxy https://127.0.0.1:8080

git config --global --unset http.proxy
git config --global --unset https.proxy

git config -l --global
```
