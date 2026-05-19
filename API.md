# 法律服务网站 MVP 接口文档

版本：v0.1  
日期：2026-05-20  
后端服务：FastAPI  
接口前缀：`/api/v1`

## 1. 文档说明

本文档定义 MVP 阶段需要交付的接口，覆盖官网前端、管理系统前端和后端服务之间的接口契约。

MVP 必做范围：

1. 用户注册、登录、当前用户信息。
2. 法律新闻列表、详情、后台管理。
3. 法律法规列表、详情、后台管理。
4. 在线法律咨询提交、我的咨询、后台处理和回复。
5. 内容分类、标签。
6. 首页基础数据。
7. 后台工作台统计。

## 2. 通用约定

### 2.1 Base URL

开发环境：

```text
http://localhost:8000/api/v1
```

生产环境：

```text
https://{domain}/api/v1
```

### 2.2 请求格式

默认使用 JSON：

```http
Content-Type: application/json
```

文件上传接口后续使用：

```http
Content-Type: multipart/form-data
```

### 2.3 认证方式

MVP 使用 Bearer Token：

```http
Authorization: Bearer <access_token>
```

后台接口必须登录，并且要求用户具有管理端权限。

### 2.4 统一响应格式

成功响应：

```json
{
  "code": 0,
  "message": "ok",
  "data": {}
}
```

失败响应：

```json
{
  "code": 40001,
  "message": "用户名或密码错误",
  "data": null
}
```

### 2.5 分页响应格式

列表接口统一返回：

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "items": [],
    "total": 0,
    "page": 1,
    "page_size": 10,
    "pages": 0
  }
}
```

### 2.6 通用分页参数

| 参数 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| page | integer | 否 | 1 | 页码，从 1 开始 |
| page_size | integer | 否 | 10 | 每页条数，最大建议 100 |

### 2.7 通用时间格式

统一使用 ISO 8601 字符串：

```text
2026-05-20T10:30:00+08:00
```

### 2.8 通用状态码

| HTTP 状态码 | 业务 code | 说明 |
| --- | --- | --- |
| 200 | 0 | 成功 |
| 400 | 40000 | 请求参数错误 |
| 401 | 40100 | 未登录或 Token 失效 |
| 403 | 40300 | 无权限 |
| 404 | 40400 | 资源不存在 |
| 409 | 40900 | 资源冲突 |
| 422 | 42200 | 数据校验失败 |
| 500 | 50000 | 服务端错误 |

## 3. 枚举定义

### 3.1 用户类型 `user_type`

| 值 | 说明 |
| --- | --- |
| personal | 个人用户 |
| enterprise | 企业用户 |
| lawyer | 律师 |
| operator | 运营人员 |
| admin | 管理员 |

### 3.2 用户状态 `user_status`

| 值 | 说明 |
| --- | --- |
| active | 正常 |
| disabled | 禁用 |

### 3.3 内容类型 `content_type`

| 值 | 说明 |
| --- | --- |
| news | 法律新闻 |
| knowledge | 法律知识 |
| case | 案例解析 |
| topic | 专题 |

MVP 阶段后台内容管理优先支持 `news`。

### 3.4 内容状态 `content_status`

| 值 | 说明 |
| --- | --- |
| draft | 草稿 |
| pending | 待审核 |
| published | 已发布 |
| offline | 已下线 |

### 3.5 法规类型 `regulation_type`

| 值 | 说明 |
| --- | --- |
| law | 法律 |
| administrative_regulation | 行政法规 |
| judicial_interpretation | 司法解释 |
| department_rule | 部门规章 |
| local_regulation | 地方性法规 |
| policy | 政策文件 |

### 3.6 法规效力状态 `validity_status`

| 值 | 说明 |
| --- | --- |
| effective | 现行有效 |
| amended | 已修改 |
| repealed | 已废止 |
| not_yet_effective | 尚未生效 |
| unknown | 未知 |

### 3.7 咨询状态 `consultation_status`

| 值 | 说明 |
| --- | --- |
| pending | 待处理 |
| processing | 处理中 |
| replied | 已回复 |
| need_more_info | 需补充 |
| closed | 已关闭 |
| invalid | 无效 |

### 3.8 咨询回复类型 `reply_type`

| 值 | 说明 |
| --- | --- |
| user | 用户补充 |
| operator | 运营回复 |
| lawyer | 律师回复 |

## 4. 数据模型

### 4.1 User

```json
{
  "id": 1,
  "username": "user001",
  "nickname": "张三",
  "phone": "13800000000",
  "email": "user@example.com",
  "user_type": "personal",
  "status": "active",
  "created_at": "2026-05-20T10:30:00+08:00",
  "updated_at": "2026-05-20T10:30:00+08:00"
}
```

### 4.2 Category

```json
{
  "id": 1,
  "name": "劳动工伤",
  "slug": "labor",
  "parent_id": null,
  "sort": 100,
  "status": "active"
}
```

### 4.3 Tag

```json
{
  "id": 1,
  "name": "劳动合同",
  "slug": "labor-contract"
}
```

### 4.4 Article

```json
{
  "id": 1,
  "title": "最高法发布劳动争议典型案例",
  "summary": "本篇介绍劳动争议相关司法动态。",
  "content": "<p>正文内容</p>",
  "cover_url": "https://example.com/news/cover.jpg",
  "content_type": "news",
  "category": {
    "id": 1,
    "name": "司法动态",
    "slug": "judicial-news"
  },
  "tags": [
    {
      "id": 1,
      "name": "劳动争议",
      "slug": "labor-dispute"
    }
  ],
  "source": "中国法院网",
  "author": "编辑部",
  "status": "published",
  "view_count": 123,
  "seo_title": "最高法发布劳动争议典型案例",
  "seo_description": "劳动争议典型案例解读",
  "published_at": "2026-05-20T10:30:00+08:00",
  "created_at": "2026-05-20T10:30:00+08:00",
  "updated_at": "2026-05-20T10:30:00+08:00"
}
```

### 4.5 Regulation

```json
{
  "id": 1,
  "title": "中华人民共和国民法典",
  "document_no": "中华人民共和国主席令第四十五号",
  "regulation_type": "law",
  "issuing_authority": "全国人民代表大会",
  "region": "全国",
  "publish_date": "2020-05-28",
  "effective_date": "2021-01-01",
  "validity_status": "effective",
  "summary": "民法典是民事领域的基础性法律。",
  "content": "<p>正文内容</p>",
  "source_url": "https://flk.npc.gov.cn/",
  "status": "published",
  "created_at": "2026-05-20T10:30:00+08:00",
  "updated_at": "2026-05-20T10:30:00+08:00"
}
```

### 4.6 Consultation

```json
{
  "id": 1,
  "title": "公司拖欠工资怎么办？",
  "description": "公司已经两个月没有发工资，我应该怎么维权？",
  "category": {
    "id": 1,
    "name": "劳动工伤",
    "slug": "labor"
  },
  "region": "上海",
  "contact_name": "张三",
  "contact_phone": "13800000000",
  "status": "pending",
  "is_public": false,
  "replies": [],
  "created_at": "2026-05-20T10:30:00+08:00",
  "updated_at": "2026-05-20T10:30:00+08:00"
}
```

### 4.7 ConsultationReply

```json
{
  "id": 1,
  "consultation_id": 1,
  "replier": {
    "id": 2,
    "nickname": "平台律师"
  },
  "content": "建议先保存劳动合同、工资流水、聊天记录等证据。",
  "reply_type": "lawyer",
  "created_at": "2026-05-20T10:30:00+08:00"
}
```

## 5. 前台接口

### 5.1 获取首页数据

```http
GET /api/v1/home
```

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "banners": [
      {
        "id": 1,
        "title": "在线法律咨询",
        "image_url": "https://example.com/banner.jpg",
        "link_url": "/consultations/new"
      }
    ],
    "latest_news": [],
    "hot_news": [],
    "recommended_regulations": [],
    "consultation_categories": [],
    "popular_tags": []
  }
}
```

### 5.2 获取新闻列表

```http
GET /api/v1/news
```

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| page | integer | 否 | 页码 |
| page_size | integer | 否 | 每页条数 |
| keyword | string | 否 | 标题/摘要关键词 |
| category_id | integer | 否 | 分类 ID |
| tag_id | integer | 否 | 标签 ID |
| sort | string | 否 | `latest`、`hot` |

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "items": [
      {
        "id": 1,
        "title": "最高法发布劳动争议典型案例",
        "summary": "本篇介绍劳动争议相关司法动态。",
        "cover_url": "https://example.com/news/cover.jpg",
        "category": {
          "id": 1,
          "name": "司法动态",
          "slug": "judicial-news"
        },
        "source": "中国法院网",
        "view_count": 123,
        "published_at": "2026-05-20T10:30:00+08:00"
      }
    ],
    "total": 1,
    "page": 1,
    "page_size": 10,
    "pages": 1
  }
}
```

### 5.3 获取新闻详情

```http
GET /api/v1/news/{id}
```

#### 路径参数

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| id | integer | 新闻 ID |

#### 响应说明

返回 `Article` 完整结构，并包含相关推荐。

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "article": {
      "id": 1,
      "title": "最高法发布劳动争议典型案例",
      "summary": "本篇介绍劳动争议相关司法动态。",
      "content": "<p>正文内容</p>",
      "cover_url": "https://example.com/news/cover.jpg",
      "content_type": "news",
      "category": {
        "id": 1,
        "name": "司法动态",
        "slug": "judicial-news"
      },
      "tags": [],
      "source": "中国法院网",
      "author": "编辑部",
      "status": "published",
      "view_count": 123,
      "seo_title": "最高法发布劳动争议典型案例",
      "seo_description": "劳动争议典型案例解读",
      "published_at": "2026-05-20T10:30:00+08:00",
      "created_at": "2026-05-20T10:30:00+08:00",
      "updated_at": "2026-05-20T10:30:00+08:00"
    },
    "related_articles": [],
    "related_regulations": []
  }
}
```

### 5.4 获取法规列表

```http
GET /api/v1/regulations
```

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| page | integer | 否 | 页码 |
| page_size | integer | 否 | 每页条数 |
| keyword | string | 否 | 标题/正文关键词 |
| regulation_type | string | 否 | 法规类型 |
| validity_status | string | 否 | 效力状态 |
| issuing_authority | string | 否 | 发布机关 |
| region | string | 否 | 地区 |
| publish_date_start | string | 否 | 发布开始日期，格式 `YYYY-MM-DD` |
| publish_date_end | string | 否 | 发布结束日期，格式 `YYYY-MM-DD` |

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "items": [
      {
        "id": 1,
        "title": "中华人民共和国民法典",
        "document_no": "中华人民共和国主席令第四十五号",
        "regulation_type": "law",
        "issuing_authority": "全国人民代表大会",
        "region": "全国",
        "publish_date": "2020-05-28",
        "effective_date": "2021-01-01",
        "validity_status": "effective",
        "summary": "民法典是民事领域的基础性法律。"
      }
    ],
    "total": 1,
    "page": 1,
    "page_size": 10,
    "pages": 1
  }
}
```

### 5.5 获取法规详情

```http
GET /api/v1/regulations/{id}
```

#### 响应说明

返回 `Regulation` 完整结构，并包含关联内容。

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "regulation": {
      "id": 1,
      "title": "中华人民共和国民法典",
      "document_no": "中华人民共和国主席令第四十五号",
      "regulation_type": "law",
      "issuing_authority": "全国人民代表大会",
      "region": "全国",
      "publish_date": "2020-05-28",
      "effective_date": "2021-01-01",
      "validity_status": "effective",
      "summary": "民法典是民事领域的基础性法律。",
      "content": "<p>正文内容</p>",
      "source_url": "https://flk.npc.gov.cn/",
      "status": "published",
      "created_at": "2026-05-20T10:30:00+08:00",
      "updated_at": "2026-05-20T10:30:00+08:00"
    },
    "related_articles": [],
    "related_consultations": []
  }
}
```

### 5.6 提交在线咨询

```http
POST /api/v1/consultations
```

登录可选。未登录用户也可以提交咨询。

#### 请求体

```json
{
  "title": "公司拖欠工资怎么办？",
  "description": "公司已经两个月没有发工资，我应该怎么维权？",
  "category_id": 1,
  "region": "上海",
  "contact_name": "张三",
  "contact_phone": "13800000000",
  "is_public": false
}
```

#### 字段说明

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| title | string | 是 | 问题标题，2-100 字 |
| description | string | 是 | 问题描述，10-2000 字 |
| category_id | integer | 是 | 咨询分类 ID |
| region | string | 否 | 所在地区 |
| contact_name | string | 否 | 联系人 |
| contact_phone | string | 是 | 联系电话 |
| is_public | boolean | 否 | 是否允许脱敏后公开，默认 false |

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "id": 1,
    "status": "pending",
    "created_at": "2026-05-20T10:30:00+08:00"
  }
}
```

### 5.7 获取咨询分类

```http
GET /api/v1/consultation-categories
```

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": [
    {
      "id": 1,
      "name": "劳动工伤",
      "slug": "labor",
      "sort": 100
    }
  ]
}
```

### 5.8 全站搜索

```http
GET /api/v1/search
```

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| keyword | string | 是 | 搜索关键词 |
| type | string | 否 | `all`、`news`、`regulation`，MVP 先支持这三类 |
| page | integer | 否 | 页码 |
| page_size | integer | 否 | 每页条数 |

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "items": [
      {
        "type": "news",
        "id": 1,
        "title": "最高法发布劳动争议典型案例",
        "summary": "本篇介绍劳动争议相关司法动态。",
        "url": "/news/1",
        "highlight": "最高法发布<em>劳动争议</em>典型案例"
      }
    ],
    "total": 1,
    "page": 1,
    "page_size": 10,
    "pages": 1
  }
}
```

## 6. 用户接口

### 6.1 用户注册

```http
POST /api/v1/auth/register
```

#### 请求体

```json
{
  "username": "user001",
  "password": "Password123",
  "nickname": "张三",
  "phone": "13800000000"
}
```

#### 字段说明

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| username | string | 是 | 用户名，4-32 位 |
| password | string | 是 | 密码，8-64 位 |
| nickname | string | 否 | 昵称 |
| phone | string | 否 | 手机号 |

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "user": {
      "id": 1,
      "username": "user001",
      "nickname": "张三",
      "phone": "13800000000",
      "email": null,
      "user_type": "personal",
      "status": "active",
      "created_at": "2026-05-20T10:30:00+08:00",
      "updated_at": "2026-05-20T10:30:00+08:00"
    },
    "access_token": "jwt-token",
    "token_type": "bearer"
  }
}
```

### 6.2 用户登录

```http
POST /api/v1/auth/login
```

#### 请求体

```json
{
  "username": "user001",
  "password": "Password123"
}
```

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "user": {
      "id": 1,
      "username": "user001",
      "nickname": "张三",
      "phone": "13800000000",
      "email": null,
      "user_type": "personal",
      "status": "active",
      "created_at": "2026-05-20T10:30:00+08:00",
      "updated_at": "2026-05-20T10:30:00+08:00"
    },
    "access_token": "jwt-token",
    "token_type": "bearer"
  }
}
```

### 6.3 获取当前用户

```http
GET /api/v1/users/me
```

需要登录。

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "id": 1,
    "username": "user001",
    "nickname": "张三",
    "phone": "13800000000",
    "email": null,
    "user_type": "personal",
    "status": "active",
    "created_at": "2026-05-20T10:30:00+08:00",
    "updated_at": "2026-05-20T10:30:00+08:00"
  }
}
```

### 6.4 获取我的咨询

```http
GET /api/v1/users/me/consultations
```

需要登录。

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| page | integer | 否 | 页码 |
| page_size | integer | 否 | 每页条数 |
| status | string | 否 | 咨询状态 |

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "items": [
      {
        "id": 1,
        "title": "公司拖欠工资怎么办？",
        "category": {
          "id": 1,
          "name": "劳动工伤",
          "slug": "labor"
        },
        "status": "replied",
        "created_at": "2026-05-20T10:30:00+08:00",
        "updated_at": "2026-05-20T11:00:00+08:00"
      }
    ],
    "total": 1,
    "page": 1,
    "page_size": 10,
    "pages": 1
  }
}
```

### 6.5 获取我的咨询详情

```http
GET /api/v1/users/me/consultations/{id}
```

需要登录，只能查看自己的咨询。

#### 响应说明

返回 `Consultation` 完整结构，包含回复列表。

### 6.6 用户补充咨询

```http
POST /api/v1/users/me/consultations/{id}/replies
```

需要登录，只能补充自己的咨询。

#### 请求体

```json
{
  "content": "我和公司签过劳动合同，但是没有工资条。"
}
```

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "id": 2,
    "consultation_id": 1,
    "reply_type": "user",
    "created_at": "2026-05-20T11:30:00+08:00"
  }
}
```

## 7. 后台接口

后台接口统一要求：

```http
Authorization: Bearer <admin_access_token>
```

### 7.1 后台登录

```http
POST /api/v1/admin/auth/login
```

#### 请求体

```json
{
  "username": "admin",
  "password": "Admin123456"
}
```

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "user": {
      "id": 100,
      "username": "admin",
      "nickname": "管理员",
      "user_type": "admin",
      "status": "active"
    },
    "access_token": "admin-jwt-token",
    "token_type": "bearer",
    "permissions": [
      "article:manage",
      "regulation:manage",
      "consultation:manage",
      "user:manage"
    ]
  }
}
```

### 7.2 获取工作台统计

```http
GET /api/v1/admin/dashboard
```

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "today_consultations": 8,
    "pending_consultations": 12,
    "published_news": 120,
    "published_regulations": 300,
    "registered_users": 560,
    "latest_consultations": [],
    "latest_articles": []
  }
}
```

## 8. 后台新闻管理

### 8.1 获取新闻列表

```http
GET /api/v1/admin/news
```

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| page | integer | 否 | 页码 |
| page_size | integer | 否 | 每页条数 |
| keyword | string | 否 | 关键词 |
| category_id | integer | 否 | 分类 ID |
| status | string | 否 | 内容状态 |

### 8.2 创建新闻

```http
POST /api/v1/admin/news
```

#### 请求体

```json
{
  "title": "最高法发布劳动争议典型案例",
  "summary": "本篇介绍劳动争议相关司法动态。",
  "content": "<p>正文内容</p>",
  "cover_url": "https://example.com/news/cover.jpg",
  "category_id": 1,
  "tag_ids": [1, 2],
  "source": "中国法院网",
  "author": "编辑部",
  "status": "draft",
  "seo_title": "最高法发布劳动争议典型案例",
  "seo_description": "劳动争议典型案例解读",
  "published_at": null
}
```

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "id": 1
  }
}
```

### 8.3 获取新闻详情

```http
GET /api/v1/admin/news/{id}
```

### 8.4 更新新闻

```http
PUT /api/v1/admin/news/{id}
```

请求体同创建新闻，字段可全量提交。

### 8.5 删除新闻

```http
DELETE /api/v1/admin/news/{id}
```

建议 MVP 执行软删除或状态下线。

### 8.6 发布新闻

```http
POST /api/v1/admin/news/{id}/publish
```

#### 请求体

```json
{
  "published_at": "2026-05-20T10:30:00+08:00"
}
```

### 8.7 下线新闻

```http
POST /api/v1/admin/news/{id}/offline
```

## 9. 后台法规管理

### 9.1 获取法规列表

```http
GET /api/v1/admin/regulations
```

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| page | integer | 否 | 页码 |
| page_size | integer | 否 | 每页条数 |
| keyword | string | 否 | 关键词 |
| regulation_type | string | 否 | 法规类型 |
| validity_status | string | 否 | 效力状态 |
| issuing_authority | string | 否 | 发布机关 |
| status | string | 否 | 展示状态 |

### 9.2 创建法规

```http
POST /api/v1/admin/regulations
```

#### 请求体

```json
{
  "title": "中华人民共和国民法典",
  "document_no": "中华人民共和国主席令第四十五号",
  "regulation_type": "law",
  "issuing_authority": "全国人民代表大会",
  "region": "全国",
  "publish_date": "2020-05-28",
  "effective_date": "2021-01-01",
  "validity_status": "effective",
  "summary": "民法典是民事领域的基础性法律。",
  "content": "<p>正文内容</p>",
  "source_url": "https://flk.npc.gov.cn/",
  "status": "published"
}
```

### 9.3 获取法规详情

```http
GET /api/v1/admin/regulations/{id}
```

### 9.4 更新法规

```http
PUT /api/v1/admin/regulations/{id}
```

请求体同创建法规，字段可全量提交。

### 9.5 删除法规

```http
DELETE /api/v1/admin/regulations/{id}
```

建议 MVP 执行软删除或状态下线。

### 9.6 批量导入法规

```http
POST /api/v1/admin/regulations/import
```

MVP 可选，如果首期不做导入，可先预留接口。

请求格式：

```http
Content-Type: multipart/form-data
```

表单字段：

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| file | file | 是 | Excel 或 CSV 文件 |

响应示例：

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "success_count": 100,
    "failed_count": 2,
    "errors": [
      {
        "row": 10,
        "message": "法规标题不能为空"
      }
    ]
  }
}
```

## 10. 后台咨询管理

### 10.1 获取咨询列表

```http
GET /api/v1/admin/consultations
```

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| page | integer | 否 | 页码 |
| page_size | integer | 否 | 每页条数 |
| keyword | string | 否 | 标题/描述关键词 |
| category_id | integer | 否 | 分类 ID |
| status | string | 否 | 咨询状态 |
| region | string | 否 | 地区 |
| created_start | string | 否 | 创建开始时间 |
| created_end | string | 否 | 创建结束时间 |

#### 响应说明

列表字段应包含咨询 ID、标题、分类、地区、联系方式脱敏值、状态、创建时间、更新时间。

### 10.2 获取咨询详情

```http
GET /api/v1/admin/consultations/{id}
```

返回咨询完整信息和回复列表。

### 10.3 回复咨询

```http
POST /api/v1/admin/consultations/{id}/replies
```

#### 请求体

```json
{
  "content": "建议先保存劳动合同、工资流水、聊天记录等证据。",
  "reply_type": "operator"
}
```

#### 响应示例

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "id": 1,
    "consultation_id": 1,
    "status": "replied",
    "created_at": "2026-05-20T11:00:00+08:00"
  }
}
```

### 10.4 更新咨询状态

```http
PATCH /api/v1/admin/consultations/{id}/status
```

#### 请求体

```json
{
  "status": "closed"
}
```

### 10.5 分派咨询

```http
PATCH /api/v1/admin/consultations/{id}/assign
```

MVP 可选。如果首期没有律师角色，可先分派给运营人员。

#### 请求体

```json
{
  "assigned_to": 100
}
```

## 11. 分类与标签接口

### 11.1 获取分类列表

```http
GET /api/v1/categories
```

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| type | string | 否 | `news`、`consultation`、`regulation` |

### 11.2 后台创建分类

```http
POST /api/v1/admin/categories
```

#### 请求体

```json
{
  "name": "劳动工伤",
  "slug": "labor",
  "type": "consultation",
  "parent_id": null,
  "sort": 100,
  "status": "active"
}
```

### 11.3 后台更新分类

```http
PUT /api/v1/admin/categories/{id}
```

### 11.4 后台删除分类

```http
DELETE /api/v1/admin/categories/{id}
```

### 11.5 获取标签列表

```http
GET /api/v1/tags
```

### 11.6 后台创建标签

```http
POST /api/v1/admin/tags
```

#### 请求体

```json
{
  "name": "劳动合同",
  "slug": "labor-contract"
}
```

### 11.7 后台更新标签

```http
PUT /api/v1/admin/tags/{id}
```

### 11.8 后台删除标签

```http
DELETE /api/v1/admin/tags/{id}
```

## 12. 文件上传接口

### 12.1 上传图片

```http
POST /api/v1/admin/uploads/images
```

后台登录后可用。

请求格式：

```http
Content-Type: multipart/form-data
```

表单字段：

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| file | file | 是 | 图片文件 |

限制：

1. 支持 jpg、jpeg、png、webp。
2. 单文件大小不超过 5MB。

响应示例：

```json
{
  "code": 0,
  "message": "ok",
  "data": {
    "url": "https://example.com/uploads/images/2026/05/cover.jpg",
    "filename": "cover.jpg",
    "size": 102400
  }
}
```

## 13. 后台用户管理

### 13.1 获取用户列表

```http
GET /api/v1/admin/users
```

#### 查询参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| page | integer | 否 | 页码 |
| page_size | integer | 否 | 每页条数 |
| keyword | string | 否 | 用户名/昵称/手机号 |
| user_type | string | 否 | 用户类型 |
| status | string | 否 | 用户状态 |

### 13.2 获取用户详情

```http
GET /api/v1/admin/users/{id}
```

### 13.3 更新用户状态

```http
PATCH /api/v1/admin/users/{id}/status
```

#### 请求体

```json
{
  "status": "disabled"
}
```

## 14. 接口鉴权矩阵

| 接口 | 游客 | 登录用户 | 运营/管理员 |
| --- | --- | --- | --- |
| `GET /home` | 是 | 是 | 是 |
| `GET /news` | 是 | 是 | 是 |
| `GET /news/{id}` | 是 | 是 | 是 |
| `GET /regulations` | 是 | 是 | 是 |
| `GET /regulations/{id}` | 是 | 是 | 是 |
| `POST /consultations` | 是 | 是 | 是 |
| `GET /users/me` | 否 | 是 | 是 |
| `GET /users/me/consultations` | 否 | 是 | 是 |
| `/admin/**` | 否 | 否 | 是 |

## 15. MVP 实施优先级

### P0

1. `POST /auth/register`
2. `POST /auth/login`
3. `GET /users/me`
4. `GET /news`
5. `GET /news/{id}`
6. `GET /regulations`
7. `GET /regulations/{id}`
8. `POST /consultations`
9. `POST /admin/auth/login`
10. `GET /admin/consultations`
11. `GET /admin/consultations/{id}`
12. `POST /admin/consultations/{id}/replies`
13. `GET /admin/news`
14. `POST /admin/news`
15. `PUT /admin/news/{id}`
16. `GET /admin/regulations`
17. `POST /admin/regulations`
18. `PUT /admin/regulations/{id}`

### P1

1. `GET /home`
2. `GET /search`
3. `GET /users/me/consultations`
4. `GET /users/me/consultations/{id}`
5. `POST /users/me/consultations/{id}/replies`
6. `POST /admin/news/{id}/publish`
7. `POST /admin/news/{id}/offline`
8. `PATCH /admin/consultations/{id}/status`
9. `GET /categories`
10. `POST /admin/categories`
11. `GET /tags`
12. `POST /admin/tags`
13. `POST /admin/uploads/images`

### P2

1. `GET /admin/dashboard`
2. `DELETE /admin/news/{id}`
3. `DELETE /admin/regulations/{id}`
4. `POST /admin/regulations/import`
5. `PATCH /admin/consultations/{id}/assign`
6. `GET /admin/users`
7. `PATCH /admin/users/{id}/status`

## 16. 待确认事项

1. MVP 阶段是否允许游客提交咨询。当前设计为允许。
2. MVP 阶段是否需要手机号验证码。当前设计为账号密码注册登录，手机号验证码后续扩展。
3. 后台是否需要完整 RBAC。当前设计为预留权限字段，MVP 可先用 `operator` 和 `admin` 两类角色。
4. 法规正文是否以 HTML 存储。当前设计为 HTML，方便前台直接渲染目录和正文。
5. 新闻正文是否使用富文本 HTML。当前设计为 HTML。
6. 图片上传是本地存储还是对象存储。当前仅定义返回 URL，不限制实现方式。

