# TrendRadar（个人定制版）

本仓库基于开源项目 **TrendRadar** 进行个人用途的配置与定制：  
只做“定时抓取热点 → 生成网页 → GitHub Pages 阅读”，不做任何消息推送。

- 上游项目（原作者）：https://github.com/sansan0/TrendRadar  
- 许可证：GPL-3.0（本仓库遵循上游许可证）

---

## 访问地址

- 发布页（带深/浅色切换）：https://znwvip.github.io/TrendRadar/

> 说明：访问网页不会触发抓取，网页只是静态展示。

---

## 本仓库做了哪些改动

### 1）只网页阅读，不推送
- 关闭通知能力（不向 Telegram/企业微信/飞书/钉钉/邮件等发送消息）
- 仅生成 HTML 报告并发布到 GitHub Pages

### 2）Pages 发布结构（/docs）
GitHub Pages 配置为从 `master` 分支的 `/docs` 目录发布。

关键文件：
- `docs/index.html`：发布页外壳（固定页面，默认深色，提供深/浅色切换与“刷新报告”按钮）
- `docs/report.html`：最新报告页（由 GitHub Actions 每次运行后生成/覆盖）

### 3）报告内容也支持深/浅色切换
每次生成 `docs/report.html` 后，workflow 会自动向报告 HTML 注入 `theme-bridge`（CSS + JS）：
- 读取 `localStorage.theme`
- 设置 `data-theme=dark/light`
- 覆盖报告背景/文字/卡片/链接等样式，使报告内容与外壳同步切换主题

### 4）定时抓取时间窗口（北京时间）
通过 GitHub Actions cron 控制在指定时间窗口内抓取更新。

---

## 如何工作

### 触发机制
抓取更新由 GitHub Actions 触发：
- 定时触发：`.github/workflows/crawler.yml` 中 `on.schedule.cron`
- 手动触发：Actions → Get Hot News → Run workflow

访问 Pages 网页不会触发抓取。

### 产物如何更新到网页
每次 workflow 成功运行后：
1. 运行 `python -m trendradar` 抓取并生成报告（输出到 `output/...`）
2. 复制最新报告为 `docs/report.html`
3. 注入 `theme-bridge`（深/浅色主题支持）
4. `git commit && git push` 把 `docs/report.html` 推回仓库
5. GitHub Pages 自动发布 `/docs`，网页展示最新报告

---

## 定时配置（北京时间 08:00–22:00，每小时一次）

GitHub Actions 的 cron 使用 UTC（北京时间 = UTC+8）。

示例：北京时间 **08:14–22:14** 每小时一次，对应 UTC **00:14–14:14**：

（把下面这一行放到 `crawler.yml` 的 `on.schedule` 中）

    - cron: "14 0-14 * * *"

---

## Check In（每 7 天一次）

上游项目包含“签到续期”机制：需要定期运行 `Check In` workflow 续期，否则定时任务可能暂停/被禁用。

路径：
- Actions → Check In → Run workflow

建议：每 7 天手动运行一次。

---

## 关键文件说明

- `.github/workflows/crawler.yml`  
  定时抓取、生成 `docs/report.html`、注入主题、提交推送。

- `docs/index.html`  
  发布页外壳（深/浅色切换、刷新按钮），iframe 加载 `docs/report.html`。

- `docs/report.html`  
  最新报告页（自动生成并覆盖），包含注入的 `theme-bridge`。

- `config/config.yaml`  
  TrendRadar 主配置（已按“只网页阅读”场景关闭通知）。

- `config/frequency_words.txt`  
  关键词筛选配置（决定最终报告里显示哪些主题）。

---

## 清理说明（可选）

仓库中可能存在上游自带的历史示例数据（例如 `output/2025年11月xx日/...`），用于演示/测试。  
如仅用于网页阅读，可自行删除以减小仓库体积，不影响本仓库的 Pages 展示。

---

## 致谢

感谢 TrendRadar 上游项目作者与贡献者：  
https://github.com/sansan0/TrendRadar
