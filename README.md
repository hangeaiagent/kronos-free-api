# 🚀 AgentPit 金融AI开放API平台

[![Free API](https://img.shields.io/badge/API-Free-green)](https://develop.agentpit.io/help)
[![Google Search](https://img.shields.io/badge/Google%20Search-Grounding-blue)](https://develop.agentpit.io/help#google-search)
[![K-Line Pro](https://img.shields.io/badge/K--Line-Pro%20多因子-orange)](https://develop.agentpit.io/help#kpred)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> 为量化开发者和金融研究者提供免费、开放的金融AI API接口  
> — Google实时搜索 + **Pro 多因子K线预测**，即刻接入，无需信用卡。

**📖 完整文档：[develop.agentpit.io/help](https://develop.agentpit.io/help)**

---

## ✨ 核心能力

| API | 功能 | 典型场景 |
|---|---|---|
| 🔍 **Google搜索API** | 实时搜索Google全网，AI自动生成摘要 | 获取券商研报、新闻资讯、机构观点 |
| 📈 **K线预测API** | **Pro 多因子模型**预测未来N日K线 + 评级 | 量化策略辅助、趋势研判、因子分析 |

> 🎁 **目前完全免费开放**

---

## 🧠 K线预测模型说明

K线预测 API 采用 **Pro 多因子模型**，非简单方向预测，而是综合 8 个维度输出：

| 因子 | 默认权重 | 说明 |
|---|---|---|
| Kronos技术 | 10% | Kronos 深度学习形态预测 |
| 均线趋势 | 10% | MA5/10/20/60 多头排列 |
| MACD动量 | 8% | MACD柱 / ATR 比值 |
| RSI超买卖 | 7% | RSI14 超买超卖判断 |
| **主力净流入** | **35%** | 近5日主力资金净流入 |
| 筹码分布 | 15% | 主力 vs 散户资金背离 |
| 近5日K线 | 5% | 近5日阳线占比 |
| PE估值 | 10% | 当前PE相对历史分位 |

模型输出：调整后K线序列、综合评级（强烈看多/温和看多/中性/温和看空/强烈看空）、各因子贡献明细。

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
curl -X POST https://api.agentpit.io/v1/open-api/kpred \
  -H "Authorization: Bearer oapk_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{"code": "300308", "days": 5}'
```

---

## 📡 API 接口文档

### 1. Google 搜索 API

**接口地址**
```
POST https://api.agentpit.io/v1/open-api/search
```

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `query` | string | ✅ | 搜索关键词，如 "贵州茅台 机构评级 最新研报" |

**Python 示例**

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
```

**Node.js 示例**

```javascript
const res = await fetch("https://api.agentpit.io/v1/open-api/search", {
  method: "POST",
  headers: {
    "Authorization": "Bearer oapk_your_key_here",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ query: "紫金矿业 铜价上涨影响分析" }),
});
const { result } = await res.json();
console.log(result);
```

**返回格式**
```json
{
  "result": "根据最新搜索，中际旭创2026年Q1净利润57.35亿元，同比增长262%...",
  "latencyMs": 3200
}
```

---

### 2. K线预测 API（Pro 多因子）

**接口地址**
```
POST https://api.agentpit.io/v1/open-api/kpred
```

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `code` | string | ✅ | 股票代码，如 `300308`、`600519` |
| `days` | number | 否 | 预测天数 1~30，默认 5 |

**Python 示例**

```python
import requests

API_KEY = "oapk_your_key_here"

resp = requests.post(
    "https://api.agentpit.io/v1/open-api/kpred",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"code": "300308", "days": 5},
)
data = resp.json()
pro = data["pro"]

# 评级与预期收益
print(f"{data['name']}  最新收盘: {data['last_close']}")
print(f"综合评级: {pro['rating']}  置信度: {pro['confidence']}")
print(f"预期涨跌幅: {pro['adj_return_pct']}%")

# 调整后K线预测
print("\n未来K线预测:")
for p in data["predictions"]:
    print(f"  {p['date']}  开:{p['open']:.2f}  高:{p['high']:.2f}  低:{p['low']:.2f}  收:{p['close']:.2f}")

# 各因子贡献（按贡献绝对值排序）
print("\n因子明细:")
for f in pro["factors"]:
    print(f"  {f['label']:8s}  得分:{f['score']:+.3f}  权重:{f['weight']:.0%}  贡献:{f['contribution']:+.3f}")
```

**Node.js 示例**

```javascript
const API_KEY = "oapk_your_key_here";

const res = await fetch("https://api.agentpit.io/v1/open-api/kpred", {
  method: "POST",
  headers: {
    "Authorization": `Bearer ${API_KEY}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ code: "600519", days: 5 }),
});

const data = await res.json();
const { name, last_close, predictions, pro } = data;

console.log(`${name} — 评级: ${pro.rating}  置信度: ${pro.confidence}`);
console.log(`预期涨跌幅: ${pro.adj_return_pct}%`);

predictions.forEach(p => {
  console.log(`  ${p.date}: 开${p.open} 高${p.high} 低${p.low} 收${p.close}`);
});
```

**返回格式**

```json
{
  "symbol": "300308",
  "name": "中际旭创",
  "last_close": 1332.52,
  "last_date": "2026-06-25",
  "predictions": [
    { "date": "2026-06-26", "open": 1310.0, "high": 1350.0, "low": 1290.0, "close": 1340.0, "volume": 420000 },
    { "date": "2026-06-27", "open": 1340.0, "high": 1370.0, "low": 1315.0, "close": 1355.0, "volume": 388000 }
  ],
  "pro": {
    "composite_score": 0.312,
    "rating": "温和看多",
    "confidence": "62%",
    "conflict_level": "low",
    "adj_return_pct": 3.85,
    "factor_return_pct": 4.12,
    "kronos_raw_return_pct": 1.20,
    "sigma_daily_pct": 2.15,
    "factors": [
      { "key": "main_flow", "label": "主力净流入", "score": 0.68, "weight": 0.35, "contribution": 0.238 },
      { "key": "flow_div",  "label": "筹码分布",   "score": 0.41, "weight": 0.15, "contribution": 0.062 },
      { "key": "kronos",   "label": "Kronos技术",  "score": 0.35, "weight": 0.10, "contribution": 0.035 },
      { "key": "ma_align", "label": "均线趋势",    "score": 0.28, "weight": 0.10, "contribution": 0.028 }
    ]
  },
  "latencyMs": 1850
}
```

> **字段说明：**
> - `composite_score`：综合得分 -1（强烈看空）到 +1（强烈看多）
> - `rating`：强烈看多 / 温和看多 / 中性观望 / 温和看空 / 强烈看空
> - `predictions`：已融合多因子调整后的K线序列（非纯模型原始输出）
> - `factors`：按 contribution 绝对值降序排列，方便快速定位主导因子

---

---

## ⚙️ 自定义因子权重

K线预测 API 支持**每个用户独立设置因子权重**，权重绑定账号，所有 API 调用自动生效。

### 如何设置

登录 [develop.agentpit.io/dashboard/open-api](https://develop.agentpit.io/dashboard/open-api)，K线预测审批通过后，页面下方会出现「自定义因子权重」面板：

- 拖动 8 个因子的滑块调整占比
- 系统自动归一化（无需手动凑 100%）
- 点击「保存权重」即时生效

### 权重策略建议

| 使用场景 | 推荐调整 |
|---|---|
| 偏重资金面 | 主力净流入 50%+，筹码分布 20% |
| 偏重技术面 | 均线趋势/MACD/RSI 合计 40%+ |
| 偏重估值 | PE估值 25%+，降低其他权重 |
| 量化中性 | 使用默认权重（主力净流入 35% 为核心）|

### 返回结果中验证权重

每次 API 响应的 `pro.factors[].weight` 字段会显示**本次使用的实际权重**，方便调试：

```python
resp = requests.post("https://api.agentpit.io/v1/open-api/kpred", ...)
for f in resp.json()["pro"]["factors"]:
    print(f"{f['label']:8s}  当前权重: {f['weight']:.0%}  贡献: {f['contribution']:+.3f}")
```

---

## ⚠️ 错误码

| HTTP状态码 | 含义 | 处理方式 |
|---|---|---|
| 401 | API Key 无效或已撤销 | 检查Key是否正确，或重新申请 |
| 429 | 超出月度Token额度 | 联系管理员提升额度 |
| 502 | 预测服务异常 | 稍后重试 |

---

## 🗺️ 路线图

- [x] Google Search Grounding API
- [x] Pro 多因子K线预测API（Kronos + 技术/资金/估值 8因子）
- [ ] 用户自定义因子权重（coming soon）
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
