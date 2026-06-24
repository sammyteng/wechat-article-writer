# HTML+Playwright 封面图生成工作流

> 当 `imagegen` CLI 因 API key 不可用而失败时，此方案是可靠的备选路径。本 session 已验证可用。

## 流程

### 1. 写 HTML 封面

- 尺寸：**900×383 px**（微信公众号标准封面比例 2.35:1）
- 放在 `<div class="cover" id="cover">` 内
- 必须包含：标题文字、副标题/导语、视觉装饰元素、品牌标识
- 使用 CSS 变量控制风格（`:root` 中定义，根据文章主题切换）

### 2. Playwright 截图

```python
# /tmp/screenshot-cover.py
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page(viewport={"width": 900, "height": 383})
    page.goto("file:///tmp/wechat-cover.html")
    page.wait_for_load_state("networkidle")
    page.screenshot(path="/tmp/wechat-cover-result.png", full_page=False)
    browser.close()
```

```bash
python3 /tmp/screenshot-cover.py
```

### 3. 插入飞书文档

```bash
cd /tmp && lark-cli docs +media-insert --doc "<DOC_ID>" --file "./wechat-cover-result.png" --as bot
```

## 样式速查

| 主题 | bg | accent | 字体风格 |
|------|-----|--------|---------|
| 科技/AI 深色 | `#080808` | `#d4af37`(金) / `#0084ff`(蓝) | 超粗 900，大号标题 |
| 产品/创业 | `#0d1117` | `#7c3aed` | 700-800 |
| 温暖/人文 | `#1a1208` | `#f59e0b` | 700，稍大圆角 |

## 本次验证的魔戒主题配置

```css
--bg: #080808;
--accent: #d4af37;  /* 金色魔戒 */
/* 视觉：同心椭圆环（rotateX 65deg）+ 中心光晕 + 网格背景 */
```

## 依赖

- `python3` + `playwright`（`pip3 install playwright && python3 -m playwright install chromium`）
- 无需任何 API key
