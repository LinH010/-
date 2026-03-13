# 1.专业名词

## 1.1 NLP(Natural Language Processing) :自然语言处理

### 1.1.1 其中有一项关键技术称为Transformer。这是一种先进的神经网络模型，是现如今AI高速发展的最主要原因。

## 1.2 LLM(Large Language Models):大模型,GPT、DeepSeek底层都是采用
Transformer神经网络模型。

## 1.3 大模型应用开发技术方案

### 1.3.1 纯Prompt回答:通过Prompt提问来完成业务

1. 用途:用于解决一些简单的问题(AI对话、文本摘要分析等)

2. 使用方式：与大模型进行一问一答


![image-20260125143409429](E:\TyporaInfo\工作\image\image-20260125143409429.png)

### 1.3.2 Agent + Function Calling:函数调用

1. 用途:用于解决当问题复杂到纯Prompt无法直接给出结果的情况

2. 使用方式：

   2.1 AI应用将复杂问题拆解为多个简单子问题,
   2.2 每个子问题使用纯Prompt方式得到结果

   2.3 综合各个子问题的结果后生成Prompt给大模型后得到结果.



![image-20260125151425363](E:\TyporaInfo\工作\image\image-20260125151425363.png)

### 1.3.3 RAG(Retrieval Augmented Generation)：检索增强生成

1. 用途:用于解决大模型的知识库不符合需要(太旧、缺少专业领域知识、需要降低模型幻觉)的情况下。

2. 使用方式：

    2.1 根据用户提出的问题从外挂的知识库中搜索知识片段
    2.2 通过知识片段生成Prompt
    
    2.3 用Prompt向大模型发出请求，得到结果

![image-20260125152207999](E:\TyporaInfo\工作\image\image-20260125152207999.png)

3. RAG具体工作流程:

   3.1 离线流程(时时刻刻都在执行):从外部收集知识、将字符串向量化后存入<u>向量数据库</u>

   3.2 在线流程:将用户提出的问题<u>向量化</u>后与向量数据库的中的片段根据<u>相似度</u>进行匹配，匹配出最相似的片段后与问题进行合并生成Prompt发给LLM后得到结果。

![image-20260130143723379](E:\TyporaInfo\工作\image\image-20260130143723379.png)

4. 向量：向量（Vector）就是文本的，“数学身份证”：它把一段文字的**语义信息**，转换成一串固定长度的**数字列表**， **文字=>数字**
   让计算机能“看懂”文字的含义并做相似度计算。简单来说，就是让计算机更方便的理解不同的文本内容，是否表述的是一个意思。

   ## 如何让文本转为向量 ？

   4.1 文本嵌入模型:（如text-embedding-v1）通过深度学习等技术，从文本提取**语义特征**并映射为固定长度的**数字序列**

  ## 已经转化为向量了,如何匹配向量？

​       4.2 余弦相似度**余弦相似度的本质是衡量两个向量之间的夹角，而不关注它们的大小。这使得它特别适用于衡量文本、图像特征向量等数据的相似性**。

![image-20260130151400399](E:\TyporaInfo\工作\image\image-20260130151400399.png)

   4.2.1 例如，向量A:[0.2,0.5,0.8] 向量B:[0.18,0.52,0.79] 算它们的余弦相似度。结果0.9995167140919307

```python
import math
a = [0.2,0.5,0.8]
b = [0.18,0.52,0.79]


x = sum(a * b for a, b in zip(a, b))
y1 = math.sqrt(sum(a1 ** 2 for a1 in a))
y2 = math.sqrt(sum(b1 ** 2 for b1 in b))


y = y1 * y2

# ans:0.9995167140919307
# 余弦相似度 = 向量的点积 / 各自的模长乘积
ans = x / y
print(ans)
```

4.3 向量的维度:是衡量语义匹配是否精准的重要指标。

- 当维度越高时，就更好的记录文本的语义特征，做语义匹配会更加精准
- 维度最高时，在计算、存储、匹配的过程中成本也相应的增加
- 向量维度需要**精准**与**性能**之间做平衡
- 一般1536维度算是较好的选择(2026/1/30)

![image-20260130152307821](E:\TyporaInfo\工作\image\image-20260130152307821.png)

### 1.3.4 Fine-tuning:模型微调(暂时不管，对程序员来说要求过高)

1. 用途:当大模型无法满足特定场景的需求时。

2. 使用方式：

## 1.2 Agent智能体

### 1.2.1 基本定义

`AI Agent`（人工智能代理）是一种能够**感知环境**、**自主决策**并**执行动作**的智能实体。与传统AI系统不同，`Agent`不仅能回答问题，还能主动完成一系列复杂任务。

简单来说，如果把`大语言模型`（`LLM`）比作一个"超级大脑"，那么`AI Agent`就是给这个大脑装上了"手脚"和"工具"，让它能够像人类一样主动行动，而不仅仅是被动回答问题。
### 1.2.2 关键特性

- ✅ **自主性**：能在没有人类直接干预的情况下运作

- ✅ **反应性**：对周围环境和接收到的信息作出及时响应

- ✅ **目标导向**：拥有明确的目标或任务，并为之努力

- ✅ **学习能力**：通过经验不断改进自身的性能和策略

### 1.2.3 与传统AI的区别

传统AI：像个听话的工具，你说"跳"，它就跳一下 AI Agent：像个有主动性的助手，你给个目标，它自己规划怎么跳、跳多高

![image-20260301084123116](E:\TyporaInfo\工作\image\image-20260301084123116.png)

### 1.2.4 ReAct行动框架

**ReAct（Reasoning + Acting）** 是一种结合**推理**与**行动**的 AI Agent 工作模式，由 Google Research 在 2022 年提出。它通过 **Thought → Action → Observation** 循环，让大语言模型（LLM）在解决问题时既能思考，又能调用外部工具获取实时信息，从而减少幻觉、提升复杂任务处理能力。

**核心循环机制**

1. **Thought（思考）**：分析问题，规划下一步。例如：“我需要知道北京今天的 PM2.5 数据。”
2. **Action（行动）**：调用外部工具（搜索引擎、API、计算器等）执行任务。
3. **Observation（观察）**：接收工具返回的结果，并基于此进入下一轮思考。 循环持续，直到得到最终答案。

**优势**

- **减少幻觉**：依赖实时数据而非仅凭记忆回答。
- **处理实时信息**：可获取最新天气、新闻、股价等。
- **多步推理**：将复杂任务拆解为可执行步骤。
- **可解释性强**：显式展示推理过程，便于调试。

**Prompt 设计要点**

- **角色定义**：明确 AI 是可思考、可行动的 Agent。

- **任务描述**：说明目标与约束条件。

- **工具列表**：列出可用工具及调用格式。

- **格式要求**：规定 Thought、Action、Observation 的输出格式。

- **示例**：提供完整循环示例帮助模型学习。、

  ```python
  from langchain.agents import create_agent
  from langchain_community.chat_models import ChatTongyi
  from langchain_core.tools import tool
  
  
  @tool(description="获取身高,返回int,单位厘米")
  def get_height() -> int:
      return 170
  
  
  @tool(description="获取体重,返回int,单位公斤")
  def get_weight():
      return 80
  
  
  agent = create_agent(
      model=ChatTongyi(model="qwen3-max-2026-01-23"),
      tools=[get_weight, get_height],
      system_prompt="""你是严格遵循ReAct框架的智能体，必须按「思考→行动→观察」的流程解决问题，
  且**每轮仅能思考并调用1个工具**，禁止单次调用多个工具。
  并告知我你的思考过程，工具的调用原因，按思考、行动、观察三个结构告知我""",
  )
  
  res = agent.stream(
      {"messages": [{"role": "user", "content": "计算我的BMI"}, ]},
      stream_mode="values"
  )
  
  for chunk in res:
      latest_message = chunk["messages"][-1]
      message_type = type(latest_message).__name__
      if latest_message.content:
          print(message_type, latest_message.content)
      try:
          if latest_message.tool_calls:
              print(f"工具调用{[tc['name'] for tc in latest_message.tool_calls]}")
      except AttributeError:
          pass
  
  ```

  

### 1.2.5 中间件

>控制并定制代理（Agent）执行的每一个步骤

  中间件提供了一种方法，可以更精细地控制**代理**（Agent）内部的执行流程。核心的代理循环包括调用模型、让模型选择并执行工具，然后在模型不再调用工具时结束：

![core_agent_loop.png](https://langchain-doc.cn/img/langchain-5e9cc07a/core_agent_loop.png)

中间件在这些步骤的**之前**和**之后**暴露了钩子 (hooks)：

![middleware_final.png](https://langchain-doc.cn/img/langchain-5e9cc07a/middleware_final.png)

## [中间件可以做什么？](https://langchain-doc.cn/v1/python/langchain/middleware.html#中间件可以做什么)

- **监控 (Monitor)**
  追踪代理行为，包括日志记录、分析和调试。

- **修改 (Modify)**
  转换提示词（prompts）、工具选择和输出格式。

- **控制 (Control)**
  添加重试、回退和提前终止逻辑。

- **强制执行 (Enforce)**
  应用速率限制、安全防护（guardrails）和个人身份信息（PII）检测。

通过将其传递给 `create_agent` 函数来添加中间件：

```python
from langchain.agents import create_agent
from langchain.agents.middleware import SummarizationMiddleware, HumanInTheLoopMiddleware


agent = create_agent(
    model="openai:gpt-4o",
    tools=[...],
    middleware=[SummarizationMiddleware(), HumanInTheLoopMiddleware()],
)
```

## [预置中间件 (Built-in middleware)](https://langchain-doc.cn/v1/python/langchain/middleware.html#预置中间件-built-in-middleware)

LangChain 为常见用例提供了预构建的中间件：

**完美适用于：**

- 超过上下文窗口的**长期对话**

- 具有大量历史记录的**多轮对话**

- 需要保留完整对话上下文的应用

### [人在回路中 (Human-in-the-loop)](https://langchain-doc.cn/v1/python/langchain/middleware.html#人在回路中-human-in-the-loop)

在工具执行之前，暂停代理执行以供人工批准、编辑或拒绝工具调用。

> **完美适用于：**
>
> - 需要人工批准的**高风险操作**（数据库写入、金融交易）
> - 强制要求人工监督的**合规工作流**
> - 使用人工反馈来指导代理的**长期对话**



### [摘要 (Summarization)](https://langchain-doc.cn/v1/python/langchain/middleware.html#摘要-summarization)

在接近**上下文窗口**（token limits）限制时，自动对对话历史进行摘要。



# 2.langchain框架

## 2.1 调用大模型

### 2.1.1 非流式调用模型

```python
from langchain_community.llms.tongyi import Tongyi

# qwen3-max-preview:大语言模型
# 获取模型对象
model = Tongyi(model="qwen3-max-preview")
# 用invoke方法向大模型提问
res = model.invoke(input="你是谁")
print(res)
```
### 2.1.2 流式调用

- invoke改为stream
- flush=True表示输出时不必等缓冲区满再一次性输出

```python
from langchain_community.llms.tongyi import Tongyi

# qwen3-max-preview:大语言模型
# 获取模型对象
model = Tongyi(model="qwen3-max-preview")
# 用invoke方法向大模型提问
res = model.stream(input="你是谁")

for chunk in res:
    print(chunk,end='',flush=True)

```



### 2.1.3 调用聊天模型

将from langchain_community.llms.tongyi import **Tongyi** 换为 from langchain_community.chat_models.tongyi import **ChatTongyi**

```py
from langchain_community.chat_models.tongyi import ChatTongyi
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage

# 获取聊天模型对象
chat = ChatTongyi(model="qwen3-max-2026-01-23")
# 用stream方法向大模型提问
messages = [
    SystemMessage(content="你是一个热爱网络小说的书虫,尤其爱<<蛊真人>>"),
    HumanMessage(content="续写,落魄谷中寒风吹,春秋蝉鸣少年归。"),
]

res = chat.stream(input=messages)

for chunk in res:
    print(chunk.content, end='', flush=True)


```

### 2.1.4 简化调用聊天模型

system/ai/human替代 SystemMessage,AIMessage, HumanMessage,简化了messages

```python
from langchain_community.chat_models.tongyi import ChatTongyi

# 获取聊天模型对象
chat = ChatTongyi(model="qwen3-max-2026-01-23")
# 消息的简化写法 system/ai/human
# 是动态的，需要在运行时,由LangChain内部机制转换为Message类对象 (推荐)
messages = [
    ("system","你是一个热爱网络小说的书虫,尤其爱<<蛊真人>>"),
    ("human","咯咯哒"),
    ("ai","咯咯哒")
]

batch_requests = [
    [("human","1+1等于多少？")],  # 请求 1
    [("human","Python 中列表怎么去重？")],  # 请求 2
    [("human","简述余弦相似度的核心用途？")],  # 请求 3
]

# 流式请求
# res = chat.stream(input=messages)
# 批量处理
res = chat.batch(batch_requests)
for chunk in res:
    print(chunk.content, end='', flush=True)

```

## 2.2 文本嵌入模型

文本=>元素为<u>浮点数</u>的<u>**列表**</u>

```python
# 文本嵌入模型
from langchain_community.embeddings import DashScopeEmbeddings
from P3_LangChainRAG开发.cosine_similarity import get_cosine_similarity

# 初始化嵌入模型对象,默认使用:text-embedding-v1
embed = DashScopeEmbeddings()

# 测试
a = embed.embed_query("我喜欢你")
# 批量转化
b = embed.embed_documents(["我稀饭你","咯咯哒哒"])
print(a)
print(b)
# print(get_cosine_similarity(a,b))


```

## 2.3 提示词模板

![image-20260201114515677](E:\TyporaInfo\工作\image\image-20260201114515677.png)

### 2.3.1 通用提示词模板 (zero-shot)

- 方式1:采用format(k=?,v=?)的形式,替换模板的字符串
- 方式2: 将提示词写入model中并返回模型

 ```python
 from langchain_core.prompts import PromptTemplate
 from langchain_community.llms.tongyi import Tongyi
 # 方式1利用format,拼接得到提示词
 # 1.获取提示词模板对象
 prompt_template = PromptTemplate.from_template(
     "我的邻居姓{lastname},刚生了{gender},帮忙请名字,请简略回答"
 )
 # 2.拼接变量,生成提示词
 prompt_text = prompt_template.format(lastname="张", gender="女儿")
 
 
 # 3.调用模型,获取结果
 # model = Tongyi(model="qwen3-max-preview")
 # res = model.invoke(input=prompt_text)
 # print(res)
 
 # 方式2按|符号(LCEL表达式)拼接提示词 (使用)
 model = Tongyi(model="qwen3-max-preview")
 # 将提示词写入model中并返回模型
 chain = prompt_template | model
 res = chain.invoke(input={"lastname":"张", "gender":"女儿"})
 print(res)
 
 
 ```

### 2.3.2 few-shot提示词模板

- example_prompt:例子的提示词模板 (必填)
- prefix: 例子之前要给的信息
- examples：例子(example或example_selecto必须提供一个)
- suffix：例子之后要给的信息 (必填)
- input_variables: 输入的参数名,一般用于替换prefix与suffix的变量 (必填)

```python
from langchain_core.prompts.few_shot import FewShotPromptTemplate
from langchain_core.prompts.prompt import PromptTemplate
from langchain_community.llms.tongyi import Tongyi
prompt_template = PromptTemplate.from_template("单词:{word},反义词:{antonym}")

# example_prompt:例子的提示词模板 (必填)
# prefix: 例子之前要给的信息
# examples：例子    example或example_selecto必须提供一个
# suffix：例子之后要给的信息 (必填)
# input_variables: 输入的参数名,一般用于替换prefix与suffix的变量 (必填)
examples=[
    {"word":"上","antonym":"下"},
    {"word":"大","antonym":"小"},
]

few_shot_template = FewShotPromptTemplate(
    example_prompt= prompt_template,
    prefix = "给出一个单词,给出它的反义词,参考以下示例",
    examples=examples,
    suffix= "基于示例告诉我,{input_word}的反义词是?",
    input_variables= ['input_word'],
)

# 生成提示词
prompt_text = few_shot_template.invoke(input={"input_word":"左"}).to_string()
print(prompt_text)

# 向模型提问
model = Tongyi(model="qwen3-max-preview")
res = model.invoke(input=prompt_text)
print(res)

```

### 2.3.3 聊天提示词模板(支持注入任意数量的<u>历史会话信息</u>)



- 核心:ChatPromptTemplate.from_messages(List[AImessae])

- 利用MessagesPlaceholder(消息占位符)实现**动态注入**

  

  - ```python
    from langchain_community.chat_models import ChatTongyi
    from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
    
    # 动态会话历史注入
    
    chat_prompt_template = ChatPromptTemplate.from_messages(
        [
            ("system","你是个边塞诗人,擅长作诗"),
            MessagesPlaceholder("history"),
            ("human","再来一首唐诗"),
        ]
    )
    
    history_data = [
        ("human","你来一首唐诗"),
        ("ai","床前明月光,疑是地上霜,举头望明月,低头思故乡"),
        ("human","好湿,好湿,再来一个OvO"),
        ("ai","锄禾日当午,汗滴禾下土,谁知盘中餐,粒粒皆辛苦"),
    ]
    
    prompt_text = chat_prompt_template.invoke({"history":history_data}).to_string()
    print(prompt_text)
    
    # 调用模型
    model = ChatTongyi(model="qwen3-max-preview")
    response = model.invoke(prompt_text)
    print(response.content,type(response))
    
    ```


### 2.3.4 模板类的format与invoke(推荐)方法

**format vs invoke**

| 区别     | format                    | invoke                         |
| -------- | ------------------------- | ------------------------------ |
| 功能     | 纯字符串替换 + 占位符解析 | Runnable 标准方法 + 占位符解析 |
| 返回值   | str                       | PromptValue 对象               |
| 传参     | .format(k=v, ...)         | .invoke({"k": v, ...})         |
| 支持解析 | {} 占位符                 | {} + MessagesPlaceholder       |

### 总结一句话口诀（2025–2026 主流写法）

**想快速看结果、调试 → 用 .format()** **想写 LCEL 链、想未来兼容、想统一风格 → 直接用 .invoke()**



## 2.4 chain链

### 2.4.1 核心工作原理:

将组件串联，**上一个组件的输出作为下一个组件的输入**。注意：Runnable的子类才能入链(Callable、Mapping接口子类对象也可加入)

![image-20260201124401817](E:\TyporaInfo\工作\image\image-20260201124401817.png)

示例:

```python
from langchain_community.chat_models import ChatTongyi
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.runnables import RunnableSerializable

# 动态会话历史注入

chat_prompt_template = ChatPromptTemplate.from_messages(
    [
        ("system","你是个边塞诗人,擅长作诗"),
        MessagesPlaceholder("history"),
        ("human","再来一首唐诗"),
    ]
)

history_data = [
    ("human","你来一首唐诗"),
    ("ai","床前明月光,疑是地上霜,举头望明月,低头思故乡"),
    ("human","好湿,好湿,再来一个OvO"),
    ("ai","锄禾日当午,汗滴禾下土,谁知盘中餐,粒粒皆辛苦"),
]

model = ChatTongyi(model="qwen3-max-preview")

# 定义链
chain:RunnableSerializable = chat_prompt_template | model

# invoke执行
# res = chain.invoke({"history":history_data})
# print(res.content)

res = chain.stream({"history":history_data})
for chunk in res:
    print(chunk.content,end='',flush=True)



```

### 2.4.2 StrOutputParser():字符串输出解释器

- 核心:将AImessage =>str

- 用途: 

  - 输出模型的调用结果
  - 类型转化

- 代码:

  ```python
  from langchain_community.chat_models import ChatTongyi
  from langchain_core.output_parsers import StrOutputParser
  from langchain_core.prompts import PromptTemplate
  
  # 功能：将第一次模型回答的结果做为输入进行第二次大模型调用
  # 问题: 第一次模型在调用后为AImessage类型不符合大模型的输入,因此引入strOutputParser转化为str
  
  
  # 定义模型与模板
  model = ChatTongyi(model="qwen3-max-preview")
  prompt_template = PromptTemplate.from_template(
      '我邻居姓:{lastname},刚生了{gender},请起名,仅告知我名字即可'
  )
  
  # strOutputParser()：AImessage类型=>str,且为runnable的子类
  parser = StrOutputParser()
  # 模板注入后,prompt_template | model为AImessage,而model的输入只支持 PromptValue, str, or list of BaseMessages
  # 需要用strOutputParser转化
  chain = prompt_template | model | parser | model
  res  = chain.invoke({"lastname":"林","gender":"女"})
  
  
  print(res.content)
  
  ```

### 2.4.3 JsonOutputParser()：Json字符串输出解释器

- 核心:将AImessage =>Json

- 用途:
  - 作为提示词模板的输入时,需要转为Json
  
- 示例代码:  
  
  ```python
  from langchain_community.chat_models import ChatTongyi
  from langchain_core.output_parsers import StrOutputParser, JsonOutputParser
  from langchain_core.prompts import PromptTemplate
  
  # 功能：将第一次模型回答的结果做为输入进行第二次大模型调用,第二次模型时定义模板,因此定义模板后传JSON字典去替代相应的关键字生成提示词进行二次调用
  # 关键: 替代提示词模板的关键字时输入必须时JSON
  
  
  # 定义模型与模板
  model = ChatTongyi(model="qwen3-max-preview")
  first_prompt = PromptTemplate.from_template(
      '我邻居姓:{lastname},刚生了{gender},请起名,请用JSON格式答复我,key为name,值为要起的名字'
  )
  
  # strOutputParser()：AImessage类型=>str,且为runnable的子类
  str_parser = StrOutputParser()
  json_parse = JsonOutputParser()
  
  second_prompt = PromptTemplate.from_template(
      '请解析{name}的含义'
  )
  # 模板注入后,prompt_template | model为AImessage,而model的输入只支持 PromptValue, str, or list of BaseMessages
  # 需要用strOutputParser转化
  # 在第二次调用JsonOutputParser: AImessage => dict
  
  # 分析：
  # 第一次调用的输出 first_prompt | model:AImessage,需要转换为第二次的调用的输入(字典),因此使用json_parse
  # 第二次模板的输入(需要是字典,即{"name":"林若溪"}):first_prompt | model | json_parse
  # 替换第二个提示词模板,| second_prompt,喂给模型后转化为str
  chain = first_prompt | model | json_parse | second_prompt | model | str_parser
  
  res  = chain.invoke({"lastname":"林","gender":"女"})
  
  print(res)
  
  ```
  
### 2.4.4 自定义函数加入链

- 作用: 自定义转换,可以将AImessage转换为任意的类型

- 使用方法: 基于RunnableLambda类,RunnableLambda可以将普通函数转为Runnable接口实例

- 语法:

- ```python
  RunnableLambda(函数或匿名函数)
  ```

- 匿名函数写法

- ```python
  (lambda 参数: 返回值)
  ```

- 

- 代码:

- ```python
  from langchain_community.chat_models import ChatTongyi
  from langchain_core.output_parsers import StrOutputParser, JsonOutputParser
  from langchain_core.prompts import PromptTemplate
  from langchain_core.runnables import RunnableLambda
  
  # 功能：将第一次模型回答的结果做为输入进行第二次大模型调用,第二次模型时定义模板,因此定义模板后传JSON字典去替代相应的关键字生成提示词进行二次调用
  # 关键: 替代提示词模板的关键字时输入必须时JSON
  
  
  # 定义模型与模板
  model = ChatTongyi(model="qwen3-max-preview")
  first_prompt = PromptTemplate.from_template(
      '我邻居姓:{lastname},刚生了{gender},请起名,请直接返回名字'
  )
  
  # strOutputParser()：AImessage类型=>str,且为runnable的子类
  str_parser = StrOutputParser()
  # 使用RunnableLambda自定义函数生成Json字典
  # 示例:to_json.content的值为 "林婉清"
  
  my_func = RunnableLambda(lambda to_json: {"name":to_json.content})
  
  
  second_prompt = PromptTemplate.from_template(
      '请解析{name}的含义'
  )
  # 模板注入后,prompt_template | model为AImessage,而model的输入只支持 PromptValue, str, or list of BaseMessages
  # 需要用strOutputParser转化
  # 在第二次调用JsonOutputParser: AImessage => dict
  
  # 分析：
  # 第一次调用的输出 first_prompt | model:AImessage,需要转换为第二次的调用的输入(字典),因此使用my_func转化为字典{"name":xxx}
  # 第二次模板的输入(需要是字典,即{"name":"林若溪"}):first_prompt | model | json_parse
  # 替换第二个提示词模板,| second_prompt,喂给模型后转化为str
  # 方式1: 用RunnableLambda定义
  # chain = first_prompt | model | my_func | second_prompt | model | str_parser
  
  # 方式2:自定义函数直接写入链,会自动转为RunnableLambda
  chain = first_prompt | model | (lambda to_json: {"name":to_json.content}) | second_prompt | model | str_parser
  
  
  
  
  res = chain.stream({"lastname": "林", "gender": "男"})
  
  for chunk in res:
      print(chunk,end='',flush=True)
  ```


 ### 2.4.5 总结


- 模型输入要求是:
  - str
  
    - 示例：
    
    -   ```python
        message = '你好'
        ```
  
  
  - list[baseMessage] 
  
  
    - 示例: 
  
       - ```python
          messages = [
              SystemMessage(content="你是一个热爱网络小说的书虫,尤其爱<<蛊真人>>"),
              HumanMessage(content="续写,落魄谷中寒风吹,春秋蝉鸣少年归。"),
          ]
          ```
  
  
  
  - tuple
  
    - 示例:
  
    - ```python
      message = ("human","你是谁"),
      ```
  
  - dict
  
    - 示例:
  
    - ```python
      message = {"role":"human","content":"你是谁"},
      ```
  
- 模型输出的类型为AImessage

- 提示词模板输入:必须为字典,如果收到一个字符串且只有一个参数时,会将整个字符串当作参数的值

- ![image-20260202114320439](E:\TyporaInfo\工作\image\image-20260202114320439.png)


  - 示例:

  - ```python
    message = {"name":"林舒静"},
    ```

- 提示词模板输出PromptValue对象

- StrOutputParser():AImessage =>str

- JsonOutputParser():AImessage =>Json

## 2.5 会话记忆

### 2.5.1 Memory 短期会话记忆

1. 核心:RunnableWithMessageHistory获取一个新的带有历史记录功能的chain

 ```python
   chat_history_chain = RunnableWithMessageHistory(
       base_chain,  # 被附加历史消息的Runnable,通常是chain
       get_history,  # session_id =>历史会话记录的函数
       input_messages_key="input", # 替代用户输入在模板的占位符
       history_messages_key="chat_history", # 替代历史信息在模板的占位符
   )
 ```

2. 配置当前会话ID

   ```python
       # 配置当前会话的session_id,因为在生成chat_history_chain对象的过程中,会将session_id传入得到InMemoryChatMessageHistory对象
       session_config = {"configurable":{"session_id":"user_001"}}
   
       # chat_history由langchain通过执行get_history得到InMemoryChatMessageHistory对象进行注入
       # 方式1:
       print("第一次提问:")
       print(chat_history_chain.invoke({"input":"小明有一只猫"},session_config))
       print("第二次提问:")
       print(chat_history_chain.invoke({"input":"小刚有两只狗"},session_config))
       print("第三次提问:")
       print(chat_history_chain.invoke({"input":"他们共有几只宠物"},session_config))
   ```

3. 示例:

   ```python
   from langchain_community.chat_models import ChatTongyi
   from langchain_core.chat_history import InMemoryChatMessageHistory
   from langchain_core.output_parsers import StrOutputParser
   from langchain_core.prompts import PromptTemplate, ChatPromptTemplate, MessagesPlaceholder
   from langchain_core.runnables import RunnableWithMessageHistory
   
   model = ChatTongyi(model="qwen3-max-preview")
   
   # prompt = PromptTemplate.from_template(
   #     "你需要基于历史对话记录回复用户的问题。历史对话记录:{chat_history}。用户当前输入:{input}"
   # )
   
   prompt = ChatPromptTemplate.from_messages(
       [
           ("system","你需要基于历史对话记录回复用户的问题。历史对话记录:"),
           MessagesPlaceholder("chat_history"),
           ("human","请回答以下问题:{input}"),
       ]
   )
   
   
   
   def argument_prompt(prompt):
       # 输出时,先将prompt转为str
       print("="*20, prompt.to_string(), "="*20)
       print()
       return prompt
   
   str_parser = StrOutputParser()
   
   # prompt | argument_prompt | model 等价于 prompt | model.只不过多了一些打印信息
   base_chain = prompt | argument_prompt | model | str_parser
   
   # 定义session_id与历史会话记录映射关系的字典
   chat_history_store = {}
   
   
   # 将session_id =>历史会话记录
   # 如果没有映射关系就建立再返回
   def get_history(session_id):
       if session_id not in chat_history_store:
           chat_history_store[session_id] = InMemoryChatMessageHistory()
       return chat_history_store[session_id]
   
   
   # 通过RunnableWithMessageHistory获取一个新的带有历史记录功能的chain
   chat_history_chain = RunnableWithMessageHistory(
       base_chain,  # 被附加历史消息的Runnable,通常是chain
       get_history,  # session_id =>历史会话记录的函数
       input_messages_key="input", # 替代用户输入在模板的占位符
       history_messages_key="chat_history", # 替代历史信息在模板的占位符
   )
   
   if __name__=="__main__":
       # 配置当前会话的session_id,因为在生成chat_history_chain对象的过程中,会将session_id传入得到InMemoryChatMessageHistory对象
       session_config = {"configurable":{"session_id":"user_001"}}
   
       # chat_history由langchain通过执行get_history得到InMemoryChatMessageHistory对象进行注入
       # 方式1:
       print("第一次提问:")
       print(chat_history_chain.invoke({"input":"小明有一只猫"},session_config))
       print("第二次提问:")
       print(chat_history_chain.invoke({"input":"小刚有两只狗"},session_config))
       print("第三次提问:")
       print(chat_history_chain.invoke({"input":"他们共有几只宠物"},session_config))
   
       # 方式2:可以在config里直接传session_id
       # print(chat_history_chain.invoke({"input":"小明有一只猫"},{"session_id":"user_001"}))
       # print(chat_history_chain.invoke({"input":"小刚有两只狗"},{"session_id":"user_001"}))
       # print(chat_history_chain.invoke({"input":"他们共有几只宠物"},{"session_id":"user_001"}))
   
   
   
   
   ```

   

### 2.5.2 Memory长期会话记忆

1. 需要重写class FileChatMessageHistory(BaseChatMessageHistory)

   其中,有三个关键函数

   - add_messages(self, messages: Sequence[BaseMessage]) -> None: 合并新信息和旧信息然后写文件
   - @property
         def messages(self) -> list[BaseMessage]:读出文件的内容
   - ​    clear(self) -> None:清除文件内容

   

2. 同时,将RunnableWithMessageHistory中的get_history返回FileChatMessageHistory对象即可

3. 代码示例:

   ```python
   import os, json
   from typing import Sequence
   
   from langchain_community.chat_models import ChatTongyi
   from langchain_core.chat_history import BaseChatMessageHistory
   from langchain_core.messages import message_to_dict, messages_from_dict, BaseMessage
   from langchain_core.output_parsers import StrOutputParser
   from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
   from langchain_core.runnables import RunnableWithMessageHistory
   
   
   # message_to_dict: 单个消息对象(BaseMessage对象实例)->字典
   # messages_from_dict: [{},{}...] -> [消息、消息...]
   
   class FileChatMessageHistory(BaseChatMessageHistory):
       def __init__(self, storage_path: str, session_id: str):
           # 文件夹路径与会话ID
           self.storage_path = storage_path
           self.session_id = session_id
           # 文件路径 = 文件夹路径 + 会话ID
           self.file_path = os.path.join(self.storage_path, session_id)
   
           # os.path.dirname(file_path):取得目录的路径,即去掉文件名的部分。例如完整路径名c:a/b/c.txt 调用后得到c:a/b
   
           # os.makedirs(file_path)：递归创建目录,exist_ok=True：目录已经存在则不抛出异常
           os.makedirs(os.path.dirname(self.file_path), exist_ok=True)
   
       def add_messages(self, messages: Sequence[BaseMessage]) -> None:
           # 1.合并旧消息和新消息
           all_messages = self.messages
           # 合并
           all_messages.extend(messages)
   
           # 2.将数据同步写入到本地文件中
           # 类对象写入文件-> 一堆二进制(不可取)
           # 将BaseMessage消息转为字典(借助json模块,以字符串的形式写入文件)
           # for message in all_messages:
           #     json_message = message_to_dict(message)
           #     new_messages.append(json_message)
   
           new_messages = [message_to_dict(message) for message in all_messages]
   
           # 写入文件
           with open(self.file_path, 'w', encoding="utf-8") as f:
               # json.dump:将列表里的所有东西转为JSON字符串往文件中写
               json.dump(new_messages, f)
   
       # @property装饰器将该方法变成成员属性,方便以.的形式使用该方法
       # 当前文件内:list[字典],需要list[BaseMessage]
       @property
       def messages(self) -> list[BaseMessage]:
           # 读出文件的内容
           try:
               with open(self.file_path, 'r', encoding="utf-8") as f:
                   messages = json.load(f)
                   return messages_from_dict(messages)
           except FileNotFoundError:
               return []
   
       def clear(self) -> None:
           try:
               with open(os.path.dirname(self.file_path), 'w', encoding="utf-8") as f:
                   json.dump([], f)
           except FileNotFoundError:
               print("文件不存在,清除内容失败!")
   
   
   model = ChatTongyi(model="qwen3-max-preview")
   
   # prompt = PromptTemplate.from_template(
   #     "你需要基于历史对话记录回复用户的问题。历史对话记录:{chat_history}。用户当前输入:{input}"
   # )
   
   prompt = ChatPromptTemplate.from_messages(
       [
           ("system", "你需要基于历史对话记录回复用户的问题。历史对话记录:"),
           MessagesPlaceholder("chat_history"),
           ("human", "请回答以下问题:{input}"),
       ]
   )
   
   
   def argument_prompt(prompt):
       # 输出时,先将prompt转为str
       print("=" * 20, prompt.to_string(), "=" * 20)
       print()
       return prompt
   
   
   str_parser = StrOutputParser()
   
   # prompt | argument_prompt | model 等价于 prompt | model.只不过多了一些打印信息
   base_chain = prompt | argument_prompt | model | str_parser
   
   
   
   # 将session_id =>历史会话记录
   # 如果没有映射关系就建立再返回
   def get_history(session_id):
       return FileChatMessageHistory(storage_path="./chat_history", session_id=session_id)
   
   
   # 通过RunnableWithMessageHistory获取一个新的带有历史记录功能的chain
   chat_history_chain = RunnableWithMessageHistory(
       base_chain,  # 被附加历史消息的Runnable,通常是chain
       get_history,  # session_id =>历史会话记录的函数
       input_messages_key="input",  # 替代用户输入在模板的占位符
           history_messages_key="chat_history",  # 替代历史信息在模板的占位符
   )
   
   if __name__ == "__main__":
       # 配置当前会话的session_id,因为在生成chat_history_chain对象的过程中,会将session_id传入得到InMemoryChatMessageHistory对象
       session_config = {"configurable": {"session_id": "user_001"}}
   
       # chat_history由langchain通过执行get_history得到InMemoryChatMessageHistory对象进行注入
       # print("第一次提问:")
       # print(chat_history_chain.invoke({"input": "小明有一只猫"}, session_config))
       # print("第二次提问:")
       # print(chat_history_chain.invoke({"input": "小刚有两只狗"}, session_config))
   
       print("第三次提问:")
       print(chat_history_chain.invoke({"input": "他们共有几只宠物"}, session_config))
   
   ```

   

## 2.6 文档加载器

文档加载器提供了一套标准接口，用于将不同来源（如CSV、PDF_或JSON等）的数据读取为LangChain
的文档格式。这确保了无论数据来源如何，都能对其进行一致性处理。文档加载器（内置或自行实现）需实现BaseLoader接口。

文档加载器（内置或自行实现）需实现BaseLoader接口。

![image-20260202171901055](E:\TyporaInfo\工作\image\image-20260202171901055.png)

### 2.6.1 CSVLoader

1. 关键函数及其参数 CSVLoader():返回列表,带有page_content与metadata

   参数：

   - file_path:表明文件参数(必填)

   - encoding="utf-8":编码

   - csv_args: dict

   - ```python
     csv_args={
         "delimiter":",", # 指定分隔符
         "quotechar":'"', # 指定字符串的引号包裹
         # 字段列表(无表头使用,有表头勿用会读取首行作为数据)
         "fieldnames":["name","age","gender","hobby"]
     }
     ```

2. 示例代码

   ```python
   from langchain_community.document_loaders.csv_loader import CSVLoader
   
   
   loader = CSVLoader(
       file_path="./data/stu.csv",
       encoding="utf-8",
       csv_args={
           "delimiter":",", # 指定分隔符
           "quotechar":'"', # 指定字符串的引号包裹
           # 字段列表(无表头使用,有表头勿用会读取首行作为数据)
           "fieldnames":["name","age","gender","hobby"]
       }
   
   )
   
   # 一次性加载全部文档 load()-> list[Document,Document,...]
   # documents = loader.load()
   # for document in documents:
   #     print(document)
   
   
   # 分段返回文档
   for document in loader.lazy_load():
       print(document)
   
   ```

   

### 2.6.2 JSONLoader

1. jq介绍:

   ![image-20260202174836156](E:\TyporaInfo\工作\image\image-20260202174836156.png)

2. 函数与参数:返回列表,带有page_content与metadata

   ```python
   loader = JSONLoader(
       file_path='data/json.json', # 文件路径
       jq_schema='.province.city', # jq语法
       text_content= False, # 抽取的是否为字符串
       json_lines= False, # 是否为JsonLines文件(每一行都是JSON的文件)
   )
   ```

3. 示例代码

   ```python
   from langchain_community.document_loaders import JSONLoader
   
   loader = JSONLoader(
       file_path='data/json.json', # 文件路径
       jq_schema='.province.city', # jq语法
       text_content= False, # 抽取的是否为字符串
       json_lines= False, # 是否为JsonLines文件(每一行都是JSON的文件)
   )
   
   # loader.lazy_load()返回迭代器
   for document in loader.lazy_load():
       print(document)
   
   ```

### 2.6.3 TextLoader

1. 函数与参数: TextLoader():返回列表,带有page_content与metadata。

   ```python
   loader = TextLoader(
       file_path="data/python基础语法.txt",
       encoding="utf-8",
   )
   ```

   

2. 问题: 所有内容只生成一个**document**,当文件内容过大时不合理。采用**文档分割器解决**

3. 文档分割器：RecursiveCharacterTextSplitter 

   ```python
   splitter =  RecursiveCharacterTextSplitter(
       chunk_size= 500, # 每段的字符数
       chunk_overlap= 50, # 分段之间允许重复的字符数
       separators= ["\n\n","\n","。","！","？",".","!","?",":"," "], # 分段的符号
       length_function= len # 统计字符数的函数
   )
   ```

   

4. 示例代码：

   ```python
   from langchain_community.document_loaders import TextLoader
   from langchain_text_splitters import RecursiveCharacterTextSplitter
   
   loader = TextLoader(
       file_path="data/python基础语法.txt",
       encoding="utf-8",
   )
   
   docs = loader.load()
   
   # 获取文档分割器对象
   splitter =  RecursiveCharacterTextSplitter(
       chunk_size= 500, # 每段的字符数
       chunk_overlap= 50, # 分段之间允许重复的字符数
       separators= ["\n\n","\n","。","！","？",".","!","?",":"," "], # 分段的符号
       length_function= len # 统计字符数的函数
   )
   
   split_docs =  splitter.split_documents(docs)
   print(split_docs)
   print(len(split_docs))
   
   for document in split_docs:
       print("="*20)   
       print(document)
       print("="*20)
   
   
   
   
   
   
   
   ```

   

### 2.6.4 PdfLoader

1. 函数与参数: TextLoader():返回document列表,带有page_content与metadata。

   ```python
   documents = PyPDFLoader(
       file_path='./data/python基础语法.pdf',
       mode = 'page', # page:每页各自生成一个document, single: 总共生成一个document
       password = '123456' # 如果加密,这里写文件密码
   )
   ```

2. 示例代码

   ```python
   from langchain_community.document_loaders import PyPDFLoader
   
   documents = PyPDFLoader(
       file_path='./data/python基础语法.pdf',
       mode = 'page', # page:每页各自生成一个document, single: 总共生成一个document
       password = '123456' # 如果加密,这里写文件密码
   )
   
   for document in documents.lazy_load():
       print("="*20)
       print(document,end='',flush=True)
       print("="*20)
   ```

   

## 2.7 VectorStores向量存储

### 2.7.1 RAG流程

1. 查询阶段(检索)：将查询**文本**通过EmbeddingModel(文本嵌入模型)转换为**<u>查询向量</u>**，根据相似性算法从向量存储库中找到匹配的向量

2. 索引阶段(存储): 将**文档**通过EmbeddingModel(文本嵌入模型)转换为**嵌入向量**，存入向量存储库

3. 需要的操作：

   1. 指定文本嵌入模型,得到向量数据库对象:

      ```python
      # 1.指定文本嵌入模型,得到向量存储库对象
      # 内存内存储向量库
      vector_store = InMemoryVectorStore(
          embedding=DashScopeEmbeddings()
      )
      
      # 外部存储向量库(以数据库形式)
      vector_store = Chroma(
          collection_name="test", # 当前向量存储库的名字,类似表名
          embedding_function=DashScopeEmbeddings(), # 嵌入模型
          persist_directory="./chroma" # 存储向量的目录的路径
      )
      
      ```

      

   2. 存入向量:对向量数据库进行add_documents(list documents,list ids),

      ```python
      # 3.存入向量 返回List[str]例如:['id1', 'id2', 'id3', 'id4', 'id5', 'id6', 'id7', 'id8', 'id9', 'id10']
      vector_store.add_documents(
          documents=documents, # 要存入的文档, 类型:list[document],可以用各类文档加载器得到
          ids = ids, # 给每个文档编号, 类型:List[Str]
      )
      ```

      

   3. 删除向量:对向量数据库进行delete

      ```python
      vector_store.delete(["id1","id2"])
      ```

   4. 向量检索:对向量数据库对象进行similarity_search

      ```python
      # 5.向量检索 返回List[Document]
      res = vector_store.similarity_search(
          "林", # 查询的内容
          2,       # 返回的结果个数
          filter={"source":"fju"} # 根据source的值进行过滤
      )
      ```

    5. 完整示例代码
    ```python
    from langchain_community.document_loaders import CSVLoader
    from langchain_community.embeddings import DashScopeEmbeddings
    from langchain_chroma import Chroma
    
    # chroma 向量数据库(轻量级)
    # 1.指定文本嵌入模型,得到向量存储库对象
    vector_store = Chroma(
        collection_name="test", # 当前向量存储库的名字,类似表名
        embedding_function=DashScopeEmbeddings(), # 嵌入模型
        persist_directory="./chroma" # 存储向量的目录的路径
    )
    
    # 2.获取CSV文档对象
    csv_loader= CSVLoader(
        file_path='./data/stu.csv',
        encoding='utf-8',
        source_column='school' # 指定数据来源
    )
    
    documents = csv_loader.load()
    
    for document in documents:
        print(document)
    
    ids = [f"id{i + 1}" for i,_ in enumerate(documents)]
    
    # 3.存入向量数据库
    add_str = vector_store.add_documents(
        documents=documents, # 要存入的文档, 类型:list[document]
        ids = ids, # 给每个document编号, 类型:List[Str]
    )
    
    
    
    # 4.删除向量
    vector_store.delete(["id1","id2"])
    
    # 5.向量检索 返回List[Document]
    res = vector_store.similarity_search(
        "林", # 查询的内容
        2,       # 返回的结果个数
        filter={"source":"fju"} # 根据source的值进行过滤
    )
    print(res)
    
    for item in res:
        print(item)
        print("="*20)
    
    ```
   ![image-20260203145909150](E:\TyporaInfo\工作\image\image-20260203145909150.png)

### 2.7.2 基于向量检索构造提示词

流程

1. 获取数据库对象

   ```python
   vector_store = Chroma(
       collection_name="test", # 当前向量存储库的名字,类似表名
       embedding_function=DashScopeEmbeddings(),
       persist_directory="./chroma" # 存储向量的目录的路径
   )
   ```

   ------

2. 根据用户输入与参考资料,向大模型提供并得到结果

   ```python
   user_input = input("请输入你的问题：")
   
   # 获取向量库总文档数
   total_docs = vector_store._collection.count()
   
   results = vector_store.similarity_search(
       user_input,
       total_docs,
   )
   ```

3. 生成参考资料

   ```python
   reference = ''
   name_list = []
   # page_content='school: mju\nname: 林雨桐\nage: 20\ngender: 女\nhobby: 篮球,RAP'
   for result in results:
       page_content  = result.page_content
       if "name" in page_content:
           name = page_content.split("name:")[1].split("\n")[0].strip()
           name_list.append(name)
   reference = "、".join(name_list) if name_list else "未查到相关信息"
   
   ```

4. 利用链向大模型进行提问并返回结果

   ```python
   chain = prompt_template | model | StrOutputParser()
   res = chain.invoke({"input":user_input,"context":reference})
   
   print(res)
   ```

### 2.7.3 向量库入链

1. 核心:如何使向量库对象入链的同时提供提示词模板所需的参数

2. 分析:

   retriever:
   输入: 用户输入:str
   输出: 向量库的检索结果:list[Document]
   prompt_template:
   输入:用户输入 + 向量库的检索结果:dict
   输出:完整的提示词:PromptValue

3. 关键:链要怎么写？

   1. 一定是**先retriever后prompt_template**

   2. 由于prompt_template的**输入需要dict**，因此链的第一个组件必须为**dict**。其中,dict的键就由提示词模板决定。

   3. chain.invoke(param),param只能传给chain的第一个组件,**其它组件要获取param的值可以用RunnablePassthrough()函数**

   4. dict的值只能是**str**,因此需要写一个函数将retriever的结果转换为str

   5. 示例:

      ```python
      chain = (
          {"input":RunnablePassthrough(),"context":retriever | format_func} | prompt_template | model | StrOutputParser()
      )
      ```

      

4. 示例代码

   ```python
   from langchain_chroma import Chroma
   from langchain_community.chat_models import ChatTongyi
   from langchain_community.embeddings import DashScopeEmbeddings
   from langchain_core.output_parsers import StrOutputParser
   from langchain_core.prompts import ChatPromptTemplate
   from langchain_core.runnables import RunnablePassthrough
   
   model = ChatTongyi(model="qwen3-max-preview")
   
   prompt_template = ChatPromptTemplate.from_messages(
       [
           ("system","已我提供的参考资料为主,简洁且专业的回答用户提出的问题。参考资料:{context}。"),
           ("user","用户提问:{input}")
       ]
   )
   # 1.根据用户提供,从向量库中搜索资料后替换{context}
   # 获取向量库对象
   vector_store = Chroma(
       collection_name="test", # 当前向量存储库的名字,类似表名
       embedding_function=DashScopeEmbeddings(),
       persist_directory="./chroma" # 存储向量的目录的路径
   )
   
   # 要想入链则必须是Runnable接口的子类,可以用as_retriever(search_kwargs={"k":2})返回Runnable接口的子类
   retriever = vector_store.as_retriever(
       search_kwargs={"k":2}
   )
   
   # 将List[Document]转为str
   def format_func(docs):
       name_list = []
       # page_content='school: mju\nname: 林雨桐\nage: 20\ngender: 女\nhobby: 篮球,RAP'
       for result in docs:
           page_content  = result.page_content
           if "name" in page_content:
               name = page_content.split("name:")[1].split("\n")[0].strip()
               name_list.append(name)
       return "、".join(name_list) if name_list else "未查到相关信息"
   '''
   关键:链要怎么写？
   retriever:
   输入: 用户输入:str
   输出: 向量库的检索结果:list[Document]
   prompt_template:
   输入:用户输入 + 向量库的检索结果:dict
   输出:完整的提示词:PromptValue
   根据
   
   '''
   chain = (
       {"input":RunnablePassthrough(),"context":retriever | format_func} | prompt_template | model | StrOutputParser()
   )
   
   # 2.根据用户输入与参考资料,向大模型提供并得到结果
   # 获取用户输入 姓林的有几人,他们的名字是什么
   # user_input = input("请输入你的问题：")
   user_input = '姓林的有几人,他们的名字是什么'
   
   # chain.invoke方法会将user_input的值给链的第一个对象。链的第一个对象是字典,字典中链的第一个对象是retriever.因此值会给它
   # 通过RunnablePassthrough将user_input的值也给input
   res = chain.invoke(user_input)
   
   print(res)
   ```

## 2.8 RAG项目实战

### 2.8.1 项目介绍

   

   1. 本次项目以“京东商品衣服“为例，以**衣服属性**构建**本地知识**。使用者可以自由更新**本地知识**，用户问题的答案也是基于本地
      知识生成的。

   2. 离线处理：向私有知识库（向量存储）源源不断添加私有知识文档。
      向知识库添加来自未来的知识文档（基于模型训练完成时间）
      向模型添加私有知识文档给出模型参考资料，规避模型幻觉（一本正经的胡说八道）

   3. 在线处理：用户提问会先基于私有知识库做检索，获取参考资料，同步组装新提示词询问大模型获取结果。

   4. 如图

      ![image-20260205155735988](E:\TyporaInfo\工作\image\image-20260205155735988.png)

### 2.8.2 离线流程

1. 流程图：

   ![1.离线流程总览](E:\TyporaInfo\工作\image\1.离线流程总览.png)

2. 从知识库中提取内容:

   1. 利用streamlit提供的st.file_uploader获取文件对象

      ```python
      uploader_file = st.file_uploader(
          "请上传TXT文件",
          type=['txt'],
          accept_multiple_files=False,  # 是否支持多个文件的上传
      )
      ```

   2. 将文件对象的<u>内容、文件名</u>传给**向量库对象**的**upload_by_str(text,file_name)**方法

3. 文本切分:

      1. 初始化文本分割器对象

         ```python
         self.spliter = RecursiveCharacterTextSplitter(
                     chunk_size=config.CHUNK_SIZE,  # 每段的最大字符数
                     chunk_overlap=config.CHUNK_OVERLAP,  # 允许的连续段之间的字符数重叠数量
                     separators=config.SEPARATORS,  # 用于分隔的字符
                     length_function=len,  # 字符长度统计用什么函数
                 )
         ```

         

      2. 将内容转换为**32位的16进制的字符串（MD5形式）**后判断该字符串是否已经在md5.text里了

         1. 如果已经在了，返回“[跳过]内容已经在知识库了”

         1. 如果不在,则判断内容的长度是否超过**分段的阈值**
            1. 如果超过了，用文本分割器对象(RecursiveCharacterTextSplitter)的split_text(content)方法分割成list[str1,str2,...]
            2. 如果没超过,分割成list[str]

4. 利用向量嵌入算法将字符串转为向量并存入数据库:

   1. 初始化chroma向量库对象，指定向量嵌入函数。默认为text-embedding-v1

      ```python
       # chroma向量库对象
              self.chroma = Chroma(
                  collection_name=config.COLLECTION_NAME,  # 表名
                  embedding_function=DashScopeEmbeddings(),  # 嵌入模型
                  persist_directory=config.PERSIST_DIRECTORY,  # 存储的目录
      
              )
      ```

   2. 用chroma.add_texts向量库中存入向量

      ```python
      metadata = {
                  "source": filename,
                  "create_time": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
                  "operator": "小林",
              }
              self.chroma.add_texts(
                  # iterable ->list,tuple
                  knowledge_chunks, # 迭代器,这里为list[str]
                  [metadata for _ in knowledge_chunks], # 额外信息列表 metadata为dict
              )
      ```

   3. 保存过的**内容**存入md5.text,并返回成功

      ```python
      # 处理过的数据放到md5.text
              save_md5(md5_hex)
              return"[成功]内容已经成功载入向量库"
      ```

### 2.8.3 在线流程

1. 流程图：

   ![2.在线流程总览.drawio](E:\TyporaInfo\工作\image\2.在线流程总览.drawio-1770631395938-7.png)

   1. 获取用户输入:

      ```python
      prompt = st.chat_input()
      ```

   2. 获取链对象

         ```python
         # 利用st.session_state存储RagService的状态
         if "rag" not in st.session_state:
             st.session_state["rag"] = RagService()
         ```

         

2. 构建Rag服务对象:

      1. 初始化Ragservice的属性

         ```python
         class RagService(object):
             def __init__(self):
                 self.vector_service = VectorStoreService(
                     DashScopeEmbeddings()
                 )
                 self.prompt_template = ChatPromptTemplate.from_messages(
                     [
                         ("system", "以我提供的已知参考材料为主，简洁和专业的回答用户提出的问题。参考资料：{context}."),
                         ("system","并且我提供用户的对话记录,如下"),
                         MessagesPlaceholder("history"),
                         ("user", "请回答用户提问:{input}")
                     ])
                 self.chat_model = ChatTongyi(model=config.CHAT_MODEL_NAME)
                 self.chain = self.__get_chain()
         class VectorStoreService(object):
             def __init__(self, embedding):
                 # 嵌入模型
                 self.embedding = embedding
                 self.vector_store = Chroma(
                     collection_name=config.COLLECTION_NAME,
                     embedding_function=self.embedding,
                     persist_directory=config.PERSIST_DIRECTORY,
                 )
         
             # 返回向量检索器,方便加入链
             def get_retriever(self):
                 return self.vector_store.as_retriever(
                     search_kwargs={"k": config.SIMILARITY_THRESHOLD}
                 )
         
         ```

      2. 编写获取最终的执行链函数

         1. 链的分析：

            1. 由于输入是dict类型，而retrieve的输入是str。则需要format_for_retrieve来转换

               ```python
                 def format_for_retrieve(value):
                           return value["input"]
               ```

            2. 由于模板中context是str,而retrieve的输出是list[docuemnt]。则需要format_func来转换

               ```python
                def format_func(docs: list[Document]):
                           if not docs:
                               return "无相关参考资料"
                           formatted_docs = ''
                           for doc in docs:
                               formatted_docs += f"文档片段:{doc.page_content}\n文档元数据{doc.metadata}\n\n"
                           return formatted_docs
               ```

            3. 由于self.prompt_template需要input、context、history三个变量,我们缺少history。需要format_for_prompt_template函数

               ```python
               def format_for_prompt_template(value):
                           new_value = {}
                           new_value["input"] = value["input"]["input"]
                           new_value["context"] = value['context']
                           new_value["history"] = value['input']['history']
                           return new_value
               ```

         2. 完整代码
         
            ```python
            from langchain_community.chat_models.tongyi import ChatTongyi
            from langchain_core.documents import Document
            from langchain_core.output_parsers import StrOutputParser
            from langchain_core.prompts import ChatPromptTemplate,MessagesPlaceholder
            from langchain_core.runnables import RunnablePassthrough, RunnableWithMessageHistory, RunnableLambda
            
            from online.vector_stores import VectorStoreService
            from langchain_community.embeddings import DashScopeEmbeddings
            import config_data as config
            from file_history_store import get_history
            
            
            class RagService(object):
                def __init__(self):
                    self.vector_service = VectorStoreService(
                        DashScopeEmbeddings()
                    )
                    self.prompt_template = ChatPromptTemplate.from_messages(
                        [
                            ("system", "以我提供的已知参考材料为主，简洁和专业的回答用户提出的问题。参考资料：{context}."),
                            ("system","并且我提供用户的对话记录,如下"),
                            MessagesPlaceholder("history"),
                            ("user", "请回答用户提问:{input}")
                        ])
                    self.chat_model = ChatTongyi(model=config.CHAT_MODEL_NAME)
                    self.chain = self.__get_chain()
            
                """
                获取最终的执行链
                """
                def __get_chain(self):
                    # 检索器
                    retrieve = self.vector_service.get_retriever()
                    def format_func(docs: list[Document]):
                        if not docs:
                            return "无相关参考资料"
                        formatted_docs = ''
                        for doc in docs:
                            formatted_docs += f"文档片段:{doc.page_content}\n文档元数据{doc.metadata}\n\n"
                        return formatted_docs
                    def format_for_retrieve(value):
                        return value["input"]
                    def format_for_prompt_template(value):
                        new_value = {}
                        new_value["input"] = value["input"]["input"]
                        new_value["context"] = value['context']
                        new_value["history"] = value['input']['history']
                        return new_value
                    def print_prompt(prompt_template):
                        print("="*20)
                        print(prompt_template.to_string())
                        print("="*20)
                        return prompt_template
                    # RunnableLambda: func->runnable
                    chain = (
                            {"input": RunnablePassthrough(),
                             "context": RunnableLambda(format_for_retrieve) | retrieve | format_func} | RunnableLambda(format_for_prompt_template) | self.prompt_template | print_prompt | self.chat_model | StrOutputParser()
                    )
                    conversation_chain = RunnableWithMessageHistory(
                        chain, #被附加历史消息的Runnable,通常是chain
                        get_history, # session_id =>历史会话记录的函数
                        input_messages_key="input", #   替代用户输入在模板的占位符
                        history_messages_key="history", # 替代历史信息在模板的占位符
                    )
                    return conversation_chain
            
            if __name__ == '__main__':
                session_config = {
                    "configurable":{
                        "session_id":"user_001",
                    }
                }
                res = RagService().chain.invoke({"input":"衣服推荐尺寸"},session_config)
                print(res)
            
            ```

3. 传入用户输入给链后得到结果

      关键：

      ```python
      ai_res_stream = st.session_state['rag'].chain.stream({"input":prompt},session_config)
      ```

      

      ```python
      # 加载
          with st.spinner("AI思考中..."):
              ai_res_stream = st.session_state['rag'].chain.stream({"input":prompt},session_config)
              # stream:返回iterable
              # iterable => str
              def capture(generator,cache_list):
                  for chunk in generator:
                      cache_list.append(chunk)
                      yield chunk
              st.chat_message("assistant").write_stream(capture(ai_res_stream,ai_res_list))
              st.session_state['message'].append({"role":"assistant","content":"".join(ai_res_list)})
      
      ```

      


### 2.8.4 总结 

1. RunnableLambda：

   1. RunnableLambda 是 LangChain 中用于将任意 Python 函数包装成 Runnable 接口的工具，让它可以无缝嵌入 LCEL 链中（与 prompt、model、retriever 等组件串联）。 

   2. RunnableLambda 就是“把你的自定义函数变成链条的一环”的桥梁，特别适合加打印、格式化、过滤、类型转换等轻量级处理。

   3. 例子：调试/打印中间值

      ```python
      def temp1(value):
          print("-----------", value)   # 打印输入看看
          return value                 # 原样返回，不改变数据
      
      chain = (
          {"input": RunnablePassthrough()}
          | RunnableLambda(temp1)      # 打印 字典
          | prompt
          | model
      )
      ```

      

# 3. OpenClaw

## 3.1 安装

1. 去官网[Getting Started - OpenClaw](https://docs.openclaw.ai/start/getting-started#send-a-test-message) 
2. 在安装node>=22的情况下`用管理员身份打开PowerShell`

![image-20260307194510208](E:\TyporaInfo\工作\image\image-20260307194510208.png)

3. `依次`运行 

    ```url
    iwr -useb https://openclaw.ai/install.ps1 | iex
    openclaw onboard --install-daemon
    ```

4. `坑来了！`希望选择Deepseek大模型,发现没有选项！选`OpenAI,因为DeepSeek 的 API 调用格式与 OpenAI 高度兼容（核心结构一致）`

5. 用文本编辑器打开`openclaw.json`

    ![image-20260307194947128](E:\TyporaInfo\工作\image\image-20260307194947128.png)

6. 删掉所有内容！复制以下文本

   ```js
   {
     "models": {
       "providers": {
         "deepseek": {
           "baseUrl": "https://api.deepseek.com/v1",
           "apiKey": "你的DeepSeek-Key",
           "api": "openai-completions",
           "models": [
             {
               "id": "deepseek-chat",
               "name": "DeepSeek Chat"
             }
           ]
         }
       }
     },
     "agents": {
       "defaults": {
         "model": {
           "primary": "deepseek/deepseek-chat"
         }
       }
     }
   }
   ```

7. 执行以下命令（设置网关运行模式，与重新生成openclaw.json文件

   ```js
   openclaw config set gateway.mode local
   openclaw doctor
   ```

   

8. 执行命令打开ui页面

   ```js
   openclaw gateway --port 18789
   ```

   

9. 测试

   ![image-20260307195438169](E:\TyporaInfo\工作\image\image-20260307195438169.png)

## 3.2 接入QQ

1. 打开QQ开发平台(专门接入OpenClaw的)

   [QQ开放平台｜机器人列表](https://q.qq.com/qqbot/openclaw/index.html)

2. 保存机器人的关键信息

   ```python
   AppID:102928751
   AppSecret:hEahgZKzjNuMix56
   ```

   
