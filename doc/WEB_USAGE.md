# 小说生成系统 - Web界面使用指南

[详细技术文档](./doc/code_analyze.md)

## 🚀 快速开始

### 1. 启动服务器
```bash
python start_web.py
```

### 2. 访问界面
打开浏览器访问: http://localhost:5001

> **注意**: 默认端口为5001，如需修改请编辑 `start_web.py` 文件

## ⚙️ 系统要求

### 环境依赖
- Python 3.8+
- 必需的Python包：
  - flask
  - flask-cors
  - langchain-openai
  - langchain-anthropic (可选)
  - langchain-google-genai (可选)
  - python-dotenv
  - pydantic

### 环境配置
创建 `.env` 文件并配置API密钥：
```env
# DeepSeek API（必需）
DEEPSEEK_API_KEY=your_deepseek_api_key

# 其他可选API
OPENAI_API_KEY=your_openai_api_key
ANTHROPIC_API_KEY=your_anthropic_api_key
GOOGLE_API_KEY=your_google_api_key

# 自定义API配置
DSF5_API_KEY=your_custom_api_key
DSF5_API_URL=your_custom_api_url
DSF5_API_MODEL=your_custom_model_name
```

## 🖥️ 界面技术栈

- **后端**: Flask + Python
- **前端**: 原生HTML + CSS + JavaScript
- **API通信**: RESTful API + JSON
- **文件管理**: 本地文件系统

## 📝 功能介绍

### 一、模版管理
创建和管理提示词模版，包含三个核心文件：
- **角色定义** (writer_role.txt): 定义AI的身份和写作风格
- **写作规则** (writing_rules.txt): 设定执行规则和输出格式  
- **状态更新规则** (update_state_rules.txt): 指导状态JSON的更新逻辑

### 二、小说生成 ⭐ **核心功能**

#### 📖 小说ID管理
- **输入小说ID**: 为每个小说项目设置唯一标识符（如: `novel001`, `fantasy_story`）
- **加载按钮**: 检查指定ID的小说是否存在，显示当前状态信息
- **列表按钮**: 查看所有已存在的小说项目
- **状态隔离**: 每个小说ID拥有独立的状态文件和设定

#### 🎯 生成配置
- **模版选择**: 选择预设的提示词模版
- **模型选择**: 支持多种AI模型（DeepSeek、GPT等）
- **生成设置**: 控制状态和世界设定的使用
- **章节内容设置**: ⭐ **新增功能** - 控制是否读取前面章节的实际文件内容
- **章节数量**: 设置读取前面几章的内容（1-10章）

#### 📖 章节内容功能详解 ⭐ **重要功能**
- **读取前面章节内容**: 启用后，系统会从xiaoshuo目录读取前面已保存的章节文件
- **智能衔接**: 确保前后章节内容的逻辑一致性和情节连贯性
- **实时同步**: 基于最新的章节文件内容生成，确保内容同步

#### 📋 章节计划
输入JSON格式的章节计划，例如：
```json
{
  "chapter_index": 1,
  "title": "第一章：觉醒",
  "main_plot": "主角发现自己的特殊能力",
  "chapter_outline": [
    "平凡的日常生活",
    "意外事件触发", 
    "能力觉醒时刻",
    "新世界的预告"
  ],
  "target_word_count": 2500,
  "mood": "神秘紧张"
}
```

#### 📄 计划字段说明
- **chapter_index**: 章节编号（必需）
- **title**: 章节标题（可选，建议提供）
- **main_plot**: 主要情节概述（必需）
- **chapter_outline**: 详细章节大纲数组（必需）
- **target_word_count**: 目标字数（可选，默认2000-3000字）
- **mood**: 章节氛围（可选，帮助AI把握写作风格）

### 三、支持的AI模型

| 模型名称 | 提供商 | 说明 | 环境变量 |
|---------|--------|------|----------|
| deepseek_chat | DeepSeek | 默认推荐模型 | DEEPSEEK_API_KEY |
| deepseek_reasoner | DeepSeek | 推理专用模型 | DEEPSEEK_API_KEY |
| openai_gpt4 | OpenAI | GPT-4模型 | OPENAI_API_KEY |
| openai_gpt35 | OpenAI | GPT-3.5模型 | OPENAI_API_KEY |
| anthropic_claude | Anthropic | Claude模型 | ANTHROPIC_API_KEY |
| google_gemini | Google | Gemini模型 | GOOGLE_API_KEY |
| dsf5 | 自定义 | 用户自定义API | DSF5_API_KEY, DSF5_API_URL |

### 四、API接口文档

#### 核心API端点

##### 健康检查
```http
GET /api/health
```

##### 获取模版列表
```http
GET /api/templates
```

##### 保存模版
```http
POST /api/templates
Content-Type: application/json

{
  "id": "template_id",
  "name": "模版名称",
  "files": {
    "writer_role": "filename1.txt",
    "writing_rules": "filename2.txt",
    "update_state_rules": "filename3.txt"
  },
  "contents": {
    "writer_role": "角色定义内容",
    "writing_rules": "写作规则内容",
    "update_state_rules": "状态更新规则内容"
  }
}
```

##### 生成小说
```http
POST /api/generate
Content-Type: application/json

{
  "template_id": "001",
  "chapter_outline": {...},
  "model_name": "deepseek_chat",
  "novel_id": "my_novel",
  "use_state": true,
  "use_world_bible": true,
  "update_state": false,
  "use_previous_chapters": true,
  "previous_chapters_count": 2
}
```



## 🗂️ 文件组织结构

### 统一ID隔离机制 ⭐ **简化设计**
使用小说ID作为唯一标识符，统一管理所有相关数据：

```
data/                                    # 状态和设定文件
├── novel001_chapter_001_state.json     # 小说001的第1章状态
├── novel001_chapter_002_state.json     # 小说001的第2章状态  
├── novel001_world_bible_00.json        # 小说001的世界设定
├── fantasy_chapter_001_state.json      # fantasy小说的第1章状态
└── fantasy_world_bible_00.json         # fantasy小说的世界设定

xiaoshuo/                                # 章节内容文件
├── zhangjiexigang/                      # 作者分类目录
│   ├── 003/                             # 小说003的章节
│   │   ├── 2.txt
│   │   ├── 3.txt
│   │   └── 4.txt
│   └── 007/                             # 小说007的章节
│       ├── 1.txt
│       ├── 2.txt
│       └── ...
├── novel001_chapter_001.txt             # 直接存储的章节文件
├── novel001_chapter_002.txt             # 小说001的第2章内容
├── fantasy_chapter_001.txt              # fantasy小说的第1章内容
└── novel001_novel_20240115_143022.txt   # 导出的完整内容



versions/                                # 多版本文件
├── novel001_chapter_1_versions.json    # 小说001第1章的多版本
└── fantasy_chapter_1_versions.json     # fantasy小说第1章的多版本
```

### 设计优势
- **统一管理**: 一个小说ID对应一套完整的数据（状态+文件）
- **逻辑清晰**: 不再需要区分"小说ID"和"会话ID"
- **用户友好**: 界面更简洁，只需要一个ID输入框
- **数据一致性**: 同一小说的所有相关数据都用同一个ID管理

### 兼容性说明
- 如果不输入小说ID，系统将使用默认标识符（向后兼容）
- 现有的无ID文件仍可正常读取和使用

## ⚙️ 高级功能

### 状态管理
- **自动状态跟踪**: 系统会自动跟踪以下状态信息：
  - 角色属性（姓名、年龄、等级、性格等）
  - 物品清单（道具、装备等）
  - 人际关系（与其他角色的关系状态）
  - 当前情节摘要
- **状态文件格式**: JSON格式，便于编程处理
- **增量更新**: 每章生成后可选择性更新状态

### 多版本生成 (实验性功能)
- 支持为同一章节生成多个不同版本
- 版本文件存储在 `versions/` 目录
- 便于选择最佳内容或进行对比分析

### 章节内容管理
- **前置章节读取**: 可读取前面已生成的章节内容
- **内容连贯性**: 确保故事情节的逻辑连贯
- **智能衔接**: 基于前文内容生成后续章节

### 模版系统
- **三文件结构**: 
  - `writer_role.txt`: AI角色定义
  - `writing_rules.txt`: 写作规则和格式要求
  - `update_state_rules.txt`: 状态更新逻辑
- **模版索引**: 通过 `template_index.json` 管理所有模版
- **动态加载**: 支持运行时加载和切换模版



## 🔧 故障排除

### 常见问题

#### 🔧 启动相关问题
1. **依赖包缺失**: 
   ```bash
   pip install flask flask-cors langchain-openai python-dotenv pydantic
   ```

2. **环境变量未配置**: 
   - 确保 `.env` 文件存在
   - 检查API密钥是否正确设置
   - 验证API密钥是否有效

3. **端口被占用**: 
   - 默认端口5001被占用时，修改 `start_web.py` 中的端口设置
   - 或者终止占用端口的进程

#### 🚨 生成相关问题
1. **API连接失败**: 
   - 确保后端服务已启动（看到"🌐 访问地址: http://localhost:5001"）
   - 检查网络连接和防火墙设置
   - 验证API服务商的服务状态

2. **生成内容质量差**: 
   - 检查章节大纲是否足够详细
   - 尝试调整模版内容
   - 更换AI模型

3. **状态文件错误**: 
   - 检查 `data/` 目录权限
   - 验证JSON格式是否正确
   - 删除损坏的状态文件重新生成

4. **模版加载失败**: 
   - 确认 `templates/` 目录存在且有读写权限
   - 检查模版文件编码（应为UTF-8）
   - 验证 `template_index.json` 格式

#### 📂 文件相关问题
1. **小说ID冲突**: 使用唯一的小说ID避免冲突
2. **章节文件丢失**: 检查 `xiaoshuo/` 目录和文件权限
3. **数据备份**: 定期备份 `data/` 和 `xiaoshuo/` 目录

### 日志查看
启动时查看详细日志信息：
```bash
# 启动时的详细日志
python start_web.py

# 如果遇到问题，可以启用调试模式
# 编辑 start_web.py，将 debug=False 改为 debug=True
```

#### 日志说明
- ✅ 表示操作成功
- ⚠️ 表示警告信息（不影响运行）
- ❌ 表示错误（需要处理）
- 🔍 表示检查过程
- 🚀 表示服务启动

## 🎯 使用技巧

### 界面操作流程
1. **首次使用**:
   - 启动服务 → 访问界面 → 创建模版 → 设置小说ID → 开始生成

2. **继续创作**:
   - 启动服务 → 输入小说ID → 点击加载 → 选择模版 → 生成下一章

3. **模版管理**:
   - 创建新模版 → 测试效果 → 调整优化 → 保存使用

### 生成参数建议
- **首章生成**: 关闭"使用状态"和"读取前面章节"
- **续写章节**: 开启"使用状态"和"读取前面章节"
- **状态更新**: 每3-5章更新一次状态，保持故事连贯性
- **章节字数**: 建议设置在2000-3000字，确保内容充实

## 💡 最佳实践

### 小说ID命名建议
- 使用有意义的名称：`scifi_adventure`, `romance_novel`
- 避免特殊字符，推荐使用字母、数字、下划线
- 保持简短但具有描述性

### 章节计划编写
- 明确章节目标和情节发展
- 设置合理的字数目标（建议2000-3000字）
- 包含足够的细节指导AI创作

### 状态管理
- 定期检查和更新角色状态
- 保持状态信息的一致性和逻辑性
- 备份重要的状态文件



## 📚 相关文档

- [开发文档](./DEV_DOC.md) - 系统架构和开发指南
- [模版指南](./TEMPLATE_GUIDE.md) - 提示词模版创建指南
- [代码分析](./code_analyze.md) - 详细技术分析
- [环境配置](./uv_setup_guide.md) - 使用uv管理Python环境

## 🤝 支持与反馈

### 问题反馈
如果您在使用过程中遇到问题，请：
1. 查看本文档的故障排除章节
2. 检查控制台输出的错误信息
3. 备份重要数据后尝试重新启动服务

### 功能建议
欢迎提出改进建议：
- 新的AI模型支持
- 界面功能优化
- 模版功能增强
- 导出格式扩展

### 版本信息
- 当前版本: 1.0
- 最后更新: 2024-01-15
- 兼容性: Python 3.8+

---

🎭 **小说生成系统** - 让AI助力你的创作之旅！

> **温馨提示**: 本系统完全基于本地运行，您的创作内容和API密钥都保存在本地，确保数据安全。