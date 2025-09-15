# AIxiezuo 项目文档结构

本目录包含AIxiezuo AI小说生成系统的完整文档，按功能和用途分类整理。

## 📁 目录结构

### 📖 guides/ - 用户指南
用户使用和环境配置相关的指南文档
- `WEB_USAGE.md` - Web界面使用指南，包含功能详解、最佳实践和故障排除
- `uv_setup_guide.md` - UV工具链安装配置指南

### 🔧 development/ - 开发文档  
开发者相关的技术文档和开发指南
- `DEV_DOC.md` - 开发文档，包含架构设计、API接口和使用方法
- `TEMPLATE_GUIDE.md` - 模板系统说明，包含模板结构和使用规范

### 🏗️ architecture/ - 架构文档
系统架构和技术分析相关文档
- `code_analyze.md` - 详细的代码分析和技术文档，包含模块功能说明和API接口

### 🤖 prompts/ - 提示词文档
AI提示词设计和角色定义相关文档
- `alpha_prompt_role.md` - AI开发助手角色定义"Archon"，专门为AIxiezuo项目服务
- `alpha_prompt.md` - 文档增强说明，包含技术背景和原理解释要求

## 📋 文档分类说明

### 按用户类型分类
- **新手用户**: 建议从 `guides/` 目录开始阅读
- **开发者**: 重点关注 `development/` 和 `architecture/` 目录
- **系统管理员**: 参考 `guides/uv_setup_guide.md` 进行环境配置
- **AI工程师**: 查看 `prompts/` 目录了解提示词设计

### 按文档类型分类
- **使用指南**: `guides/WEB_USAGE.md`
- **安装配置**: `guides/uv_setup_guide.md`
- **开发指南**: `development/DEV_DOC.md`, `development/TEMPLATE_GUIDE.md`
- **技术分析**: `architecture/code_analyze.md`
- **AI设计**: `prompts/alpha_prompt_role.md`, `prompts/alpha_prompt.md`

## 🚀 快速导航

### 我想要...
- **快速上手使用系统** → `guides/WEB_USAGE.md`
- **配置开发环境** → `guides/uv_setup_guide.md`
- **了解系统架构** → `architecture/code_analyze.md`
- **进行二次开发** → `development/DEV_DOC.md`
- **自定义模板** → `development/TEMPLATE_GUIDE.md`
- **优化AI提示词** → `prompts/alpha_prompt_role.md`

## 📝 文档维护

### 更新原则
1. **用户指南**优先保持最新，确保用户体验
2. **开发文档**随代码变更同步更新
3. **架构文档**在重大重构时更新
4. **提示词文档**根据AI效果优化调整

### 贡献指南
- 新增文档请放入对应分类目录
- 更新现有文档请保持格式一致
- 重要变更请同步更新本README

---

*最后更新: 2024-01-15*
*文档版本: v2.0*