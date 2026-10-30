## AI Agent

### 提示词工程

#### 编写明确和具体的指令
- 使用分隔符清楚的知识输入的不同部分
  - `"""`, ` ``` `, `---`, `<>`, `<tag></tag>`
- 要求结构化输出
  - html、json、yaml、markdown、csv、xml
- 要求模型检查是否满足条件
  - 不满足则停止任务
- 少量训练提示
  - 模型执行任务之前提供成功执行任务的示例

#### 给模型足够的时间来思考
- 指定完成任务所需的步骤
- 指示模型在匆忙做出结论之前思考解决方案

#### 幻觉
- 要求模型首先从文本中找到任何相关的引用，基于相关引用回答问题

#### 大模型擅长
- 摘要、推理、转换、扩展(温度)、聊天 

#### 智能体设计模式
- 反射、工具使用、评估、规划、多智能体协作

#### LangChain 标准化调用大模型，与LLM进行交互
- LLM: 接哪个模型都行，换模型基本只改一行代码
- PromptTemplate: 提示词做成模版，不在代码里拼字符串
- Chains: 模型 +提示词 + 后处理，串成一条流程
- Memory: 记住上文，不在每次都是新对话
- Tools & Agents: 自己决定用不用工具，下一步干什么
- Vector Store: 接向量数据库，RAG的底座

#### LangGraph 流程复杂了，怎么编排
- State: 贯穿全程的共享数据
- Node: 一个个具体干活的函数
- Edge: 定义节点之间怎么走
- Graph: 节点和边编排成整张图

#### LangSmith 每一步发生了什么
- Tracing: 每一步都留痕，网页里组成展开回放
- Evaluation: 拿同一批测试集跑分，不靠感觉判断
- Prompt: 照着评估结果改，改一版看一次分

#### RAG 检索增强生成
- 分片: 文档切分成多个片段，按字数、章节、段落、页码等
- 索引: 通过 Embedding 模型将分片转为向量，存入向量数据库
- 召回: Embedding 问题得到问题的向量，去向量数据库中检索相关的近似分片
  - 余弦相似度、欧式距离、点积
- 重排: 将召回的分片按相关性排序，选出最相关的前几个分片
  - cross-encoder
- 生成: 将重排后的分片作为上下文，连同问题一起输入 LLM，生成最终答案

#### RAG 实战
- uv: Python 包管理器
- Jupyter Notebook: 交互式计算环境
- sentence-transformers: 加载 Embedding 模型和 cross-encoder 模型
- chromadb: 向量数据库
- google-genai: Google 的 AI SDK，调用 gemini-2.5-flash 必备
- python-dotenv: 将 Gemini API Key 映射到环境变量中