# 📄 LLM-Patent-Classifier

## 💡 项目简介 (Introduction)

本项目是一个基于大语言模型（LLM）的自动化文献与专利筛选系统。本系统最初为解决复杂的 **AI 专利主题分类与打标** 任务而设计，通过调用外部主流大模型（如 DeepSeek、通义千问等）的 API，对专利摘要进行深度的语义推理，从而判断其是否属于特定的技术领域。

系统告别了传统的“关键词匹配”以及常规深度学习模型等模式，引入了多种先进的提示词工程（Prompt Engineering）策略。它能够在缺乏明显关键词的情况下，精准捕捉技术实质，非常适合技术情报分析、学术研究以及专利挖掘。

---

## ✨ 核心特性 (Key Features)

* **🧠 多策略提示工程 (Multi-Strategy Prompting)**
* **Zero-Shot**: 极速推理，直接输入判定规则与摘要进行零样本分类。
* **Few-Shot**: 内置动态样本召回机制，通过 `ChineseBERT` 将专利文本向量化，自动从训练库中检索与待测样本语义最相似的正负例作为上下文（In-Context Learning），大幅提升复杂判例的准确率。
* **Role-Based**: 角色扮演模式，赋予 LLM “资深专利审查员”身份，输出更符合领域逻辑的专业判断。


* **🛡️ 鲁棒的工程设计 (Robust Engineering)**
* **多数表决机制 (Majority Voting)**: 支持对单条数据进行多次 API 请求并进行投票，有效降低 LLM 幻觉带来的随机误差。
* **指数退避重试 (Exponential Backoff)**: 针对大批量并发请求设计的网络容错机制，确保在 API 限流或网络波动下平稳运行。


* **🔌 高度可扩展 (Highly Extensible)**
* **无缝领域迁移**: 当前内置的 Prompt 是针对“AI相关专利”深度定制的。但只需在代码中修改核心的 `AI_DEFINITION_CORE` 变量，即可无缝迁移至固态电池、生物医药、自动驾驶等任何其他技术领域。
* **兼容主流 LLM**: 接口封装规范，轻松适配 OpenAI 格式的各类大模型 API。



---

## 🛠️ 技术栈 (Tech Stack)

* **核心语言**: Python
* **数据处理**: `pandas`, `numpy`
* **向量检索 & NLP**: `PyTorch`, `Transformers` (HuggingFace), `scikit-learn`, `ChineseBERT-base`
* **API 交互**: `requests`

---

## 🚀 快速开始 (Quick Start)

### 1. 环境安装

克隆本仓库后，安装必要的依赖项：

```bash
pip install pandas numpy torch transformers scikit-learn tqdm python-dotenv requests

```

### 2. 数据准备

请在项目目录下准备您的数据文件（CSV 格式，需包含 `orig_index`, `abs`, `label` 三列）：

* `train_data.csv`: 用于 Few-Shot 策略的本地检索库。
* `test_data.csv`: 需要进行预测的独立测试集。

### 3. API 配置

在代码的配置区或通过 `.env` 文件填入您的大模型 API 密钥（以 `deepseek-reasoner` 为例）：

```python
DEEPSEEK_API_URL = "https://api.deepseek.com/chat/completions"
DEEPSEEK_MODEL   = "deepseek-reasoner"
DEEPSEEK_API_KEY = "your_api_key_here"

```

### 4. 运行推理

打开 Jupyter Notebook (`LLM-based model.ipynb`)，根据需要选择提示词策略 (`PROMPT_STRATEGY`) 和投票次数 (`VOTE_N`)，顺序执行单元格。预测结果将自动导出为 `test_preds_xxx.csv` 文件。

---

## 📊 指标与评估 (Evaluation)

系统内置了基于 `scikit-learn` 的评估模块，在测试集推理完成后，会自动计算并汇出 Accuracy, Precision, Recall 和 F1-Score 等核心学术指标，方便直接写入研究报告或论文中。
