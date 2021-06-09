# A Survey on Conversational Recommender Systems

Recommender systems are software applications that help users to find items of interest in situations of information overload. Current research often assumes a one-shot interaction paradigm, where the users’ preferences are estimated based on past observed behavior and where the presentation of a ranked list of suggestions is themain, one-directional form of user interaction. Conversational recommender systems (CRS) take a different approach and support a richer set of interactions. These interactions can, for example, help to improve the preference elicitation process or allow the user to ask questions about the recommendations and to give feedback. The interest in CRS has significantly increased in the past few years. This development is mainly due to the significant progress in the area of natural language processing, the emergence of new voice-controlled home assistants, and the increased use of chatbot technology.With this article, we provide a detailed survey of existing approaches to conversational recommendation.We categorize these approaches in various dimensions, e.g., in terms of the supported user intents or the knowledge they use in the background. Moreover, we discuss technological approaches, review how CRS are evaluated, and finally identify a number of gaps that deserve more research in the future.

推荐系统是一种软件应用程序，可帮助用户在信息过载的情况下找到感兴趣的项目。当前的研究通常采用一次性交互范式，其中根据过去观察到的行为来估计用户的偏好，并且建议的排名列表的呈现是用户交互的主要单向形式。会话推荐系统 (CRS) 采用不同的方法并支持更丰富的交互集。例如，这些交互可以帮助改进偏好获取过程或允许用户提出有关推荐的问题并提供反馈。过去几年，人们对 CRS 的兴趣显着增加。这一发展主要是由于自然语言处理领域的重大进步、新的语音控制家庭助理的出现以及聊天机器人技术的增加使用。 通过本文，我们对现有的对话式推荐方法进行了详细调查.我们在不同的维度对这些方法进行分类，例如，根据支持的用户意图或他们在后台使用的知识。此外，我们讨论了技术方法，回顾了 CRS 的评估方式，并最终确定了一些值得在未来进行更多研究的差距。

## 1 INTRODUCTION

Recommender systems are among the most visible success stories of AI in practice. Typically, the main task of such systems is to point users to potential items of interest, e.g., in the context of an e-commerce site. Thereby, they not only help users in situations of information overload [126], but they also can significantly contribute to the business success of the service providers [57].

推荐系统是人工智能在实践中最明显的成功案例之一。 通常，此类系统的主要任务是将用户指向潜在感兴趣的项目，例如，在电子商务站点的上下文中。 因此，它们不仅可以在信息过载的情况下帮助用户 [126]，而且还可以为服务提供商的业务成功做出重大贡献 [57]。

- [126] Francesco Ricci, Lior Rokach, Bracha Shapira, and Paul B. Kantor. 2015. Recommender Systems Handbook (2nd ed.). Springer-Verlag.
- [57] Dietmar Jannach and Michael Jugovac. 2019. Measuring the business value of recommender systems. ACM Trans. Manage. Inf. Syst. 10, 4 (2019), 1–23.
  
In many of these practical applications, recommending is a one-shot interaction process. Typically, the underlying system monitors the behavior of its users over time and then presents a tailored set of recommendations in pre-defined navigational situations, e.g., when a user logs in to the service. Although such an approach is common and useful in various domains, it can have a number of potential limitations. There are, for example, a number of application scenarios, where the user preferences cannot be reliably estimated from their past interactions. This is often the case with high-involvement products (e.g., when recommending a smartphone), where we even might have no past observations at all. Furthermore, what to include in the set of recommendations can be highly context dependent, and it might be difficult to automatically determine the user’s current situation or needs. Finally, another assumption often is that users already know their preferences when they arrive at the site. This might, however, not necessarily be true. Users might also construct their preferences only during the decision process [152], when they become aware of the space of the options. In some cases, they might also learn about the domain and the available options only during the interaction with the recommender [154].

在许多这些实际应用中，推荐是一个一次性的交互过程。通常，底层系统会随着时间的推移监控其用户的行为，然后在预定义的导航情况下（例如，当用户登录服务时）呈现一组量身定制的建议。尽管这种方法在各个领域都很常见且有用，但它可能有许多潜在的限制。例如，在许多应用场景中，无法从用户过去的交互中可靠地估计用户偏好。对于高度参与的产品（例如，在推荐智能手机时），我们甚至可能根本没有过去的观察。此外，推荐集中包含的内容可能高度依赖于上下文，并且可能难以自动确定用户的当前情况或需求。最后，另一个假设通常是用户在到达站点时已经知道他们的偏好。然而，这可能不一定是真的。用户也可能仅在决策过程中构建他们的偏好 [152]，当他们意识到选项的空间时。在某些情况下，他们也可能仅在与推荐者交互期间了解域和可用选项 [154]。

The promise of Conversational Recommender Systems (CRS) is that they can help to address many of these challenges. The general idea of such systems, broadly speaking, is that they support a task-oriented, multi-turn dialogue with their users. During such a dialogue, the system can elicit the detailed and current preferences of the user, provide explanations for the item suggestions, or process feedback by users on the made suggestions. Given the significant potential of such systems, research on CRS already has some tradition. Already in the late 1970s, Rich [127] envisioned a computerized librarian that makes reading suggestions to users by interactively asking them questions, in natural language, about their personality and preferences. Besides interfaces based on natural language processing (NLP), a variety of form-based user interfaces1 were proposed over the years. One of the earlier interaction approaches in CRS based on such interfaces is called critiquing, which was proposed as a means for query reformulation in the database field already in 1982 [144]. In critiquing approaches, users are presented with a recommendation soon in the dialogue and can then apply pre-defined critiques on the recommendations, e.g., (“less $$”) [15, 49].

会话推荐系统 (CRS) 的承诺是它们可以帮助解决许多这些挑战。从广义上讲，此类系统的总体思路是它们支持与用户进行面向任务的多轮对话。在这样的对话期间，系统可以引出用户的详细和当前偏好，为项目建议提供解释，或者处理用户对提出的建议的反馈。鉴于此类系统的巨大潜力，对 CRS 的研究已经有了一些传统。早在 1970 年代后期，Rich [127] 就设想了一个计算机化的图书管理员，通过以自然语言交互式地向用户询问有关他们的个性和偏好的问题，从而为用户提供阅读建议。除了基于自然语言处理 (NLP) 的界面外，多年来还提出了各种基于表单的用户界面 1。基于此类接口的 CRS 中较早的交互方法之一称为批评，它早在 1982 年就被提议作为数据库领域中查询重构的一种手段 [144]。在评论方法中，用户很快就会在对话中看到推荐，然后可以对推荐应用预定义的评论，例如 (“less $$”) [15, 49]。


Form-based approaches can generally be attractive as the actions available to the users are predefined and non-ambiguous. However, such dialogues may also appear non-natural, and users might feel constrained in the ways they can express their preferences. NLP-based approaches, however, for a long time suffered from existing limitations, e.g., in the context of processing voice commands. In recent years, however, major advances were made in language technology. As a result, we are nowadays used to issuing voice commands to our smartphones and digital home assistants, and these devices have reached an impressive level of recognition accuracy. In parallel to these developments in the area of voice assistants, we have observed a fast uptake of chatbot technology in recent years. Chatbots, both rather simple and more sophisticated ones, are usually able to process natural language and are nowadays widely used in various application domains, e.g., to deal with customer service requests.

基于表单的方法通常很有吸引力，因为用户可用的操作是预定义的且不含糊的。 然而，这样的对话也可能显得不自然，用户可能会在表达偏好的方式上感到受限。 然而，基于 NLP 的方法长期以来受到现有限制的影响，例如在处理语音命令的上下文中。 然而，近年来，语言技术取得了重大进展。 因此，我们现在习惯于向我们的智能手机和数字家庭助理发出语音命令，这些设备的识别准确率已经达到了令人印象深刻的水平。 在语音助手领域取得这些发展的同时，我们观察到近年来聊天机器人技术的快速发展。 聊天机器人，既简单又复杂，通常能够处理自然语言，现在广泛用于各种应用领域，例如处理客户服务请求。

These technological advances led to an increased interest in CRS during the last years. In contrast to many earlier approaches, we however observe that today’s technical proposals are more often based onmachine learning technology instead of following pre-defined dialogue paths. However, often there still remains a gap between the capabilities of today’s voice assistants and chatbots compared to what is desirable to support truly conversational recommendation scenarios [117], in particular when the system is voice controlled [161, 165].

这些技术进步导致过去几年对 CRS 的兴趣增加。 然而，与许多早期的方法相比，我们观察到今天的技术建议更多地基于机器学习技术，而不是遵循预先定义的对话路径。 然而，与支持真正对话式推荐场景 [117] 所需的能力相比，当今的语音助手和聊天机器人的能力之间通常仍然存在差距 [117]，特别是当系统是语音控制的 [161, 165] 时。

In this article, we review the literature on CRS in terms of common building blocks of a typical conceptual architecture of CRS. Specifically, after providing a definition and a conceptual architecture of a CRS in Section 2, we discuss (i) interaction modalities of CRS (Section 3), (ii) the knowledge and data they are based upon (Section 4), and (iii) the computational tasks that have to be accomplished in a typical CRS (Section 5). Afterwards, we discuss evaluation approaches for CRS (Section 6) and finally give an outlook on future directions.

在本文中，我们根据典型的 CRS 概念架构的通用构建块来回顾有关 CRS 的文献。 具体而言，在第 2 节中提供 CRS 的定义和概念架构之后，我们将讨论 (i) CRS 的交互模式（第 3 节），（ii）它们所基于的知识和数据（第 4 节），以及（iii） ) 必须在典型的 CRS 中完成的计算任务（第 5 节）。 之后，我们讨论了 CRS 的评估方法（第 6 节），最后对未来的方向进行了展望。

## 2 DEFINITIONS AND RESEARCH METHODOLOGY

In this section, we discuss relevant preliminaries to our work. First, we provide a general characterization and conceptual model of CRS. Second, we discuss our research methodology.

在本节中，我们将讨论我们工作的相关预备知识。 首先，我们提供了 CRS 的一般特征和概念模型。 其次，我们讨论我们的研究方法。

### 2.1 Characterization of Conversational Recommender Systems

There is no widely established definition in the literature of what represents a CRS. In this work, we use the following definition.

Definition 2.1 (Conversational Recommender System–CRS). A CRS is a software system that supports its users in achieving recommendation-related goals through a multi-turn dialogue.

One fundamental characteristic of CRS is their task-orientation, i.e., they support recommendation specific tasks and goals. The main task of the system is to provide recommendations to the users, with the goal to support their users’ decision-making process or to help them find relevant information. Additional tasks of CRS include the acquisition of user preferences or the provision of explanations. This specific task orientation distinguishes CRS from other dialogue-based systems, such as the early ELIZA system [158] or similar chat robot systems [151].

CRS 的一个基本特征是它们的任务导向，即它们支持推荐特定的任务和目标。 系统的主要任务是向用户提供推荐，目的是支持用户的决策过程或帮助他们找到相关信息。 CRS 的其他任务包括获取用户偏好或提供解释。 这种特定的任务方向将 CRS 与其他基于对话的系统区分开来，例如早期的 ELIZA 系统 [158] 或类似的聊天机器人系统 [151]。

The other main feature of a CRS according to our definition is that there is a multi-turn conversational interaction. This stands in contrast to systems that merely support question-answering (Q&A tools). Providing one-shot Q&A-style recommendations is a common feature of personal digital assistants like Apple’s Siri and similar products. While these systems already today can reliably respond to recommendation requests, e.g., for a restaurant, they often face difficulties maintaining a multi-turn conversation. A CRS therefore explicitly or implicitly implements some form of dialogue state management to keep track of the conversation history and the current state.

根据我们的定义，CRS 的另一个主要特征是多轮对话交互。 这与仅支持问答（问答工具）的系统形成对比。 提供一次性问答式推荐是 Apple 的 Siri 和类似产品等个人数字助理的常见功能。 虽然这些系统今天已经可以可靠地响应推荐请求，例如，对于一家餐馆，他们经常面临保持多轮对话的困难。 因此，CRS 显式或隐式地实施某种形式的对话状态管理以跟踪对话历史和当前状态。

Note that our definition does not make any assumptions regarding the modality of the inputs and the outputs. CRS can be voice controlled, accept typed text, or obtain their inputs via form fields, buttons, or even gestures. Likewise, the output is not constrained and can be voice, speech, text, or multimedia content. No assumptions are also made regarding who drives the dialogue.

请注意，我们的定义并未对输入和输出的模态做出任何假设。 CRS 可以进行语音控制，接受输入的文本，或通过表单字段、按钮甚至手势获取输入。 同样，输出不受限制，可以是语音、语音、文本或多媒体内容。 也没有对谁推动对话做出任何假设。

Generally, conversational recommendation shares a number of similarities with conversational search [115]. In terms of the underlying tasks, search and recommendation have in common that one main task is to rank the objects according to their assumed relevance, either for a given query (search) or the preferences of the user (recommendation). Furthermore, in terms of the conversational part, both types of systems have to interpret user utterances and disambiguate user intents in case natural language interactions are supported. In conversational search systems, however, the assumption often is that the interaction is based on “written or spoken form” [115], whereas in our definition of CRS various types of input modalities are possible. Overall, the boundary between (personalized) conversational search and recommendation systems often seems blurry, see References [86, 139, 172], in particular as often similar technological approaches are applied. In this survey, we limit ourselves to works that explicitly mention recommendation as one of their target problems.

通常，会话推荐与会话搜索[115]有许多相似之处。就基本任务而言，搜索和推荐的共同点是，一项主要任务是根据对象假定的相关性对对象进行排名，无论是针对给定的查询（搜索）还是用户的偏好（推荐）。此外，就会话部分而言，如果支持自然语言交互，两种类型的系统都必须解释用户话语并消除用户意图的歧义。然而，在对话搜索系统中，通常假设交互基于“书面或口头形式”[115]，而在我们对 CRS 的定义中，各种类型的输入模式都是可能的。总体而言，（个性化）对话搜索和推荐系统之间的界限通常看起来很模糊，参见参考文献 [86, 139, 172]，特别是经常应用类似的技术方法。在本次调查中，我们将自己限制在明确提到推荐作为其目标问题之一的作品。

### 2.2 Conceptual Architecture of a CRS

A variety of technical approaches for building CRS were proposed in the past two decades. The specifics of the technical architecture of such solutions depend on the system’s functionality, i.e., whether or not voice input is supported. Still, a number of typical interoperating conceptual components of such architectures can be identified, as shown in Figure 1.

- Computational Elements. One central part of such an architecture usually is a Dialogue Management System (also called “state tracker” or similarly in some systems). This component drives the process flow. It receives the processed inputs, e.g., the recognized intents, entities and preferences, and correspondingly updates the dialogue state and user model. After that, using a recommendation and reasoning engine and background knowledge, it determines the next action and returns appropriate content like a recommendation list, an explanation, or a question to the output generation component.
- The User Modeling System can be a component of its own, in particular when there are long term user preferences to be considered, or not. In some cases, the current preference profile is implicitly part of the dialogue system. The Recommendation and Reasoning Engine is responsible for retrieving a set of recommendations, given the current dialogue state and preference model. This component might also implement other complex reasoning functionality, e.g., to generate explanations or to compute a query relaxation (see later). Besides these central components, typical CRS architectures comprise modules for input and output processing. These can, for example, include speech-to-text conversion and speech generation. On the input side—in particular in the case of natural language input—additional tasks are usually supported, including intent detection and named entity recognition [66, 99], for identifying the users’ intentions and entities (e.g., attributes of item) in their utterances.
- Knowledge Elements. Various types of knowledge are used in CRS. The Item Database is something that is present in almost all solutions, representing the set of recommendable items, sometimes including details about their attributes. In addition to that, different types of Domain and Background Knowledge are often leveraged by CRS. Many approaches explicitly encode dialogue knowledge in different ways, e.g., in the form of pre-defined dialogue states, supported user intents, and the possible transitions between the states. This knowledge can be general or specific to a particular domain. The knowledge can furthermore either be encoded by the system designers or automatically learned from other sources or previous interactions. A typical example for learning approaches are those that use machine learning to build statistical models from corpora of recorded dialogues. Generally, domain and background knowledge can be used by all computational elements. Input processing may need information about entities to be recognized or knowledge about the pre-defined intents. The user modeling component may be built on estimated interest weights regarding certain item features, and the reasoning engine may use explicit inference knowledge to derive the set of suitable recommendations.

### 2.3 Research Method: Identifying RelevantWorks

We followed a semi-systematic approach to identify relevant papers.We first queried several digital libraries2 using pre-defined search strings such as “conversational recommender system,” “interactive recommendation,” “advisory system,” or “chatbot recommender.” The returned papers were then manually checked for their relevance based on titles and abstracts. Papers considered relevant were read in detail and, if considered to be in the scope of the paper, used as a starting point for a snowballing procedure. Overall, the paper selection process surfaced 121 papers on CRS that we considered in this work.3 Looking at the type of these papers, the majority of the works described technical proposals for one of the computational components of a CRS architecture. A smaller set of papers described demo systems. Another smaller set were analytical ones that, for example, reviewed certain general characteristics of CRS.

Generally, we only included papers that are compliant with our definition of a CRS given above. We therefore did not include papers that discussed one-shot or multi-step question-answering systems [133, 166], even when the question or task was about a recommendation.We also did not consider general dialogue systems like chatbot systems, which are not task-oriented, or systems that only support a query-response interaction process like a search engine without further dialogue steps, e.g., Reference [31]. Furthermore, we did not include dialogue-based systems, which were task-oriented, but not on a recommendation task, e.g., the end-to-end learning approaches presented in Reference [159] and Reference [76], which focus on restaurant search and movie-ticket booking. Furthermore, we excluded a few works like Reference [50] or Reference [174], which use the term “interactive recommendation,” which however rather refers to a system that addresses observed user interest changes over time, but is not designed to support a dialogue with the user. Other works like Reference [138] or Reference [174] mainly focus on finding good strategies for acquiring an initial set of ratings for cold-start users.While these works can be seen as supporting an interactive process, there is only one type of interaction, which is furthermore mostly limited to a profile-building phase. Finally, there are a number of works where users of a recommendation systems are provided with mechanisms to fine-tune their recommendations, which is sometimes referred to as “user control” [61]. Such works, e.g., Reference [163], in principle support user actions that can be found in some CRS, for example to give feedback on a recommendation. The interaction style of such approaches is however not a dialogue with the system.


## 3 INTERACTION MODALITIES OF CRS

### 3.1 Input and Output Modalities

### 3.2 Application Environment

### 3.3 Interaction Initiative

### 3.4 Discussion

## 4 UNDERLYING KNOWLEDGE AND DATA

### 4.1 User Intents

### 4.2 User Modeling

### 4.3 Dialogue States

### 4.4 Background Knowledge

### 4.5 Discussion

## 5 COMPUTATIONAL TASKS

### 5.1 Main Tasks

### 5.2 Supporting Tasks

### 5.3 Discussion

## 6 EVALUATION OF CONVERSATIONAL RECOMMENDERS

### 6.1 Overview ofQuality Dimensions, Measurements, and Methods

### 6.2 Review of Evaluation Approaches

### 6.3 Discussion

## 7 OUTLOOK

Our study reveals an increased rise in the area of CRS in the past few years, where the most recent approaches rely on machine learning techniques, in particular deep learning, and natural language based interactions. Despite these advances, a number of research questions remain open, as outlined in the discussion sections throughout the paper. In this final section, we briefly discuss four more general research directions.

我们的研究表明，在过去几年中，CRS 领域呈上升趋势，其中最新的方法依赖于机器学习技术，尤其是深度学习和基于自然语言的交互。 尽管取得了这些进展，一些研究问题仍然悬而未决，如整篇论文的讨论部分所述。 在最后一节中，我们将简要讨论四个更一般的研究方向。

One first question is “Which interaction modality supports the user best in a given task?.” While voice and written natural language have become more popular recently, more research is required to understand which modality is suited for a given task and situation at hand or if alternative modalities should be offered to the user. An interesting direction of research also lies in the interpretation of non-verbal communication acts by users. Furthermore, entirely voice-based CRS have limitations when it comes to present an entire set of recommendations in one interaction cycle. In such a setting, a summarization of a set of recommendations might be needed, as it might in most cases not be meaningful when the CRS reads out several options to the user.

第一个问题是“在给定任务中哪种交互方式最能支持用户？”。 虽然语音和书面自然语言最近变得越来越流行，但需要更多的研究来了解哪种模式适合手头的给定任务和情况，或者是否应该向用户提供替代模式。 一个有趣的研究方向还在于用户对非语言交流行为的解释。 此外，在一个交互周期中呈现一整套建议时，完全基于语音的 CRS 具有局限性。 在这种情况下，可能需要总结一组建议，因为在大多数情况下，当 CRS 向用户读出多个选项时，这可能没有意义。

Second, we ask: “What are challenges and requirements in non-standard application environments?” Today, most existing research focuses on interactive web or mobile applications, either with forms and buttons or with natural language input in chatbot applications. Some of the discussed works go beyond such scenarios and consider alternative environments where CRS can be used, e.g., within physical stores, in cars, on kiosk solutions, or as a feature of (humanoid) robots. However, little is known so far about the specific requirements, challenges, and opportunities that come with such application scenarios and regarding the critical factors that determine the adoption and value of such systems. Regarding the usage scenarios, most research works discussed in our survey focus on one-to-one communications. However, there are additional scenarios that are not much explored yet, for example, where the CRS supports group decision processes [1, 103].

其次，我们问：“非标准应用环境中的挑战和要求是什么？” 今天，大多数现有研究都集中在交互式网络或移动应用程序上，无论是在聊天机器人应用程序中使用表单和按钮还是使用自然语言输入。 一些讨论的工作超出了这些场景，并考虑了可以使用 CRS 的替代环境，例如，在实体店内、汽车中、信息亭解决方案上，或作为（人形）机器人的特征。 然而，到目前为止，对于此类应用场景带来的具体要求、挑战和机遇，以及决定此类系统的采用和价值的关键因素，我们知之甚少。 关于使用场景，我们调查中讨论的大多数研究工作都集中在一对一通信上。 然而，还有一些其他场景尚未得到充分探索，例如，CRS 支持群体决策过程 [1, 103]。

A third question is “What can we learn from theories of conversation?,” see also Reference [141]. Regarding the underpinnings and adoption factors of CRS, only very few works are based on concepts and insights from Conversation Analysis, Communication Theory or related fields. In some works, at least certain communication patterns in real-world recommendation dialogues were discussed at a qualitative or anecdotal level.What seems to be mostly missing so far, however, is a clearer understanding of what makes a CRS truly helpful, what users expect from such a system, what makes them fail [95], and which intents we should or must support in a system. Explanations are often considered as a main feature for a convincing dialogue, but these aspects are not explored a lot. In addition, more research is required to understand the mechanisms that increase the adoption of CRS, e.g., by increasing the user’s trust and developing intimacy [72], or by adapting the communication style (e.g., with respect to the initiative and language) to the individual user.

第三个问题是“我们可以从对话理论中学到什么？”，另见参考文献 [141]。关于 CRS 的基础和采用因素，很少有作品基于对话分析、传播理论或相关领域的概念和见解。在一些作品中，至少在定性或轶事层面讨论了现实世界推荐对话中的某些通信模式。 然而，到目前为止，似乎主要缺少的是对 CRS 真正有用的原因和用户期望的更清晰的理解从这样的系统中，是什么导致它们失败 [95]，以及我们应该或必须在系统中支持哪些意图。解释通常被认为是令人信服的对话的主要特征，但对这些方面的探讨并不多。此外，还需要更多的研究来了解增加采用 CRS 的机制，例如，通过增加用户的信任和发展亲密关系 [72]，或通过调整沟通方式（例如，在主动性和语言方面）以适应个人用户。

Finally, from a technical and methodological perspective, we ask: “How far do we get with pure end-to-end learning approaches, i.e., by creating systems where, besides the item database, only a corpus of past conversations serves as input. Tremendous advances were made in NLP technology in recent years, but it stands to question if today’s learning-based CRS are actually useful, see Reference [59]. In part, the problem of assessing this aspect is tied to how we evaluate such systems. Computational metrics like BLEU can only answer certain aspects of the question. But also the human evaluations in the reviewed papers are sometimes not too insightful, in particular when a newly proposed system is evaluated relative to a previous system by a few human judges. We therefore should revisit our evaluation practice and also investigate what users actually expect from a CRS, how tolerant they are with respect to misunderstandings or poor recommendations, how we can influence these expectations, and how useful the systems are considered on an absolute scale. Technically, combining learning techniques with other sorts of structured knowledge seems to be key to more usable, reliable and also predictable conversational recommender systems in the future.

最后，从技术和方法的角度来看，我们问：“纯端到端学习方法，即通过创建系统，除了项目数据库外，只有过去对话的语料库作为输入，我们能走多远。近年来，NLP 技术取得了巨大进步，但今天的基于学习的 CRS 是否真的有用值得怀疑，参见参考文献 [59]。在某种程度上，评估这方面的问题与我们如何评估此类系统有关。 BLEU等计算指标只能回答问题的某些方面。但是，评论论文中的人工评估有时也不是很有洞察力，特别是当几个人工法官相对于以前的系统对新提出的系统进行评估时。因此，我们应该重新审视我们的评估实践，并调查用户对 CRS 的实际期望，他们对误解或糟糕建议的容忍度，我们如何影响这些期望，以及系统在绝对规模上的有用性。从技术上讲，将学习技术与其他类型的结构化知识相结合似乎是未来更可用、更可靠且可预测的对话式推荐系统的关键。
