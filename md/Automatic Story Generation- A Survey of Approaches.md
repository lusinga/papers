# Automatic Story Generation: A Survey of Approaches

Computational generation of stories is a subfield of computational creativity where artificial intelligence and psychology intersect to teach computers how to mimic humans’ creativity. It helps generate many stories with minimum effort and customize the stories for the users’ education and entertainment needs. Although the automatic generation of stories started to receive attention many decades ago, advances in this field to date are less than expected and suffer from many limitations. This survey presents an extensive study of research in the area of non-interactive textual story generation, as well as covering resources, corpora, and evaluation methods that have been used in those studies. It also shed light on factors of story interestingness.

故事的计算生成是计算创造力的一个子领域，其中人工智能和心理学相交以教计算机如何模仿人类的创造力。 它有助于以最少的努力生成许多故事，并根据用户的教育和娱乐需求定制故事。 虽然故事的自动生成在几十年前就开始受到关注，但迄今为止该领域的进展并不如预期，并且受到许多限制。 该调查对非交互式文本故事生成领域的研究进行了广泛的研究，并涵盖了这些研究中使用的资源、语料库和评估方法。 它还阐明了故事有趣的因素。

## 1 INTRODUCTION

Stories are a significant part of every culture, attracting people regardless of age. For that reason, stories have always been a medium for entertainment, moral lessons, and wisdom inspiration. In recent decades, stories have also been used as tools for assessing and educating children.

故事是每一种文化的重要组成部分，吸引着不分年龄的人们。 出于这个原因，故事一直是娱乐、道德课程和智慧启发的媒介。 近几十年来，故事也被用作评估和教育儿童的工具。

We may define creativity as the ability to generate novel and valuable ideas, where valuable means beautiful, interesting, and useful [26]. Generating stories using computers is a complex task of computational creativity, which lies in the area where psychology and artificial intelligence (AI) intersect. To teach computers how to generate a story, we need to understand how humans create one. Knowing this enables computer scientists to mimic the human brain. However, generating stories using computers helps psychologists better understand human cognition.

我们可以将创造力定义为产生新颖且有价值的想法的能力，其中有价值意味着美丽、有趣和有用 [26]。 使用计算机生成故事是计算创造力的一项复杂任务，它位于心理学和人工智能 (AI) 交叉的领域。 为了教计算机如何生成故事，我们需要了解人类是如何创造故事的。 知道这一点，计算机科学家就可以模仿人脑。 然而，使用计算机生成故事有助于心理学家更好地理解人类认知。

Many applications can benefit from automatic story generation, e.g., entertainment, where many stories can be produced with minimum effort [12, 107]. It can also be used for education where stories are customized to the learners’ needs [13, 99]. In gaming, interactive stories play a major role in increasing the interestingness of games [67, 173].

许多应用程序可以从自动故事生成中受益，例如娱乐，其中可以以最少的努力生成许多故事 [12, 107]。 它还可以用于根据学习者的需求定制故事的教育[13, 99]。 在游戏中，互动故事在增加游戏的趣味性方面发挥着重要作用 [67, 173]。

Automated story generation is the problem of mechanically selecting a sequence of events or actions that meet a set of criteria and can be told as a story [90]. Each story has a story world, interacting characters, and objects. Furthermore, a story may have an author goal, which is the message that the author aims to deliver to the story receiver through the story events. Although story generation systems started in early 1960s [150], they did not achieve outstanding results and are still classified as weak AI systems because their creativity is not comparable to humans [75].

自动故事生成是机械地选择符合一组标准并可以作为故事讲述的一系列事件或动作的问题 [90]。 每个故事都有一个故事世界、互动角色和对象。 此外，一个故事可能有一个作者目标，即作者旨在通过故事事件传递给故事接收者的信息。 尽管故事生成系统始于 1960 年代初期 [150]，但它们并没有取得突出的成果，仍然被归类为弱人工智能系统，因为它们的创造力无法与人类相媲美 [75]。

For a computer system to be creative, it must generate stories different from past seen ones. Many attributes must be considered, including story settings such as time and space, story characters, their desires, and plans to achieve these desires. In addition, the interactions between characters and conflicts that may occur between characters’ desires are essential. These attributes result in an enormous growth of the story space, and hence searching for a story in this vast space is difficult, inefficient, or impossible. The large number of attributes makes story generation difficult, and accounting for its aim, believability, and interestingness further complicates the generation process. Open story generation, where stories are generated without relying on a priori engineered domain models, adds two extra challenges to story generation: the automatic construction of the domain model and evaluating the story progress to guide the generation process.

要使计算机系统具有创造性，它必须生成与以往不同的故事。 必须考虑许多属性，包括时间和空间等故事设置、故事人物、他们的愿望以及实现这些愿望的计划。 此外，人物之间的互动以及人物欲望之间可能发生的冲突也是必不可少的。 这些属性导致故事空间的巨大增长，因此在这个广阔的空间中搜索故事是困难的、低效的或不可能的。 大量的属性使故事生成变得困难，考虑到其目标、可信度和趣味性，生成过程进一步复杂化。 开放式故事生成，即在不依赖先验工程领域模型的情况下生成故事，为故事生成增加了两个额外的挑战：领域模型的自动构建和评估故事进度以指导生成过程。

## 2 DEFINITIONS

## 3 STRUCTURAL MODELS

### 3.1 Graph-Based Approaches

### 3.2 Grammar-Based Approaches

## 4 PLANNING-BASED MODELS

### 4.1 Goal-Directed Approaches

### 4.2 Analogy-Based Approaches

### 4.3 Heuristic Search Approaches

## 5 ML MODELS

### 5.1 Story Abstraction

### 5.2 Script Learning and Generation

### 5.3 Story Completion

### 5.4 Story Generation

## 6 KNOWLEDGE SOURCES FOR STORYTELLING

### 6.1 Types of Knowledge for Story Generation

### 6.2 Story and Semantic Relationships Corpora

### 6.3 Crowdsourcing Knowledge

### 6.4 Commonsense Knowledge

## 7 TOWARD INTERESTING STORIES

## 8 STORY EVALUATION

## 9 DISCUSSION

## 10 CONCLUSION AND FUTURE DIRECTIONS

Although automatic story generation is a long-standing computational problem, the recent advances in ML are expected to accelerate this field’s development. It is worth mentioning that there is a renewed interest in automatic story generation. We believe that as of this writing, there is much ongoing research on this topic. Therefore, it is worth pointing out the limitations of our survey and propose some future research directions.


