# GitHub 自动化+变现工具速查（2026-05-21）

来源：GitHub API 搜索，xiaoyan调研，166行完整报告在 `/tmp/github_skills_research.md`

---

## P0 必须关注

### MoneyPrinterTurbo
- **链接**：https://github.com/harry0703/MoneyPrinterTurbo
- **Stars**：57k
- **功能**：AI大模型一键生成高清短视频（抖音/视频号）
- **对我价值**：抖音书单号内容批量生产，可替代当前HyperFrames手动制作流程
- **注意**：需高配GPU，现有阿里云服务器是否支持待测

### ChatGPT-On-CS
- **链接**：https://github.com/cs-lazy-tools/ChatGPT-On-CS
- **Stars**：4k
- **功能**：多平台AI客服机器人（微信/抖音/小红书/知乎），支持知识库定制
- **对我价值**：公众号自动回复、微信客服自动化
- **注意**：需自建或对接现有微信账号

### Playwright
- **链接**：https://github.com/microsoft/playwright
- **Stars**：89k
- **功能**：浏览器自动化框架，支持Chromium/Firefox/WebKit
- **对我价值**：H5页面自动化测试、预约H5数据抓取、网页数据监控
- **注意**：微软官方维护，Node.js/Python均可调用

---

## P1 高价值

| 工具 | Stars | 功能 | 对我价值 |
|------|-------|------|---------|
| n8n | 189k | 工作流自动化平台，400+集成 | 跨工具链整合（微信/H5/抖音/钉钉） |
| Dify | 107k | 可部署的Agentic Workflow平台 | 快速构建AI应用嵌入现有业务 |
| VidiBot | - | 多平台视频自动上传 | 抖音/视频号批量定时发布 |
| ShortGPT | 13k | YouTube Shorts/TikTok自动化框架 | 短视频自动化编辑+字幕+配音 |

---

## 技术笔记

- **视频批量发布**：VidiBot > MoneyPrinterTurbo（前者专注发布，后者专注生成）
- **浏览器自动化**：Playwright > Puppeteer（微软维护，跨浏览器支持更好）
- **变现聚合**：`awesome-ai-money-machine` (https://github.com/HackMyTask/awesome-ai-money-machine) 汇总AI变现思路+ n8n工作流模板
