# API Image Backend

本技能可以复用用户已安装的 `api-image-main` 或 `api-image` 技能完成实际图片生成。仓库只保存调用约定，不保存 Provider 地址、API key、头像或生成图片。

## 发现脚本

按顺序查找：

```text
${CODEX_HOME:-$HOME}/skills/api-image-main/scripts/generate_image.py
${CODEX_HOME:-$HOME}/skills/api-image/scripts/generate_image.py
```

如果两个脚本都不存在，转到 `SKILL.md` 中的内置 `image_gen` 或显式本地 imagegen 分支。

## 认证

API Image 默认读取用户 Codex 根目录的 `auth.json` 和 `config.toml`。也可以用一次性环境变量覆盖：

```bash
export API_IMAGE_KEY="..."
python "$API_IMAGE" edit \
  --base-url "https://provider.example/v1" \
  --api-key-env API_IMAGE_KEY \
  ...
```

不要把 key 写进这个技能、提示词、manifest、日志或 GitHub。`401`、`missing_carpool_key`、缺少 `auth.json` 或超时都必须原样归类为后端鉴权/连接失败。

## 封面调用约定

- 纯文字新图可用 `generate`；带头像、参考图或 mask 时用 `edit`。
- 第一张输入图是头像身份参考，使用 `--image-role "identity reference portrait of the creator"`。
- 3:4 使用 `1024x1536`；4:3 使用 `1536x1024`。
- 默认 `gpt-image-2`、`quality high`、PNG。
- 每个画幅单独调用一次，输出固定为 `cover-3x4.png` 和 `cover-4x3.png`。
- 生成后用图像查看工具检查：标题/副标题是否完整、人物是否可辨认、手指姿势是否正确、主题元素是否存在、比例是否正确。

## Manifest

在输出目录写入 `cover-manifest.json`，至少包含：

```json
{
  "status": "complete",
  "backend": "api-image-main provider script",
  "input_identity_reference": "portrait.png",
  "titles": {"main": "...", "subtitle": "..."},
  "variants": [
    {"aspect_ratio": "3:4", "output": "cover-3x4.png"},
    {"aspect_ratio": "4:3", "output": "cover-4x3.png"}
  ]
}
```

失败时使用 `status: "blocked_backend"`，记录非敏感错误类别，不记录 token。
