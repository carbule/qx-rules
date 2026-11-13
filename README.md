# Quantumult X 分流规则

从一份约 7500 条的 Quantumult X 配置里抽出分流规则，去掉重复项和标错的归属，收成一份可以直接引用的列表。列表里只有两种策略：`DIRECT`（直连）和 `Proxies`（代理）。节点、证书、订阅地址不在这个仓库里。

当前文件有 6704 条：直连 4239 条，代理 2465 条。

## 引用

配置里需要已经有名为 `Proxies` 的策略组。`DIRECT` 是 Quantumult X 自带的。

把下面这一行放进 `[filter_remote]`。不要加 `force-policy`，否则每一条都会被改成同一个策略。

```
https://raw.githubusercontent.com/carbule/qx-rules/main/quantumultx.list, tag=分流, opt-parser=false, inserted-resource=true, enabled=true
```

`[filter_local]` 保留这几行，放在引用规则之后：

```
host-suffix, local, DIRECT
host-suffix, lan, DIRECT
host-keyword, ., Proxies
GEOIP,CN,DIRECT
FINAL,Proxies
```

`inserted-resource=true` 让列表里的后缀规则先于 `host-keyword, ., Proxies`。列表里写成直连的域名走直连，写成代理的域名走代理。列表没有覆盖到的域名，由 `host-keyword, ., Proxies` 走代理。纯 IP 连接仍由 `GEOIP,CN,DIRECT` 判断。

## 整理时做的处理

同一条域名原来写了两遍的，只留一条。原来分成 AI、Google、YouTube、Steam、Telegram、Scholar 等十几个策略组，这里全部并进 `Proxies`。Apple、Bilibili，以及原来标成直连的国内域名，保留为 `DIRECT`。

下面这些域名按实际用途改了归属：

| 域名 | 现在的策略 | 原因 |
|---|---|---|
| outlook.com、office.com、office.net、office365.com、hotmail.com、live.com、sharepoint.com、onedrive.com、microsoftonline.com、cloud.microsoft | Proxies | 邮箱和 OneDrive 走代理。原来 Microsoft 策略组的默认是直连，国内清单里又写了一次直连 |
| login.live.com、logincdn.msauth.net | Proxies | 原来被单独标成 Xbox |
| windowsupdate.com、update.microsoft.com、download.microsoft.com | DIRECT | 微软更新和下载走国内节点更合适 |
| bing.com | Proxies | 国际版 Bing 走代理 |
| cqvip.com、pkulaw.com | DIRECT | 维普、北大法宝是国内站点，原来被放进学术代理 |
| snssdk.com、bytedapm.com | DIRECT | 抖音在用。TikTok 自己的域名仍然走代理 |
| futunn.com、futu5.com | DIRECT | 富途国内接入，原来被放进加密货币代理 |
| ip138.com | DIRECT | 国内查 IP 的网站，走代理会看到节点地址 |
| azureedge.net、edgesuite.net、aspnetcdn.com、gfx.ms | DIRECT | 国内大量站点用的 CDN。整段改成代理会把这些国内站点带进代理 |

另外删掉了这些内容：

- 重复规则
- `HOST-KEYWORD,microsoft`。这条关键字会把更新域名和名字里碰巧带 microsoft 的域名一起卷进同一个策略
- 7 条 User-Agent 规则。打开分流匹配优化后它们基本不会生效
- 标成直连的 IP 段，共 334 条。国内 IP 交给配置里的 `GEOIP,CN,DIRECT`。Telegram、Google 等标成代理的 IP 段保留了下来
- 列表末尾的 `GEOIP` 和 `FINAL`。这两条留在本地配置里

华美银行、iFAST 的域名是直连。Instagram 和它依赖的 Meta、Facebook 域名是代理。
