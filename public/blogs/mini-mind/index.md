![](/blogs/mini-mind/07c09629d24a34b7.png)

1. hello 输入 通过 字符串-int的映射（Tokenizer Encoder） 直接转化为vocab中的一个id，再通过embedding变为嵌入向量 后续再进入Transformer

2. 首先进入GQA后通过RMSnorm（默认均值为0的归一化），再通过线性层转化为 Q，K，V三种向量（K，V需要做缓存来减少重复运算） （考虑到GQA的多头问题 应该Q 有很多种 KV的种类会少几倍）

3. 之后对Q，K做位置编码，考虑到llm context length的可扩展性，要对RoPE做Yarn放缩

4. Q K 做matmul后得到相关性矩阵，再进行因果掩码，完成softmax后 对V做点积 获得上下文侧重信息

5. 再通过linear层进行多头拼接 投影回原维度，加上原本向量，完成残差连接

6. GQA完成后 送入FFN 先做RMSnorm 再做SwiGLU门控
	- 具体而言 是左侧做linear层升维 右侧做linaer+SiLu激活
	- 之后两者逐元素相乘 做了一个升维后的门控

7. 之后再做linear降维 dropout 和标准化之前的残差连接

8. Transformer 反复k次后 才算完成 之后再做一次RMSnorm  再通过lm_head 线性层将维度变换到词表大小 真正将结果变换为不同词语的得分 也就是logits 再通过softmax归一化 就变成了概率分布 采样一下 就会得到下一个词语接龙的词汇了


