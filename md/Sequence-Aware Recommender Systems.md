# Sequence-Aware Recommender Systems

Recommender systems are one of the most successful applications of data mining and machine-learning technology in practice. Academic research in the field is historically often based on the matrix completion problem formulation,where for each user-item-pair only one interaction (e.g., a rating) is considered. In many application domains, however, multiple user-item interactions of different types can be recorded over time. And, a number of recent works have shown that this information can be used to build richer individual user models and to discover additional behavioral patterns that can be leveraged in the recommendation process.

推荐系统是数据挖掘和机器学习技术在实践中最成功的应用之一。该领域的学术研究在历史上通常基于矩阵完成问题公式，其中每个用户-项目对只考虑一个交互 (例如，评级)。然而，在许多应用程序域中，随着时间的推移，可以记录不同类型的多个用户-项目交互。而且，许多最近的工作已经表明，这些信息可以用来构建更丰富的个人用户模型，并发现可以在推荐过程中使用的其他行为模式。

In this work, we review existing works that consider information from such sequentially ordered user-item interaction logs in the recommendation process. Based on this review, we propose a categorization of the corresponding recommendation tasks and goals, summarize existing algorithmic solutions, discuss methodological approaches when benchmarking what we call sequence-aware recommender systems, and outline open challenges in the area.

## 1 INTRODUCTION

Recommender Systems (RS) are software applications that support users in finding items of interest within larger collections of objects, often in a personalized way. Today, such systems are used in a variety of application domains, including, for example, e-commerce or media streaming, and receiving automated recommendations of different forms has become a part of our daily online user experience. Internally, such systems analyze the past behavior of individual users or of a user community as a whole to detect patterns in the data. On typical online sites, various types of relevant actions of a user can be recorded, for example, that a user views an item or makes a purchase, and several of the actions of a single user may relate to the same item. These recorded actions and the detected patterns are then used to compute recommendations that match the preference profiles of individual users.

推荐系统 (RS) 是支持用户在更大的对象集合中找到感兴趣的项目的软件应用程序，通常是以个性化的方式。今天，这种系统被用于各种应用领域，例如电子商务或媒体流, 接受不同形式的自动推荐已经成为我们日常在线用户体验的一部分。在内部，这些系统分析单个用户或整个用户社区的过去行为，以检测数据中的模式。在典型的在线网站上，可以记录用户的各种类型的相关行为，例如，用户查看项目或进行购买, 单个用户的几个操作可能与同一项目相关。然后，这些记录的操作和检测到的模式用于计算与单个用户的偏好配置文件相匹配的建议。

## 2 CHARACTERIZING SEQUENCE-AWARE RECOMMENDER SYSTEMS

## 3 A CATEGORIZATION OF SEQUENCE-AWARE RECOMMENDATION TASKS

## 4 ALGORITHMS FOR SEQUENCE-AWARE RECOMMENDER SYSTEMS

## 5 EVALUATION OF SEQUENCE-AWARE RECOMMENDER SYSTEMS

## 6 SUMMARY AND FUTURE DIRECTIONS

