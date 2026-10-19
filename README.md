# Nginx Bark 实时访问通知

极致轻量，单次运行内存 < 8MB，不占服务器资源。直接解析 Nginx 访问日志，自动识别访客信息，通过 Bark 推送到手机。

## 功能特性

- ✅ 实时通知：新访客打开网站立刻推送
- ✅ 精准识别：
  - 系统：iOS / Android / HarmonyOS 鸿蒙 / HyperOS 澎湃 OS / Windows / macOS
  - 设备：iPhone 全系列型号、华为/荣耀/小米/红米/vivo/OPPO/三星安卓机型自动识别
  - 浏览器：Safari / Chrome / Edge / 微信内置 / QQ浏览器 / 抖音内置 / 小红书内置
  - 来源：微信 / QQ / 抖音 / 小红书 / 百度 / 谷歌 / 直接访问
  - 网络：移动流量 / 宽带WiFi / 代理VPN
  - 运营商自动转中文：中国移动 / 中国电信 / 中国联通
- ✅ 自动过滤静态资源、扫描探测请求，只有真实页面访问才通知
- ✅ 单实例锁，绝不重复推送
- ✅ 每个站点单独记录读取位置，日志轮转自动处理
- ✅ IP结果缓存，不重复请求接口
- ✅ 国外访问自动标注🌍
- ✅ 真人/脚本爬虫自动区分，极小提示
- ✅ 每日0点自动发送昨日访问汇总

## 快速部署

### 1. 复制脚本到服务器
```bash
cp visit_notify.py /root/visit_notify.py
chmod +x /root/visit_notify.py
```

### 2. 修改配置
编辑脚本顶部：
```python
BARK_KEY = "你的Bark Key"
SITES = [
    ("你的域名1", "/www/站点1/log/access.log"),
    ("你的域名2", "/www/站点2/log/access.log"),
]
```

### 3. 添加定时任务
```bash
# 每分钟检查新访问
(crontab -l; echo "* * * * * /usr/bin/python3 /root/visit_notify.py > /dev/null 2>&1") | crontab -
# 每天0点发送昨日汇总
(crontab -l; echo "0 0 * * * /usr/bin/python3 /root/daily_summary.py > /dev/null 2>&1") | crontab -
```

## 通知效果示例
```
👤 新访客
广西柳州 | 112.74.115.220
来源: 微信
设备: 手机 iPhone 15 Pro
系统: iOS 18.1
浏览器: 微信内置浏览器
网络: 广西移动 移动流量
页面: https://furryhk.cn/
时间: 2026-10-20 12:34:56 (北京时间)
· 真人访问
```

## 低占用设计
- 单实例文件锁，上一次没跑完直接退出，不重复运行
- 每个日志文件单独记录读取位置，不会重复读历史日志
- IP地理位置结果最多缓存300条，文件永久<100KB
- 只在新日志产生时才处理，平时无任何后台开销
- 每分钟运行一次，一次运行<1秒，CPU占用可忽略