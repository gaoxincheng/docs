### 选择合适的预训练模型
根据具体任务选择合适的预训练模型。例如：
自然语言处理任务：若处理文本分类、情感分析等任务，可选 BERT、RoBERTa 等；对于生成式任务，如文本生成、机器翻译，GPT 系列模型更合适。
计算机视觉任务：处理图像分类任务，可选用 ResNet、VGG 等；处理目标检测任务，Faster R - CNN、YOLO 系列模型较为常用。
### 准备数据集
	- 数据收集：收集与特定任务相关的数据集。确保数据的质量和多样性，数据应具有代表性，能够覆盖任务可能遇到的各种情况。
	- 数据标注：对于监督学习任务，需要对数据进行标注。例如在图像分类任务中，为每张图像标注其所属的类别；在文本分类任务中，为每条文本标注其对应的类别标签。
	- 数据划分：将数据集划分为训练集、验证集和测试集。通常训练集占大部分（如 70% - 80%），用于模型的训练；验证集用于在训练过程中评估模型的性能，调整超参数；测试集用于最终评估模型的泛化能力。
	- 数据预处理：根据模型的要求对数据进行预处理。在自然语言处理中，需要进行分词、构建词表、将文本转换为模型可接受的输入格式（如词向量）等操作；在计算机视觉中，需要进行图像的缩放、裁剪、归一化等操作。
### 调整模型结构（可选）
根据具体任务的需求，对预训练模型的结构进行适当调整：
	- 添加新的层：在预训练模型的基础上添加新的全连接层或其他类型的层，以适应特定任务的输出要求。例如，在图像分类任务中，可在预训练模型的末尾添加一个全连接层，其输出节点数等于类别数。
	- 冻结部分层：为了减少训练参数的数量，加快训练速度，可选择冻结预训练模型的部分层。通常冻结模型的前几层，因为这些层学习到的是通用的特征，不需要在微调过程中进行更新。
### 选择优化器和损失函数
  - 优化器：选择合适的优化器来更新模型的参数。常见的优化器有随机梯度下降（SGD）、Adam、Adagrad 等。例如，Adam 优化器具有自适应学习率的特点，在很多任务中表现良好。
  - 损失函数：根据任务的类型选择合适的损失函数。在分类任务中，常用交叉熵损失函数；在回归任务中，常用均方误差（MSE）损失函数。
### 进行微调训练
 - 设置超参数：设置训练的超参数，如学习率、批量大小、训练轮数等。学习率控制模型参数更新的步长，需要根据具体情况进行调整；批量大小指每次训练时使用的样本数量；训练轮数表示模型对整个训练集进行训练的次数。
 - 训练过程：在训练过程中，将训练数据按批量输入到模型中，计算损失函数的值，然后使用优化器更新模型的参数。在每个训练轮次结束后，使用验证集评估模型的性能，根据验证集的结果调整超参数。
### 评估和调优
	- 模型评估：使用测试集对训练好的模型进行评估，计算模型在测试集上的性能指标，如准确率、召回率、F1 值等。
	- 调优：根据评估结果对模型进行调优。可以尝试调整超参数、修改模型结构、增加训练数据等方法，以提高模型的性能。
	
	
### 提升模型的方法

	- 微调
	```
		直接调整模型参数，增强特定领域知识输出效果，针对实时性不强的信息，有知识截止时间问题
	```
	- 知识库(RAG)
	```
		添加外部知识库，优化提示词，由模型总结输出，需对知识库维护更新
	```
	
### 什么是 embedding？

 ```
 " Embedding "是一种将离散型变量（如单词、句子或者整个文档）转化为连续的向量表示的技术。

 直白地说，在我们将自然语言处理为数据时会将文本转为向量，这个过程被称作 embedding。语义相似的文本的向量会位置相近，语义相反的文本的向量位置相反。LLM 使用这样的数据做训练，预测出后续的向量，从而生成文本。
 ```
	
### 函数调用

```
	LLM 函数调用（LLM Function Calling）是指在大语言模型（LLM）的交互过程中，让模型能够识别特定的输入并调用外部函数来完成更复杂、专业的任务，从而扩展模型的能力
```
	
	
### Dify "Too many incorrect password attempts"

```
	docker exec -it container-name redis-cli FLUSHALL
	或者
	找到redis容器，进入命令行控制台执行
	redis-cli FLUSHALL
```

### 数据标注工具

- [Label Studio](https://labelstud.io/guide/quick_start)
- [Doccano](https://github.com/doccano/doccano)

### 参考链接

- [DeepSeek 微调全攻略](https://www.intoep.com/othen/65051.html)

- [模型微调、知识库、智能体](https://blog.csdn.net/sunyuhua_keyboard/article/details/140046768)

- [Dify 大模型应用开发平台](https://dify.ai/zh)

- [Dify 中文文档](https://docs.dify.ai/zh-hans)

- [win10 Dify 502错误](https://blog.csdn.net/qq_53597256/article/details/143745465)

- [Dify 本地知识库搭建](https://zhuanlan.zhihu.com/p/20619350390)

- [微信对接API](http://doc.geweapi.com/)

- [个人微信对接API-E云管家](http://doc.geweapi.com/)

- [Dify使用教程](https://mp.weixin.qq.com/s/ft-nyKctk_jZb3Bps3Uyvw)

- 微信公众号消息有5秒超时限制，5秒内未回复，微信会尝试3次重复请求

- 微信个人公众号无客服消息权限？目前没在官网看到明确说明，认证后可推客服消息？

## Dify 与 微信对接环境搭建

### wsl update failed
	- [开启hyper-v、Linux子系统等](https://www.cnblogs.com/bokemoqi/p/17926296.html)
	- [wsl update failed](https://zhuanlan.zhihu.com/p/693634781)
	
### Docker 修改存储路径及设置Engine地址

### gewe、 dify(api)、dify on wechat网络配置

- 关掉翻墙软件
	- gewechat要求必须搭建服务到同省服务器或者电脑里方可正常使用

- 修改dify docker-compose.yaml 文件，在nginx配置中添加以下配置：
	```
	networks:
      - ssrf_proxy_network
      - default
	```

- 注释掉`ssrf_proxy_network` 中internal 设置，让其可以访问外网;

- gewe、dify on wechat在同一个docker-compose.yaml文件中启动，并添加以下网络配置:
	```
	networks:
	  gewechat_dify_on_wechat:
		driver: bridge
	  docker_ssrf_proxy_network:
		external: true
	```
- **检查gewe容器网络情况，ping qq.com等，若是不行重启docker**

- docker 镜像添加host
```sh
	extra_hosts:
      - "updates.dify.ai:104.26.8.156"
```


### docker 安装ping

```sh
	apt-get update
	apt-get install iputils-ping
```

### 问题收集
- 本地部署的模型，会出现无响应，线上的暂未遇到
- 本地部署的模型可以配置模型参数，线上的没有配置选项
- 不确切问题，模型没有询问确认问题，而是给出多个查询结果的组合（例如将女神节奖励和氪金图榜单奖励混合了）
- 知识库没有明确说明的问题，给出了不相干的回答（模拟其他软件的行为进行回答）
- 语音、图片消息