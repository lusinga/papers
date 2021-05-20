# Time-Aware Click Model

Click-through information is considered as a valuable source of users’ implicit relevance feedback for commercial search engines. As existing studies have shown that the search result position in a search engine result page (SERP) has a very strong influence on users’ examination behavior, most existing click models are position based, assuming that users examine results from top to bottom in a linear fashion. Although these click models have been successful, most do not take temporal information into account. As many existing studies have shown, click dwell time and click sequence information are strongly correlated with users’ perceived relevance and search satisfaction. Incorporating temporal information may be important to improve performance of user click models for Web searches. In this article, we investigate the problem of properly incorporating temporal information into click models. We first carry out a laboratory eye-tracking study to analyze users’ examination behavior in different click sequences and find that the user common examination path among adjacent clicks is linear. Next, we analyze the user dwell time distribution in different search logs and find that we cannot simply use a click dwell time threshold (e.g., 30 seconds) to distinguish relevant/irrelevant results. Finally, we propose a novel time-aware click model (TACM), which captures the temporal information of user behavior. We compare the TACM to several existing click models using two real-world search engine logs. Experimental results show that the TACM outperforms other click models in terms of both predicting click behavior (perplexity) and estimating result relevance (NDCG).

对于商业搜索引擎来说，点击贯通信息被认为是一种有价值的用户隐性相关性反馈来源。已有研究表明，搜索引擎结果页面(SERP)中的搜索结果位置对用户的检查行为有很强的影响，现有的大多数点击模型都是基于位置的，假设用户从上到下以线性方式检查结果。虽然这些点击模型是成功的，但是大多数没有考虑到时间信息。已有研究表明，点击停留时间和点击顺序信息与用户感知相关性和搜索满意度有很强的相关性。合并时间信息对于提高Web搜索的用户点击模型的性能可能很重要。在本文中，我们研究了将时间信息适当地合并到单击模型中的问题。我们首先进行了实验室眼动研究，分析了用户在不同点击序列下的检测行为，发现相邻点击之间的用户共同检测路径是线性的。接下来，我们分析了不同搜索日志中的用户停留时间分布，发现我们不能简单地使用点击停留时间阈值(例如30秒)来区分相关/不相关的结果。最后，我们提出了一种新的时间感知点击模型(TACM)，它可以捕获用户行为的时间信息。我们使用两个真实的搜索引擎日志将TACM与几个现有的点击模型进行比较。实验结果表明，该模型在预测点击行为(perplexity)和估计结果相关性(NDCG)方面均优于其他点击模型。

## 1. INTRODUCTION

## 2. RELATED WORK

## 3. USER BEHAVIOR ANALYSIS

## 4. TIME-AWARE CLICK MODEL

## 5. EXPERIMENTS

## 6. CONCLUSIONS AND FUTURE WORK

In this article, we address the problem of properly incorporating temporal information into click models. First, we carried out a laboratory eye-tracking experiment to analyze search users’ examination behaviors. From the observations, we formulated two assumptions: the locally unidirectional assumption and the non–first-order examination assumption. We also made an analysis of user click dwell time in different search logs. Based on our findings, we proposed a new click model named TACM, which incorporates both click dwell time information and nonsequential click behaviors into click models while following the two assumptions on the examinations between two clicks. The experimental results on large-scale click-through data showed that our model outperforms existing models in click prediction. We also conducted a test on query-result relevance estimation. The experimental results also showed that the TACM outperforms existing models in relevance evaluation tasks.

在本文中，我们解决了将时间信息适当地合并到单击模型中的问题。首先，我们进行了实验室眼动实验，分析搜索用户的检查行为。根据观测结果，我们给出了两个假设:局部单向假设和非一阶检验假设。我们还分析了用户在不同搜索日志中的点击停留时间。在此基础上，我们提出了一种新的点击模型，名为TACM，它将点击停留时间信息和非连续的点击行为合并到点击模型中，同时遵循两次点击之间检查的两个假设。大规模点击数据的实验结果表明，我们的模型在点击预测方面优于现有模型。我们还对查询结果相关性估计进行了测试。实验结果也表明，该模型在关联评价任务方面优于现有模型。

This study shows the importance for a click model to correctly cope with users’ interactions. Compared to previous models, the assumptions made in our model are more realistic and correspond better to observations from practice. Our experimental results show that different click dwell times among click actions indicate different kinds of feedback information. A longer dwell time represents that users are willing to spend more time on the search result and may further indicate that the search result contains more useful information. We also find that the law of diminishing marginal utility exists in a search environment, as the negative exponential mapping function performs best among several different mapping functions.

这项研究显示了点击模型正确处理用户交互的重要性。与以前的模型相比，我们的模型所作的假设更加真实，更符合实践观察。我们的实验结果表明，不同的点击停留时间表明不同的反馈信息。停留时间越长，说明用户愿意在搜索结果上花费更多的时间，可能进一步表明搜索结果包含更多有用的信息。我们还发现在搜索环境中存在边际效用递减的规律，即负指数映射函数在几种不同的映射函数中表现最好。

The proposed model can be further improved in several aspects. As different search users may follow different behavior patterns, we plan to add factors that can tell the difference between different users and thus make our model more personalized. Meanwhile, we will try to improve the dwell time mapping function to make it more adaptable to scenarios with different search intents. Furthermore, with more and more multimodal content incorporated into search interfaces, SERPs become more and more heterogeneous. We plan to extend the TACM to model user behaviors in a heterogeneous search environment. In addition, we believe that the proposed model can be extended to model user interaction behaviors other than clicks in Web search scenarios (hover, scroll, etc.)

该模型还可以在几个方面进行进一步的改进。由于不同的搜索用户可能遵循不同的行为模式，我们计划添加能够区分不同用户之间差异的因素，从而使我们的模型更加个性化。同时，我们将尝试改进停留时间映射函数，使其更适应不同搜索意图的场景。此外，随着越来越多的多模式内容被合并到搜索界面中，serp变得越来越异构。我们计划对TACM进行扩展，以对异构搜索环境中的用户行为进行建模。此外，我们相信所提出的模型可以扩展到模型用户交互行为，而不是Web搜索场景中的点击(悬停、滚动等)。