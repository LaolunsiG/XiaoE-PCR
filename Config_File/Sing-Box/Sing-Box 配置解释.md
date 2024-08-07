# Sing_Box配置解释
**文件内不支持注释**

> name: XiaoE_Sing_Box
>
> content：
>
> updata: 2024/07/17
>
> updata_log:
> 
> updata_url:



## 官方配置
```json
{
  "log": {},
  "dns": {},
  "inbounds": [],
  "outbounds": [],
  "route": {},
  "experimental": {}
}
```
### 参数
|Key| Format |
|:--|:--:|
| log|	日志|
|dns	|DNS|
|inbounds|	入站|
|outbounds	|出站|
|route	|路由|
|experimental	|实验性|

## Sing_Box配置
### 日志
```json
{
  "log": { "level": "info", "timestamp": true },
}
```
- 参数
  - disabled: 禁用日志，启动后不输出日志。
  - level: 日志等级，可选值：trace, debug, info, warn, error, fatal, panic。
  - output: 输出文件路径，启动后将不输出到控制台。
  - timestamp: 添加时间到每行。

### DNS配置
```json
{
  "dns": {
    "hosts": {
      "miwifi.com": [ "192.168.31.1" ],
      "doh.pub": [ "1.12.12.12", "120.53.53.53", "2402:4e00::" ],
      "dns.alidns.com": [ "223.5.5.5", "223.6.6.6", "2400:3200::1", "2400:3200:baba::1" ],
      "dns.google": [ "8.8.8.8", "8.8.4.4", "2001:4860:4860::8888", "2001:4860:4860::8844" ],
      "cloudflare-dns.com": [ "1.1.1.1", "1.0.0.1", "2606:4700:4700::1111", "2606:4700:4700::1001" ]
    },
    "servers": [
      { "tag": "dns_proxy", "address": "https://1.1.1.1/dns-query", "address_resolver": "dns_resolver", "strategy": "ipv4_only", "detour": "select" },
      { "tag": "dns_direct", "address": "h3://dns.alidns.com/dns-query", "address_resolver": "dns_resolver", "strategy": "ipv4_only", "detour": "direct" },
      { "tag": "dns_block", "address": "rcode://refused" },
      { "tag": "dns_resolver", "address": "223.5.5.5", "strategy": "ipv4_only", "detour": "direct" }
    ]
      "rules": [
        // 注释：对于任何出站连接（不管是直接连接还是通过代理），使用 "dns_resolver" 服务器进行 DNS 解析（这一句主要用来解析代理节点本身的 IP 地址）。
      { "outbound": "any", "server": "dns_resolver" },
         // 注释：在直连模式（不经过代理）下，使用 "dns_direct" 服务器进行 DNS 解析。
      { "clash_mode": "direct", "server": "dns_direct" },
        // 注释：在全局代理模式下，使用 "dns_proxy" 服务器进行 DNS 解析。
      { "clash_mode": "global", "server": "dns_proxy" },
        // 注释：当特定的进程（如 TencentMeeting、WeChat 等）发起 DNS 请求时，使用 "dns_direct" 服务器进行直连 DNS 解析。
      { "process_name": [ "TencentMeeting", "NemoDesktop", "ToDesk", "ToDesk_Service","WeChat", "Tailscale", "wireguard-go", "Tunnelblick", "softwareupdated", "kubectl" ], "server": "dns_direct" },
        // 注释：对于特定后缀的域名（如 icloudnative.io 等），使用 "dns_direct" 服务器进行直连 DNS 解析。
      { "domain_suffix": [ "icloudnative.io", "fuckcloudnative.io", "sealos.io", "cdn.jsdelivr.net" ], "server": "dns_direct" },
        // 注释：当 Dropbox 相关进程发起 DNS 请求时，使用 "dns_proxy" 服务器通过代理进行 DNS 解析。
      { "process_name": [ "DropboxMacUpdate", "Dropbox" ], "server": "dns_proxy" },
        // 注释：对于特定的 Android 应用包名（如 YouTube、Telegram 等），使用 "dns_proxy" 服务器通过代理进行 DNS 解析。
      { "package_name": [ "com.google.android.youtube", "com.android.vending","org.telegram.messenger", "org.telegram.plus" ], "server": "dns_proxy" },
        // 注释：对于 geosite 数据库中定义的非中国地区的地理位置相关的域名，使用 "dns_proxy" 服务器通过代理进行 DNS 解析。
      { "rule_set": "geosite-geolocation-!cn", "server": "dns_proxy" },
        // 注释：对于定义在 "Global" 规则集中的域名，使用 "dns_proxy" 服务器通过代理进行 DNS 解析。
      { "rule_set": "Global", "server": "dns_proxy" },
        // 注释：对于特定的服务和地理位置相关的域名（如 YouTube、Netflix、谷歌、Telegram 相关的域名），使用 "dns_proxy" 服务器通过代理进行 DNS 解析。
      { "rule_set": [ "YouTube", "Telegram", "Netflix", "geoip-google","geoip-telegram", "geoip-twitter", "geoip-netflix" ], "server": "dns_proxy" }
      ],
      // 注释：如果上述规则都不适用，则默认使用 "dns_direct" 服务器进行直连 DNS 解析。
    "final": "dns_direct",
    "strategy": "prefer_ipv6",
    "disable_cache": false,
    "disable_expire": false,
    "independent_cache": true,
    "reverse_mapping": true,
    "client_subnet": "",
    "fakeip": { "enabled": true, "inet4_range": "198.18.0.0/15", "inet6_range": "fc00::/18", "exclude_rule": { "rule_set": [ "fakeip-filter", "private" ] } }
  },
}
```
> 参数
- address_resolver
  - 如果服务器地址包括域名则必须
  - 用于解析本 DNS 服务器的域名的另一个 DNS 服务器的标签。
= address_strategy: 
  - 用于解析本 DNS 服务器的域名的策略。
  - 可选项：prefer_ipv4 prefer_ipv6 ipv4_only ipv6_only。
  - 默认使用 dns.strategy。
- strategy: 
  - 默认解析策略。
  - 可选项：prefer_ipv4 prefer_ipv6 ipv4_only ipv6_only。
  - 如果被其他设置覆盖则不生效。
- detour:
  - 用于连接到 DNS 服务器的出站的标签。
  - 如果为空，将使用默认出站。
- client_subnet
  - 默认情况下，将带有指定 IP 前缀的 edns0-subnet OPT 附加记录附加到每个查询。
  - 如果值是 IP 地址而不是前缀，则会自动附加 /32 或 /128。
  - 可以被 rules.[].client_subnet 覆盖。
  - 将覆盖 dns.client_subnet。

### 入站配置
```json
{
  "inbounds": [ 
    {
      "type": "tun",
      "inet4_address": "198.18.0.1/16",
      "auto_route": true,
      "exclude_package": [
        "cmb.pb",
        "cn.gov.pbc.dcep",
        "com.MobileTicket",
        "com.adguard.android",
        "com.ainemo.dragoon",
        "com.alibaba.android.rimet",
        "com.alicloud.databox",
        "com.amazing.cloudisk.tv",
        "com.autonavi.minimap",
        "com.bilibili.app.in",
        "com.bishua666.luxxx1",
        "com.cainiao.wireless",
        "com.chebada",
        "com.chinamworld.main",
        "com.cmbchina.ccd.pluto.cmbActivity",
        "com.coolapk.market",
        "com.ctrip.ct",
        "com.dianping.v1",
        "com.douban.frodo",
        "com.eg.android.AlipayGphone",
        "com.farplace.qingzhuo",
        "com.hanweb.android.zhejiang.activity",
        "com.leoao.fitness",
        "com.lucinhu.bili_you",
        "com.mikrotik.android.tikapp",
        "com.moji.mjweather",
        "com.motorola.cn.calendar",
        "com.motorola.cn.lrhealth",
        "com.netease.cloudmusic",
        "com.sankuai.meituan",
        "com.sina.weibo",
        "com.smartisan.notes",
        "com.sohu.inputmethod.sogou.moto",
        "com.sonelli.juicessh",
        "com.ss.android.article.news",
        "com.ss.android.lark",
        "com.ss.android.ugc.aweme",
        "com.tailscale.ipn",
        "com.taobao.idlefish",
        "com.taobao.taobao",
        "com.tencent.mm",
        "com.tencent.mp",
        "com.tencent.soter.soterserver",
        "com.tencent.wemeet.app",
        "com.tencent.weread",
        "com.tencent.wework",
        "com.ttxapps.wifiadb",
        "com.unionpay",
        "com.unnoo.quan",
        "com.wireguard.android",
        "com.xingin.xhs",
        "com.xunmeng.pinduoduo",
        "com.zui.zhealthy",
        "ctrip.android.view",
        "io.kubenav.kubenav",
        "org.geekbang.geekTime",
        "tv.danmaku.bili"
      ],
      "stack": "mixed",
      "sniff": true
      "platform": { "http_proxy": { "enabled": true, "server": "127.0.0.1", "server_port": 2080 } }
     }
  ],
}
```

### 出站配置
```json
{
  "outbounds": [
    { "tag": "🚀 节点选择", "type": "selector", "outbounds": [ "🇭🇰 香港节点", "🇹🇼 台湾节点", "🇯🇵 日本节点", "🇰🇷 韩国节点", "🇸🇬 新加坡节点", "🇺🇸 美国节点" ] },
    { "tag": "🐟 漏网之鱼", "type": "selector", "outbounds": [ "🚀 节点选择", "🎯 全球直连" ] },
    { "tag": "📈 网络测试", "type": "selector", "outbounds": [ "🎯 全球直连", "🇭🇰 香港节点", "🇹🇼 台湾节点", "🇯🇵 日本节点", "🇰🇷 韩国节点", "🇸🇬 新加坡节点", "🇺🇸 美国节点" ] },
    { "tag": "🤖 人工智能", "type": "selector", "outbounds": [ "🚀 节点选择", "🇭🇰 香港节点", "🇹🇼 台湾节点", "🇯🇵 日本节点", "🇰🇷 韩国节点", "🇸🇬 新加坡节点", "🇺🇸 美国节点" ] },
    { "tag": "🎮 游戏服务", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🪟 微软服务", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🇬 谷歌服务", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🍎 苹果服务", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🇨🇳 直连域名", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🇨🇳 直连 IP", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🪜 代理域名", "type": "selector", "outbounds": [ "🚀 节点选择", "🎯 全球直连" ] },
    { "tag": "📲 电报消息", "type": "selector", "outbounds": [ "🚀 节点选择" ] },
    { "tag": "🖥️ 直连软件", "type": "selector", "outbounds": [ "🎯 全球直连" ] },
    { "tag": "🔒 私有网络", "type": "selector", "outbounds": [ "🎯 全球直连" ] },
    { "tag": "🛑 广告拦截", "type": "selector", "outbounds": [ "REJECT" ] },
    { "tag": "🎯 全球直连", "type": "selector", "outbounds": [ "DIRECT" ] },
    { "tag": "REJECT", "type": "block" },
    { "tag": "DIRECT", "type": "direct" },
    { "tag": "GLOBAL", "type": "selector", "outbounds": [ "DIRECT", "REJECT", "🇭🇰 香港节点", "🇹🇼 台湾节点", "🇯🇵 日本节点", "🇰🇷 韩国节点", "🇸🇬 新加坡节点", "🇺🇸 美国节点" ] },
    { "tag": "dns-out", "type": "dns" },
    { "tag": "🇭🇰 香港节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇭🇰" ] },
    { "tag": "🇹🇼 台湾节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇹🇼" ] },
    { "tag": "🇯🇵 日本节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇯🇵" ] },
    { "tag": "🇰🇷 韩国节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇰🇷" ] },
    { "tag": "🇸🇬 新加坡节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇸🇬" ] },
    { "tag": "🇺🇸 美国节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇺🇸" ] }
  ],
  "outbound_providers": [
    {
      "tag": "🛫 我的机场",
      "type": "remote",
      // 修改为你的 Clash 订阅链接
      "download_url": "https://example.com/xxx/xxx&flag=clash",
      "path": "./providers/airport.yaml",
      "download_interval": "24h",
      "download_ua": "clash.meta",
      "includes": [ "🇭🇰|🇹🇼|🇯🇵|🇰🇷|🇸🇬|🇺🇸" ],
      "healthcheck_url": "https://www.gstatic.com/generate_204",
      "healthcheck_interval": "10m"
    }
  ],
  "route": {
    "rules": [
      { "protocol": [ "dns" ], "outbound": "dns-out" },
      { "clash_mode": "Direct", "outbound": "DIRECT" },
      { "clash_mode": "Global", "outbound": "GLOBAL" },
      { "rule_set": [ "ads" ], "outbound": "🛑 广告拦截" },
      { "rule_set": [ "applications" ], "outbound": "🖥️ 直连软件" },
      { "rule_set": [ "private" ], "outbound": "🔒 私有网络" },
      { "rule_set": [ "microsoft-cn" ], "outbound": "🪟 微软服务" },
      { "rule_set": [ "apple-cn" ], "outbound": "🍎 苹果服务" },
      { "rule_set": [ "google-cn" ], "outbound": "🇬 谷歌服务" },
      { "rule_set": [ "games-cn" ], "outbound": "🎮 游戏服务" },
      { "rule_set": [ "ai" ], "outbound": "🤖 人工智能" },
      { "rule_set": [ "networktest" ], "outbound": "📈 网络测试" },
      { "rule_set": [ "proxy" ], "outbound": "🪜 代理域名" },
      { "rule_set": [ "cn" ], "outbound": "🇨🇳 直连域名" },
      { "rule_set": [ "telegramip" ], "outbound": "📲 电报消息", "skip_resolve": true },
      { "rule_set": [ "privateip" ], "outbound": "🔒 私有网络", "skip_resolve": true },
      { "rule_set": [ "cnip" ], "outbound": "🇨🇳 直连 IP" }
    ],
}
```

### 路由规则

```json
    "rule_set": [
      {
        "tag": "fakeip-filter",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/fakeip-filter.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/fakeip-filter.srs"
      },
      {
        "tag": "ads",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/ads.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/ads.srs"
      },
      {
        "tag": "applications",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/applications.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/applications.srs"
      },
      {
        "tag": "private",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/private.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/private.srs"
      },
      {
        "tag": "microsoft-cn",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/microsoft-cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/microsoft-cn.srs"
      },
      {
        "tag": "apple-cn",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/apple-cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/apple-cn.srs"
      },
      {
        "tag": "google-cn",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/google-cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/google-cn.srs"
      },
      {
        "tag": "games-cn",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/games-cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/games-cn.srs"
      },
      {
        "tag": "ai",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/ai.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/ai.srs"
      },
      {
        "tag": "networktest",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/networktest.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/networktest.srs"
      },
      {
        "tag": "proxy",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/proxy.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/proxy.srs"
      },
      {
        "tag": "cn",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/cn.srs"
      },
      {
        "tag": "telegramip",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/telegramip.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/telegramip.srs"
      },
      {
        "tag": "privateip",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/privateip.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/privateip.srs"
      },
      {
        "tag": "cnip",
        "type": "remote",
        "format": "binary",
        "path": "./ruleset/cnip.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/cnip.srs"
      }
    ],
    "final": "🐟 漏网之鱼",
    "auto_detect_interface": true,
    "concurrent_dial": true
  },
}
```

### 实验性配置
  "experimental": { "cache_file": { "enabled": true }, "clash_api": { "external_controller": "127.0.0.1:9090", "secret": "", "default_mode": "Rule" } }
}
```
