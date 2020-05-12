# Practical TLA+

## PART I - The Semantics of TLA+ and PlusCal

### CHAPTER 1 An Example

Let’s write our first specification! In this chapter we will take a simple problem and translate it into a specification. Then, we’ll model check the specification and see if it has a flaw. Spoiler alert, it will have a flaw. This will be a whirlwind tour: we will be using concepts we will gradually learn over the course of the book.

让我们编写我们的第一个规范!在本章中，我们将把一个简单的问题转化为一个规范。然后，我们将模型检查规范，看看它是否有缺陷。剧透警告，它将有一个缺陷。这将是一个旋风之旅:我们将使用概念，我们将逐步学习的过程中，这本书。

#### The Problem

Alice and Bob have accounts at Bankgroup. Each account has 0 or more dollars in it. Bankgroup wants to add a new “wire” feature, where any user can transfer money to any other user. This feature has the following requirements: 
- Each wire must be between two different people in the bank and wire at least one dollar. 
- If a wire is successful, the value of the wire is deducted from the sender account and added to the receiver account. 
- If the wire fails, the two accounts are unchanged. 
- A wire may not cause an account to have a negative balance. 
- For scaling reasons, multiple wires may happen simultaneously.

爱丽丝和鲍勃在银行集团有账户。每个账户都有0或更多的美元。Bankgroup希望增加一个新的“电汇”功能，任何用户都可以将钱转到其他用户。该功能有以下要求:
- 每个电汇必须是在银行的两个不同的人之间，并电汇至少一美元。
- 如果连线成功，连线的值将从发送方帐户中扣除，并添加到接收方帐户中。
- 如果连线失败，两个帐号保持不变。
- 电汇可能不会导致帐户出现负余额。
- 由于扩展的原因，多个线可能同时发生。

Your implementation must guarantee all properties, even if wires can take an arbitrary amount of time. In other words, even if wire A starts before B, A may still finish after wire B. Your algorithm must guarantee that, even in those cases, we satisfy all of the requirements. Given that money is on the line, you should probably design this in advance.

您的实现必须保证所有属性，即使连接可能需要任意长的时间。换句话说，即使A在B之前开始，A也可能在B之后结束。你的算法必须保证，即使在这种情况下，我们满足所有的要求。考虑到钱的问题，你可能应该提前设计好。

