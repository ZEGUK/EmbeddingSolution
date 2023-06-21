# Embeddings Solution

Embeddings是一种特殊的数据表示形式，可以对一段文本的语义含义进行信息密集表示。每个Embeddings都是浮点数类型的向量。

在向量空间中两个Embeddings的距离与原始格式的两个输入文本之间的语义相似性相关。换句话说，如果两个文本相似，则它们的向量表示也应相似。因此，Embeddings 使得语义空间的搜索匹配成为可能。

一种典型的基于Embeddings的企业私域数据 Q&A 方案架构： 

![image](https://github.com/ZEGUK/pdf-form-table-demo-test-script/assets/32155786/a606bd28-3e29-4943-aace-12811e5290e3)

1） Embeddings 向量生成

如果熟悉机器学习与深度学习，那么对Embeddings一定不陌生。机器学习的内核即为对数据进行高维度的抽象表达，从而更精细地了解数据特征（Feature)。数据的高维度抽象表达会生成特征向量，一个数据的所有特征向量构成向量空间。

其中，高维度的抽象表达如果是通过深度学习模型中的专有层（layer)对原始数据进行空间映射来实现，这种抽象表达就是Embeddings；如果通过简单的机器学习模型进行空间映射，得到的抽象表达就是数据特征。因此，数据特征可以理解为广义的Embeddings。

能够生成Embeddings 的模型/算法也不仅仅只有GPT，实际上几乎所有的深度学习模型都可以生成广义的Embeddings, 只不过在自然语言处理领域，特别是大语言模型方向，Embeddings因为GPT的强大而逐渐被大家关注。

在上图中，使用text-embeddings-ada-002作为Embeddings的生成模型。

我们基于bill_sum_data 文档（BillSum 是美国国会和加利福尼亚州法案的数据集，语料库包括国会第103-115届会议（1993-2018）的法案。）对GPT Embedding模型和其他Embedding 生成模型的进行搜索准确度对比：

分别使用OpenAI text-embeddings-ada-002 和 HUggingFace "sentence-transformers/all-MiniLM-L6-v2"模型生成Embedding：（![code](），其他搜索配置不变，一些示例查询如下：

#"Can gene chips accurately predict whether or not breast cancer tumors would spread?"

#"How many percentage of cancers are diagosed at age 55 and above."

#"What is the Lance Armstrong?"

#"How many cancer survivors in the United States in 2001?"

#"What is Gleevec?"

根据两种Embedding生成模型的返回结果，我们分别将其进行查询-搜索匹配，对于类似#"Can gene chips accurately predict whether or not breast cancer tumors would spread?"、#"How many percentage of cancers are diagosed at age 55 and above."、 #"How many cancer survivors in the United States in 2001?"的问题，两个模型都能给出正确的结果：

![image](https://github.com/huqianghui/pdf-form-table-demo-test-script/assets/32155786/c028e6f0-ae21-4a90-b373-988e458afa42)


对于问题#"What is Gleevec?"，实际上只有index = 3的section与Gleevec有关，而基于HUggingFace "sentence-transformers/all-MiniLM-L6-v2"模型的结果并没有Gleevec相关内容。

![image](https://github.com/huqianghui/pdf-form-table-demo-test-script/assets/32155786/cf4c75a7-df2e-4a77-9cf0-562b100813ce)

对于问题#"What is the Lance Armstrong?"，结果类似，OpenAI text-embeddings-ada-002 比 HUggingFace "sentence-transformers/all-MiniLM-L6-v2"表现更好。
![image](https://github.com/huqianghui/pdf-form-table-demo-test-script/assets/32155786/008e9e98-c673-4c24-8eb4-8dadc0a9033a)

这一实验结果其实与我们对GPT模型的理解一致：对于在文中有明确表达、以文本理解为主的问题，GPT与其他大语言模型可能差别不明显，但是对于在文档中没有明确文本表示、需要语义理解进行查询匹配时，GPT的表现会优于其他大语言模型。


2） Embeddings 存储选择

Embeddings的存储涉及到向量存储与检索。在Azure平台上，支持向量存储和检索的有：PostgreSQL、Redis Enterprise和Azure Cognitive Search(private preview for vector search feature, https://github.com/Azure/cognitive-search-vector-pr)。

在开源社区，向量数据库例如：Qdrant、Pinecone、Redis、Weaviate、Milvus...关于开源向量数据库的性能测评，可以参考：

https://jina.ai/news/benchmark-vector-search-databases-with-one-million-data/


3） Embeddings 向量相似性搜索中的阈值设置
