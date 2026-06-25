# 🚀 AgentPit 金融AI开放API平台

[![Free API](https://img.shields.io/badge/API-Free-green)](https://develop.agentpit.io/help)
[![Google Search](https://img.shields.io/badge/Google%20Search-Grounding-blue)](https://develop.agentpit.io/help#google-search)
[![K-Line Prediction](https://img.shields.io/badge/K--Line-AI%20Prediction-orange)](https://develop.agentpit.io/help#kpred)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> 为量化开发者和金融研究者提供免费、开放的金融AI API接口
> — Google实时搜索 + 多因子K线预测，即刻接入，无需信用卡。

**📖 完整文档：[develop.agentpit.io/help](https://develop.agentpit.io/help)**

---

## ✨ 核心能力

| API | 功能 | 计费 | 典型场景 |
|---|---|---|---|
| 🔍 **Google搜索API** | 实时搜索Google全网，AI自动生成摘要 | 按Token计费 | 获取券商研报、新闻资讯、机构观点 |
| 📈 **K线预测API** | 多因子模型预测未来5日K线方向+置信度 | 100 Token/次 | 量化策略辅助、趋势研判 |

> 🎁 **目前完全免费开放**，默认月度额度 10,000 Token

---

## 🏁 快速开始

### 第一步：注册账号

访问 **[agentpit.io](https://www.agentpit.io/finance)** → 点击右上角注册（免费，无需信用卡）

### 第二步：申请API Key

登录后 → 点右上角头像 → **「进入后台」** → 左侧「开发API」→「我的API」→「申请新API」

选择 Google搜索 或 K线预测，提交申请。管理员审批通过后你会收到一个 `oapk_` 开头的API Key。

### 第三步：开始调用

```bash
# 快速测试（curl）
curl -X POST https://api.agentpit.io/v1/open-api/search \
  -H "Authorization: Bearer oapk_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{"query": "中际旭创 最新研报 2026"}'
```

---

## 📡 API 接口文档

### 1. Google 搜索 API

**接口地址**
```
POST https://api.agentpit.io/v1/open-api/search
```

**请求示例**

```python
import requests

API_KEY = "oapk_your_key_here"

resp = requests.post(
    "https://api.agentpit.io/v1/open-api/search",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"query": "贵州茅台 机构评级 最新研报 2026"},
)
data = resp.json()
print(data["result"])      # AI搜索摘要
print(data["tokensUsed"])  # 本次消耗Token
```

```javascript
// Node.js
const res = await fetch("https://api.agentpit.io/v1/open-api/search", {
  method: "POST",
  headers: {
    "Authorization": "Bearer oapk_your_key_here",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ query: "紫金矿业 铜价上涨影响分析" }),
});
const { result, tokensUsed } = await res.json();
console.log(result);
```

**返回格式**
```json
{
  "result": "根据最新搜索，中际旭创2026年Q1净利润57.35亿元，同比增长262%...",
  "tokensUsed": 856,
  "latencyMs": 3200
}
```

---

### 2. K线预测 API

**接口地址**
```
POST https://api.agentpit.io/v1/open-api/kpred
```

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `code` | string | ✅ | 股票代码，如 `300308`、`600519` |
| `market` | string | 否 | `A`（默认）/ `HK` / `US` |
| `days` | number | 否 | 预测天数，默认5，最大10 |

**请求示例**

```python
import requests

API_KEY = "oapk_your_key_here"

resp = requests.post(
    "https://api.agentpit.io/v1/open-api/kpred",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"code": "300308", "market": "A", "days": 5},
)
data = resp.json()

print(f"{data['name']} 未来5日预测：")
for p in data["predictions"]:
    arrow = "↑" if p["direction"] == "up" else ("↓" if p["direction"] == "down" else "→")
    print(f"  {p['date']}  {arrow}  置信度 {p['confidence']:.0%}")
```

```javascript
// Node.js
const res = await fetch("https://api.agentpit.io/v1/open-api/kpred", {
  method: "POST",
  headers: {
    "Authorization": "Bearer oapk_your_key_here",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ code: "600519", market: "A" }),
});
const { name, predictions } = await res.json();
predictions.forEach(p => {
  console.log(`${p.date}: ${p.direction} (${(p.confidence * 100).toFixed(0)}%)`);
});
```

**返回示例**
```json
{
  "code": "300308",
  "name": "中际旭创",
  "predictions": [
    { "date": "2026-06-26", "direction": "up",   "confidence": 0.72 },
    { "date": "2026-06-27", "direction": "up",   "confidence": 0.68 },
    { "date": "2026-06-28", "direction": "flat", "confidence": 0.55 },
    { "date": "2026-06-29", "direction": "down", "confidence": 0.61 },
    { "date": "2026-06-30", "direction": "up",   "confidence": 0.64 }
  ],
  "latencyMs": 420
}
```

---

## ⚠️ 错误码

| HTTP状态码 | 含义 | 处理方式 |
|---|---|---|
| 401 | API Key 无效或已撤销 | 检查Key是否正确，或重新申请 |
| 429 | 超出月度Token额度 | 联系管理员提升额度 |
| 502 | 上游服务异常 | 稍后重试 |

---

## 🗺️ 路线图

- [x] Google Search Grounding API
- [x] Kronos 多因子K线预测API
- [ ] FinGPT（清华金融NLP）API
- [ ] 轩辕 XuanYuan（度小满金融大模型）API
- [ ] DISC-FinLLM（复旦大学金融大模型）API
- [ ] InvestLM（香港科大投资分析模型）API
- [ ] 更多金融开源大模型持续上线中...

> ⭐ Star 本仓库，第一时间获取新API上线通知

---

## 💬 开发者社群

扫码加入 **AgentPit 金融AI开发者群**，与量化开发者交流API使用技巧和金融AI应用。

<!-- 微信群二维码（定期更新） -->
> 微信群二维码即将更新，Star本仓库持续关注

---

## 📄 License

MIT License — 免费使用，欢迎 Fork 和 Star ⭐

---

<p align="center">
  Made with ❤️ by <a href="https://www.agentpit.io">AgentPit</a> ·
  <a href="https://develop.agentpit.io/help">完整文档</a> ·
  <a href="https://www.agentpit.io/finance">立即注册</a>
</p>
