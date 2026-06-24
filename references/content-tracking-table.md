# 内容流水线多维表格

## 用途

每次写完公众号/小红书/飞书文档内容后，自动同步到飞书多维表格统一追踪。

## 表格位置

- 名称：`Skill101 内容流水线`
- URL：`https://fcnx2uasxmdm.feishu.cn/base/Zyjvbb4AgaKrQdsvYNHc0LLanIf`
- Base Token：`Zyjvbb4AgaKrQdsvYNHc0LLanIf`
- Table ID：`tbldHALVwno8T3rx`

## 表结构

| 列序 | 字段名 | 类型 | 说明 |
|------|--------|------|------|
| 1 | ID | auto_number | 系统主键，自动编号 |
| 2 | 主题 | text | 文章/图文标题 |
| 3 | 平台 | select(单选) | 公众号 / 小红书 / 飞书文档 |
| 4 | 类型 | select(单选) | 深度文章 / 图文笔记 / 简报 |
| 5 | 日期 | datetime | 创建日期 (yyyy-MM-dd) |
| 6 | 发布状态 | select(单选) | 草稿 / 已发布 / 已废弃 |
| 7 | 飞书链接 | text(url) | 飞书文档 URL |
| 8 | 摘要 | text | 100字以内核心概述 |

## 写入流程

每次内容产出后，用 `lark-cli base +record-batch-create` 写入：

```bash
cd /tmp
cat > bitable_sync.json << 'EOF'
{
    "fields": ["主题","平台","类型","日期","发布状态","飞书链接","摘要"],
    "rows": [
      ["文章标题","公众号","深度文章","2026-05-15","草稿","https://...","摘要内容"]
    ]
}
EOF

lark-cli base +record-batch-create \
  --base-token Zyjvbb4AgaKrQdsvYNHc0LLanIf \
  --table-id tbldHALVwno8T3rx \
  --json @bitable_sync.json \
  --as bot
```

## 坑点

- `--json` 必须是对象 `{"fields":[...], "rows":[...]}`，不能直接传数组
- `--json @file.json` 用相对路径，先 `cd` 到文件所在目录
- 日期字段用 `YYYY-MM-DD` 字符串格式
- bot 创建 Base 后需手动授权用户 `full_access`
