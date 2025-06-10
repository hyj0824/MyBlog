---
tags:
  - 环境
date: 2024-11-24
publish: true
---


## 查看命令运行时间

为了容易地完成测量，可以将命令包装在 **time** 命令中，就像这样：**`time { ../configure ... && make && make install; }`**。

1 SBU

real    1m23.843s
user    3m5.491s
sys     1m4.481s

real并不等于user+sys的总和。real代表的是程序从开始到结束的全部时间，即使程序不占CPU也统计时间。而user+sys是程序占用CPU的总时间，因此real总是大于或者等于user+sys的（单核）。

## linux系统代理

```bash
sudo vim /etc/profile.d/proxy.sh

 # set proxy config via profile.d - should apply for all users
export http_proxy="http://10.10.1.10:8080/"
export https_proxy="http://10.10.1.10:8080/"
export ftp_proxy="http://10.10.1.10:8080/"
export no_proxy="127.0.0.1,localhost"
# For curl
export HTTP_PROXY="http://10.10.1.10:8080/"
export HTTPS_PROXY="http://10.10.1.10:8080/"
export FTP_PROXY="http://10.10.1.10:8080/"
export NO_PROXY="127.0.0.1,localhost"
#将要从代理中排除的其他IP添加到NO_PROXY和no_proxy环境变量中

sudo chmod +x /etc/profile.d/proxy.sh
```

[【ubuntu】系统代理的设置\_ubuntu network proxy-CSDN博客](https://blog.csdn.net/u011119817/article/details/110856212)