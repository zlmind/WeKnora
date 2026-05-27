# WeKnora 品牌定制修改清单

> 本文档记录将 WeKnora 开源项目改为自己产品时，需要修改的前后端内容。

---

## 一、前端修改内容

### 1. Logo/图片
| 文件 | 说明 | 处理方式 |
|------|------|---------|
| `frontend/src/assets/img/weknora.png` | 登录页Logo | 手动更新为你的Logo |
| `frontend/public/favicon.ico` | Favicon图标 | 手动更新为你的Favicon |

### 2. 登录页 `frontend/src/views/auth/Login.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 96 | `https://github.com/Tencent/WeKnora` | GitHub链接 | 删除此链接 |
| 97 | `weknora.png` | Logo图片 | 替换为你的Logo文件名 |
| 102 | `https://weknora.weixin.qq.com` | 官网链接 | 删除或替换为你的官网链接 |
| 111 | `https://github.com/Tencent/WeKnora` | GitHub链接 | 删除此链接 |

### 3. 设置页面

#### `frontend/src/views/settings/SystemInfo.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 186 | `https://github.com/Tencent/WeKnora/blob/main/docs/migration-troubleshooting.md` | 文档链接 | 替换为 `http://171.16.46.5/AI/WeKnora/blob/main/docs/migration-troubleshooting.md` |
| 192 | `https://github.com/Tencent/WeKnora/issues/new` | Issue链接 | 替换为 `http://171.16.46.5/AI/WeKnora/issues/new` |
| 202 | `WeKnora version` | 版本信息显示 | 改为 `version` (去掉WeKnora) |

#### `frontend/src/views/settings/Settings.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 39-40 | WeKnora Cloud SVG图标 | 菜单图标 | 隐藏左侧菜单 |
| 93-95 | WeKnora Cloud相关div | 设置面板 | 注释掉 |
| 188 | `import WeKnoraCloudSettings` | 组件导入 | 删除此行 |
| 232 | `weknoracloud: 'admin'` | 权限配置 | 删除此行 |
| 262 | `label: 'WeKnora Cloud'` | 菜单标签 | 删除此行 |

#### `frontend/src/views/settings/WeKnoraCloudSettings.vue`
| 说明 | 处理方式 |
|------|---------|
| 整个文件 | 隐藏左侧菜单，不删除文件 |

#### `frontend/src/views/settings/ParserEngineSettings.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 100-122 | weknoracloud凭证状态UI | 解析引擎配置 | 删除weknoracloud相关代码块 |
| 251 | `getWeKnoraCloudStatus` | API调用 | 删除此行 |
| 261 | weknoracloud文档链接 | 帮助文档 | 删除或替换 |
| 311 | `weknoracloud: 1` | 引擎标识 | 删除此行 |
| 496, 502, 520, 763 | WeKnoraCloud相关逻辑 | 凭证状态检查 | 删除相关函数和逻辑 |

#### `frontend/src/views/settings/TenantMembers.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 57 | `https://github.com/Tencent/WeKnora/blob/main/docs/RBAC%E8%AF%B4%E6%98%8E.md` | RBAC文档链接 | 替换为 `http://171.16.46.5/AI/WeKnora/blob/main/docs/RBAC说明.md` |

#### `frontend/src/views/settings/GeneralSettings.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 146 | `https://github.com/Tencent/WeKnora/blob/main/docs/KnowledgeGraph.md` | 知识图谱文档链接 | 替换为 `http://171.16.46.5/AI/WeKnora/blob/main/docs/KnowledgeGraph.md` |

#### `frontend/src/views/settings/ModelSettings.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 25 | `https://github.com/Tencent/WeKnora/blob/main/docs/BUILTIN_MODELS.md` | 内置模型文档链接 | 替换为 `http://171.16.46.5/AI/WeKnora/blob/main/docs/BUILTIN_MODELS.md` |

#### `frontend/src/views/settings/ApiInfo.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 201 | `WeKnora Lite (Wails)` | 类型定义注释 | 改为 `Product Lite (Wails)` |
| 243 | `WeKnoraDesktopWindow` | 类型定义 | 改为 `ProductDesktopWindow` |
| 262, 288, 314, 319, 328, 351, 370 | WeKnoraDesktopWindow相关函数 | 桌面应用API | 替换为新名称 |
| 402 | `https://github.com/Tencent/WeKnora/blob/main/docs/api/README.md` | API文档链接 | 替换为 `http://171.16.46.5/AI/WeKnora/blob/main/docs/api/README.md` |

### 4. 知识库相关页面

#### `frontend/src/views/knowledge/GraphSettings.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 559 | `https://github.com/Tencent/WeKnora/blob/main/docs/KnowledgeGraph.md` | 知识图谱文档链接 | 替换为 `http://171.16.46.5/AI/WeKnora/blob/main/docs/KnowledgeGraph.md` |

#### `frontend/src/views/knowledge/KnowledgeBase.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 345 | `weknora.kb.docs.viewMode` | localStorage key | 保持不变(内部实现) |
| 987, 993 | `weknora:open-knowledge` | 自定义事件名 | 保持不变(内部实现) |

#### `frontend/src/views/knowledge/settings/chunkingSamples.ts`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 13-74 | WeKnora知识框架示例文档 | Markdown示例 | 不展示，删除 |
| 112-124 | WeKnora部署FAQ | Docker镜像名等 | 不展示，删除 |

#### `frontend/src/views/knowledge/components/FAQEntryManager.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 2569-2572 | FAQ示例中的"WeKnora" | 示例问题数据 | 把"WeKnora"改为"Rag" |

### 5. Agent相关页面

#### `frontend/src/views/agent/AgentEditorModal.vue`
| 行号 | 原始内容 | 说明 | 处理方式 |
|------|---------|------|---------|
| 1190 | `https://github.com/Tencent/WeKnora/blob/main/docs/IM%E9%9B%86%E6%88%90%E5%BC%80%E5%8F%91%E6%96%87%E6%A1%A3.md` | IM集成文档链接 | 替换为 `http://171.16.46.5/AI/WeKnora/blob/main/docs/IM集成开发文档.md` |

### 6. localStorage Key（内部实现，用户不可见）
| 文件 | Key名 | 处理方式 |
|------|-------|---------|
| `security.ts` | `weknora_token`, `weknora_selected_tenant_id` | 保持不变 |
| `request.ts` | `weknora_token`, `weknora_selected_tenant_id`, `weknora_refresh_token` | 保持不变 |
| `tenantSwitch.ts` | `weknora_pending_tenant_switch_toast` | 保持不变 |

### 7. 自定义事件名（内部实现）
| 文件 | 事件名 | 处理方式 |
|------|-------|---------|
| `platform/index.vue` | `weknora:chat-file-drop` | 保持不变 |
| `KnowledgeBase.vue` | `weknora:open-knowledge` | 保持不变 |

---

## 二、后端修改内容

### 1. 向导/System prompt中的默认名称
| 文件 | 原始内容 | 处理方式 |
|------|---------|---------|
| `internal/utils/presign.go` | 第44行注释: `weknora.example.com` | 替换为 `https://htdt.cn/#/index` |

### 2. 向量存储默认名称
| 文件 | 原始内容 | 处理方式 |
|------|---------|---------|
| `internal/types/vectorstore.go` | 默认数据库名 `weknora`，集合前缀 `weknora_embeddings` | 暂不处理 |

---

## 三、功能隐藏

### 1. 登录后首页 - 隐藏左侧菜单
| 文件 | 菜单项 | 处理方式 | 状态 |
|------|--------|---------|------|
| `frontend/src/components/menu.vue` | 智能体 (agents) | 隐藏 | ✅ 已完成 |
| `frontend/src/components/menu.vue` | 共享空间 (organizations) | 隐藏 | ✅ 已完成 |
| `frontend/src/components/menu.vue` | 对话 (creatChat) | 隐藏 | ✅ 已完成 |
| `frontend/src/components/menu.vue` | 成员 | 隐藏 | ✅ 已完成 |
| `frontend/src/components/menu.vue` | 网络搜索 | 隐藏 | ✅ 已完成 |
| `frontend/src/components/menu.vue` | MCP | 隐藏 | ✅ 已完成 |
| `frontend/src/components/menu.vue` | IM | 隐藏 | ✅ 已完成 |
| `frontend/src/components/menu.vue` | Chrome插件 | 隐藏 | ✅ 已完成 |

### 2. 首页账号弹出菜单 - 隐藏菜单项
| 文件 | 菜单项 | 处理方式 | 状态 |
|------|--------|---------|------|
| `frontend/src/components/UserMenu.vue` | 成员管理 (members) | 注释隐藏 | ✅ 已完成 |
| `frontend/src/components/UserMenu.vue` | 网络搜索 (websearch) | 注释隐藏 | ✅ 已完成 |
| `frontend/src/components/UserMenu.vue` | MCP服务 (mcp) | 注释隐藏 | ✅ 已完成 |
| `frontend/src/components/UserMenu.vue` | 已接入的 IM | 注释隐藏 | ✅ 已完成 |
| `frontend/src/components/UserMenu.vue` | Claw Skill | 注释隐藏 | ✅ 已完成 |
| `frontend/src/components/UserMenu.vue` | Chrome 插件 | 注释隐藏 | ✅ 已完成 |
| `frontend/src/components/UserMenu.vue` | GitHub ★ | 注释隐藏 | ✅ 已完成 |

### 3. 设置页面 - 隐藏左侧菜单
| 文件 | 菜单项 | 处理方式 |
|------|--------|---------|
| `frontend/src/views/settings/Settings.vue` | 空间信息 (tenant) | 隐藏 |
| `frontend/src/views/settings/Settings.vue` | 成员管理 (members) | 隐藏 |
| `frontend/src/views/settings/Settings.vue` | WeKnora Cloud (weknoracloud) | 隐藏 |
| `frontend/src/views/settings/Settings.vue` | 网络搜索 (websearch) | 隐藏 |
| `frontend/src/views/settings/Settings.vue` | MCP (mcp) | 隐藏 |
| `frontend/src/views/settings/Settings.vue` | 消息管理 (chathistory) | 隐藏 |
| `frontend/src/views/settings/Settings.vue` | 系统设置 (system) - 内部WeKnora信息（应用版本，UI 版本） | 隐藏 |

---

## 四、链接替换规则

将 `https://github.com/Tencent/` 替换为 `http://171.16.46.5/AI/`

示例：
- `https://github.com/Tencent/WeKnora/blob/main/docs/RBAC%E8%AF%B4%E6%98%8E.md`
- → `http://171.16.46.5/AI/WeKnora/blob/main/docs/RBAC%E8%AF%B4%E6%98%8E.md`

---