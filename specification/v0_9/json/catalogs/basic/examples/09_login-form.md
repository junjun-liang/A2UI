# 09_login-form.json 说明文档

## 文件概述

本文件展示了登录表单，演示了 TextField 组件的 obscured 变体和表单布局。

## UI 场景

展示一个登录表单，包含标题、邮箱输入框、密码输入框（ obscured 变体）、登录按钮和注册链接。

## 使用的组件

| 组件 | 用途 |
|------|------|
| Card | 卡片容器 |
| Column, Row | 布局 |
| TextField (shortText) | 邮箱输入框 |
| TextField (obscured) | 密码输入框 |
| Button | 登录和注册按钮 |
| Text | 标题、副标题、提示文本 |
| Divider | 分隔线 |

## 消息流程

1. **createSurface** — 创建界面
2. **updateComponents** — 构建登录表单布局
3. **updateDataModel** — 初始化空邮箱和密码
