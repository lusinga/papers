# Deep Reinforcement Learning from Human Preferences

## Abstract

For sophisticated reinforcement learning (RL) systems to interact usefully with real-world environments, we need to communicate complex goals to these systems. In this work, we explore goals defined in terms of (non-expert) human preferences between pairs of trajectory segments. We show that this approach can effectively solve complex RL tasks without access to the reward function, including Atari games and simulated robot locomotion, while providing feedback on less than 1% of our agent’s interactions with the environment. This reduces the cost of human oversight far enough that it can be practically applied to state-of-the-art RL systems. To demonstrate the flexibility of our approach, we show that we can successfully train complex novel behaviors with about an hour of human time. These behaviors and environments are considerably more complex than any which have been previously learned from human feedback.

为了让复杂的强化学习（RL）系统能够有用地与现实世界的环境互动，我们需要向这些系统传达复杂的目标。在这项工作中，我们探讨了根据（非专家）人类在轨迹片段对之间的偏好来定义的目标。我们证明了这种方法可以在没有奖励函数的情况下有效地解决复杂的RL任务，包括Atari游戏和模拟机器人运动，同时对我们的智能体与环境的互动提供不到1%的反馈。这大大降低了人类监督的成本，使其能够实际应用于最先进的RL系统。为了展示我们方法的灵活性，我们展示了如何在大约一个小时的人类时间内成功训练复杂的新颖行为。这些行为和环境的复杂性远远超过了以前从人类反馈中学到的任何行为。

## 1 Introduction

Recent success in scaling reinforcement learning (RL) to large problems has been driven in domains that have a well-specified reward function (Mnih et al., 2015, 2016; Silver et al., 2016). Unfortunately, many tasks involve goals that are complex, poorly-defined, or hard to specify. Overcoming this limitation would greatly expand the possible impact of deep RL and could increase the reach of machine learning more broadly.

近年来，强化学习（RL）在大规模问题中的成功扩展主要源于那些具有明确奖励函数的领域（Mnih et al., 2015, 2016; Silver et al., 2016）。不幸的是，许多任务涉及到复杂的、定义不清楚的或难以明确的目标。克服这一局限性将极大地扩大深度强化学习的潜在影响，并可能更广泛地提高机器学习的覆盖范围。

For example, suppose that we wanted to use reinforcement learning to train a robot to clean a table or scramble an egg. It’s not clear how to construct a suitable reward function, which will need to be a function of the robot’s sensors. We could try to design a simple reward function that approximately captures the intended behavior, but this will often result in behavior that optimizes our reward function without actually satisfying our preferences. This difficulty underlies recent concerns about misalignment between our values and the objectives of our RL systems (Bostrom, 2014; Russell, 2016; Amodei et al., 2016). If we could successfully communicate our actual objectives to our agents, it would be a significant step towards addressing these concerns.

例如，假设我们想要使用强化学习来训练一个机器人清理桌子或者搅拌鸡蛋。我们如何构建一个合适的奖励函数，这个函数需要根据机器人的传感器信息来构建，目前尚不清楚。我们可以尝试设计一个简单的奖励函数，大致捕捉预期的行为，但这通常会导致行为优化我们的奖励函数，而实际上并不能满足我们的偏好。这个困难潜在地关系到我们的价值观与RL系统目标之间的不一致问题（Bostrom, 2014; Russell, 2016; Amodei et al., 2016）。如果我们能成功地将我们的实际目标传达给我们的智能体，那么将是解决这些问题的重要一步。


If we have demonstrations of the desired task, we can use inverse reinforcement learning (Ng and Russell, 2000) or imitation learning to copy the demonstrated behavior. But these approaches are not directly applicable to behaviors that are difficult for humans to demonstrate (such as controlling a robot with many degrees of freedom but non-human morphology).

如果我们有所需任务的演示，我们可以使用逆强化学习（Ng and Russell, 2000）或模仿学习来复制演示的行为。但是，这些方法并不适用于难以让人类展示的行为（例如，控制一个具有多个自由度但非人类形态的机器人）。

An alternative approach is to allow a human to provide feedback on our system’s current behavior and to use this feedback to define the task. In principle this fits within the paradigm of reinforcement learning, but using human feedback directly as a reward function is prohibitively expensive for RL systems that require hundreds or thousands of hours of experience. In order to practically train deep RL systems with human feedback, we need to decrease the amount of feedback required by several orders of magnitude.

另一种方法是让人类对我们系统的当前行为提供反馈，并使用这些反馈来定义任务。从原理上讲，这符合强化学习的范式，但是将人类反馈直接用作奖励函数对于需要数百或数千小时经验的RL系统来说，成本过高。为了在实际中用人类反馈训练深度强化学习系统，我们需要将所需反馈量减少数个数量级。

We overcome this difficulty by asking humans to compare possible trajectories of the agent, using that data to learn a reward function, and optimizing the learned reward function with RL.

我们通过让人类比较机器人可能的路径，使用这些数据来学习一个奖励函数，并使用强化学习优化所学习的奖励函数来克服这个困难。

This basic approach has been explored in the past, but we confront the challenges involved in scaling it up to modern deep RL and demonstrate by far the most complex behaviors yet learned from human feedback.

这种基本方法过去已经被探索过，但我们面临着将其扩展到现代深度强化学习所涉及的挑战，并且通过迄今为止从人类反馈中学到的最复杂行为来展示。

Our experiments take place in two domains: Atari games in the Arcade Learning Environment (Bellemare et al., 2013), and robotics tasks in the physics simulator MuJoCo (Todorov et al., 2012). We show that a small amount of feedback from a non-expert human, ranging from fifteen minutes to five hours, suffice to learn both standard RL tasks and novel hard-to-specify behaviors such as performing a backflip or driving with the flow of traffic.

我们的实验在两个领域进行：Atari游戏在街机学习环境（Arcade Learning Environment，Bellemare et al., 2013）中进行，以及在物理模拟器MuJoCo（Todorov et al., 2012）中进行的机器人任务。我们证明了非专家人类提供少量的反馈，从十五分钟到五个小时，就足以学习标准的强化学习任务以及难以明确描述的新颖行为，如完成后空翻或顺着交通流行驶。

### 1.1 Related Work

A long line of work studies reinforcement learning from human ratings or rankings, including Akrour et al. (2011), Pilarski et al. (2011), Akrour et al. (2012), Wilson et al. (2012), Sugiyama et al. (2012), Wirth and Fürnkranz (2013), Daniel et al. (2015), El Asri et al. (2016), Wang et al. (2016), and Wirth et al. (2016). Other lines of research consider the general problem of reinforcement learning from preferences rather than absolute reward values (Fürnkranz et al., 2012; Akrour et al., 2014; Wirth et al., 2016), and optimizing using human preferences in settings other than reinforcement learning (Machwe and Parmee, 2006; Secretan et al., 2008; Brochu et al., 2010; Sørensen et al., 2016).

大量的研究关注从人类评级或排名中进行强化学习，包括 Akrour 等人（2011），Pilarski 等人（2011），Akrour 等人（2012），Wilson 等人（2012），Sugiyama 等人（2012），Wirth 和 Fürnkranz（2013），Daniel 等人（2015），El Asri 等人（2016），Wang 等人（2016）和 Wirth 等人（2016）。其他研究方向考虑了从偏好而非绝对奖励值进行强化学习的一般问题（Fürnkranz 等人，2012；Akrour 等人，2014；Wirth 等人，2016），以及在强化学习以外的设置中使用人类偏好进行优化（Machwe 和 Parmee，2006；Secretan 等人，2008；Brochu 等人，2010；Sørensen 等人，2016）。

Our algorithm follows the same basic approach as Akrour et al. (2012) and Akrour et al. (2014), but considers much more complex domains and behaviors. The complexity of our environments force us to use different RL algorithms, reward models, and training strategies. One notable difference is that Akrour et al. (2012) and Akrour et al. (2014) elicit preferences over whole trajectories rather than short clips, and so would require about an order of magnitude more human time per data point. Our approach to feedback elicitation closely follows Wilson et al. (2012). However, Wilson et al. (2012) assumes that the reward function is the distance to some unknown (linear) “target” policy, and is never tested with real human feedback.

我们的算法基本遵循 Akrour 等人（2012）和 Akrour 等人（2014）的方法，但考虑了更复杂的领域和行为。我们环境的复杂性迫使我们使用不同的强化学习算法、奖励模型和训练策略。一个显著的区别是 Akrour 等人（2012）和 Akrour 等人（2014）在整个轨迹上获取偏好，而不是短片段，因此每个数据点大约需要更多一个数量级的人类时间。我们的反馈获取方法紧密遵循 Wilson 等人（2012）。然而，Wilson 等人（2012）假设奖励函数是到某个未知（线性）“目标”策略的距离，并且从未在真实人类反馈中进行测试。


TAMER (Knox, 2012; Knox and Stone, 2013) also learns a reward function from human feedback, but learns from ratings rather than comparisons, has the human observe the agent as it behaves, and has been applied to settings where the desired policy can be learned orders of magnitude more quickly.

TAMER（Knox，2012; Knox 和 Stone，2013）同样从人类反馈中学习奖励函数，但它是从评级而非比较中学习，让人类在智能体行为时进行观察，并应用于可以以数量级更快的速度学习所需策略的场景。

Compared to all prior work, our key contribution is to scale human feedback up to deep reinforcement learning and to learn much more complex behaviors. This fits into a recent trend of scaling reward learning methods to large deep learning systems, for example inverse RL (Finn et al., 2016), imitation learning (Ho and Ermon, 2016; Stadie et al., 2017), semi-supervised skill generalization (Finn et al., 2017), and bootstrapping RL from demonstrations (Silver et al., 2016; Hester et al., 2017).

与之前的所有研究相比，我们的关键贡献是将人类反馈扩展到深度强化学习，并学习更复杂的行为。这符合最近将奖励学习方法扩展到大型深度学习系统的趋势，例如逆强化学习（Finn 等人，2016），模仿学习（Ho 和 Ermon，2016；Stadie 等人，2017），半监督技能泛化（Finn 等人，2017）以及从示范中引导强化学习（Silver 等人，2016；Hester 等人，2017）。

## 2 Preliminaries and Method

### 2.1 Setting and Goal

We consider an agent interacting with an environment over a sequence of steps; at each time t the agent receives an observation $o_t \in O$ from the environment and then sends an action $a_t \in A$ to the environment.

In traditional reinforcement learning, the environment would also supply a reward $r_t \in R$ and the agent’s goal would be to maximize the discounted sum of rewards. Instead of assuming that the environment produces a reward signal, we assume that there is a human overseer who can express 2 preferences between trajectory segments. A trajectory segment is a sequence of observations and actions, $ \sigma = ((o_0, a_0), (o_1, a_1), ... , (o_{k−1}, a_{k−1})) \in (O \times A)^k$. Write $\sigma_1$ to indicate that the human preferred trajectory segment !1 to trajectory segment !2. Informally, the goal of the agent is to produce trajectories which are preferred by the human, while making as few queries as possible to the human.

### 2.2 Our Method

#### 2.2.1 Optimizing the Policy

#### 2.2.2 Preference Elicitation

#### 2.2.3 Fitting the Reward Function

#### 2.2.4 Selecting Queries

## 3 Experimental Results

## 4 Discussion and Conclusions

Agent-environment interactions are often radically cheaper than human interaction. We show that by learning a separate reward model using supervised learning, it is possible to reduce the interaction complexity by roughly 3 orders of magnitude.

代理与环境的互动通常比人与人之间的互动成本低得多。我们展示了通过使用监督学习学习一个独立的奖励模型，可以大约将互动复杂性降低3个数量级。

Although there is a large literature on preference elicitation and reinforcement learning from unknown reward functions, we provide the first evidence that these techniques can be economically scaled up to state-of-the-art reinforcement learning systems. This represents a step towards practical applications of deep RL to complex real-world tasks.

尽管关于偏好揭示和从未知奖励函数中进行强化学习的文献很多，但我们提供了第一个证据，证明这些技术可以经济地扩展到最先进的强化学习系统。这代表了朝着将深度强化学习应用于复杂现实世界任务的实际应用迈出了一步。

In the long run it would be desirable to make learning a task from human preferences no more difficult than learning it from a programmatic reward signal, ensuring that powerful RL systems can be applied in the service of complex human values rather than low-complexity goals.

从长远来看，我们希望让从人类偏好中学习任务的难度不超过从程序化奖励信号中学习任务，确保强大的强化学习系统能够服务于复杂的人类价值观，而非低复杂度目标。

