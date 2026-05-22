---
name: wechat-mp-article-workflow
description: "微信公众号「迈不惑」文章完整自动化工作流：从选题到草稿箱提交。小牛作为统筹者调用各子agent执行，用户确认后再推进下一步。触发词：写公众号文章、公众号自动化、发布迈不惑文章。"
---

# 微信公众号「迈不惑」完整工作流

## 流程总览

```
用户指定主题
  ↓
xiaoyan 调研（公众号低粉爆款主题方向，不搜抖音）
  ↓
小牛 整理调研结果 → 用户确认选题方向
  ↓
xiaozuo 按迈不惑提示词写初稿
  ↓
小牛 humanizer 去AI味 → 加迈不惑人设细节
  ↓
★ 飞书发送预览给用户（HTML富文本格式）★  ← 必须等待用户确认
  ↓
用户 微调确认"可以"
  ↓
小牛 生成/上传配图（封面+文中图，全部用add_material）
  ↓
小牛 组装HTML → execute_code提交草稿箱
  ↓
小牛 验证media_id返回 → 告知用户完成
```

## 角色分工

| 步骤 | 执行者 | 说明 |
|------|--------|------|
| 选题调研 | xiaoyan | 公众号平台，不搜抖音 |
| 写初稿 | xiaozuo | 按迈不惑写作提示词 |
| 去AI味 | 小牛 | humanizer双重打磨 |
| 飞书预览 | 小牛 | 必须发飞书等用户确认 |
| 配图+提交 | 小牛 | 固定工具流程 |

## 第一步：选题调研（xiaoyan执行）

**⚠️ 平台边界：公众号 ≠ 抖音。调研必须在微信/公众号生态内进行，禁止使用任何抖音数据工具。平台逻辑完全不同，混用会导致选题方向错误。**

**输入：** 用户给的模糊方向（如"中年危机""亲子关系"等）

**调研任务派单格式：**
```
@xiaoyan
调研任务：女性向公众号内容角度调研

背景：
迈不惑公众号定位调整——目标读者确认为35-45岁女性（爱看爱转发），男性视角为辅助代入感。
内容主轴：职场男性如何过得好、家庭里如何过得稳。

调研要求（全部在微信/公众号生态内，禁止用抖音）：
1. 用 mmx search 搜索以下关键词，收集公众号爆款文章案例：
   - "中年婚姻  男性  反思  公众号爆款"
   - "职场男性  育儿  家庭  公众号低粉爆款"
   - "中年男人  婚姻  戳心  公众号10万+"
2. 找3-5个真实低粉爆款案例，分析：标题切入角度、开头钩子写法、女性共鸣点、用词风格
3. 提炼：女性读者喜欢什么样的"中年男人叙事"？她们更愿意转发哪类内容？

输出文件：/tmp/wechat_female_reader_research.md
要求：先读完所有搜索结果再写文件，不要边搜边写中途提交
```

**输出：** `/tmp/wechat_female_reader_research.md`（小牛读取后提炼要点给用户）

---

## 第二步：用户确认选题方向

小牛把调研结果整理成简短选项发给用户，选哪个由用户拍板。

---

## 第三步：写初稿（xiaozuo执行）

**关键：派单时传文件路径，不传摘要**

小牛先确认最新提示词路径（知识库里的`迈不惑写作风格提示词_v*.md`），然后派单：

```
@xiaozuo
写稿任务：公众号「迈不惑」风格文章

文章主题：[用户确认的主题]
参考调研：/tmp/wechat_topic_research.md（如果存在）

写作要求：
1. 100%按「迈不惑写作风格提示词_v*.md」的要求写（路径在知识库找最新的）
2. 约1000字，不要低于800字、不要超过1200字
3. 必须有人设细节：口误/自我修正、思路跳跃、具体人名（老王阿强老陈）、多细节不抽象
4. 固定尾标：▼-END- + 关注语
5. 禁用双引号用「」，数字用阿拉伯数字
6. 去AI化：禁用整齐排比三点、禁用"研究表明"、禁用破折号滥用、禁用答应式结尾

输出文件：/tmp/wechat_article.md（纯文字，非HTML）
```

---

## 第四步：飞书预览（必须步骤，不可跳过）

小牛 humanizer 打磨后，用**飞书**把文章按公众号样式发给用户看：

**发送格式要求：**
- 用飞书富文本消息发送，不要发纯文字
- 标题加粗居中
- 正文分段清晰
- 末尾加「关注迈不惑，唠育儿、聊职场、谈生活……」的引导语

**★ 必须等待用户确认 — 用户说"可以"才进入下一步 ★**

**禁止行为：**
- ❌ 不等用户确认就自己进入配图环节
- ❌ 用户说"改一下"后不重新发预览就当"可以"了
- ❌ 用户还没回复就继续推进流程

---

## 第五步：用户微调确认

用户提出修改 → 小牛转给 xiaozuo 改 → 改完再发预览 → 直到用户说"可以"

---

## 第六步：配图生成

**⚠️ 禁止使用AI生图，必须用真实照片（Unsplash）**
**配图原则：不一定都配人物；如果要配人物，必须是中国人物面孔（加"asian+people"关键词）。**
**图片必须横向（水平），禁用竖图；4:3比例优先但不强求。**
**用orientation=landscape搜索，下载后用 `file` 命令验证实际尺寸。**
配图规格：
- 封面图：2.35:1（900×383px），共1张
  - ⚠️ **封面图只用于提交草稿，不出现在正文中**
- 文中图：横向（水平）图片，禁止竖图，共3张
  - 横图格式：横向即可，不强求4:3（比例参考如1080×720、1080×600、1080×560）
  - 如果找到的图片是竖图，**必须换图**，不能直接使用
- 图片风格：真实感、生活化，不要过度美颜/设计感

**搜索工具：** `bash ~/.hermes/profiles/xiaoniu/skills/wechat-mp-draft/scripts/unsplash_search.sh`

**⚠️ unsplash_search.sh 搜不到时，用 curl 直接调 Unsplash API：**
```bash
curl -s "https://api.unsplash.com/search/photos?query=中文关键词&orientation=landscape&per_page=5" \
  -H "Authorization: Client-ID MXTLDvubbHLmBol0mfph6s2j_C1PtxzCYtOmcSzk9mc" | \
python3 -c "
import sys,json
d=json.load(sys.stdin)
for r in d.get('results',[]):
    print(r['urls']['regular'], r['width'], r['height'])
"
```

**命令示例（封面1张 + 文中图3张）：**
```bash
# 封面（横图2.35:1）
bash unsplash_search.sh "男人背影夜色城市街道" /tmp/fengmian_001.jpg landscape

# 图1-3（横图，横向即可）
bash unsplash_search.sh "中年男人深夜思考" /tmp/neihan1_001.jpg landscape
bash unsplash_search.sh "中年女性疲惫照顾家人" /tmp/neihan2_001.jpg landscape
bash unsplash_search.sh "父子相处温情瞬间" /tmp/neihan3_001.jpg landscape
```

---

## 第七步：上传图片获取media_id

**⚠️ 必须用 permanent 素材接口 `material/add_material`，不能用 `media/upload`**

关键区别：
- `media/upload?type=image`（临时素材）：只返回 `media_id`，**不返回 URL**
- `material/add_material?type=image`（永久素材）：返回 `media_id` **+ `url`**，图片URL用于组装HTML
- `material/add_material?type=thumb`（永久素材）：封面用这个，只返回 `media_id`，不返回 url

**⚠️ execute_code 沙盒无法读取 config.sh 的环境变量**，必须用 shell 提取后再传给 Python：
```bash
source ~/.hermes/profiles/xiaoniu/skills/wechat-mp-draft/scripts/config.sh
TOKEN=$(curl -s "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=$APPID&secret=$APPSECRET" | python3 -c "import sys,json; print(json.load(sys.stdin)['access_token'])")

# 上传封面（type=thumb，只返回media_id，不返回url）
UPLOAD_RESP=$(curl -s -F "media=@/tmp/fengmian_001.jpg" "https://api.weixin.qq.com/cgi-bin/material/add_material?access_token=$TOKEN&type=thumb")
THUMB_ID=$(echo "$UPLOAD_RESP" | python3 -c "import sys,json; print(json.load(sys.stdin)['media_id'])")

# 上传文中图（type=image，返回media_id + url，用这个url组装HTML）
IMG1_RESP=$(curl -s -F "media=@/tmp/neihan1_001.jpg" "https://api.weixin.qq.com/cgi-bin/material/add_material?access_token=$TOKEN&type=image")
IMG1_URL=$(echo "$IMG1_RESP" | python3 -c "import sys,json; print(json.load(sys.stdin)['url'])")
IMG2_RESP=$(curl -s -F "media=@/tmp/neihan2_001.jpg" "https://api.weixin.qq.com/cgi-bin/material/add_material?access_token=$TOKEN&type=image")
IMG2_URL=$(echo "$IMG2_RESP" | python3 -c "import sys,json; print(json.load(sys.stdin)['url'])")
IMG3_RESP=$(curl -s -F "media=@/tmp/neihan3_001.jpg" "https://api.weixin.qq.com/cgi-bin/material/add_material?access_token=$TOKEN&type=image")
IMG3_URL=$(echo "$IMG3_RESP" | python3 -c "import sys,json; print(json.load(sys.stdin)['url'])")
```

**⚠️ 每次提交草稿前，必须重新获取token+重新上传封面（thumb_media_id不可复用）**

---

## 第八步：组装HTML并提交草稿

**HTML排版固定格式（不变）：**
```html
<p style="text-indent:2em;font-size:16px;line-height:1.75;color:#333;">正文内容...</p>
<p style="text-align:center;"><img src="mmbiz_url" width="85%" style="margin:0 auto;display:block;" /></p>
<p style="text-align:center;color:#999;font-size:14px;margin-top:30px;">▼-END-</p>
<p style="text-align:center;color:#999;font-size:14px;">关注迈不惑，唠育儿、聊职场、谈生活，说说中年人的实在话、心里话</p>
```

**提交用Python（execute_code），不用shell拼接JSON：**
```python
import json, urllib.request, subprocess

# 获取token
result = subprocess.run(['bash', '-c', 'source ~/.hermes/profiles/xiaoniu/skills/wechat-mp-draft/scripts/config.sh && curl -s "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=$APPID&secret=$APPSECRET"'], capture_output=True, text=True)
token = json.loads(result.stdout)['access_token']

payload = {
    "articles": [{
        "title": "文章标题",
        "author": "迈不惑",
        "digest": "摘要（不超过128字）",
        "content": open('/tmp/wechat_article.html').read(),
        "thumb_media_id": "封面图media_id",
        "need_open_comment": 1,
        "only_fans_can_comment": 0
    }]
}
req = urllib.request.Request(
    f"https://api.weixin.qq.com/cgi-bin/draft/add?access_token={token}",
    data=json.dumps(payload, ensure_ascii=False).encode('utf-8'),
    headers={'Content-Type': 'application/json'}
)
with urllib.request.urlopen(req) as resp:
    print(json.loads(resp.read().decode('utf-8')))
```

---

## 变量部分（每次需确认）

| 变量 | 确认时机 | 说明 |
|------|---------|------|
| 文章主题 | 调研后 | 用户拍板 |
| 迈不惑提示词版本 | 写稿前 | 读知识库最新版本 |
| WSL出口IP | 每次API前 | 查当前IP，若报40164立即告知用户加白 |
| AppSecret | 凭证变更时 | 若重置，立即更新config.sh |
| 配图Prompt | 生成前 | 根据文章主题调整 |

## 固定部分（不变）

- 排版：16px / #333 / 行距1.75 / 首行不缩进
- 封面尺寸：2.35:1（900×383px），1张
- 文中图：横向，3张，宽度85%
- API顺序：获取token → 上传封面(type=thumb) → 上传3张文中图(type=image，用add_material) → 提交草稿
- 提交方式：Python execute_code，不走shell curl
- 错误处理：40007重新上传 / 40164查IP加白 / token过期重新获取

## 女性向内容参考

调研文件：`references/wechat_female_reader_research_20260521.md`

**核心结论：**
- 目标读者：35-45岁女性（爱看爱转发），男性视角是"药引子"
- 女性爱看：反思型+责任型+成长型男性叙事，戳痛点但有希望
- 女性不爱看：卖惨甩锅型、说教型、油腻型
- 叙事核心：帮女性读者表达和宣泄情绪

**⚠️ 每次提交草稿前必须确认IP未变化**——出口IP每次会话可能不同，换IP后报40164需重新加白。

## 格式调试信号与正确格式溯源

调研文件：`references/wechat_female_reader_research_20260521.md`

当用户说"字体颜色不对"等格式投诉时，**不要猜测**，按以下顺序溯源：

1. `ls -lt /tmp/wechat_article*.html | head -5` 找最近提交的本地HTML文件
2. 参考已验证正确的历史文件：`wechat_article_bear_v2.html`（2026-05-20提交，确认格式正确）
3. 已验证正确格式：
   - 正文颜色：`#333`（不是 `#4f4f4f`）
   - 行距：`1.75`（不是 `1.8`）
   - 图片宽度：`width="85%"`，外层 `<p style="text-align:center;">`
   - 结尾：`color:#999;font-size:14px;margin-top:30px;`
4. 确认正确后，立即 patch 本skill的「HTML排版固定格式」章节，防止下次复用错误格式

## 常见错误

| 错误 | 原因 | 解决 |
|------|------|------|
| 40006 | 图片太大/格式不对 | 检查文件大小和格式 |
| 40007 | thumb_media_id无效或过期 | 每次提交前重新上传封面 |
| 40164 | IP不在白名单 | 先查当前出口IP（curl ifconfig.me），告知用户加白后再试 |
| 44002 | POST数据为空 | 检查JSON序列化 |
| 47001 | JSON格式错误 | 用Python json.dumps处理 |
| media_id复用无效 | 微信限制 | 封面media_id不可跨次复用 |
| 上传图片只返media_id无URL | 用了`media/upload`接口 | 换用`material/add_material`接口，type=image才返回url |

**⚠️ 每次提交草稿前必须确认IP未变化**——出口IP每次会话可能不同，换IP后报40164需重新加白。
