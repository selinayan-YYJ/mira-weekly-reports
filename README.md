# Mira Weekly Data Reports

每周一上午 9:30（北京时间）自动生成的 Mira 数据周报。

## 自动化机制

- **触发**：Claude Code scheduled remote agent，cron `30 1 * * 1`（UTC）
- **数据源**：PostHog project 332580
- **Cohort**：全用户排除 Internal Users cohort (id 223578)
- **活跃定义**：`task_created` 事件去重
- **留存口径**：business day 主（跳过周末），calendar day 辅

## 报告命名

`W{week_num}_data_report_{YYYYMMDD}.md`，其中 YYYYMMDD 是生成日期（一般是周一）。

## 同步到本地

```bash
cd ~/mira-work/weekly-reports/data-reports
git pull
```
