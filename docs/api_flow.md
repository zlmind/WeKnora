# WeKnora API 主流程链路

> 基础 URL：`http://localhost:8080/api/v1`  
> 认证：除 `/auth/*` 注册/登录外，所有接口均需 `X-API-Key: sk-xxxxx`  
> 完整参数参考：`http://localhost:8080/swagger/index.html`

---

## 目录

1. [流程总览](#流程总览)
2. [阶段一：用户注册与认证](#阶段一用户注册与认证)
3. [阶段二：模型配置（前置依赖）](#阶段二模型配置前置依赖)
4. [阶段三：创建知识库](#阶段三创建知识库)
5. [阶段四：上传知识内容](#阶段四上传知识内容)
6. [阶段五：知识召回（纯检索）](#阶段五知识召回纯检索)
7. [阶段六：创建智能体（可选）](#阶段六创建智能体可选)
8. [阶段七：对话问答](#阶段七对话问答)
9. [阶段八：消息与会话管理](#阶段八消息与会话管理)
10. [附录：其他能力链路](#附录其他能力链路)

---

## 流程总览

```
注册/登录 → 获取 API Key
    ↓
配置模型（Chat / Embedding / Rerank）
    ↓
创建知识库（绑定 Embedding 模型）
    ↓
上传知识内容（文件 / URL / 手工 Markdown）
    ↓
等待解析完成（parse_status: completed）
    ↓
┌─────────────────────────────────────────┐
│  纯召回路径                              │
│  POST /knowledge-search                 │
│  GET  /knowledge-bases/:id/hybrid-search│
└─────────────────────────────────────────┘
        ↓
┌─────────────────────────────────────────┐
│  对话问答路径                            │
│  创建会话 → 发起 Chat（SSE 流式）        │
│  knowledge-chat 或 agent-chat           │
└─────────────────────────────────────────┘
```

---

## 阶段一：用户注册与认证

### 1.1 注册账号

注册成功后响应中直接返回 `tenant.api_key`，后续所有 API 调用均使用该 Key。

```bash
POST /auth/register
Content-Type: application/json

{
    "username": "alice",
    "email": "alice@example.com",
    "password": "secret123"
}
```

**响应关键字段：**

| 字段 | 说明 |
|------|------|
| `user.id` | 用户 ID |
| `tenant.id` | 租户 ID |
| `tenant.api_key` | **后续所有 API 调用的凭证** |

### 1.2 登录（已有账号）

```bash
POST /auth/login
Content-Type: application/json

{
    "email": "alice@example.com",
    "password": "secret123"
}
```

响应同样包含 `tenant.api_key`，以及 JWT `token` 和 `refresh_token`（用于 Web 前端场景）。

### 1.3 获取当前用户信息

```bash
GET /auth/me
Authorization: Bearer <jwt_token>
```

> **后续所有接口**均使用 `X-API-Key: sk-xxxxx` 头，不再需要 JWT。

---

## 阶段二：模型配置（前置依赖）

知识库的 Embedding、召回的 Rerank、对话的 Chat 模型均需提前注册。

### 2.1 查看支持的服务商

```bash
GET /models/providers?model_type=embedding
X-API-Key: sk-xxxxx
```

### 2.2 注册 Chat 模型（对话用）

```bash
POST /models
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "name": "qwen-plus",
    "type": "KnowledgeQA",
    "source": "remote",
    "description": "阿里云 Qwen 对话模型",
    "parameters": {
        "base_url": "https://dashscope.aliyuncs.com/compatible-mode/v1",
        "api_key": "sk-your-dashscope-key",
        "provider": "aliyun"
    }
}
```

响应返回 `data.id`，记为 `<chat_model_id>`。

### 2.3 注册 Embedding 模型（向量化用）

```bash
POST /models
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "name": "text-embedding-v3",
    "type": "Embedding",
    "source": "remote",
    "parameters": {
        "base_url": "https://dashscope.aliyuncs.com/compatible-mode/v1",
        "api_key": "sk-your-dashscope-key",
        "provider": "aliyun",
        "embedding_parameters": { "dimension": 1024 }
    }
}
```

响应返回 `data.id`，记为 `<embedding_model_id>`。

### 2.4 注册 Rerank 模型（重排序用，可选但推荐）

```bash
POST /models
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "name": "gte-rerank",
    "type": "Rerank",
    "source": "remote",
    "parameters": {
        "base_url": "https://dashscope.aliyuncs.com/api/v1/services/rerank/text-rerank/text-rerank",
        "api_key": "sk-your-dashscope-key",
        "provider": "aliyun"
    }
}
```

响应返回 `data.id`，记为 `<rerank_model_id>`。

### 2.5 查看已注册模型列表

```bash
GET /models
X-API-Key: sk-xxxxx
```

---

## 阶段三：创建知识库

知识库是知识内容的容器，创建时绑定 Embedding 模型（**创建后不可更换**）。

```bash
POST /knowledge-bases
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "name": "产品文档库",
    "description": "存放产品相关文档",
    "type": "document",
    "embedding_model_id": "<embedding_model_id>",
    "chunking_config": {
        "chunk_size": 1000,
        "chunk_overlap": 200,
        "separators": ["\n\n", "\n", "。"]
    },
    "storage_provider_config": { "provider": "local" }
}
```

响应返回 `data.id`，记为 `<kb_id>`。

### 3.1 初始化知识库模型配置（绑定 Chat / Rerank 模型）

```bash
POST /initialization/initialize/<kb_id>
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "chat_model_id": "<chat_model_id>",
    "embedding_model_id": "<embedding_model_id>",
    "rerank_model_id": "<rerank_model_id>"
}
```

---

## 阶段四：上传知识内容

支持三种方式，均为异步处理，上传后 `parse_status` 为 `processing`，需轮询至 `completed`。

### 4.1 上传文件

```bash
POST /knowledge-bases/<kb_id>/knowledge/file
X-API-Key: sk-xxxxx
Content-Type: multipart/form-data

file=@"/path/to/document.pdf"
enable_multimodel="false"
```

### 4.2 从 URL 抓取网页

```bash
POST /knowledge-bases/<kb_id>/knowledge/url
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "url": "https://example.com/article",
    "title": "文章标题（可选）"
}
```

### 4.3 手工录入 Markdown

```bash
POST /knowledge-bases/<kb_id>/knowledge/manual
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "title": "产品使用指南",
    "content": "# 产品使用指南\n\n## 快速入门\n...",
    "status": "published"
}
```

### 4.4 轮询解析状态

```bash
GET /knowledge-bases/<kb_id>/knowledge?page=1&page_size=20
X-API-Key: sk-xxxxx
```

当 `parse_status` 变为 `completed`、`enable_status` 变为 `enabled` 时，该知识可被检索。

---

## 阶段五：知识召回（纯检索）

不经过 LLM，直接返回相关分块，适合自行处理检索结果的场景。

### 5.1 标准知识搜索

```bash
POST /knowledge-search
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "query": "如何配置知识库",
    "knowledge_base_id": "<kb_id>"
}
```

**响应字段说明：**

| 字段 | 说明 |
|------|------|
| `id` | 分块 ID |
| `content` | 命中的文本内容 |
| `knowledge_id` | 所属知识 ID |
| `score` | 相似度得分（rerank 后归一化） |
| `knowledge_title` | 来源知识标题 |

### 5.2 混合搜索（向量 + 关键词）

```bash
GET /knowledge-bases/<kb_id>/hybrid-search
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "query_text": "如何配置知识库",
    "vector_threshold": 0.5,
    "match_count": 10
}
```

> 注意：此接口使用 GET 方法但需要 JSON 请求体。

### 5.3 跨知识库搜索

```bash
POST /knowledge-search
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "query": "如何配置知识库",
    "knowledge_base_ids": ["<kb_id_1>", "<kb_id_2>"]
}
```

---

## 阶段六：创建智能体（可选）

系统内置三个智能体可直接使用，也可创建自定义智能体。

**内置智能体：**

| ID | 名称 | 模式 |
|----|------|------|
| `builtin-quick-answer` | 快速问答 | RAG |
| `builtin-smart-reasoning` | 智能推理 | ReAct |
| `builtin-data-analyst` | 数据分析师 | ReAct |

### 6.1 创建自定义智能体

```bash
POST /agents
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "name": "客服助手",
    "description": "专注产品问答的客服智能体",
    "config": {
        "agent_mode": "quick-answer",
        "system_prompt": "你是一个专业的产品客服...",
        "temperature": 0.3,
        "kb_selection_mode": "selected",
        "knowledge_bases": ["<kb_id>"],
        "multi_turn_enabled": true,
        "history_turns": 5,
        "embedding_top_k": 10,
        "rerank_top_k": 5
    }
}
```

响应返回 `data.id`，记为 `<agent_id>`。

---

## 阶段七：对话问答

### 7.1 创建会话

```bash
POST /sessions
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "title": "产品咨询"
}
```

响应返回 `data.id`，记为 `<session_id>`。

### 7.2 基于知识库的 RAG 问答（SSE 流式）

```bash
POST /knowledge-chat/<session_id>
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "query": "如何快速上手这个产品？",
    "knowledge_base_ids": ["<kb_id>"],
    "agent_id": "builtin-quick-answer"
}
```

**SSE 响应事件类型：**

| response_type | 说明 |
|---------------|------|
| `references` | 知识库检索引用（先于回答返回） |
| `answer` | 流式回答内容（`done: true` 表示结束） |
| `session_title` | 自动生成的会话标题 |

### 7.3 基于 Agent 的智能问答（SSE 流式）

```bash
POST /agent-chat/<session_id>
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "query": "帮我分析一下最近的销售数据",
    "agent_enabled": true,
    "agent_id": "<agent_id>",
    "knowledge_base_ids": ["<kb_id>"]
}
```

**SSE 响应事件类型（Agent 模式额外包含）：**

| response_type | 说明 |
|---------------|------|
| `thinking` | Agent 思考过程 |
| `tool_call` | 工具调用信息 |
| `tool_result` | 工具调用结果 |
| `references` | 知识库检索引用 |
| `answer` | 最终回答 |

### 7.4 停止生成

```bash
POST /sessions/<session_id>/stop
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "message_id": "<message_id>"
}
```

---

## 阶段八：消息与会话管理

### 8.1 获取会话消息历史

```bash
GET /messages/<session_id>/load
X-API-Key: sk-xxxxx
```

### 8.2 获取会话列表

```bash
GET /sessions?page=1&page_size=10
X-API-Key: sk-xxxxx
```

### 8.3 删除会话

```bash
DELETE /sessions/<session_id>
X-API-Key: sk-xxxxx
```

### 8.4 断线重连（继续未完成的流式响应）

```bash
GET /sessions/continue-stream/<session_id>?message_id=<message_id>
X-API-Key: sk-xxxxx
```

---

## 附录：其他能力链路

### A. FAQ 知识库流程

FAQ 类型知识库专为问答对场景设计，召回时优先匹配相似问题。

```
创建 FAQ 知识库（type: "faq"）
    ↓
POST /knowledge-bases/<kb_id>/knowledge/manual  （录入 Q&A 对）
    ↓
对话时 Agent 自动优先匹配 FAQ
```

### B. 标签管理

标签用于对知识内容分类，召回时可按标签过滤。

```bash
# 创建标签
POST /knowledge-bases/<kb_id>/tags

# 上传知识时指定标签
POST /knowledge-bases/<kb_id>/knowledge/file
form: tag_id=<tag_id>

# 召回时按标签过滤
GET /knowledge-bases/<kb_id>/hybrid-search
body: { "tag_ids": ["<tag_id>"] }
```

### C. 知识重新解析

当分块配置变更或解析失败时，可触发重新解析：

```bash
POST /knowledge/<knowledge_id>/reparse
X-API-Key: sk-xxxxx
```

### D. 组织协作与知识库共享

多人协作场景下，可将知识库或智能体共享给组织成员：

```bash
# 创建组织
POST /organizations

# 邀请成员
POST /organizations/<org_id>/members

# 共享知识库给组织
POST /agents/<agent_id>/shares
```

详见 [organization.md](./api/organization.md)。

### E. 评估流程

对召回质量或模型回答进行评估：

```bash
POST /evaluations
X-API-Key: sk-xxxxx
Content-Type: application/json

{
    "knowledge_base_id": "<kb_id>",
    "questions": ["问题1", "问题2"]
}
```

详见 [evaluation.md](./api/evaluation.md)。

---

## 关键 ID 依赖关系

```
tenant.api_key          ← 注册/登录时获取，所有请求必带
    │
    ├── model.id (chat)         ← POST /models (type: KnowledgeQA)
    ├── model.id (embedding)    ← POST /models (type: Embedding)
    └── model.id (rerank)       ← POST /models (type: Rerank)
            │
            └── knowledge_base.id   ← POST /knowledge-bases (绑定 embedding_model_id)
                    │
                    ├── knowledge.id    ← POST /knowledge-bases/:id/knowledge/*
                    └── agent.id        ← POST /agents (引用 kb_id)
                            │
                            └── session.id  ← POST /sessions
                                    │
                                    └── POST /knowledge-chat/:session_id
                                        POST /agent-chat/:session_id
```
