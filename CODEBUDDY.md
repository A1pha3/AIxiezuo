# AI小说生成系统 - CodeBuddy 开发指南

这是一个基于LangChain的AI小说生成工具，支持多种大语言模型，具备状态管理和Web界面。系统采用模块化架构，支持多小说项目隔离管理、章节状态跟踪和记忆管理。

## 快速开始

### 环境设置
```bash
# 推荐使用 uv 工具管理依赖
curl -LsSf https://astral.sh/uv/install.sh | sh
uv pip install -r requirements.txt

# 或使用传统pip
pip install -r requirements.txt
```

### 配置API密钥
```bash
cp .env.example .env
# 编辑 .env 文件，至少配置一个模型的API密钥（推荐DeepSeek）
```

### 运行应用
```bash
# Web界面启动（推荐）
python start_web.py  # 访问 http://127.0.0.1:5001

# 直接启动Web服务器
python web_server.py  # 访问 http://127.0.0.1:5000

# 命令行测试
python main.py
```

## 核心架构

### 主要组件
- **NovelGenerator** (`main.py:300+`): 核心生成器，统一管理所有功能，包含章节生成、状态管理、记忆管理
- **LLMCaller** (`main.py:100+`): 统一的大模型调用接口，支持多种模型的配置和调用
- **StateManager** (`main.py:200+`): 章节状态和世界设定管理，使用Pydantic模型确保数据一致性
- **MemoryManager** (`main.py:250+`): 分片存储的记忆管理系统，支持压缩和索引
- **Web服务器** (`web_server.py`): Flask API服务，提供Web界面和RESTful API
- **LLMConfigManager** (`main.py:40+`): 模型配置管理器，集中管理各种AI模型的配置

### 数据存储结构
```
data/                           # 状态和设定文件
├── {novel_id}_chapter_{xxx}_state.json    # 章节状态
└── {novel_id}_world_bible_{xx}.json       # 世界设定

xiaoshuo/                       # 生成的小说内容
├── {novel_id}_chapter_{xxx}.txt           # 章节文件
└── zhangjiexigang/{novel_id}/             # 章节细纲

memory/                         # 记忆管理
├── chunks/                     # 分片存储
├── summaries/                  # 压缩摘要
└── {session_id}_index.json     # 会话索引

prompts/                        # 提示词模板
templates/                      # 写作要求模板
web/                           # Web界面文件
```

### 支持的模型
- **DeepSeek** (推荐): `deepseek_chat`, `deepseek_reasoner` - 性价比高
- **OpenAI**: `openai_gpt4`, `openai_gpt35` - 质量稳定
- **Anthropic**: `anthropic_claude` - 长文本处理优秀
- **Google**: `google_gemini` - 多模态支持
- **自定义API**: `dsf5`, `glm` - 支持第三方兼容接口

## 常用开发命令

### 测试和调试
```bash
# 测试核心功能
python main.py

# 启动Web服务器（调试模式）
python web_server.py  # 默认启用debug模式，修改代码自动重载

# 启动Web界面
python start_web.py
```

### 依赖管理
```bash
# 使用uv管理依赖（推荐）
uv pip install package_name
uv pip freeze > requirements.txt

# 传统pip方式
pip install package_name
pip freeze > requirements.txt
```

### 数据清理
```bash
# 清理生成的数据（谨慎使用）
rm -rf data/*.json
rm -rf xiaoshuo/*.txt
rm -rf memory/chunks/*
```

## 开发工作流

### 生成章节
```python
from main import NovelGenerator

generator = NovelGenerator()
content = generator.generate_chapter(
    chapter_outline="第一章：开始的故事",
    model_name="deepseek_chat",
    novel_id="my_novel",
    use_state=True,
    use_world_bible=True,
    update_state=True
)
```

### 状态管理
```python
# 加载最新状态
state = generator.state_manager.load_latest_state("novel_id")

# 更新状态
new_state = generator.update_state(
    chapter_content=content,
    current_state=state,
    novel_id="novel_id"
)
```

### 记忆管理
```python
# 保存对话消息
generator.memory_manager.save_message(session_id, message)

# 加载历史记录
history = generator.memory_manager.load_recent_messages(
    session_id, count=20, use_compression=True
)

# 压缩记忆分片
generator.compress_memory_chunk(session_id, chunk_index)
```

## API接口

### 核心端点
- `POST /api/generate` - 生成章节内容
- `GET /api/novels` - 获取小说列表
- `GET /api/novels/{novel_id}/info` - 获取小说详细信息
- `POST /api/save-chapter` - 保存章节到文件
- `POST /api/update-state` - 手动更新角色设定

### 模板管理
- `GET /api/templates` - 获取模板列表
- `POST /api/templates` - 保存模板
- `GET /api/template-file/{filename}` - 获取模板文件内容

### 设定管理
- `GET /api/settings/{novel_id}` - 获取设定文件列表
- `GET /api/settings/{novel_id}/character/{version}` - 获取人物设定
- `GET /api/settings/{novel_id}/world/{version}` - 获取世界设定

## 关键特性

### 多小说项目管理
系统通过 `novel_id` 参数实现多小说项目隔离，每个小说有独立的：
- 章节状态文件
- 世界设定文件
- 章节内容文件
- 记忆会话

### 状态跟踪系统
使用Pydantic模型定义严格的状态结构：
- `ChapterState`: 章节状态（主角、物品、关系、剧情）
- `Protagonist`: 主角信息
- `InventoryItem`: 物品信息
- `Relationship`: 人物关系

### 记忆管理
采用分片存储和压缩机制：
- 每100条消息为一个分片
- 支持实时压缩和预存储压缩
- 索引文件管理分片元数据

### 模板系统
支持自定义写作模板：
- `writer_role.txt`: 作者角色设定
- `writing_rules.txt`: 写作规则
- `update_state_rules.txt`: 状态更新规则

## 重要开发注意事项

### 模型配置
- **禁止修改** `LLMConfigManager.get_config()` 中的模型配置，这些是用户自定义的固定配置
- 新增模型需要在配置字典中添加对应的provider、model、api_key等信息
- 所有API密钥通过环境变量管理，不得硬编码

### 状态管理
- 使用Pydantic模型确保数据结构一致性
- 状态文件命名规范：`{novel_id}_chapter_{xxx}_state.json`
- 世界设定文件：`{novel_id}_world_bible_{xx}.json`

### 记忆管理
- 每100条消息为一个分片，存储在 `memory/chunks/` 目录
- 支持实时压缩和预存储压缩机制
- 索引文件管理分片元数据：`{session_id}_index.json`

## 文件命名规范

- 章节文件: `{novel_id}_chapter_{xxx}.txt`
- 状态文件: `{novel_id}_chapter_{xxx}_state.json`
- 世界设定: `{novel_id}_world_bible_{xx}.json`
- 模板文件: `{template_id}_{type}.txt`
- 记忆分片: `{session_id}_chunk_{xxx}.json`

## 配置说明

### 模型配置
在 `LLMConfigManager.get_config()` 中定义，包含：
- provider: 模型提供商
- model: 模型名称
- api_key: API密钥
- base_url: API基础URL
- temperature: 生成温度

### 环境变量
- `DEEPSEEK_API_KEY`: DeepSeek API密钥
- `OPENAI_API_KEY`: OpenAI API密钥
- `ANTHROPIC_API_KEY`: Anthropic API密钥
- `GOOGLE_API_KEY`: Google API密钥
- `DSF5_API_*`: 第三方API配置