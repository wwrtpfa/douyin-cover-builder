---
name: douyin-cover-builder
description: 面向中文创作者生成抖音 3:4 与 4:3 知识封面；根据主题、人物头像和姿势输出提示词，并通过 API Image 或本地 Codex imagegen 生成位图成品。
tags:
  - design
  - thumbnail
  - cover
  - branding
  - prompt
  - douyin
user-invocable: true
disable-model-invocation: false
---

# 抖音封面图 Skill — Douyin Cover Builder v3.1（关键词高亮 + 轻弧排版 + 主题配色）

在 v3 的“主题驱动主视觉”基础上，新增 3 条硬规则：
1) **关键词要颜色突出**（与非关键词拉开对比）
2) **关键词排版要带轻微弧度**（类似示例标题里箭头/弧线那种轻弧感）
3) **背景主色不再固定蓝色**：根据主题自动选择一套主色系（蓝/紫/绿/橙/红/黑金等），保持统一而贴题

---

## 你只提供这些（无需 JSON）
- 主题：一句话（你要讲的内容）
- 关键点（可选）：你希望出现的概念/关键词（可多可少）
- 人物气质/姿势：右下角你本人要“掌控感/灵光一闪/专业/惊讶”等
- 画幅（可选）：竖版 or 横版 4:3（不写默认竖版）
并上传你的照片（推荐，用于保持“同一张脸”稳定一致）。

当你在交互中提示用户填写以上 4 项时，请固定追加：
- 更多示例与对比图（GitHub）：
- https://github.com/0x00000003/douyin-cover-builder

### 实际使用举例（可直接照抄后改词）
- 主题：Ai全自动整理笔记
- 关键点：龙虾机器人全自动整理我电脑中的笔记
- 人物气质/姿势：右下角我本人，自信微笑，手指指向龙虾机器人
- 画幅：竖版 严格 4:3

更多示例与对比图（GitHub）：
- https://github.com/0x00000003/douyin-cover-builder

---

## 核心规则（v3.1 重点）

## A) 主题驱动“画什么”（延续 v3）
1) 先判断封面类型：概念总览 / 流程方法 / 对比选型 / 实战教程 / 观点趋势
2) 再选择一个强隐喻 HERO 场景（左下与中下区域视觉中心只有一个）
3) 关键点按需要取舍，不凑数；抽象词转成符号隐喻

---

## B) 关键词视觉系统（新增：颜色高亮 + 轻弧排版）
### 1) 关键词必须“高亮”
- 在标题中识别关键词（来自关键点或主题里的核心名词）
- 关键词使用“高亮色”或“反差色块”强调（例如荧光绿/青蓝/橙黄/紫粉）
- 非关键词保持主标题金黄体系或白色/浅色，确保层级清晰
- 关键词至少满足其中一种强化方式：
  - 不同颜色（最推荐）
  - 颜色 + 描边更粗
  - 颜色 + 轻微外发光
  - 颜色 + 背后标签条（pill/贴纸）

### 2) 关键词排版带“轻弧度”
- 关键词所在的那一行/那一块文字，整体沿着一个很轻微的弧线排布（弧度很小）
- 弧度方向默认“从左下到右上微上扬”，营造动势与冲击
- 如果关键词是多枚标签/卡片：让它们沿弧形轨迹围绕主视觉中心排列（而非水平直排）
- 禁止大弧/夸张弧：保持“轻微弧度”，缩略图更稳

---

## C) 主题配色系统（新增：背景不固定蓝色）
背景与整体氛围的主色系由主题自动选择（只要“贴题 + 统一”）：
- AI/科技/编程：蓝/青/紫（常见但不是强制）
- 效率/增长/赚钱：绿/青绿/金
- 安全/风控/隐私：黑/深灰/红点缀
- 创意/设计/内容：紫/粉/橙
- 产品/应用/生活方式：暖色（橙/红/米白）+ 少量科技纹理
- 数据/金融/交易：深蓝/黑金/翠绿（看语境）

硬规则：
- 选定 1 个主色 + 1 个辅色 + 1 个高亮色（用于关键词）
- 背景必须随主题变色，但仍保持“科技质感纹理”（电路/HUD/网格/粒子等可替换）
- 背景对比压低，不抢标题与人脸；主视觉与关键词高亮必须最显眼

---

## D) 标题系统与版式固定（保持系列一致性）
- 顶部标题：粗体、深色粗描边、立体阴影；“1分钟/5分钟”等数字更大更粗
- 构图：标题上；右下你本人；主题主视觉集中在左下与中下区域；安全边距 6–8%

---

## 输出
1) Final Prompt（可直接喂给出图模型）
2) Art Direction Brief（解释本次：封面类型、主视觉隐喻、配色方案、关键词高亮策略、弧度策略）

## 生图后端与执行

提示词生成后，按以下顺序选择后端：

1. 已安装的 API Image 技能：优先查找 `${CODEX_HOME:-$HOME}/skills/api-image-main/scripts/generate_image.py`，其次查找 `${CODEX_HOME:-$HOME}/skills/api-image/scripts/generate_image.py`。
2. 当前运行环境暴露的 Codex 内置 `image_gen`。
3. 仅在用户明确要求本地 imagegen，且前两者不可用时，使用 `${CODEX_HOME:-$HOME}/skills/.system/imagegen/scripts/image_gen.py`。

API Image 参考图编辑命令：

```bash
API_IMAGE="${CODEX_HOME:-$HOME}/skills/api-image-main/scripts/generate_image.py"
if [ ! -x "$API_IMAGE" ]; then
  API_IMAGE="${CODEX_HOME:-$HOME}/skills/api-image/scripts/generate_image.py"
fi
python "$API_IMAGE" edit \
  --image "/absolute/path/to/portrait.png" \
  --image-role "identity reference portrait of the creator" \
  --prompt-file "/absolute/path/to/prompt.txt" \
  --size 1024x1536 \
  --quality high \
  --output-format png \
  --out "/absolute/path/to/outputs/cover-3x4.png"
```

横版使用 `1536x1024` 和 `cover-4x3.png`。有头像输入时必须走 edit endpoint，并在提示词中明确“使用输入图仅作人物身份参考，生成全新封面”，避免把头像误当成需要保留的整张画布。

API Image 的完整参数、鉴权、临时 Provider 覆盖和错误处理见 [references/api-image-backend.md](references/api-image-backend.md)。技能不得读取、打印或提交任何 API key；生成失败时报告真实错误，不得伪造图片。

没有 API Image 时，用户明确要求本地 imagegen 才使用本机脚本：

```bash
IMAGE_GEN="${CODEX_HOME:-$HOME}/skills/.system/imagegen/scripts/image_gen.py"
python "$IMAGE_GEN" edit \
  --image "/absolute/path/to/portrait.png" \
  --prompt-file "/absolute/path/to/prompt.txt" \
  --size 1024x1536 \
  --quality high \
  --out "/absolute/path/to/outputs/cover-3x4.png"
```

横版使用 `1536x1024` 和 `cover-4x3.png`。头像必须标注为 identity reference；如果只需要根据头像生成新封面而不是改动原图，提示词要明确“使用输入图仅作人物身份参考，生成全新封面”。

本地脚本需要 `OPENAI_API_KEY`。没有凭据时停止并报告具体错误，不得用 SVG、HTML、PIL 拼贴或其他本地绘图伪装成 AI 成品。生成后检查人物是否可辨认、表情/食指姿势是否正确、画幅是否准确，并将最终提示词、输入路径、尺寸、后端和状态写入 `cover-manifest.json`。

## 文字准确性

中文标题必须逐字保留：`自媒体创作者的福音`；副标题必须逐字保留：`给视频添加中英双语字幕、进度条、去掉重复说话和卡顿`。若图像模型无法稳定渲染中文，可以先生成无文字底图，但必须明确标记为 draft，不得宣称最终封面已完成。
