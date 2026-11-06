# Egern 配置（由 Loon 迁移）

本目录是 `Tool/Loon/Lcf/zh-CN/Loon_Auto_Select_Config_By_iKeLee.lcf` 的 Egern 等价版，2026-11-06 迁移。

- 配置文件：`Yaml/zh-CN/Egern_Auto_Select_Config_By_iKeLee.yaml`
- 订阅地址（导入用）：`https://raw.githubusercontent.com/xuanwoa/ProxyResource/main/Tool/Egern/Yaml/zh-CN/Egern_Auto_Select_Config_By_iKeLee.yaml`

## 迁移对应关系

| Loon | Egern |
| --- | --- |
| [Remote Proxy]（本地添加的机场订阅） | 策略组「机场节点池」（external 类型，订阅合并成一个池） |
| [Remote Filter] 地区正则 | 各手动策略组的 `filter`（正则原样搬运） |
| [Proxy Group] 6 个手动 select 组 | 同名 select 组（全节点 / 菲律宾 / 韩国 / 美国 / 英国 / 德国） |
| [Remote Rule] 10 条 | `rules` 里的 rule_set，顺序与策略绑定不变 |
| 内置 GEOIP CN 直连 | `geoip: CN → DIRECT` |
| FINAL, 全节点手动策略 | `default → 全节点手动策略` |
| [Plugin] 约 20 个插件 | 不进配置文件，在 App 里以「模组」逐个添加（见下方清单） |
| [Mitm] 本地 CA | 在 Egern 里重新生成 CA 并信任（一次性） |

原 Loon 配置里注释停用的各国 url-test 时延优选组，Egern 版同样未启用；需要时照菲律宾组的样子加 `auto_test` 组即可。

## 导入步骤

1. Egern → 配置 → 从 URL 导入上面的订阅地址。
2. 打开 Loon → 远程代理，把机场订阅链接抄出来，填进配置里「机场节点池」的 `urls`（占位链接共 2 条，只有一个机场删掉一行）。也可以直接在仓库里改这个文件再等自动更新，或在 App 的策略组里改。
3. Egern → 工具 → MITM：生成 CA 证书 → 安装描述文件 → 设置 → 通用 → 关于本机 → 证书信任设置里开启完全信任。
4. 按下方清单逐个添加模组：工具 → 模组 → 右上角 + → 粘贴链接。Egern 会自动转换 Loon 插件。
5. 首跑检查：各策略组的节点数应与 Loon 里一致（验证 filter 正则生效）；逐条远程规则应显示已加载；打开几个常用 App 验证去广告是否工作。

## 插件（模组）清单

### 直接导入（Loon 链接原样粘贴，大概率直通）

- HTTPDNS 拦截：https://raw.githubusercontent.com/VirgilClyne/GetSomeFries/main/plugin/HTTPDNS.Block.plugin
- 广告拦截（kelee）：https://kelee.one/Tool/Loon/Lpx/BlockAdvertisers.lpx
- 去广告（kelee）：https://kelee.one/Tool/Loon/Lpx/Remove_ads_by_keli.lpx
- 高德去广告：https://kelee.one/Tool/Loon/Lpx/Amap_remove_ads.lpx
- B站去广告：https://kelee.one/Tool/Loon/Lpx/Bilibili_remove_ads.lpx
- YouTube 去广告：https://kelee.one/Tool/Loon/Lpx/YouTube_remove_ads.lpx
- 知乎去广告：https://kelee.one/Tool/Loon/Lpx/Zhihu_remove_ads.lpx
- 微信公众号去广告：https://kelee.one/Tool/Loon/Lpx/Weixin_Official_Accounts_remove_ads.lpx
- 微信小程序去广告：https://kelee.one/Tool/Loon/Lpx/WexinMiniPrograms_Remove_ads.lpx
- 微信外链解锁：https://kelee.one/Tool/Loon/Lpx/Weixin_external_links_unlock.lpx
- 防 DNS 泄露：https://kelee.one/Tool/Loon/Lpx/Prevent_DNS_Leaks.lpx （导入后确认其策略指向「全节点手动策略」）

### 有对应版 / 需验活

- BoxJS：https://raw.githubusercontent.com/chavyleung/scripts/master/box/rewrite/boxjs.rewrite.loon.plugin
- Sub-Store：https://raw.githubusercontent.com/sub-store-org/Sub-Store/master/config/Loon.plugin
- Script-Hub：https://raw.githubusercontent.com/Script-Hub-Org/Script-Hub/main/modules/script-hub.loon.plugin （某个插件在 Egern 里哑火时，用它把该插件转成 Egern 模组再导入）
- QuickSearch：https://kelee.one/Tool/Loon/Lpx/QuickSearch.lpx
- 节点检测工具：https://kelee.one/Tool/Loon/Lpx/Node_detection_tool.lpx
- NodeLinkCheck：https://kelee.one/Tool/Loon/Lpx/NodeLinkCheck.lpx
- TestFlight 地区解锁：https://kelee.one/Tool/Loon/Lpx/TestFlightRegionUnlock.lpx （Loon 里本来就是禁用状态，按需）

### 直接扔

- WLOC（插件 `wloc.lpx` + 规则集 `WLOC.lsr`）：已于 2026-11-06 从 Loon 与 Egern 两份配置中一并移除/注释（WLOC 于 2026-11-04 被苹果证书固定封死，全版本通杀）。仓库 `Tool/Loon/Rules/WLOC.lsr` 文件保留未删，仅作存档，不再被任何配置引用。

## 与 Loon 版的有意差异

- 私有/保留网段采用**规则直连**（`rules` 顶部的 `ip_cidr → DIRECT`，借鉴官方默认配置的写法），不走 `bypass_tunnel_proxy` 绕过：流量先进 Egern 再被规则放行，以后加 Tailscale 子网路由回家时只需在规则顶部插一条即可。`100.64.0.0/10`（Tailscale CGNAT 网段）在 bypass 和直连规则里都不出现，完整留给内置 Tailscale。官方默认档里的 `geoip: GB → DIRECT` 明确不借（按地区/定位生成的坑，本人在大陆会把英国流量直连送死）。
- 补了官方默认档的 `default_proxy_group`（指向「全节点手动策略」）：在 App 里手动新加的单个节点会自动落进主组。
- DNS 沿用系统 DNS（Loon 原配置即 `dns-server = system`），未额外指定 DoH；kelee 的 Prevent_DNS_Leaks 模组导入后照旧工作。

## Tailscale（本次迁移的主要目的）

1. 在 Egern 里添加 Tailscale 代理（浏览器登录，不要用长期 auth key），Prev Hop（前置代理）指向一个香港节点或「全节点手动策略」作为国内兜底。
2. 想固定走香港前置的话，照菲律宾组的样子新建一个 select 组，filter 用原 Loon 模板的香港正则：
   `^(?=.*((?i)🇭🇰|香港|(\b(HK|HKG|Hong\s?Kong)(\d+)?\b)))(?!.*((?i)回国|校园|游戏|🎮|(\b(GAME)\b))).*$`
3. 家里若有旁路由/软路由跑 Tailscale 子网路由（宣告 192.168.31.0/24），手机这边在 Tailscale 设置里接受子网路由即可直达内网设备。
