# 配置文件说明

## 配置文件格式

1. 主配置文件采用json格式，命名为`xxx.json`。
2. 默认V2bX会使用软件运行目录下的`config.json`作为配置文件。

配置文件基本格式，Cores下可以同时添加多个内核，Nodes下可以同时添加多个面板，多个节点配置信息，只需添加相同格式的Nodes item即可。

```json
{
	"Log": {
		// V2bX 的日志配置，独立于各 Core 的 log 配置

		// 日志等级，info, warn, error, none
		"Level": "error",
		// 日志输出路径，默认输出到标准输出
		"Output": ""
	},
	"Cores": [{
			// Core类型
			"Type": "sing",
			// Core标识名，可选，如果需要启动多个同类型内核则必填
			"Name": "sing1",
			"Log": {
				// 同 SingBox log 部分配置
				"Level": "error",
				"Timestamp": true
			},
			"NTP": {
				// 同 SingBox ntp 部分配置
				// VMess 建议开启
				"Enable": ,
				"Server": "time.apple.com",
				"ServerPort": 0
			},
			// SingBox源配置文件目录，用于引用标准SingBox配置文件
			"OriginalPath": "/etc/V2bX/sing_origin.json",
			// 在删除用户时清理已建立的连接
			"EnableConnClear": false,
		},
		{
			"Type": "sing",
			"Name": "sing2",
			"Log": {
				"Level": "info",
				"Timestamp": false
			}
		},
		{
			"Type": "xray",
			"Log": {
				// 同 Xray-core log 部分配置
				"Level": "error",
				"AccessPath": "",
				"ErrorPath": ""
			},
			// 静态资源文件目录
			"AssetPath": "",
			// DNS配置文件目录
			"DnsConfigPath": "",
			// 路由配置文件目录
			"RouteConfigPath": "",
			// 本地策略相关配置
			"ConnectionConfig": {
				// 详见 https://xtls.github.io/config/policy.html#levelpolicyobject
				"handshake": 4,
				"connIdle": 300,
				"uplinkOnly": 2,
				"downlinkOnly": 5,
				"statsUserUplink": false,
				"statsUserDownlink": false,
				"bufferSize": 4
			},
			// 自定义Inbound配置文件目录
			"InboundConfigPath": "",
			// 自定义Outbound配置文件目录
			"OutboundConfigPath": ""
		}
	],
	"Nodes": [{
			// sing内核

			// Node标识名，便于查看日志，不填将通过下发的节点配置自动生成
			// 务必注意不要重复，否则会出现问题
			"Name": "sing_node1",

			// 要使用的Core的类型
			// 如果填写了CoreName可不填，但单内核务必填写
			// 建议视情况填写Core和CoreName其中一个，如果均没有填写将随机选择支持的内核
			"Core": "sing",

			// 要使用的Core的标识名，如果没有定义多个同类型内核可不填
			"CoreName": "sing1",

			// API接口地址
			"ApiHost": "http://127.0.0.1",

			// API密钥，即Token
			"ApiKey": "test",

			// 节点ID
			"NodeID": 33,

			// 节点类型
			"NodeType": "shadowsocks",

			// 请求超时时间
			"Timeout": 30,

			// 监听IP
			"ListenIP": "0.0.0.0",

			// 发送IP
			"SendIP": "0.0.0.0",

			// 计入设备数限制的流量阈值，单位kB，节点用户每次上报流量超出该值时记录为1个设备
			"DeviceOnlineMinTraffic": 100,

			// 开启 TCP Fast Open
			"EnableTFO": true,

			// 开启 DNS
			"EnableDNS": true,
			// 关闭嗅探及嗅探覆盖（默认开启）
			"EnableSniff": false,
			"SniffOverrideDestination": false,
			// 设置 Domain Strategy 需要开启 DNS ，默认 AsIS
			// 可选 prefer_ipv4 / prefer_ipv6 / ipv4_only / ipv6_only
			"DomainStrategy": "ipv4_only",
			//sing mux相关配置
			"MultiplexConfig": {
				//开启mux
				"Enable": true,
				//启用填充
				"Padding": true,
				//TCP Brutal 设置
				"Brutal": {
					//开启TCP Brutal
					"Enable": true,
					//上传带宽
					"UpMbps": 500,
					//下载带宽
					"DownMbps": 500
				}
			},
			// 本地限制器相关配置
			"LimitConfig": {
				// 开启实时TCP连接数限制
				"EnableRealtime": false,

				// 用户速度限制
				"SpeedLimit": 0,

				// 用户TCP连接数限制
				"ConnLimit": 0,

				// 开启动态限速
				"EnableDynamicSpeedLimit": false,

				// 动态限速相关配置
				"DynamicSpeedLimitConfig": {
					// 检查周期
					"Periodic": 60,

					// 检查周期内触发限制的流量数
					"Traffic": 1000,

					// 触发限制后的速度限制
					"SpeedLimit": 100,

					// 速度限制过期时间
					"ExpireTime": 60

				}
			},

			// 证书相关配置
			"CertConfig": {
				// 证书申请模式，none、http、dns、self
				"CertMode": "none",

				"RejectUnknownSni": false,

				// 证书域名
				"CertDomain": "test.com",

				// 证书文件目录
				"CertFile": "/etc/V2bX/cert/1.pem",

				// 密钥文件目录
				"KeyFile": "/etc/V2bX/cert/1.key",

				// 申请证书时使用的用户邮箱
				"Email": "1@test.com",

				// DNS解析提供者
				"Provider": "cloudflare",

				// DNS解析提供者的环境变量，详见 https://go-acme.github.io/lego/dns/
				"DNSEnv": {
					"CF_DNS_API_TOKEN" = "1234567890abcdefghijklmnopqrstuvwxyz "
				}
			}
		},
		{
			// xray内核

			"Name": "xray_node1",
			"Core": "xray",
			"CoreName": "",
			"ApiHost": "http://127.0.0.1",
			"ApiKey": "test",
			"NodeID": 33,
			"NodeType": "shadowsocks",
			"Timeout": 30,
			"ListenIP": "0.0.0.0",
			"SendIP": "0.0.0.0",
			"DeviceOnlineMinTraffic": 100,
			"EnableProxyProtocol": true,
			"EnableTFO": true,
			// 以上同 sing

			// 开启自定义DNS
			"EnableDNS": false,

			// DNS解析类型，AsIs、UseIP、UseIPv4、UseIPv6
			"DNSType": "AsIs",

			// 开启udp over tcp
			"EnableUot": false,

			// 禁用IVCheck
			"DisableIVCheck": false,

			// 禁用嗅探
			"DisableSniffing": false,

			// 开启回落
			"EnableFallback": false,

			// 回落相关配置 详见 https://xtls.github.io/config/features/fallback.html#fallbackobject
			"FallBackConfigs": [
				{
					"SNI": "",
					"Alpn": "",
					"Path": "",
					"Dest": "",
					"ProxyProtocolVer": 0
				}
			]
		},
		{
			// 引用本地其他配置文件
			"Include": "../example/config_full_node1.json"
		},
		{
			// 通过Http引用远端配置文件
			"Include": "http://127.0.0.1:11451/config_full_node1.json"
		}
	]
}
```
