# 🎭 小说生成系统 - 专业使用指南

## 📖 文档概览

| 章节 | 内容 | 目标读者 |
|------|------|----------|
| [🚀 快速开始](#-快速开始) | 5分钟上手指南 | 新用户 |
| [⚙️ 环境配置](#️-环境配置) | 系统要求和API设置 | 所有用户 |
| [🖥️ 界面详解](#️-界面详解) | 功能模块详细说明 | 中级用户 |
| [📝 核心功能](#-核心功能) | 生成流程和技术原理 | 高级用户 |
| [🔧 故障排除](#-故障排除) | 常见问题解决方案 | 运维人员 |
| [🎯 最佳实践](#-最佳实践) | 专业使用技巧 | 专业作者 |

## 🚀 快速开始

### 1. 环境准备
```bash
# 克隆项目（如未完成）
git clone <repository-url>
cd AIxiezuo

# 安装依赖
pip install flask flask-cors langchain-openai python-dotenv pydantic
```

### 2. 配置API密钥
创建 `.env` 文件：
```env
# DeepSeek API（必需）
DEEPSEEK_API_KEY=sk-your-deepseek-api-key-here

# 可选API（至少配置一个）
OPENAI_API_KEY=sk-your-openai-api-key
ANTHROPIC_API_KEY=sk-your-anthropic-api-key
GOOGLE_API_KEY=your-google-api-key
```

### 3. 启动服务
```bash
python start_web.py
```

### 4. 访问界面
🌐 打开浏览器访问: http://localhost:5001

---

## ⚙️ 环境配置

### 系统要求
- **Python**: 3.8+ （推荐3.10+）
- **内存**: 至少4GB RAM
- **存储**: 至少1GB可用空间
- **网络**: 稳定的互联网连接（用于API调用）

### 支持的AI模型

| 模型 | 提供商 | 推荐场景 | 性能特点 |
|------|--------|----------|----------|
| `deepseek_chat` | DeepSeek | 通用写作 | 性价比高，中文优化 |
| `deepseek_reasoner` | DeepSeek | 复杂剧情 | 逻辑推理能力强 |
| `openai_gpt4` | OpenAI | 高质量创作 | 创意丰富，质量最高 |
| `openai_gpt35` | OpenAI | 快速生成 | 响应速度快 |
| `anthropic_claude` | Anthropic | 长文本 | 上下文长度大 |
| `google_gemini` | Google | 多模态 | 支持图片输入 |

### 环境变量详解

```env
# 必需配置
DEEPSEEK_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx

# 可选配置（至少配置一个）
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx
ANTHROPIC_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx  
GOOGLE_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxx

# 高级配置（自定义API）
DSF5_API_KEY=your_custom_key
DSF5_API_URL=https://api.example.com/v1/chat/completions
DSF5_API_MODEL=custom-model-name

# 系统配置（可选）
PORT=5001  # 修改服务端口
DEBUG=True  # 启用调试模式
```

---

## 🖥️ 界面详解

### 主界面功能模块

#### 1. 📋 模版管理区
- **模版选择**: 下拉选择预设提示词模版
- **模版预览**: 查看选中模版的三个组件内容
- **新建模版**: 创建自定义写作模版

#### 2. 🆔 小说标识区
- **小说ID输入**: 为当前作品设置唯一标识
- **加载状态**: 读取已有小说的状态和设定
- **列表查看**: 显示所有已存在的小说项目

#### 3. 🎯 生成配置区
- **模型选择**: 选择AI模型提供商
- **状态控制**: 启用/禁用状态跟踪功能
- **章节读取**: 设置前面章节内容读取数量（1-10章）

#### 4. 📖 章节计划区
- **JSON编辑器**: 输入详细的章节计划
- **格式验证**: 实时JSON语法检查
- **预设模板**: 提供标准章节计划结构

#### 5. ⚡ 操作控制区
- **生成按钮**: 开始生成章节内容
- **状态显示**: 实时生成进度和结果

### 键盘快捷键
- `Ctrl+Enter` (Mac: `Cmd+Enter`): 快速生成
- `Ctrl+S` (Mac: `Cmd+S`): 保存当前配置
- `Ctrl+L` (Mac: `Cmd+L`): 加载小说状态

---

## 📝 核心功能

### 一、智能状态管理 🧠

#### 状态跟踪维度
```json
{
  "characters": {
    "主角": {
      "name": "林风",
      "age": 22,
      "level": "筑基初期",
      "attributes": {"力量": 15, "敏捷": 18, "智力": 20},
      "skills": ["御剑术", "炼丹术"],
      "relationships": {"师父": "尊敬", "师妹": "保护"}
    }
  },
  "inventory": [
    {"name": "青钢剑", "type": "武器", "quality": "精良"},
    {"name": "回元丹", "type": "消耗品", "count": 3}
  ],
  "plot_summary": "主角发现古老秘境，获得神秘传承",
  "world_state": {
    "current_location": "青云宗",
    "time_period": "仙历302年",
    "major_events": ["魔教复苏", "宗门大比临近"]
  }
}
```

#### 状态更新策略
- **增量更新**: 每章只更新变化的部分
- **一致性检查**: 自动验证状态逻辑合理性
- **版本回溯**: 支持状态文件的历史版本

### 二、章节内容连贯性 📚

#### 内容读取机制
```python
# 伪代码：章节内容读取逻辑
def read_previous_chapters(novel_id, current_chapter, count=3):
    chapters = []
    for i in range(max(1, current_chapter - count), current_chapter):
        chapter_file = f"xiaoshuo/{novel_id}_chapter_{i:03d}.txt"
        if os.path.exists(chapter_file):
            with open(chapter_file, 'r', encoding='utf-8') as f:
                chapters.append(f"第{i}章内容:\n{f.read()}")
    return "\n\n".join(chapters)
```

#### 连贯性保障
1. **情节衔接**: 确保故事发展逻辑连贯
2. **角色一致性**: 保持角色性格和行为模式统一
3. **世界观统一**: 维护设定的物理规则和社会结构

### 三、模版系统架构 🏗️

#### 模版文件结构
```
templates/
├── template_index.json          # 模版元数据
├── 001/                         # 模版ID
│   ├── writer_role.txt          # AI角色定义
│   ├── writing_rules.txt        # 写作规则
│   └── update_state_rules.txt  # 状态更新规则
└── 002/
    └── ...                     # 另一个模版
```

#### 模版索引示例
```json
{
  "001": {
    "name": "仙侠小说模版",
    "description": "适用于修仙、武侠类小说",
    "created_at": "2024-01-15",
    "version": "1.0"
  },
  "002": {
    "name": "都市言情模版", 
    "description": "适用于现代都市爱情故事",
    "created_at": "2024-01-16",
    "version": "1.0"
  }
}
```

---

## 🔧 故障排除

### 常见问题解决方案

#### 🚨 API连接问题
```bash
# 测试API连接
curl -X POST https://api.deepseek.com/v1/chat/completions \
  -H "Authorization: Bearer $DEEPSEEK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-chat","messages":[{"role":"user","content":"Hello"}]}'
```

#### 📂 文件权限问题
```bash
# 修复文件权限
chmod 755 data/ xiaoshuo/ templates/
chmod 644 data/*.json xiaoshuo/*.txt templates/*/*.txt
```

#### 🐍 Python依赖问题
```bash
# 创建虚拟环境（推荐）
python -m venv venv
source venv/bin/activate  # Linux/Mac
# venv\Scripts\activate  # Windows

pip install -r requirements.txt
```

### 错误代码参考

| 错误代码 | 含义 | 解决方案 |
|----------|------|----------|
| `ERR_API_001` | API密钥无效 | 检查环境变量配置 |
| `ERR_FILE_002` | 文件权限不足 | 调整文件权限 |
| `ERR_JSON_003` | JSON格式错误 | 验证输入格式 |
| `ERR_TEMPLATE_004` | 模版加载失败 | 检查模版文件存在 |

### 调试模式
```python
# 启用详细日志
import logging
logging.basicConfig(level=logging.DEBUG)

# 或者在启动时添加参数
python start_web.py --debug
```

---

## 🎯 最佳实践

### 创作工作流优化

#### 1. 前期准备阶段
```json
// 章节计划示例 - 第1章
{
  "chapter_index": 1,
  "title": "第一章：天降异象",
  "main_plot": "平凡少年意外获得神秘力量",
  "chapter_outline": [
    "日常生活的描写",
    "意外事件的发生", 
    "能力觉醒的过程",
    "新世界的初步探索"
  ],
  "target_word_count": 2500,
  "mood": "神秘、紧张、充满希望",
  "key_characters": ["主角", "引路人"],
  "key_locations": ["小村庄", "后山秘境"]
}
```

#### 2. 生成参数建议

| 章节类型 | 状态使用 | 章节读取 | 模型选择 | 字数目标 |
|----------|----------|----------|----------|----------|
| 开头章节 | ❌ 关闭 | ❌ 关闭 | `deepseek_chat` | 2000-3000 |
| 发展章节 | ✅ 开启 | ✅ 2-3章 | `openai_gpt4` | 2500-3500 |
| 高潮章节 | ✅ 开启 | ✅ 3-5章 | `deepseek_reasoner` | 3000-4000 |
| 结局章节 | ✅ 开启 | ✅ 5-10章 | `anthropic_claude` | 2000-3000 |

#### 3. 状态管理策略
- **每3章**更新一次角色状态
- **每5章**更新一次世界观设定
- **重要事件**后立即更新相关状态

### 性能优化技巧

#### 减少API调用成本
```python
# 批量处理章节计划
chapter_plans = [
    {"chapter_index": 1, "title": "第一章...", ...},
    {"chapter_index": 2, "title": "第二章...", ...},
    # 最多建议3章批量处理
]

# 使用更高效的模型组合
if chapter_index % 3 == 0:  # 每3章用一次高质量模型
    model_name = "openai_gpt4"
else:
    model_name = "deepseek_chat"
```

#### 文件存储优化
```bash
# 定期清理临时文件
find data/ -name "*_temp_*.json" -mtime +7 -delete
find xiaoshuo/ -name "*_backup_*.txt" -mtime +30 -delete
```

### 质量保障措施

#### 内容审核流程
1. **初稿生成**: 使用标准参数生成内容
2. **人工审核**: 检查情节连贯性和文笔质量
3. **优化重生成**: 针对问题部分调整参数重新生成
4. **最终定稿**: 确认无误后保存最终版本

#### 版本控制策略
```bash
# 备份重要文件
cp -r data/ data_backup_$(date +%Y%m%d_%H%M%S)/
cp -r xiaoshuo/ xiaoshuo_backup_$(date +%Y%m%d_%H%M%S)/
```

---

## 🔄 API接口详解

### 核心端点

#### 生成小说章节
```http
POST /generate
Content-Type: application/json

{
  "template_id": "001",
  "chapter_outline": "第一章：开始\n主角的起源故事\n场景1: 日常生活\n场景2: 意外发生\n场景3: 能力觉醒",
  "model_name": "deepseek_chat",
  "novel_id": "my_novel_001",
  "use_state": true,
  "use_world_bible": false,
  "update_state": true,
  "use_previous_chapters": true,
  "previous_chapters_count": 2
}
```

#### 响应格式
```json
{
  "success": true,
  "data": {
    "chapter_content": "生成的章节内容...",
    "new_state": {"updated": "state data"},
    "chapter_file": "xiaoshuo/my_novel_001_chapter_001.txt",
    "state_file": "data/my_novel_001_chapter_001_state.json",
    "word_count": 2345,
    "generation_time": 45.2
  },
  "warnings": ["状态更新建议: 检查角色关系一致性"]
}
```

### 错误处理
```json
{
  "success": false,
  "error": {
    "code": "ERR_API_001",
    "message": "API密钥无效",
    "details": "请检查DEEPSEEK_API_KEY环境变量"
  },
  "suggestions": [
    "验证API密钥格式",
    "检查网络连接",
    "确认API服务商额度"
  ]
}
```

---

## 📊 监控与日志

### 日志级别配置
```python
# 日志配置示例
logging.config.dictConfig({
    'version': 1,
    'formatters': {
        'detailed': {
            'format': '%(asctime)s %(levelname)-8s %(name)-15s %(message)s'
        }
    },
    'handlers': {
        'file': {
            'class': 'logging.FileHandler',
            'filename': 'logs/app.log',
            'formatter': 'detailed',
        },
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'detailed',
        }
    },
    'root': {
        'level': 'INFO',
        'handlers': ['file', 'console']
    }
})
```

### 关键监控指标
- **API响应时间**: < 30秒为正常
- **生成字数**: 2000-4000字/章节
- **成功率**: > 95%为良好
- **资源使用**: 内存<500MB, CPU<30%

---

## 🛡️ 安全建议

### 数据保护
- 🔒 API密钥存储在 `.env` 文件中，不要提交到版本控制
- 📁 定期备份 `data/` 和 `xiaoshuo/` 目录
- 🚫 不要将敏感信息写入章节内容

### 访问控制
```bash
# 限制网络访问（生产环境）
python start_web.py --host 127.0.0.1 --port 5001
```

### 合规使用
- ✅ 遵守各AI服务商的使用条款
- ✅ 尊重版权和知识产权
- ✅ 人工审核生成内容后再发布

---

## 🔮 未来规划

### 即将推出的功能
- [ ] **多语言支持**: 英文、日文等语言生成
- [ ] **可视化编辑**: 图形化情节设计界面
- [ ] **协作功能**: 多作者协同创作
- [ ] **导出格式**: EPUB、PDF、Word文档导出
- [ ] **插件系统**: 扩展功能和集成第三方服务

### 技术路线图
1. **性能优化**: 减少API调用，提高生成速度
2. **质量提升**: 增强内容连贯性和文学性
3. **用户体验**: 简化操作流程，降低使用门槛
4. **生态建设**: 建立模版市场和作者社区

---

## 🤝 支持与贡献

### 获取帮助
- 📚 查看本文档和代码注释
- 🐛 提交Issue报告问题
- 💡 提出新功能建议

### 贡献指南
1. Fork项目仓库
2. 创建特性分支 (`git checkout -b feature/amazing-feature`)
3. 提交更改 (`git commit -m 'Add amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建Pull Request

### 社区资源
- 📖 示例模版库
- 🎥 视频教程
- 👥 作者交流群组
- 🏆 创作比赛活动

---

## 📄 版本信息

- **当前版本**: 2.0.0
- **最后更新**: 2024-01-15
- **Python要求**: 3.8+
- **许可证**: MIT License

---

🎉 **开始你的创作之旅吧！**

> 💡 提示：建议先从简单的短篇故事开始，熟悉系统功能后再尝试长篇创作。
> 
> 🌟 记住：AI是辅助工具，你的创意和指导才是作品成功的关键！