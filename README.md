# Smart Doc Platform

> 仓库示例：[shenshuo-maker/smart-doc-platform](https://github.com/shenshuo-maker/smart-doc-platform)

基于 **RAG + LangGraph ReAct Agent** 的企业文档智能问答：支持 PDF 入库、语义检索与持久化向量库，通过 Agent 工具完成**单文档问答**与**多文档对比**，强调依据上下文作答以降低幻觉。

## 功能概览

- **PDF 解析与分块**：`PyPDFLoader` + `RecursiveCharacterTextSplitter`
- **向量检索**：Chroma 持久化（`data/chroma`），嵌入默认 `sentence-transformers/all-MiniLM-L6-v2`（本地推理，无需单独申请嵌入 API）
- **ReAct Agent**：LangGraph `create_react_agent`，工具包括「文档问答」「文档对比」
- **CLI 对话**：`python main.py` 交互式提问，空行或 `quit` / `exit` 退出

## 技术栈

| 类别 | 选型 |
|------|------|
| Agent / RAG | LangChain、LangGraph、Chroma |
| LLM | OpenAI 兼容 API（`langchain-openai`） |
| 文档 | PyPDF |
| 环境 | Python 3.10+ |

## 目录结构

```
smart_doc_platform/
├── main.py              # CLI 对话入口
├── ingest.py            # 入库脚本入口（封装 src/rag）
├── requirements.txt
├── .env.example         # 环境变量模板
├── data/
│   ├── pdfs/            # 放置待索引的 PDF
│   └── chroma/          # 向量库持久化（.gitignore 已忽略）
└── src/
    ├── config.py        # 分块大小、检索条数等可调参数
    ├── agent/           # ReAct 图与工具
    └── rag/             # 解析、切块、向量库
```

## 快速开始

### 1. 安装依赖

```bash
cd smart_doc_platform
pip install -r requirements.txt
```

### 2. 配置环境变量

复制 `.env.example` 为 `.env`，填写 **`OPENAI_API_KEY`**（用于 Agent 推理与答案生成）。可选：`OPENAI_API_BASE`、`OPENAI_MODEL`（见 `.env.example` 注释）。

> 请勿将 `.env` 提交到 Git 仓库。

### 3. 放入 PDF 并建库

将 PDF 放入 `data/pdfs/`，执行：

```bash
python ingest.py
```

若需覆盖同名文件旧索引：

```bash
python ingest.py --replace
```

### 4. 启动对话

```bash
python main.py
```

## 可调参数（控制粒度与幻觉风险）

编辑 `src/config.py` 中的常量即可，例如：

- `CHUNK_SIZE` / `CHUNK_OVERLAP`：分块大小与重叠
- `RETRIEVER_K`：每次检索返回的文档片段条数

## 说明与局限

- 当前为 **命令行** 演示形态，便于本地调试与接入其它前端。
- 回答质量依赖 PDF 内容、分块策略与检索条数；系统提示词要求模型在上下文不足时明确说明「知识库中未找到相关依据」。

## License

如需开源许可，请在仓库根目录自行添加 `LICENSE` 文件。
