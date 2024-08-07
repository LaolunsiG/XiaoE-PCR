# Surge 配置教程

## 全局配置
```conf
# 全局配置
[General]
# 日志级别
loglevel = notify
# 当遇到 REJECT 策略时显示错误页
show-error-page-for-reject = true

# IPV6配置-是否启动完整的 IPv6 支持 (默认值: false)参数：true：开启，false：关闭
ipv6 = false
# UDP流量配置-如果代理不支持 udp 中继，则使用 DIRECT 或 REJECT 代替
udp-policy-not-supported-behaviour = DIRECT

# 供外网访问的服务端口
http-listen = 0.0.0.0:8888
socks5-listen = 0.0.0.0:8889

# 测速链接
# Internet 测试 URL
internet-test-url = http://wifi.vivo.com.cn/generate_204
# 代理测速 URL
proxy-test-url = http://www.google.com/generate_204
# 测试超时（s/秒）:Surge 将向该 URL 发送一个 HTTP HEAD 请求。测试只关心是否收到了返回数据，并不关心数据内容。仅支持 http:// 协议
test-timeout = 5
```

## DNS配置
```conf
# DNS配置
# DNS 服务器
dns-server = 223.5.5.5, 114.114.114.114, 119.29.29.29, 1.1.1.1
always-real-ip = *.srv.nintendo.net, *.stun.playstation.net, xbox.*.microsoft.com, *.xboxlive.com
```

## 入站配置
```conf
# 入站配置
# 跳过代理
skip-proxy = 192.168.0.0/16,10.0.0.0/8,172.16.0.0/12,localhost,*.local
```

## 路由规则
```conf
# 路由规则
# 规则类型
# 规则集
## 内置规则集
  # SYSTEM: 这个规则集包含了绝大多数来自 macOS 和 iOS 系统本身所发送的请求。
  # LAN: 包括了 'local' 后缀和私有 IP 地址。注意：这个规则集会触发 DNS 查询。
## 订阅规则(远程规则)
  # RULE-SET：匹配规则集内容。规则集的内容需包含规则类型，如"https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/Apple/Apple.list"。（兼容仅包含IP地址且不带规则类型的规则集）
  # DOMAIN-SET：匹配域名集内容。域名集的内容不包含规则类型，如"https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/Apple/Apple_Domain.list"。
# 单个规则
## 域名规则
  # DOMAIN-SUFFIX：匹配请求域名的后缀。如“DOMAIN-SUFFIX,example.com,DIRECT”可以匹配到“a.example.com、a.b.example.com”。
  # DOMAIN-KEYWORD：匹配请求域名的关键词。如“DOMAIN-KEYWORD,exa,DIRECT”可以匹配到“a.example.com、a.b.example.com”。
  # DOMAIN：匹配请求的完整域名。如“DOMAIN,www.example.com,DIRECT”只能匹配到“www.example.com”。
## IP规则 [当域名请求遇到IP类规则时，Surge会向本地DNS服务器发送查询请求，以判断主机IP是否匹配规则。若IP类规则加“no-resolve”(如：IP-CIDR,172.16.0.0/12,DIRECT,no-resolve)，则域名请求将会跳过此规则，不会触发本地DNS查询。]
  # IP-CIDR: 匹配IPv4地址。如“IP-CIDR,192.168.1.0/24,DIRECT”可以匹配到IP段“192.168.1.1～192.168.1.254”。
  # IP-CIDR6：匹配IPv6地址。
  # GEOIP：匹配IP数据库。如“GEOIP,CN,DIRECT”可以匹配到归属地为CN的IP地址。
  # IP-ASN：匹配IP地址隶属的ASN编号。如"IP-ASN,56040,DIRECT"可以匹配到属于China Mobile Communications Corporation网络的IP地址。
## HTTP规则
  # USER-AGENT：匹配用户代理字符串，支持使用通配符“*”。如“USER-AGENT,MicroMessenger*,DIRECT”可以匹配到“MicroMessenger Client”。
  # URL-REGEX：匹配URL正则式。如“URL-REGEX,^https?://.+/item.+,REJECT”可以匹配到“https://www.example.com/item/abc/123”。
## 进程规则
  # PROCESS-NAME: PROCESS-NAME,Telegram,Proxy,规则会匹配这个进程名的程序，支持 * 和 ? 两种通配符。
## 逻辑规则
  # AND：逻辑规则，与规则。'AND,((#Rule1), (#Rule2), (#Rule3)...),Policy'，如“AND,((DOMAIN,www.example.com),(DST-PORT,123)),DIRECT”可以匹配到“www.example.com:123”。
  # NOT：逻辑规则，非规则。'NOT,((#Rule1)),Policy，如“NOT,((SRC-IP,192.168.1.110),(DOMAIN, example.com)),DIRECT”。
  # OR：逻辑规则，或规则。'OR,((#Rule1), (#Rule2), (#Rule3)...),Policy'，如“OR,((DST-PORT,123),(DST-PORT,456)),DIRECT”可以匹配到“123”或“456”端口的所有请求。
## 杂项规则(其他规则)
  # SCRIPT：匹配脚本名称。
  # DST-PORT：匹配目标主机名的端口号。如“DST-PORT,443,DIRECT”可以匹配到443目标端口。
## 兜底规则
  # FINAL：兜底策略。如“FINAL,PROXY”表示当其他所有规则都匹配不到时才使用FINAL规则的策略。
  
# 规则策略：
# PROXY：代理。通过代理服务器转发流量。
# DIRECT：直连。连接不经过任何代理服务器。
# REJECT：拒绝。拒绝该请求，当连接类型为 HTTP 时，会返回一个错误页面。
# REJECT-TINYGIF：拒绝该请求，当连接类型为 HTTP 时，返回一个 1px 的 GIF 图片响应。若为其他类型连接则直接断开。该策略主要用于 Web 广告屏蔽。
# REJECT-DROP：拒绝。丢弃IP包。
# 除此之外，规则策略还可以选择「代理分组」、「订阅名称」、「分组」、「节点」。

# 规则匹配的优先级：
# 规则从上到下依次匹配，换句话说，第一条规则的优先级最高。

[Rule]
DOMAIN-KEYWORD,mubu,🌐 全球直连
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Lan/Lan.list,🌐 全球直连
# Sub-Store
RULE-SET,https://raw.githubusercontent.com/getsomecat/GetSomeCats/Surge/rule/substore.list,🚀 策略选择
# 广告拦截
#DOMAIN-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/Advertising/Advertising_Domain.list,🚫 全球拦截
# 人工智能
RULE-SET,https://raw.githubusercontent.com/LaolunsiG/XiaoE-PCR/main/rules/Shadowrocket/AI/AI_Globle.list,🤖️ 人工智能
# 服务平台
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Microsoft/Microsoft.list,Ⓜ️ 微软服务
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Apple/Apple.list,🍎 苹果服务
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/AppStore/AppStore.list,🍎 苹果服务
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/AppleProxy/AppleProxy.list,🚀 策略选择
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Google/Google.list,🚀 策略选择
# 社交通讯平台
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Telegram/Telegram.list,📲 Telegram
RULE-SET,https://raw.githubusercontent.com/LaolunsiG/XiaoE_PCR/main/rules/Shadowrocket/Weibo/Weibo.list,🍟 新浪微博
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/WeChat/WeChat.list,🌐 全球直连
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Twitter/Twitter.list,📄 Twitter
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/Facebook/Facebook.list,👤 Facebook
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/Reddit/Reddit.list,📖 Reddit
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/Discord/Discord.list,🐦 Discord
# 娱乐平台
# 短视频&影视
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/YouTube/YouTube.list,📹 YouTube
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/YouTubeMusic/YouTubeMusic.list,📹 YouTube
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Netflix/Netflix.list,🎥 Netflix
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Disney/Disney.list,🎬 Disney+
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Twitch/Twitch.list,🚀 策略选择
RULE-SET,https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/ProxyMedia.list,🍿 国外媒体
RULE-SET,https://raw.githubusercontent.com/LaolunsiG/XiaoE_PCR/main/rules/Shadowrocket/BiliBili/BiliBili_Classical.list,📽 哔哩哔哩
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Douyu/Douyu.list,🍔 国内媒体
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/ChinaMedia/ChinaMedia.list,🍔 国内媒体
## 游戏
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/PlayStation/PlayStation.list,🎮 游戏平台
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Epic/Epic.list,🎮 游戏平台
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Sony/Sony.list,🎮 游戏平台
RULE-SET,https://raw.githubusercontent.com/LaolunsiG/XiaoE_PCR/main/rules/Shadowrocket/Steam/Steam_Classical.list,🎮 游戏平台
DOMAIN-KEYWORD,ubisoft,🎮 游戏平台
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Nintendo/Nintendo.list,🎮 游戏平台
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/WanMeiShiJie/WanMeiShiJie.list,🎮 游戏平台
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Blizzard/Blizzard.list,🎮 游戏平台
## 音乐
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/Spotify/Spotify.list,🎻 Spotify
# 支付平台
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/PayPal/PayPal.list,🪙 Paypal
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Proxy/Proxy.list,🚀 策略选择
DOMAIN-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Proxy/Proxy_Domain.list,🚀 策略选择
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/Download/Download.list,💳 Download
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/ChinaMaxNoIP/ChinaMaxNoIP.list,🌐 全球直连
# RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/ChinaMax/ChinaMax_All_No_Resolve.list,🌐 全球直连
RULE-SET,https://raw.githubusercontent.com/LaolunsiG/XiaoE_PCR/main/rules/Shadowrocket/China/GEOIP_CN.list,🌐 全球直连
FINAL,🐟 漏网之鱼
```

## 出站代理(本地节点)
```conf
# 出站代理
# 本地节点
[Proxy]
```

## 策略组(订阅节点)
```conf
# 策略组(订阅节点和策略组)
# 手动选择
 # Select = select, no-alert=0, hidden=0, include-all-proxies=0, include-other-group='', policy-path=, update-interval=0, policy-regex-filter=
# 自动选择
 # Urltest = url-test, no-alert=0, hidden=0, include-all-proxies=0, include-other-group='', policy-path=, update-interval=0, policy-regex-filter=
# 故障转移
 # Fallback = fallback, no-alert=0, hidden=0, include-all-proxies=0, include-other-group='', policy-path=, update-interval=0, policy-regex-filter=
# 负载均衡
 # Loadbalance = load-balance, 同上
# 子网
 # Ssid = ssid, 同上

# 参数解析
# no-alert= [0/1, 是否通知，1为true 也就是通知] 
# hidden= [0/1 是否隐藏] 

[Proxy Group]
🚀 策略选择 = select, 🛫 机场选择, 🎣 钓鱼机场, 🗺 地区选择, 🌐 全球直连, hidden=0
🗺 地区选择 = select, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
🛫 机场选择 = select, 机场1, 机场2, 机场3, 机场4, 机场5, 机场6, 机场7, 机场8, 🌐 全球直连, hidden=0
🎣 钓鱼机场 = select, 机场1, hidden=0
# 这个策略组是为了筛选地区节点，所以是必要的
♻️ All = url-test, hidden=1, include-other-group="机场1, 机场2, 机场3, 机场4, 机场5, 机场6, 机场7, 机场8"

# 机场分类
# 订阅代理(远程代理)
# 隐藏机场策略组
# Payment-pithecia
机场1 = url-test, policy-path=https://gist.githubusercontent.com/LaolunsiG/24c46058083d500d43342378ed8bc7d0/raw/Surge1, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)
机场2 = url-test, policy-path=https://gist.githubusercontent.com/LaolunsiG/24c46058083d500d43342378ed8bc7d0/raw/Surge2, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)
机场3 = url-test, policy-path=https://gist.githubusercontent.com/LaolunsiG/24c46058083d500d43342378ed8bc7d0/raw/Surge3, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)
机场4 = url-test, policy-path=https://gist.githubusercontent.com/LaolunsiG/24c46058083d500d43342378ed8bc7d0/raw/Surge4, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)
机场5 = url-test, policy-path=https://gist.githubusercontent.com/LaolunsiG/24c46058083d500d43342378ed8bc7d0/raw/Surge5, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)
# hello_World节点
机场5 = url-test, policy-path=https://gist.githubusercontent.com/LaolunsiG/24c46058083d500d43342378ed8bc7d0/raw/Surge5, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)
# 三分机场(钓鱼机场)
机场6 = url-test, policy-path=https://sub.sanfen018.xyz/api/v1/client/subscribe?token=10e3910f8a610464fd09aac4abb147bb&flag=surge, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)
# Payment-Tblack
机场7 = url-test, policy-path=https://yellowsea.xn--9kqa348u.org/api/v1/client/subscribe?token=918a5b2ffa256cea273acf49568de8df, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)
# Payment-超级机场
机场8 = url-test, policy-path=https://subcj.efshop.cc/api/v1/client/subscribe?token=97b91df444afd8e91fe143412e41e5aa, update-interval=86400, hidden=1, include-all-proxies=0, policy-regex-filter=^(?!.*直连|DIRECT)

# 兜底策略组
🐟 漏网之鱼 = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
💳 Download= select, 🌐 全球直连, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
# 服务平台
Ⓜ️ 微软服务 = select, 🌐 全球直连, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
🍎 苹果服务 = select, 🌐 全球直连, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
# 人工智能
🤖️ 人工智能 = select, 🇯🇵 日本, 🇨🇳 台湾, 🇰🇷 韩国, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇬🇧 英国, 🇩🇪 德国, hidden=0
# 社交通讯平台
📲 Telegram = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
📄 Twitter = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
👤 Facebook = select, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, 🚀 策略选择, hidden=0
📖 Reddit = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
🐦 Discord = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
# 娱乐平台
## 短视频||影视
📹 YouTube = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
🎥 Netflix = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
🎬 Disney+ = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
📽 哔哩哔哩 = select, 🌐 全球直连, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
🍿 国外媒体 = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
🍟 新浪微博 = select, 🌐 全球直连, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
🍔 国内媒体 = select, 🌐 全球直连, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
## 音乐
🎻 Spotify = select, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
## 游戏
🎮 游戏平台 = select, 🌐 全球直连, 🚀 策略选择, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇺🇸 美国, 🇨🇦 加拿大, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, hidden=0
# 支付平台
🪙 Paypal = select, 🇺🇸 美国, 🇭🇰 香港, 🇨🇳 台湾, 🇰🇷 韩国, 🇯🇵 日本, 🇸🇬 新加坡, 🇷🇺 俄罗斯, 🇬🇧 英国, 🇩🇪 德国, 🇮🇳 印度, 🚀 策略选择, hidden=0
# 手动选择策略组
🇭🇰 香港 = select, 香港优选节点, 香港故障转移, 香港负载均衡, hidden=0
# 🇲🇴 澳门 = select, 澳门优选节点, 澳门故障转移, 澳门负载均衡, hidden=0
🇨🇳 台湾 = select, 台湾优选节点, 台湾故障转移, 台湾负载均衡, hidden=0
🇰🇷 韩国 = select, 韩国优选节点, 韩国故障转移, 韩国负载均衡, hidden=0
🇯🇵 日本 = select, 日本优选节点, 日本故障转移, 日本负载均衡, hidden=0
🇸🇬 新加坡 = select, 新加坡优选节点, 新加坡故障转移, 新加坡负载均衡, hidden=0
🇷🇺 俄罗斯 = select, 俄罗斯优选节点, 俄罗斯故障转移, 俄罗斯负载均衡, hidden=0
🇺🇸 美国 = select, 美国优选节点, 美国故障转移, 美国负载均衡, hidden=0
🇨🇦 加拿大 = select, 加拿大优选节点, 加拿大故障转移, 加拿大负载均衡, hidden=0
# 🇳🇱 荷兰 = select, 荷兰优选节点, 荷兰故障转移, 荷兰负载均衡, hidden=0
🇬🇧 英国 = select, 英国优选节点, 英国故障转移, 英国负载均衡, hidden=0
🇩🇪 德国 = select, 德国优选节点, 德国故障转移, 德国负载均衡, hidden=0
# 🇦🇺 澳大利亚 = select, 澳大利亚优选节点, 澳大利亚故障转移, 澳大利亚负载均衡, hidden=0
🇮🇳 印度 = select, 印度优选节点, 印度故障转移, 印度负载均衡, hidden=0
🌐 全球直连 = select, DIRECT, hidden=1, include-all-proxies=0
🚫 全球拦截 = select, REJECT, REJECT-TINYGIF, REJECT-DROP, hidden=1, include-all-proxies=0

# 优选节点策略组
香港优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇭🇰|港|虚通|HK|Hong))(?!.*((?i)回国|校园|游戏|教育|GAME|IPV6)).*$
# 澳门优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇲🇴|澳门|MO|Oman))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
台湾优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇹🇼|台|TW|Tai))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
韩国优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇰🇷|韩|韓|首尔|KR|Korea))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
日本优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇯🇵|日|川日|东京|大阪|泉日|埼玉|JP|Japan))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
新加坡优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇸🇬|新加坡|狮|SG|Singapore))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
俄罗斯优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇷🇺|俄|莫斯科|新西伯利亚|Новосиби́рская|Moscow|RU|Russia))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
美国优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇺🇸|美|波特兰|达拉斯|俄勒冈|凤凰城|费利蒙|硅谷|拉斯维加斯|洛杉矶|圣何塞|圣克拉拉|西雅图|芝加哥|US|United States))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
加拿大优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇨🇦|加拿大|CA|Canada))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
# 荷兰优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇳🇱|荷|NL|Holland|Netherlands))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
英国优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇬🇧|英|伦敦|UK|United Kingdom))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
德国优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇩🇪|德|DE|Germany))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
# 澳大利亚优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇦🇺|澳大|AU|Australia))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
印度优选节点 = url-test, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇮🇳|印|班加罗尔|孟买|Mumbai|IN|India))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
# 故障转移策略组
香港故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇭🇰|港|虚通|HK|Hong))(?!.*((?i)回国|校园|游戏|教育|GAME|IPV6)).*$
# 澳门故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇲🇴|澳门|MO|Oman))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
台湾故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇹🇼|台|TW|Tai))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
韩国故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇰🇷|韩|韓|首尔|KR|Korea))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
日本故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇯🇵|日|川日|东京|大阪|泉日|埼玉|JP|Japan))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
新加坡故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇸🇬|新加坡|狮|SG|Singapore))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
俄罗斯故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇷🇺|俄|莫斯科|新西伯利亚|Новосиби́рская|Moscow|RU|Russia))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
美国故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇺🇸|美|波特兰|达拉斯|俄勒冈|凤凰城|费利蒙|硅谷|拉斯维加斯|洛杉矶|圣何塞|圣克拉拉|西雅图|芝加哥|US|United States))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
加拿大故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇨🇦|加拿大|CA|Canada))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
# 荷兰故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇳🇱|荷|NL|Holland|Netherlands))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
英国故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇬🇧|英|伦敦|UK|United Kingdom))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
德国故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇩🇪|德|DE|Germany))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
# 澳大利亚故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇦🇺|澳大|AU|Australia))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
印度故障转移 = fallback, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇮🇳|印|班加罗尔|孟买|Mumbai|IN|India))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
# 负载均衡策略组
香港负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇭🇰|港|虚通|HK|Hong))(?!.*((?i)回国|校园|游戏|教育|GAME|IPV6)).*$
# 澳门负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇲🇴|澳门|MO|Oman))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
台湾负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇹🇼|台|TW|Tai))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
韩国负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇰🇷|韩|韓|首尔|KR|Korea))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
日本负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇯🇵|日|川日|东京|大阪|泉日|埼玉|JP|Japan))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
新加坡负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇸🇬|新加坡|狮|SG|Singapore))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
俄罗斯负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇷🇺|俄|莫斯科|新西伯利亚|Новосиби́рская|Moscow|RU|Russia))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
美国负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇺🇸|美|波特兰|达拉斯|俄勒冈|凤凰城|费利蒙|硅谷|拉斯维加斯|洛杉矶|圣何塞|圣克拉拉|西雅图|芝加哥|US|United States))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
加拿大负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇨🇦|加拿大|CA|Canada))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
# 荷兰负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇳🇱|荷|NL|Holland|Netherlands))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
英国负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇬🇧|英|伦敦|UK|United Kingdom))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
德国负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇩🇪|德|DE|Germany))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
# 澳大利亚负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇦🇺|澳大|AU|Australia))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
印度负载均衡 = load-balance, hidden=1, include-all-proxies=1, include-other-group="♻️ All", policy-regex-filter=^(?=.*((?i)🇮🇳|印|班加罗尔|孟买|Mumbai|IN|India))(?!.*((?i)回国|校园|游戏|教育|久虚|GAME|IPV6)).*$
```

