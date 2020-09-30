# Playing Atari with Deep Reinforcement Learning

## Abstract

We present the first deep learning model to successfully learn control policies directly from high-dimensional sensory input using reinforcement learning. The model is a convolutional neural network, trained with a variant of Q-learning, whose input is raw pixels and whose output is a value function estimating future rewards. We apply our method to seven Atari 2600 games from the Arcade Learning Environment, with no adjustment of the architecture or learning algorithm. We find that it outperforms all previous approaches on six of the games and surpasses a human expert on three of them.

我们提出了第一个利用强化学习成功地从高维感官输入中直接学习控制策略的深度学习模型。该模型是一个卷积神经网络，用一种Q-learning的变体进行训练，输入为原始像素，输出为估计未来回报的价值函数。我们将我们的方法应用到7款来自街机学习环境的雅达利2600游戏中，没有调整架构或学习算法。我们发现它在六款游戏中超过了之前所有的方法，在其中三款游戏中超过了人类专家。

## 1 Introduction

Learning to control agents directly from high-dimensional sensory inputs like vision and speech is one of the long-standing challenges of reinforcement learning (RL). Most successful RL applications that operate on these domains have relied on hand-crafted features combined with linear value functions or policy representations. Clearly, the performance of such systems heavily relies on the quality of the feature representation.

学习直接从像视觉和语言这样的高维感官输入来控制主体是强化学习(RL)的长期挑战之一。在这些领域上操作的大多数成功的RL应用程序都依赖于结合线性值函数或策略表示的手工制作的特性。显然，这类系统的性能很大程度上依赖于特征表示的质量。

Recent advances in deep learning have made it possible to extract high-level features from raw sensory data, leading to breakthroughs in computer vision [11, 22, 16] and speech recognition [6, 7]. These methods utilise a range of neural network architectures, including convolutional networks, multilayer perceptrons, restricted Boltzmann machines and recurrent neural networks, and have exploited both supervised and unsupervised learning. It seems natural to ask whether similar techniques could also be beneficial for RL with sensory data.

深度学习的最新进展使得从原始感觉数据中提取高级特征成为可能，导致计算机视觉[11,22,16]和语音识别[6,7]的突破。这些方法利用一系列的神经网络架构，包括卷积网络，多层感知器，限制玻尔兹曼机器和循环神经网络，并利用了监督和非监督学习。人们很自然地会问，类似的技术是否也能对感知数据的RL有益。

However reinforcement learning presents several challenges from a deep learning perspective. Firstly, most successful deep learning applications to date have required large amounts of handlabelled training data. RL algorithms, on the other hand, must be able to learn from a scalar reward signal that is frequently sparse, noisy and delayed. The delay between actions and resulting rewards, which can be thousands of timesteps long, seems particularly daunting when compared to the direct association between inputs and targets found in supervised learning. Another issue is that most deep learning algorithms assume the data samples to be independent, while in reinforcement learning one typically encounters sequences of highly correlated states. Furthermore, in RL the data distribution changes as the algorithm learns new behaviours, which can be problematic for deep learning methods that assume a fixed underlying distribution.

然而，从深度学习的角度来看，强化学习提出了几个挑战。首先，迄今为止，大多数成功的深度学习应用都需要大量的手标记训练数据。另一方面，RL算法必须能够从频繁稀疏、有噪声和延迟的标量奖励信号中学习。与监督学习中发现的输入和目标之间的直接联系相比，行动和结果奖励之间的延迟可能有数千个时间步长，似乎特别令人畏惧。另一个问题是，大多数深度学习算法假设数据样本是独立的，而在强化学习中，通常会遇到一系列高度相关的状态。此外，在RL中，随着算法学习新的行为，数据分布也会发生变化，这对于假设底层分布固定的深度学习方法来说可能是个问题。

This paper demonstrates that a convolutional neural network can overcome these challenges to learn successful control policies from raw video data in complex RL environments. The network is trained with a variant of the Q-learning [26] algorithm, with stochastic gradient descent to update the weights. To alleviate the problems of correlated data and non-stationary distributions, we use an experience replay mechanism [13] which randomly samples previous transitions, and thereby smooths the training distribution over many past behaviors.

本文证明了卷积神经网络能够克服这些挑战，在复杂的RL环境中从原始视频数据学习成功的控制策略。该网络使用Q-learning[26]算法的一种变体进行训练，使用随机梯度下降来更新权值。为了缓解相关数据和非平稳分布的问题，我们使用了一种经验重放机制[13]，该机制随机抽样以往的过渡，从而平滑了许多过去行为的训练分布。

We apply our approach to a range of Atari 2600 games implemented in The Arcade Learning Environment (ALE) [3]. Atari 2600 is a challenging RL testbed that presents agents with a high dimensional visual input (210  160 RGB video at 60Hz) and a diverse and interesting set of tasks that were designed to be difficult for humans players. Our goal is to create a single neural network agent that is able to successfully learn to play as many of the games as possible. The network was not provided with any game-specific information or hand-designed visual features, and was not privy to the internal state of the emulator; it learned from nothing but the video input, the reward and terminal signals, and the set of possible actions—just as a human player would. Furthermore the network architecture and all hyperparameters used for training were kept constant across the games. So far the network has outperformed all previous RL algorithms on six of the seven games we have attempted and surpassed an expert human player on three of them. Figure 1 provides sample screenshots from five of the games used for training.

我们将我们的方法应用到一系列在街机学习环境(ALE)[3]中实施的雅达利2600游戏中。雅达利2600是一个具有挑战性的RL试验台,提出代理高维视觉输入(210  160 RGB视频60 hz)和一组不同的和有趣的任务,设计为人类的球员是很困难的。我们的目标是创建一个能够成功地学习玩尽可能多的游戏的单一神经网络代理。该网络未提供任何游戏特定信息或手工设计的视觉功能，且不了解仿真器的内部状态;它只从视频输入、奖励和终端信号以及一系列可能的行为中学习——就像人类玩家一样。此外，用于训练的网络架构和所有超参数在整个比赛中保持不变。到目前为止，网络已经超过了所有以前的RL算法，我们已经尝试了七场比赛中的六场，并在其中三场比赛中超过了人类专家玩家。图1提供了用于训练的五个游戏的示例屏幕截图。

## 2 Background

We consider tasks in which an agent interacts with an environment $\mathcal{E}$, in this case the Atari emulator, in a sequence of actions, observations and rewards. At each time-step the agent selects an action $a_t$ from the set of legal game actions, $A=\{1,...,K\}$. The action is passed to the emulator and modifies its internal state and the game score. In general $\mathcal{E}$ may be stochastic. The emulator’s internal state is not observed by the agent; instead it observes an image $x_t\in\mathbb{R}^d$ from the emulator, which is a vector of raw pixel values representing the current screen. In addition it receives a reward $r_t$ representing the change in game score. Note that in general the game score may depend on the whole prior sequence of actions and observations; feedback about an action may only be received after many thousands of time-steps have elapsed.

我们以一系列的操作、观察和奖励来考虑代理与环境$\Epsilon$交互的任务，在本例中为Atari模拟器。在每个时间步，代理从合法游戏动作集中选择一个动作$a_t$， $A=\{1，…，K\}$。动作被传递到模拟器并修改其内部状态和游戏分数。一般来说，E是随机的。仿真器的内部状态不被代理观察;相反，它观察来自模拟器的$\mathbb{R}^d$中的图像$x_t$，该图像是表示当前屏幕的原始像素值的向量。此外，它还会获得代表游戏分数变化的奖励$r_t$。请注意，一般情况下，游戏分数可能取决于整个之前的动作和观察顺序;只有在经过数千个时间步骤之后，才会收到关于某个操作的反馈。

Since the agent only observes images of the current screen, the task is partially observed and many emulator states are perceptually aliased, i.e. it is impossible to fully understand the current situation from only the current screen $x_t$. We therefore consider sequences of actions and observations, $s_t = x_1, a_1, x_2, ..., a_{t-1}, x_t$, and learn game strategies that depend upon these sequences. All sequences in the emulator are assumed to terminate in a finite number of time-steps. This formalism gives rise to a large but finite Markov decision process (MDP) in which each sequence is a distinct state. As a result, we can apply standard reinforcement learning methods for MDPs, simply by using the complete sequence $s_t$ as the state representation at time t.

由于代理只观察当前屏幕的图像，因此任务被部分观察，并且许多模拟器状态被感知到别名，即仅从当前屏幕$x_t$不可能完全了解当前的情况。因此我们考虑动作和观察的序列$s_t = x_1, a_1, x_2，…， a_{t-1}， x_t$，并学习依赖于这些序列的游戏策略。假设仿真器中的所有序列在有限的时间步数中终止。这种形式导致了一个大型但有限的马尔可夫决策过程(MDP)，其中每个序列是一个不同的状态。因此，我们可以对MDPs应用标准的强化学习方法，只需使用完整序列$s_t$作为t时刻的状态表示。

The goal of the agent is to interact with the emulator by selecting actions in a way that maximises future rewards. We make the standard assumption that future rewards are discounted by a factor of $\gamma$ per time-step, and define the future discounted return at time t as $R_t = \sum_{t'=t}^T\gamma^{t'-t}r_{t'}$, where T is the time-step at which the game terminates. We define the optimal action-value function $Q^*(s,a)$ as the maximum expected return achievable by following any strategy, after seeing some sequence s and then taking some action a$Q^*(s,a)=max_{\pi}\mathbb{E}[R_t|s_t=s,a_t=a,\pi]$, where $\pi$ is a policy mapping sequences to actions (or distributions over actions).

代理的目标是通过选择能够最大化未来回报的行为与仿真器进行交互。我们做出标准假设，未来的奖励以$\gamma$每时间步折现，并定义时间t的未来折现回报为$R_t = \sum_{t'=t} t \gamma^{t'-t}r_{t'}$，其中t是游戏终止的时间步长。我们定义了最优的动作-值函数$Q^*(s,a)$作为遵循任何策略所能获得的最大期望回报，在看到某个序列s并采取某个动作之后，a $Q^*(s,a)=max_{\pi}\mathbb{E}[R_t|s_t=s,a_t=a，\pi]$，其中$\pi$是一个策略映射序列到动作(或动作的分布)。

The optimal action-value function obeys an important identity known as the Bellman equation. This is based on the following intuition: if the optimal value $Q^*(s',a')$ of the sequence s' at the next time-step was known for all possible actions a', then the optimal strategy is to select the action a' maximising the expected value of $r+\gamma Q^*(s',a')$

$Q^*(s,a)=\mathbb{E}_{s'\sim\epsilon}[r+\gamma max_{a'}Q^*(s',a')|s,a]$ (1)

最优行动值函数服从一个重要的恒等式，即贝尔曼方程。这是基于以下直觉:如果已知序列s'在下一个时间步的所有可能动作a'的最优值$Q^*(s'，a')$，那么最优策略是选择动作a'，使$r+\gamma Q^*(s'，a')$的期望值最大化

The basic idea behind many reinforcement learning algorithms is to estimate the action-value function, by using the Bellman equation as an iterative update, $Q_{i+1}(s,a)=\mathbb{E}[r+\gamma max_{a'}Q_i(s',a')|s,a]$. Such value iteration algorithms converge to the optimal actionvalue function, $Q_i \to Q^*$ as $i \to \infty$ [23]. In practice, this basic approach is totally impractical, because the action-value function is estimated separately for each sequence, without any generalisation. Instead, it is common to use a function approximator to estimate the action-value function, $Q(s,a;\theta)\approx Q^*(s,a)$. In the reinforcement learning community this is typically a linear function approximator, but sometimes a non-linear function approximator is used instead, such as a neural network. We refer to a neural network function approximator with weights $\theta$ as a Q-network. A Q-network can be trained by minimising a sequence of loss functions $L_i(\theta_i)$ that changes at each iteration i,

$L_i(\theta_i) = \mathbb{E}_{s,a\sim \rho(.)}[(y_i-Q(s,a;\theta_i))^2]$ (2)

where $y_i =\mathbb{E}_{s'\sim\epsilon}[r+\gamma \max_{a'}Q(s',a';\theta_{i-1})]$ is the target for iteration i and $\rho(s,a)$ is a
probability distribution over sequences s and actions a that we refer to as the behaviour distribution. The parameters from the previous iteration $\theta_{i-1}$ are held fixed when optimising the loss function $L_i(\theta_i)$. Note that the targets depend on the network weights; this is in contrast with the targets used for supervised learning, which are fixed before learning begins. Differentiating the loss function with respect to the weights we arrive at the following gradient,

## 3 Related Work

## 4 Deep Reinforcement Learning

### 4.1 Preprocessing and Model Architecture

## 5 Experiments

### 5.1 Training and Stability

### 5.2 Visualizing the Value Function

### 5.3 Main Evaluation

## 6 Conclusion

This paper introduced a new deep learning model for reinforcement learning, and demonstrated its ability to master difficult control policies for Atari 2600 computer games, using only raw pixels as input. We also presented a variant of online Q-learning that combines stochastic minibatch updates with experience replay memory to ease the training of deep networks for RL. Our approach gave state-of-the-art results in six of the seven games it was tested on, with no adjustment of the architecture or hyperparameters.

为输入，就能够掌握Atari 2600电脑游戏的复杂控制策略。我们还提出了在线Q-learning的一种变体，它结合了随机小批量更新和经验重放记忆，以简化深度网络的RL训练。在测试的7款游戏中，我们的方法在6款游戏中获得了最先进的结果，并且没有调整架构或超参数。
