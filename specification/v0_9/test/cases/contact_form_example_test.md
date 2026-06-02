# contact_form_example_test.json 说明文档

## 文件概述

本文件是联系人表单（Contact Form）集成测试用例，用于验证 `server_to_client.json` 中完整的表单交互流程，包括创建界面、更新组件和更新数据模型三个阶段。

## 测试目标

验证一个典型的表单场景中，服务端到客户端消息的完整生命周期：
1. 通过 `createSurface` 创建界面
2. 通过 `updateComponents` 渲染表单组件（含验证规则）
3. 通过 `updateDataModel` 更新数据模型

确保各消息类型在组合使用时均符合 schema 定义。

## 数据结构

| 字段 | 类型 | 说明 |
|------|------|------|
| schema | string | 被测试的 schema 文件，固定为 `server_to_client.json` |
| tests | array | 测试用例数组，每项包含 description、valid 和 data |

### tests 数组元素

| 字段 | 类型 | 说明 |
|------|------|------|
| description | string | 测试用例的描述信息 |
| valid | boolean | 该测试数据是否应通过 schema 校验 |
| data | object | 测试数据，遵循 server_to_client 消息格式 |

### 涉及的消息类型

| 消息类型 | 说明 |
|----------|------|
| createSurface | 创建界面，指定 surfaceId 和 catalogId |
| updateComponents | 更新组件，包含 Column、Text、TextField、Button 等组件 |
| updateDataModel | 更新数据模型，指定路径和值 |

### 表单组件结构

| 组件 ID | 组件类型 | 说明 |
|---------|----------|------|
| root | Column | 根布局容器，纵向排列所有子组件 |
| first_name_label / first_name_field | Text / TextField | 名字标签与输入框 |
| last_name_label / last_name_field | Text / TextField | 姓氏标签与输入框 |
| email_label / email_field | Text / TextField | 邮箱标签与输入框（含 email 验证） |
| phone_label / phone_field | Text / TextField | 电话标签与输入框 |
| notes_label / notes_field | Text / TextField | 备注标签与输入框（variant 为 longText） |
| submit_button_label / submit_button | Text / Button | 提交按钮标签与按钮（variant 为 primary） |

## 测试场景与预期结果

| 测试描述 | 预期结果 | 说明 |
|----------|----------|------|
| Contact Form Example: Create Surface | ✅ 通过 | 有效的 createSurface 消息，创建联系人表单界面 |
| Contact Form Example: Update Components | ✅ 通过 | 有效的 updateComponents 消息，渲染完整表单（含 email 验证规则和提交按钮） |
| Contact Form Example: Update Data Model | ✅ 通过 | 有效的 updateDataModel 消息，填充表单初始数据 |
