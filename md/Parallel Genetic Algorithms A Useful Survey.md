# Parallel Genetic Algorithms: A Useful Survey

In this article, we encompass an analysis of the recent advances in parallel genetic algorithms (PGAs). We have selected these algorithms because of the deep interest in many research fields for techniques that can face complex applications where running times and other computational resources are greedily consumed by present solvers, and PGAs act then as efficient procedures that fully use modern computational platforms at the same time that allow the resolution of cutting-edge open problems. We have faced this survey on PGAs with the aim of helping newcomers or busy researchers who want to have a wide vision on the field. Then, we discuss the most well-known models and their implementations from a recent (last six years) and useful point of view: We discuss on highly cited articles, keywords, the venues where they can be found, a very comprehensive (and new) taxonomy covering different research domains involved in PGAs, and a set of recent applications. We also introduce a new vision on open challenges and try to give hints that guide practitioners and specialized researchers. Our conclusion is that there are many advantages to using these techniques and lots of potential interactions to other evolutionary algorithms; as well, we contribute to creating a body of knowledge in PGAs by summarizing them in a structured way, so the reader can find this article useful for practical research, graduate teaching, and as a pedagogical guide to this exciting domain.

在本文中，我们将分析并行遗传算法(PGAs)的最新进展。我们选择这些算法由于浓厚的兴趣可能面临的许多研究领域技术,复杂的应用程序,运行时间和其他计算资源贪婪被解决,和pga然后采取有效的程序,充分利用现代计算平台同时允许尖端开放问题的决议。我们面对这个PGAs调查的目的是帮助新来者或忙碌的研究人员谁想有一个广阔的视野在该领域。然后,我们讨论最知名模型及其实现从近期(六年)和有用的观点:我们讨论高被引文章,关键字,他们可以找到的场所,一个非常全面的(新)分类覆盖不同的研究领域参与pga,和最近的一组应用程序。我们还介绍了一个关于开放挑战的新观点，并试图给出一些提示来指导从业者和专业研究人员。我们的结论是，使用这些技术有许多优点和许多潜在的相互作用，与其他进化算法;此外，我们通过结构化的方式总结PGAs的知识体系，从而为创建PGAs的知识体系做出贡献，因此读者可以发现本文对实际研究、研究生教学以及这一令人兴奋的领域的教学指导非常有用。

## 1 INTRODUCTION

## 2 PARALLEL GENETIC ALGORITHM

## 3 ANALYSIS OF RECENT PGA STUDIES

## 4 LITERATURE REVIEW

## 5 CHALLENGES AND NEW TRENDS

## 6 CONCLUSIONS

We now end this article by summarizing the main contents and findings in it. Let us first remind of the intention of not only having a source of information for modern research in PGAs, but in including the added value in every section so it is actually useful to a wide audience. We did our survey in a systematic way and defined four meaningful research questions that guided our discussion.

最后，我们总结了本文的主要内容和发现。让我们首先提醒一下，我们的目的不仅是为PGAs的现代研究提供信息来源，还包括在每个部分中增加价值，这样它实际上对广泛的受众是有用的。我们系统地进行了调查，并定义了四个有意义的研究问题来指导我们的讨论。

On the contents, we tried to visit all common places and then talked on models for PGAs, existing fresh works on this domain, and a guiding tour around the venues where PGAs are being endorsed. On conferences, ACM GECCO and IEEE CEC are the preferred places for publishing in PGAs. On journals, the two first ones have a clear profile in parallelism, Concurrency and Computation: Practice and Experience and International Journal of Distributed Sensor Networks, an explicit lesson that the “P” in PGAs has deep importance.

在内容上，我们试着访问了所有常见的地方，然后讨论了PGAs的模型、该领域现有的新鲜作品，并参观了PGAs正在被认可的场地。在会议上，ACM GECCO和IEEE CEC是PGAs发表的首选场所。在期刊上，前两篇论文在并行性、并发性和计算:Practice and Experience和International Journal of Distributed Sensor Networks上有一个清晰的概述，明确说明了PGAs中的“P”的重要性。

As a global conclusion, it is clear that the domain is healthy (almost 4K researchers publishing in the last years), though there is a trend in focusing in the applications and not that much in the algorithms themselves. This is good in the short term but not so good in the long term if we want to understand and better exploit these techniques for future practice. We really encourage researchers to go on the fundamentals and understand them well before passing to focus in the application itself to avoid the classic “wall” of no further enhancements in the performance for a lack of knowledge on the solver used.

作为一个全球性的结论，很明显这个领域是健康的(过去几年几乎有4K研究人员发表)，尽管有一种趋势是关注应用程序，而不是算法本身。这在短期内是好的，但如果我们想要理解和更好地利用这些技术在未来的实践中，在长期内就不太好了。我们鼓励研究人员在专注于应用程序本身之前深入基础知识并充分理解它们，以避免由于对所使用的求解器缺乏了解而导致性能得不到进一步增强的经典“墙”。

The environment in research on PGAs is shifting in topics and places also. China is emerging as a source ofmany new works, and our understanding of PGAs would need to deal with a whole new taxonomy on hardware and software that is somewhat complex because of the many advances in these two domains. Researchers and practitioners should pay attention to the fundamentals of the models, the best practices in non-deterministic algorithms, and the basic concepts existing in the domain of parallelism and HPC that are, to some degree, continuously missed in present works where the application or at most the algorithm is the target, while a whole body of information exists for parallel processing that could lead this domain to new levels of creative thinking and highly efficient solvers.






