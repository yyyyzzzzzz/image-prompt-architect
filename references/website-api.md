# 梦想织机生图接口

站点：`https://pic.1dream.cn`

所有接口都要求请求头：

```
X-Auth-Code: <20 位授权码>
Content-Type: application/json
```

授权码在网站顶部「Skills」页或「文档中心」页点「复制」获得，只展示前后四位。它等价于账号密码，**不要写进代码仓库、日志、截图或公开对话**。

## 目录

1. [获取模型列表](#1-获取模型列表)
2. [查询积分](#2-查询积分)
3. [提交生图](#3-提交生图)
4. [查询任务](#4-查询任务)
5. [完整调用流程](#5-完整调用流程)
6. [错误码](#6-错误码)
7. [限制与注意事项](#7-限制与注意事项)

## 1. 获取模型列表

```http
GET /api/open/skill/models
X-Auth-Code: <授权码>
```

返回免配渠道当前可用的模型。`display_name` 已按真实模型家族映射，对用户展示时用这个字段，不要只念原始 id。

```json
{
  "total": 8,
  "models": [
    {
      "id": "banana-2",
      "display_name": "Nano Banana · banana-2",
      "family": "Nano Banana",
      "vendor": "Google Gemini",
      "points_cost": 6,
      "max_reference_images": 3,
      "aspect_ratios": ["1:1", "3:4", "4:3", "9:16", "16:9"],
      "resolutions": ["1k", "2k"],
      "min_plan": ""
    },
    {
      "id": "yy-image-2",
      "display_name": "GPT-Image · yy-image-2",
      "family": "GPT-Image",
      "vendor": "OpenAI",
      "points_cost": 6,
      "max_reference_images": 5,
      "aspect_ratios": ["1:1", "4:5", "16:9"],
      "resolutions": ["1k", "2k", "4k"],
      "min_plan": ""
    }
  ]
}
```

映射规则：

| 原始 id 含 | 对外家族 |
| --- | --- |
| `banana` | Nano Banana（Google Gemini） |
| `yy-image` / `zi-image` / `gpt-image` | GPT-Image（OpenAI） |
| 其它 | 通用生图 |

模型 id 由渠道维护，可能随时增减；**永远以接口返回为准**，不要在代码里写死。

## 2. 查询积分

```http
GET /api/open/skill/points
X-Auth-Code: <授权码>
```

```json
{
  "username": "alice",
  "is_admin": false,
  "balance": 120,
  "points_per_yuan": 10
}
```

`points_per_yuan` 是充值换算：1 元 = 10 积分。

## 3. 提交生图

```http
POST /api/open/skill/generate
X-Auth-Code: <授权码>
Content-Type: application/json
```

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `model` | string | 是 | 模型原始 id，取自模型列表 |
| `prompt` | string | 是 | 主提示词 |
| `negative_prompt` | string | 否 | 负向提示词 |
| `aspect_ratio` | string | 否 | 必须在该模型的 `aspect_ratios` 里 |
| `resolution` | string | 否 | 必须在该模型的 `resolutions` 里 |
| `seed` | integer | 否 | 固定种子可复现；留空随机 |
| `reference_images` | string[] | 否 | `data:image/png;base64,...` 形式的 data URL |
| `mode` | string | 否 | `async`（默认，推荐）或 `sync` |
| `wait_seconds` | integer | 否 | 仅 `sync` 有效，10–300，默认 90 |

```json
{
  "model": "banana-2",
  "prompt": "一张 4:5 的商业产品摄影……",
  "negative_prompt": "cartoon, CGI, watermark, extra text",
  "aspect_ratio": "4:5",
  "resolution": "2k",
  "mode": "async"
}
```

返回一个任务对象（**提交成功 ≠ 出图成功**）：

```json
{
  "task_id": "9f2c1a7b3d4e",
  "status": "pending",
  "mode": "async",
  "model": "banana-2",
  "display_name": "Nano Banana · banana-2",
  "family": "Nano Banana",
  "points_cost": 6,
  "points_before": 120,
  "points_after": null,
  "refunded": false,
  "images": [],
  "error": "",
  "upstream_status": "pending",
  "params": { "prompt": "……", "aspectRatio": "4:5", "resolution": "2k" },
  "created_at": 1761300000.0,
  "next_poll_after": 5
}
```

- 积分在**提交时预扣**。任务失败会原额退回，`refunded` 为 `true`。
- `mode: "sync"` 时会一直等到出图或 `wait_seconds` 用完；没用完就返回 `pending` / `running` 和 `task_id`，此时必须继续轮询，**不要重新提交**。
- 同一张图重复提交会重复扣积分。

## 4. 查询任务

```http
GET /api/open/skill/tasks/{task_id}
X-Auth-Code: <授权码>
```

每次调用都会顺带向渠道推进一步，建议间隔 5 秒。`status` 取值：`pending`、`running`、`success`、`failed`。

成功：

```json
{
  "task_id": "9f2c1a7b3d4e",
  "status": "success",
  "model": "banana-2",
  "display_name": "Nano Banana · banana-2",
  "points_cost": 6,
  "points_before": 120,
  "points_after": 114,
  "refunded": false,
  "images": [
    {
      "file": "alice/9f2c1a7b3d4e-1.png",
      "url": "https://pic.1dream.cn/media/alice/9f2c1a7b3d4e-1.png?sig=...",
      "thumb_url": "https://pic.1dream.cn/media/alice/thumb/9f2c1a7b3d4e-1.webp?sig=...",
      "mime": "image/png",
      "ext": "png"
    }
  ],
  "error": "",
  "finished_at": 1761300060.0,
  "next_poll_after": null
}
```

失败（积分已退回）：

```json
{
  "task_id": "9f2c1a7b3d4e",
  "status": "failed",
  "points_cost": 6,
  "points_before": 120,
  "points_after": 120,
  "refunded": true,
  "error": "内容审核未通过",
  "images": []
}
```

统计口径：

- `points_before`：**预扣之前**的余额；
- `points_cost`：本次预扣的积分；
- `points_after`：任务结束（含退款）后的余额。任务未结束时为 `null`。

## 5. 完整调用流程

```bash
BASE=https://pic.1dream.cn
CODE=<授权码>

# 1) 看余额
curl -s "$BASE/api/open/skill/points" -H "X-Auth-Code: $CODE"

# 2) 看模型
curl -s "$BASE/api/open/skill/models" -H "X-Auth-Code: $CODE"

# 3) 提交（先让用户确认模型与积分）
curl -s -X POST "$BASE/api/open/skill/generate" \
  -H "X-Auth-Code: $CODE" -H "Content-Type: application/json" \
  -d '{"model":"banana-2","prompt":"一张 4:5 的商业产品摄影……","aspect_ratio":"4:5","resolution":"2k","mode":"async"}'

# 4) 轮询（每 5 秒，直到 success / failed）
curl -s "$BASE/api/open/skill/tasks/<task_id>" -H "X-Auth-Code: $CODE"
```

Python（只用标准库）：

```python
import json, time, urllib.request

BASE = "https://pic.1dream.cn"
CODE = "<授权码>"

def call(path, body=None):
    req = urllib.request.Request(
        BASE + path,
        data=None if body is None else json.dumps(body).encode(),
        headers={"X-Auth-Code": CODE, "Content-Type": "application/json"},
        method="GET" if body is None else "POST",
    )
    with urllib.request.urlopen(req, timeout=120) as resp:
        return json.load(resp)

task = call("/api/open/skill/generate", {
    "model": "banana-2",
    "prompt": "……",
    "mode": "async",
})
while task["status"] in ("pending", "running"):
    time.sleep(5)
    task = call(f"/api/open/skill/tasks/{task['task_id']}")
print(task["status"], task["points_before"], task["points_after"])
```

## 6. 错误码

失败时返回 `{"detail": "错误说明"}`，HTTP 状态码：

| 状态码 | 含义 | 处理 |
| --- | --- | --- |
| 400 | 参数错误，例如比例不在白名单、参考图格式不对 | 按 `detail` 修正后重试 |
| 401 | 授权码缺失或无效 | 让用户到网站 Skills 页重新复制完整授权码 |
| 402 | 积分不足 | 提示用户到「获取积分」充值；不要反复重试 |
| 403 | 账号被停用 | 提示联系客服 |
| 404 | 模型或任务不存在（任务只能查自己的） | 检查 `model` / `task_id` |
| 429 | 提交太频繁（30 次/分钟/IP） | 等一会儿再试 |
| 502 / 504 | 渠道或网络问题 | 稍后重试；任务超时会自动退款 |

## 7. 限制与注意事项

- 参考图只收 data URL（PNG / JPEG / WebP），最多 5 张、单张 ≤ 8 MB、合计 ≤ 24 MB；**不接受远程 URL**（防止被当作代理访问内网）。
- 比例与分辨率必须来自该模型返回的白名单。
- 任务记录保留 7 天；任务成功后应立刻把 `images[].url` 下载到当前项目的 `image/` 目录（不存在就新建），再向用户汇报本地路径。图片直链带签名，可长期访问。
- 授权码按用户唯一。不要把 A 用户的授权码给 B 用户使用。
- 生成内容需遵守网站与模型服务的内容规范；涉及真实人物、品牌时使用授权素材或改为虚构设定。
