# 🚀 AgentPit 金融AI开放API平台

[![Free API](https://img.shields.io/badge/API-Free-green)](https://develop.agentpit.io/help)
[![Google Search](https://img.shields.io/badge/Google%20Search-Grounding-blue)](https://develop.agentpit.io/help#google-search)
[![K-Line Pro](https://img.shields.io/badge/K--Line-Pro%20多因子-orange)](https://develop.agentpit.io/help#kpred)
[![Fin-R1](https://img.shields.io/badge/Fin--R1-金融大模型-purple)](https://develop.agentpit.io/help#llm)
[![FinGPT](https://img.shields.io/badge/FinGPT-情感分析-cyan)](https://develop.agentpit.io/help#fingpt)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> 为量化开发者和金融研究者提供免费、开放的金融AI API接口  
> — Google实时搜索 + **Pro 多因子K线预测** + **Fin-R1 金融大模型** + **FinGPT 情感分析**，即刻接入，无需信用卡。

**📖 完整文档：[develop.agentpit.io/help](https://develop.agentpit.io/help)**

---

## ✨ 核心能力

| API | 功能 | 典型场景 |
|---|---|---|
| 🔍 **Google搜索API** | 实时搜索Google全网，AI自动生成摘要 | 获取券商研报、新闻资讯、机构观点 |
| 📈 **K线预测API** | **Pro 多因子模型**预测未来N日K线 + 评级 | 量化策略辅助、趋势研判、因子分析 |
| 🧠 **Fin-R1 金融大模型API** | 上海财经大学开源金融LLM，链式推理 | 研报解读、财报问答、投资逻辑分析 |
| 💬 **FinGPT 情感分析API** | 哥伦比亚大学AI4Finance，14k+ Stars | 金融新闻情感打标，事件驱动信号辅助 |

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

选择需要的 API（Google搜索 / K线预测 / Fin-R1大模型 / FinGPT情感分析），提交申请。管理员审批通过后你会收到一个 `oapk_` 开头的API Key。

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

---

### 3. Fin-R1 金融大模型 API

**接口地址**
```
POST https://api.agentpit.io/v1/open-api/llm
```

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `messages` | array | ✅ | 对话消息数组，格式同 OpenAI |
| `max_tokens` | number | 否 | 最大输出 token，默认 1024 |
| `temperature` | number | 否 | 随机性 0~1，默认 0.7 |

**Python 示例**

```python
import requests

API_KEY = "oapk_your_key_here"

resp = requests.post(
    "https://api.agentpit.io/v1/open-api/llm",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "messages": [
            {"role": "user", "content": "分析贵州茅台2026年Q1财报净利润下降5%，给出投资建议"}
        ],
        "max_tokens": 800,
    },
)
data = resp.json()
print(data["content"])        # 模型分析文字
print(data["tokensUsed"])     # 消耗 token 数
print(data["latencyMs"])      # 响应耗时(ms)
```

**Node.js 示例**

```javascript
const res = await fetch("https://api.agentpit.io/v1/open-api/llm", {
  method: "POST",
  headers: {
    "Authorization": "Bearer oapk_your_key_here",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    messages: [{ role: "user", content: "紫金矿业铜价上涨对业绩的影响分析" }],
    max_tokens: 600,
  }),
});
const { content, latencyMs } = await res.json();
console.log(content);
```

**返回格式**

```json
{
  "model": "fin-r1",
  "content": "根据贵州茅台2026年Q1财报数据分析...\n\n1. 净利润下降原因：...\n2. 投资建议：...",
  "tokensUsed": 856,
  "latencyMs": 8500
}
```

> **适用场景：** 研报解读、财报问答、投资逻辑分析、行业趋势分析  
> **不适合：** 实时行情查询（无联网）、高频调用（响应约5~15秒）、结构化JSON输出

---

### 4. FinGPT 金融情感分析 API

基于哥伦比亚大学 AI4Finance Foundation 开源的 FinGPT（GitHub 14k+ Stars，MIT协议），使用 ChatGLM2-6B + LoRA 微调版本，专门针对金融文本情感分析优化，支持中英文双语输入。

**接口地址**
```
POST https://api.agentpit.io/v1/open-api/fingpt
```

**请求参数**

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| `text` | string | ✅ | 待分析的金融文本，中英文均可，建议 500 字以内 |

**Python 示例**

```python
import requests

API_KEY = "oapk_your_key_here"

def analyze_sentiment(text: str) -> dict:
    resp = requests.post(
        "https://api.agentpit.io/v1/open-api/fingpt",
        headers={"Authorization": f"Bearer {API_KEY}"},
        json={"text": text},
        timeout=30,
    )
    resp.raise_for_status()
    return resp.json()

# 单条分析
result = analyze_sentiment("贵州茅台Q1净利润同比增长18%，超市场预期")
print(f"情感：{result['sentiment']}  延迟：{result['latencyMs']}ms")
# 情感：positive  延迟：118ms

# 批量分析新闻流
news_list = [
    "贵州茅台Q1净利润同比增长18%，超市场预期",
    "比亚迪Q2营收大幅下滑，市场预期落空",
    "央行维持利率不变，市场反应平稳",
    "Apple reports record quarterly earnings, beating analyst estimates",
]

for news in news_list:
    r = analyze_sentiment(news)
    label = {"positive": "📈 正面", "negative": "📉 负面", "neutral": "➖ 中性"}[r["sentiment"]]
    print(f"{label}  {news[:30]}")
```

**Node.js 示例**

```javascript
const API_KEY = "oapk_your_key_here";

async function analyzeSentiment(text) {
  const res = await fetch("https://api.agentpit.io/v1/open-api/fingpt", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${API_KEY}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ text }),
  });
  return res.json();
}

// 事件驱动策略辅助
const news = "Apple reports record quarterly revenue, shares surge in after-hours";
const { sentiment } = await analyzeSentiment(news);
if (sentiment === "positive") {
  console.log("触发正面事件信号，考虑买入或持仓");
} else if (sentiment === "negative") {
  console.log("触发负面事件信号，考虑风控或减仓");
}
```

**返回格式**

```json
{
  "model": "fingpt-sentiment",
  "sentiment": "positive",
  "text": "贵州茅台Q1净利润同比增长18%，超市场预期",
  "tokensUsed": 22,
  "latencyMs": 118
}
```

**情感标签说明**

| 标签 | 含义 | 典型例子 |
|---|---|---|
| `positive` | 正面/利好 | 净利润增长、超预期、获得大合同、股价创新高 |
| `negative` | 负面/利空 | 营收下滑、亏损、被调降评级、负面监管事件 |
| `neutral` | 中性 | 维持利率不变、正常季报披露、行业例行公告 |

> **注意事项：**
> - 首次调用有约 60 秒热身延迟（GPU 冷启动），后续请求正常 100~300ms
> - 输入文本建议 500 字以内，纯金融新闻/公告效果最佳
> - 情感以金融语境为准，非通用情感（如"茅台跌停" → negative，不是中性）

---


### 5. Kronos K线预测 API（纯基础模型）

> **Kronos** — 清华大学 IIIS + 自动化系，AAAI 2026 录用，GitHub 31k+ Stars，MIT 协议  
> 直接调用 Kronos 原始模型，输出纯 OHLCV 预测序列，**不叠加任何多因子权重**  
> 📌 与上方 K线预测 API（Pro 多因子）的区别：Pro 版叠加了主力资金、RSI、MACD 等 8 个因子；本接口为模型原始输出，适合自行融合因子或对比研究

**接口信息**

| 项目 | 内容 |
|------|------|
| 端点 | POST https://api.agentpit.io/v1/open-api/kronos |
| 认证 | Authorization: Bearer oapk_YOUR_KEY |
| 申请 | https://develop.agentpit.io/dashboard/open-api/apply?type=KRONOS |

**请求参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | A 股股票代码，如 300308、600519 |
| days | number | ❌ | 预测天数，范围 1~30，默认 5 |

**请求示例（curl）**

`ash
curl -X POST https://api.agentpit.io/v1/open-api/kronos \
  -H "Authorization: Bearer oapk_YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"code":"300308","days":5}'
`

**响应示例**

`json
{
  "symbol": "300308",
  "name": "中际旭创",
  "last_date": "2026-06-26",
  "last_close": 1332.52,
  "predictions": [
    {"date":"2026-06-27","open":1340.1,"high":1368.5,"low":1318.0,"close":1355.2,"volume":412000},
    {"date":"2026-06-30","open":1355.2,"high":1380.0,"low":1340.0,"close":1372.8,"volume":388000},
    {"date":"2026-07-01","open":1372.8,"high":1395.0,"low":1355.0,"close":1360.5,"volume":356000},
    {"date":"2026-07-02","open":1360.5,"high":1370.0,"low":1330.0,"close":1348.0,"volume":320000},
    {"date":"2026-07-03","open":1348.0,"high":1365.0,"low":1325.0,"close":1358.5,"volume":298000}
  ],
  "latencyMs": 820
}
`

**Python 示例**

`python
import requests

API_KEY = "oapk_YOUR_KEY"

resp = requests.post(
    "https://api.agentpit.io/v1/open-api/kronos",
    json={"code": "300308", "days": 5},
    headers={"Authorization": f"Bearer {API_KEY}"},
    timeout=15,
)
data = resp.json()

print(f"{data['name']}  最新收盘: {data['last_close']}")
for p in data["predictions"]:
    chg = (p["close"] - data["last_close"]) / data["last_close"] * 100
    print(f"  {p['date']}  收:{p['close']:.2f}  ({chg:+.2f}%)")
`

**Node.js 示例**

`javascript
const res = await fetch("https://api.agentpit.io/v1/open-api/kronos", {
  method: "POST",
  headers: {
    "Authorization": "Bearer oapk_YOUR_KEY",
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ code: "600519", days: 5 }),
});

const { name, last_close, predictions } = await res.json();
console.log(${name} 最新收盘: );
predictions.forEach(p => {
  const chg = ((p.close - last_close) / last_close * 100).toFixed(2);
  console.log(    收:  (%));
});
`

> **注意事项：**
> - 本 API 为纯 Kronos 模型输出，预测仅基于历史价量形态，**不含资金面、基本面信息**
> - 如需多因子融合版，请使用上方的 K线预测 API（Pro 多因子，KPRED）
> - 预测天数越长不确定性越高，建议配合其他信号使用，不构成投资建议

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
- [x] 用户自定义因子权重
- [x] **Fin-R1 金融大模型API**（上海财经大学，A股中文优化，链式推理）
- [x] **FinGPT 情感分析API**（哥伦比亚大学 AI4Finance，中英文双语，positive/negative/neutral）
- [ ] 轩辕 XuanYuan（度小满金融大模型）API
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