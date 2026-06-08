# Mira Weekly Data Report — Standard Operating Procedure

> 此 SOP 是 Mira 周数据报告的标准格式与方法论。**每次生成新周报必须严格按此结构产出**。模板基线：`W23_data_report_20260608.md`。

## 1. 时间窗口

- **本周** = 周一 00:00 UTC ~ 周日 23:59 UTC（7 个完整日历日）
- **上周** = 上周一 ~ 上周日（作为环比基线）
- **周编号** = ISO Week Number（W18 = 4 月 27 - 5 月 3 那周）
- **生成时间** = 每周日 20:00 北京时间（= 周日 12:00 UTC）
- **报告文件名** = `W{N}_data_report_{YYYYMMDD}.md`，其中 YYYYMMDD = 生成日（即周日）

## 2. 数据口径（不可更改）

- **项目**：PostHog project 332580
- **内部用户排除**：cohort 223578（当前 39 人；运行时实时查 cohort 当前大小）
- **排除方式**：`person_id NOT IN (SELECT person_id FROM static_cohort_people WHERE cohort_id = 223578) AND person_id NOT IN (SELECT person_id FROM raw_cohort_people WHERE cohort_id = 223578)`
- **DAU / WAU / MAU**：用 `task_created` 事件按 `person_id` 去重（不是任意事件 DAU）
- **新用户**：person 的首次 `task_created` 落在该周
- **留存**：calendar D7（含周末）— 与 PRR business day D7 不同口径，本报系列保持 calendar D7 一致

## 3. 必含章节（按顺序）

每份周报**必须**包含以下章节，顺序固定。缺章节 = 报告不合格。

### 3.1 头部元信息

```
# Mira 数据周报 | W{N}（{周一日期} ~ {周日日期}）

**口径**：所有指标已排除 Internal Users cohort 223578 共 {N} 人 · 活跃 = task_created 按 person_id 去重 · 留存口径 = calendar D7
**对比基线**：W{N-1}（{上周一日期} ~ {上周日日期}，同 cohort 排除）
**数据快照**：{生成时刻 UTC}
```

### 3.2 本周一句话

一句话总结核心矛盾。必须同时点出**正面信号**与**警示信号**，不允许只报喜或只报忧。

### 3.3 ⭐ 北极星：人均周任务数

公式：`周 task_created / 周末 WAU`。给出环比变化。

### 3.4 📊 核心指标对照表

W{N} vs W{N-1} 表格，含：
- WAU（周末值）
- 工作日 DAU 均
- 工作日峰 DAU
- 周 task_created
- **人均周任务（北极星）**
- 新增用户
- sign_up 事件
- **上周 cohort D7 留存**（cal 口径，回看上周 cohort 完整 D7）
- WoW 留存（本周 vs 上周）
- Abort 率
- Rageclick 事件 + 用户
- **phone_enrich 用户**（人选质量信号）
- phone_enrich 事件
- **人均 phone_enrich**（在 enrich 用户中）
- phone_copy 用户（次要参考）
- people_data_downloaded 用户 + 事件
- share_link_created / copied 用户
- candidate_email_copied 用户

### 3.5 📈 W{N} 逐日 DAU

按日列出 Mon-Sun DAU + 任务数。计算工作日均粘性（DAU/WAU）、工作日 DAU/MAU。

### 3.6 🔄 周-周流失结构

3 个用户群：
- 留存（W{N-1} + W{N} 都活跃）
- 流失（W{N-1} 活跃但 W{N} 未回）— 这是召回池
- 新/复活（W{N-1} 未动但 W{N} 活跃）—— 拆"纯新用户" vs "复活老用户"

### 3.7 📉 留存：上周 cohort 完整 D7 回看

- 上周 cohort（{N-1}）各日入坑用户数 + D1-D7 内回访数 + D7 cal cum
- **留存基线趋势表**：列出近 4-5 周的 calendar D7（W19/W20/W21/W22/W{N-1}/...）

### 3.8 👥 新用户行为聚类

W{N} cohort 因观察期不足，注明"待 7 day 后回看"；W{N-1} cohort 已满 D7 可分桶：
- 01 一次性试用（1天1任务）
- 02 一日多试（1天≥2）
- 03 浅留存（2-4天）
- 04 深留存（5+天）

### 3.9 🎚️ Multi-Layer Quality Framework（PRR #001 沿用 — 必含）

**这是周报的核心质量章节，不可省略。**

#### Layer 1 — Sourcing 占比
- 公式：`unique task_id with people_search_rendered ÷ task_created`
- 表格：Total tasks / Sourcing tasks / Layer 1 占比 / W{N} vs W{N-1}

#### Layer 2 — Task Value Transfer Rate
- 公式：`sourcing tasks with downstream action ÷ sourcing tasks`
- 下游事件清单：`candidate_card_platform_clicked`, `people_data_downloaded`, `candidate_phone_enrich_clicked`, `candidate_email_copied`, `candidate_phone_copied`, `share_link_created`
- 表格：Sourcing tasks / 其中有下游动作 / Layer 2 占比 / W{N} vs W{N-1}

#### 用户级下游动作（占 WAU 比 — 必须看相对值）
- 表格列：动作 / W{N} 用户 / W{N} 占 WAU / W{N-1} 用户 / W{N-1} 占 WAU / 变化
- 必含动作：candidate_card_platform_clicked / people_data_downloaded / candidate_phone_enrich_clicked / share_link_created / candidate_email_copied / candidate_phone_copied
- 必须按"占 WAU 比"排，不只看绝对数

#### Layer 1/2 视角下的本周一句话改写
- 用 Layer 框架重新表达本周状态，对照表面北极星

### 3.10 🎯 候选人质量信号 & 价值兑现路径

- 信号 1：phone_enrich（人选质量信号）— 用户、事件、人均
- 信号 2：价值兑现路径汇总（多路径，含直接拨号无埋点的提示）
- 用户级 cohort 分析提醒（PRR P0 项）

### 3.11 🧪 任务质量

- task_created / task_stream_completed / task_aborted / task_stream_failed / abort rate / stream/task 比 / rageclick 事件 + 用户 + 人均

### 3.12 💡 AI 洞察

3-8 条 bullet，覆盖：
- 正面信号
- 警示信号
- **Multi-Layer Quality 视角的洞察**（不能漏）
- 跟流失调研 / PRR 框架的串联（如适用）

### 3.13 ⚠️ 数据质量备注

每周必须列出当前已知的数据口径与噪音：
- cohort 状态（人数、本周是否变更）
- 上周 cohort 是否已完整观察
- 本周 D7 何时可观察
- sign_up 漏埋率
- phone_copy 不作主指标的说明
- MAU 估算口径

### 3.14 📎 跟踪项 + 跨周持续指标盯盘

- 跟踪项：本周发现的 P0/P1 行动项
- **跨周持续指标盯盘表**：固定盯 Layer 1 / Layer 2 / platform_click 占 WAU / phone_enrich 占 WAU 的 W-1 vs W 数据，并标明目标水位

## 4. 关键 SQL 模板

### 4.1 内部排除子句

```sql
person_id NOT IN (SELECT person_id FROM static_cohort_people WHERE cohort_id = 223578)
AND person_id NOT IN (SELECT person_id FROM raw_cohort_people WHERE cohort_id = 223578)
```

### 4.2 Daily DAU + tasks

```sql
SELECT toDate(timestamp) AS day, count() AS tasks, count(DISTINCT person_id) AS dau
FROM events
WHERE event='task_created'
  AND timestamp >= '{week_start}' AND timestamp < '{week_end + 1}'
  AND {内部排除子句}
GROUP BY day ORDER BY day
```

### 4.3 Layer 1 sourcing tasks

```sql
SELECT count(DISTINCT properties.task_id) AS sourcing_tasks,
       count(DISTINCT person_id) AS sourcing_users
FROM events
WHERE event='people_search_rendered'
  AND timestamp >= '{week_start}' AND timestamp < '{week_end + 1}'
  AND {内部排除子句}
```

### 4.4 Layer 2 transfer rate

```sql
WITH sourcing_tasks AS (
  SELECT DISTINCT properties.task_id AS task_id
  FROM events WHERE event='people_search_rendered'
    AND timestamp >= '{week_start}' AND timestamp < '{week_end + 1}'
    AND {内部排除子句}
),
downstream_tasks AS (
  SELECT DISTINCT properties.task_id AS task_id
  FROM events
  WHERE event IN ('candidate_card_platform_clicked','people_data_downloaded',
                  'candidate_phone_enrich_clicked','candidate_email_copied',
                  'candidate_phone_copied','share_link_created')
    AND timestamp >= '{week_start}' AND timestamp < '{week_end + 14 days}'
    AND {内部排除子句}
)
SELECT (SELECT count() FROM sourcing_tasks) AS sourcing,
       (SELECT count(DISTINCT s.task_id) FROM sourcing_tasks s
        JOIN downstream_tasks d ON s.task_id = d.task_id) AS with_downstream
```

### 4.5 W-1 cohort D7 回看

```sql
SELECT first_day, count() AS cohort, sum(d1to7) AS retained,
       round(sum(d1to7)/count()*100, 1) AS d7_pct
FROM (
  SELECT nu.person_id AS pid, nu.first_day,
         max(if(e.d > nu.first_day AND e.d <= nu.first_day + 7, 1, 0)) AS d1to7
  FROM (
    SELECT person_id, min(toDate(timestamp)) AS first_day
    FROM events WHERE event='task_created' AND {内部排除子句}
    GROUP BY person_id
    HAVING first_day >= '{w-1 周一}' AND first_day < '{w-1 周日 + 1}'
  ) nu
  LEFT JOIN (
    SELECT DISTINCT person_id, toDate(timestamp) AS d
    FROM events WHERE event='task_created'
      AND timestamp >= '{w-1 周一}' AND timestamp < '{w 周日 + 1}'
      AND {内部排除子句}
  ) e ON e.person_id = nu.person_id
  GROUP BY pid, first_day
)
GROUP BY first_day ORDER BY first_day
```

## 5. 写作规范

- **不要 AI 味**：避免"令人欣慰的是"、"值得注意的是"、"综上所述"、过度排比、过度的连接词
- **关键数字 bold**：用 **粗体**强调本周最关键的 3-5 个数字
- **环比箭头与表情**：📈 / 📉 / 🚨（重大警示）/ ✅（正面）/ ⚠️（注意）/ 🟡（中性）
- **括号交叉对照**：写 "W{N} = X（W{N-1} = Y，±Z%）" 风格
- **不堆术语**：每个新缩写第一次出现要简短解释（PRR / WAU / Layer 1 等）
- **每段结论一行就够**：避免段落式论述，多用 → 箭头列要点

## 6. 产出与上链

报告写好后：
1. 保存到 `~/mira-work/weekly-reports/data-reports/W{N}_data_report_{YYYYMMDD}.md`
2. `git add` + commit + push
3. **推送钉钉群**（详见 §6.1）

### 6.1 钉钉群同步（每周必做）

每份周报产出后立即推送一份**简版**到钉钉群，全文交给点链接的人去 GitHub 看。

**钉钉简版必含 5 段**（按顺序）：

1. **标题** — `Mira 数据周报 W{N}（YYYY-MM-DD ~ YYYY-MM-DD）`
2. **核心数字**（3-5 条 bullet）— WAU / 工作日 DAU 均 / 周 task / 北极星 / DAU/MAU 粘性
3. **正面信号**（2-3 条 ✅）
4. **警示信号**（1-3 条 🚨/⚠️）— 含 Layer 1/2 状态
5. **下周必做**（2-4 条 P0/P1）

**风格**：DingTalk markdown 兼容；不超过 2,000 字；不要 AI 味。

**推送方式（带加签）**：

钉钉机器人开启了"加签"安全模式 — 每次发送必须附带 `timestamp` + `sign`（用 SEC 密钥 HMAC-SHA256 计算）。

```bash
WEBHOOK='https://oapi.dingtalk.com/robot/send?access_token=467e46ac50ff929739fa23e889e03bbd57f51f85466298eba2816498c5042a31'
SECRET='SECdc954e416fef3d7bfa45eee8ec17c612327b39a5a65711612633524262914c01'

timestamp=$(($(date +%s) * 1000))
sign=$(printf "%s\n%s" "$timestamp" "$SECRET" | openssl dgst -sha256 -hmac "$SECRET" -binary | base64)
encoded_sign=$(python3 -c "import urllib.parse,sys; print(urllib.parse.quote(sys.argv[1]))" "$sign")
URL="${WEBHOOK}&timestamp=${timestamp}&sign=${encoded_sign}"

# 把消息写到临时文件再用 curl -d @file，避开 shell 转义
cat > /tmp/dingtalk_msg.json <<'EOF'
{
  "msgtype": "markdown",
  "markdown": {
    "title": "Mira 数据周报 W{N}",
    "text": "## Mira 数据周报 W{N}（YYYY-MM-DD ~ YYYY-MM-DD）\n\n**核心数字**\n- ...\n\n**正面信号**\n- ✅ ...\n\n**警示信号**\n- 🚨 ...\n\n**下周必做**\n1. ...\n\n完整报告：https://github.com/selinayan-YYJ/mira-weekly-reports/blob/main/W{N}_data_report_{YYYYMMDD}.md"
  }
}
EOF

curl -s "$URL" -H 'Content-Type: application/json' -d @/tmp/dingtalk_msg.json
```

**校验**：
- 返回 `{"errcode":0,"errmsg":"ok"}` = 成功
- 返回 `{"errcode":310000,"errmsg":"...签名不匹配..."}` = SEC 密钥错或签名算法不对
- 返回其它错误 → 在报告顶部加 ⚠️ 钉钉推送失败 备注，但仍 push 报告

**安全**：webhook URL + SEC 密钥都在本文件 + routine prompt 中。如泄露：钉钉群 → 智能群助手 → 机器人 → 撤销 / 改密钥 → 同步更新本文件 + routine。

## 7. 自检清单（每次报告产出前）

- [ ] 头部 cohort 排除人数与当前 cohort version 一致
- [ ] 所有数字均已排除 cohort 223578
- [ ] 北极星指标有环比
- [ ] Multi-Layer Quality Framework 章节完整（L1 + L2 + 用户级占 WAU 比）
- [ ] 上周 cohort D7 已完整回看
- [ ] 留存基线趋势表覆盖至少 4 周
- [ ] AI 洞察包含 L1/L2 视角
- [ ] 数据质量备注覆盖所有已知噪音
- [ ] 跨周盯盘表保留 W-1 vs W 对照
