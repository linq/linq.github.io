---
title: "Agent Arena：一个用于评估和比较大型语言模型Agent的平台"
date: 2025-01-16
taxonomies:
  tags:
    - "Agent"
    - "AI"
extra:
  emoji: 🤖
---

## 简介

> 这篇文章介绍了 Agent Arena 一个用于评估和比较 LLM Agent的交互式平台。该平台允许用户根据自己的需求测试不同的Agent组合(包括模型、框架和工具)，并通过投票系统对其性能进行评估。文章详细讨论了平台的核心组件，包括路由系统、评估机制、排行榜等，并通过多个实际用例展示了Agent在教育、金融、医疗等领域的应用潜力。此外，文章还介绍了平台的未来发展路线图，展望了 LLM Agent评估的发展方向。
>
> 原文地址：https://gorilla.cs.berkeley.edu/blogs/14_agent_arena.html

随着大语言模型(LLM)Agent的日益普及，我们需要一个统一和系统的方法来评估Agent。

LLMAgent正被应用于各种用例中，从搜索和代码生成到金融和研究等复杂任务。我们认为LLMAgent由三个组件构成 - LLM模型(如GPT-4、Claude、Llama 3.1)、框架(LangChain、LlamaIndex、CrewAI等)和工具(代码解释器、Brave Search或Yahoo Finance等API)。例如，一个用于总结财报的Agent可能由GPT-4模型驱动，使用PDFReader工具来读取财报PDF，并由langchain来编排! Agent-arena捕获并对Agent作为整体以及三个子组件的用户偏好进行排名，为模型开发者、工具开发者以及更重要的是LLMAgent用户提供见解!

Agent在模型和框架评估方面有许多细微差别。例如，假设我想构建一个检索本周表现最佳股票的金融助手。

> ❓**我应该使用什么模型?** 一个模型可能接受过更多金融数据的训练💸，而另一个可能在推理♟️和计算➗方面表现出色。

> ❓**框架呢?** 一个平台可能有更多的API集成，但另一个可能在互联网索引方面做得更好。

> ❓**我应该使用什么工具?** 对于这个特定用例，我是需要返回股票价格📈的工具还是能返回市场新闻📰的API。

正如这个例子所示，在设计Agent工作流程时需要考虑很多因素 - 而这仅仅是金融领域潜在数十个用例中的一个。不同的用例需要不同的模型、工具和框架组合。

我们很高兴发布 **🤖 Agent Arena**，这是一个交互式沙盒，用户可以根据自己的需求比较、可视化和评价Agent工作流程。Agent Arena允许用户从任务、LLM提供商、框架、工具等组合中进行选择，并对其性能进行投票。我们使用户能够以结构化和系统化的方式看到不同Agent之间的表现对比。通过这样做，我们相信用户可以对他们的*Agent技术栈*做出更明智的决定。此外，通过Agent Arena，我们希望展示Agent的不足之处和令人印象深刻的进展!

Agent Arena还包括按领域分组的LLM模型、框架和工具的实时排行榜和排名。此外，我们认为这些排名可以帮助指导模型、工具和框架开发者，帮助他们了解他们在各种用例中的表现以及如何改进。同时认识到基于用户投票的选举会受到选择偏差的影响，作为一个新功能，Agent Arena还包括一个Prompt Hub，您可以在那里订阅特定的提示专家并查看他们对各种任务的个人意见。**您**也可以发布您的提示集!

**快速链接:**

- **Agent Arena:** [Agent-Arena](https://www.agent-arena.com/)
- **排行榜:** [Agent 排行榜](https://www.agent-arena.com/leaderboard) 
- **用户提示:** [提示中心](https://www.agent-arena.com/users)
- **Agent对战数据:** [Agent对战数据](https://github.com/ShishirPatil/gorilla/blob/main/agent-arena/evalutation/agent_ratings_V0.json)

## 🦜 什么是Agent?

在 Agent Arena 中，Agent被定义为能够利用各种子组件执行复杂任务的实体。我们将每个Agent定义为由三个组件组成 - LLM模型、工具和框架。我们考虑的Agent来自成熟的框架，如 LangChain、LlamaIndex、CrewAI、Composio，以及 OpenAI 和 Anthropic 提供的助手。这些Agent都可能展现出思维链推理、工具使用和函数调用等特性，使它们能够高效执行复杂任务。对于平台来说，我们使用支持函数调用和工具使用的模型，这些是 LLM Agent的关键方面。

例如，LangChain 和 LlamaIndex Agent配备了特定的工具包，可以增强其解决问题的能力。OpenAI 的助手，如代码解释器和文件处理模型，由于其展示出解释代码、处理文件和调用外部函数的能力，也可以称为Agent。Anthropic 的Agent与外部工具集成，其他框架的类似示例进一步增强了它们在特定任务中的实用性。

## Agent Arena 平台

![执行流程](https://oss-ata.alibaba.com/article/2025/01/fc4ac1e9-d733-41a9-8e30-1c4a93e95dd7.png)

*基于用户目标、模型、框架和执行时间、ELO等性能指标的Agent比较高层概述*

Agent Arena 的核心是允许基于目标的Agent比较。从高层次来看，用户首先输入他们想要完成的任务。然后，LLM 会根据任务自动分配相关Agent。这些Agent随后被指派完成目标，Agent的行动和*思维链*会实时传输给用户。一旦Agent完成任务，用户可以并排比较输出结果，并对哪个Agent表现更好进行投票。

评估过程包括对Agent性能进行投票，用户评估哪个Agent更有效地满足了任务要求。这种用户驱动的评估有助于形成一个不断发展的排行榜系统，该系统根据Agent在多个任务和竞赛中的相对表现对其进行排名。这种比较不仅限于Agent整体，还延伸到构成每个Agent的各个组件(即 LLM 模型、工具和框架)。

在下面的章节中，我们将深入探讨 Agent Arena 的核心组件，包括路由系统、执行、评估和排名机制、排行榜和提示中心。我们还将探索一些可以在平台上执行的示例任务和应用。

## 路由系统:Agent匹配和任务分配

Agent Arena 的一个核心要素是其路由系统，目前由 GPT-4o 驱动。我们计划在所有模型之间轮换，并评估每个模型，使用提示词将其路由到最相关Agents上! 路由系统的主要功能是将用户指定的目标与平台上最合适的Agent进行匹配。

路由系统通过分析用户的输入(目标或任务)来运作，并选择两个最适合完成该任务的Agent。这个选择过程会考虑Agent在类似任务中的历史表现，以及它们在模型、工具和框架方面的配置。

例如，用户可能提供以下输入:`input("Tell me about whats going on in NVIDIA in the last week.")` 然后，路由系统会根据可用的Agent和排行榜 ELO 分数选择两个合适的选项。对于这个用例，路由系统可能会选择Agent `agent_a = Agent(model="GPT-4o"， tools=["Yahoo Finance"， "Matplotlib"]， framework="Langchain")` 来分析 NVIDIA 的股票信息。另一方面，为了与Agent A 进行比较，路由系统可能会选择组合: `agent_b = Agent(model="Claude"， tools=["Yahoo News"]， framework="CrewAI")` 从新闻的角度来观察目标。

这种比较是有成效的，因为它让平台和用户能够理解Agent能力的细微差别以及它们处理相同任务的不同方式。然后，用户可以对他们更喜欢的风格进行投票。

## 评估和排名系统

Agent Arena 采用全面的排名系统，基于Agent在一对一比较中的表现进行评估。排行榜不仅根据Agent的整体表现进行排名，还会分解各个组件(如 LLM 模型、工具和框架)的表现。排名过程参考了用户评估和基于 ELO 的评分系统，这在竞争性排名环境中很常见，Agent的表现在每次任务或比较后都会动态调整。

Agent Arena 的评分系统旨在反映Agent在广泛任务中的累积表现，考虑以下因素:

![img](https://oss-ata.alibaba.com/article/2025/01/fc4ac1e9-d733-41a9-8e30-1c4a93e95dd7.png)

*分析Agent子组件的排行榜*

- **模型表现:** 评估底层 LLM 模型的效果(如 GPT-4、Claude、Llama 3.1)。
- **工具效率:** 对Agent用于完成任务的工具进行排名(如代码解释器、Brave Search 或 Yahoo Finance 等 API)。
- **框架功能:** 评估支持Agent的更广泛框架，如 LangChain、LlamaIndex 和 CrewAI。

查看我们排行榜上各类别的最新排名:[Agent Arena 排行榜](https://www.agent-arena.com/leaderboard)。

### ⚖️ 使用Extended Bradley-Terry 模型评估Agent

### Extended Bradley-Terry 模型

Agent Arena 使用 [Bradley-Terry 扩展](https://blog.lmarena.ai/blog/2024/extended-arena/)，这使我们能够基于Agent的子组件(包括工具、模型和框架)来比较不同的Agent。我们不仅仅是对Agent进行整体评估，还会评估每个独立子组件的表现。这让我们能够更准确地找出Agent的优势所在。例如，我们的第一个Agent可能是 LangChain、Brave-Search 和 GPT-4o-2024-08-06 的组合，而第二个Agent可能是 LlamaIndex、Wikipedia 和 Claude-3-5-Sonnet-20240620。因此，我们为扩展 Bradley-Terry 模型提出以下观察模型。给定 `P_1`，

对于每场对战 𝑖∈[𝑛]，我们有一个提示和两个Agent，编码如下:

- `Agent A`: 第一个被比较的Agent，ELO 分数为 `E_A`，子组件为 `(A_T， A_M， A_F)`
- `Agent B`: 第二个被比较的Agent，ELO 分数为 `E_B`，子组件为 `(B_T， B_M， B_F)`
- `Y_i`: 对战结果(1 表示胜利，0 表示失败)

#### 🧑‍💻 示例: LangChain Brave-Search Agent vs. LlamaIndex Wikipedia Agent

让我们通过一个例子来说明扩展 Bradley-Terry 模型在实践中是如何工作的。以下面的Agent及其子组件为例:

- `Agent A` 是 Langchain Brave-Search Agent，使用以下子组件: `{ Brave-Search (A_T)， LangChain (A_F)， 和 GPT-4o-2024-08-06 (A_M) }`，ELO 分数为 1600。
- `Agent B` 是 LlamaIndex Wikipedia Agent，子组件为: `{Wikipedia (B_T)， LlamaIndex (B_F)， 和 Claude-3-5-Sonnet-20240620 (B_M)}`，ELO 分数为 1500。

在传统的 ELO 系统中，我们会计算 Brave-Search Agent获胜概率为 64%。然后根据实际对战结果 `Y_1`，假设 Brave-Search Agent获胜，Agent的新评分将分别为 1601.44 和 1498.56。

然而，在 Bradley-Terry 模型中，我们通过最小化以下损失函数来计算评分，给定实际对战结果 `Y_i`:

$$ L = -\sum_{i = 1}^{n} Y_{i} \cdot \log(\frac{1}{1 + e^{E_{A}-E_{B}}}) + (1 - Y_{i}) \cdot \log(\frac{1}{1 + e^{E_{B}-E_{A}}}) $$

最后，为了对Agent进行整体评估，我们将其所有子组件合并到单一分析中。我们不是将每个子组件作为独立实体处理，而是考虑它们在更广泛的Agent架构中的相互作用。对于每场对战，我们构建一个设计矩阵 `X`，代表所有涉及的子组件。在这里，假设 A(Brave-Search Agent)赢得了对战，那么设计矩阵将如下所示:

$$ X = [+\log(A_{T})， +\log(A_{M})， +\log(A_{F})， -\log(B_{T})， -\log(B_{M})， -\log(B_{F})] $$

这使我们能够在单一计算中评估子组件(工具、模型、框架)的集体贡献。然后我们应用带有 L2 正则化的逻辑回归，以控制由频繁配对导致的过拟合和混淆效应。

通过使用这种组合方法，Agent Arena 确保了对Agent及其子组件进行更准确的排名。🔄 这种方法为每个Agent的表现和贡献提供了更清晰的洞察，防止了由频繁配对或过度使用的配置导致的偏差。

🎉 因此，我们的系统生成了一个实时、持续更新的排行榜，不仅反映了Agent的整体表现，还反映了它们特定子组件的优势。🏆

查看我们的Agent、工具、模型和框架的实时排行榜[点击这里](https://www.agent-arena.com/leaderboard)!

## 提示词中心

Agent Arena 还包含一个提示词中心，其中有超过 1000+ 个经过测试和验证可在平台上运行的任务。用户可以搜索与他们类似的用例，并观察不同提示是如何执行和表现的。此外，平台还允许用户向社区发布他们的提示。这种对通过 agent arena 评估的提示的公开视图为未来的代理开发和评估提供了强大的基础设施和数据。

![img](https://oss-ata.alibaba.com/article/2025/01/acd41109-c94e-45e9-b688-74d2c6f577e6.jpeg?x-oss-process=image%2Fauto-orient%2C1%2Fresize%2Cm_lfit%2Cw_1600%2Fquality%2CQ_80%2Fformat%2Cwebp)

*展示竞技场中注册用户的提示词中心*

### 🏠 提示中心概述

提示词中心是用户与其他用户互动并查看用户对代理的个人和领域特定用例需求的独特视角的方式。这是一个很好的方式来从细粒度层面查看用户活动，了解用户具体使用Agent做什么以及如何优先考虑未来的Agent开发。

![img](https://oss-ata.alibaba.com/article/2025/01/a65a019f-b364-4302-923d-7c0a21642260.jpeg?x-oss-process=image%2Fauto-orient%2C1%2Fresize%2Cm_lfit%2Cw_1600%2Fquality%2CQ_80%2Fformat%2Cwebp)

*查看、点赞和点踩个人用户提示词*

### 🧑‍💻 个人用户视图

此外，用户可以通过在提示词中心对其他用户的个别提示词进行点赞和点踩来提供反馈。这为未来的提示分析提供了额外的数据点，以潜在评估竞技场中各种代理在特定领域的表现。

## 💼 案例研究

您选择的模型、框架和工具通常会根据领域应用和用例而有很大差异。特定领域的代理开发者需要找到这些因素的最佳组合以最大化性能。未来的愿景是，最终代理将变得足够准确，以至于我们可以让它们在不需要人工参与的情况下做出明智甚至关键的决策。虽然还有很长的路要走，但以下是一些可能被代理颠覆的行业:

![代理分析师示例](https://oss-ata.alibaba.com/article/2025/01/aab290de-a053-4b66-9442-1f58b455931d.png)

*LLM Agent根据相关收益和竞争对手提供通用电气股票价格预测和见解的示例流程*

### 真实世界Agent工作流程:有趣的用户场景

在以下部分，我们展示了来自 Agent Arena 的一些最有趣的真实示例。这些示例代表了不同的用户场景，Agent被要求解决特定挑战，突出了所使用的各种模型、框架和工具。每个提示都说明了Agent的思维过程、执行情况和需要改进的领域，为开发者和用户提供了见解。

## 用例示例

##### 教育与个性化辅导 📚

> *"为这道高中物理题生成逐步解答和解释:一个5公斤的物体从10米高处落下。需要多长时间才能落到地面?"*

\+ 提示执行

```
ref
```



在教育场景中，LLM Agent有潜力提供丰富的逐步解释，引导学生解决复杂问题，如物理计算。在确定5公斤物体从10米高处落下所需时间的例子中，Agents 使用基本运动方程来解决问题。然而，虽然**AgentA**(**anthropic计算器工具， claude-3-opus-20240229**)提供了详细的解决方案分解，但其方法的简单性突显了需要更细致地处理运动学，比如根据用户查询动态调整的自适应响应。同时，**AgentB**(**langchain Wolfram Alpha， claude-3-haiku-20240307**)虽然利用了Wolfram Alpha但在从工具获取相关数据时遇到困难，表明API集成存在影响实时计算准确性的缺陷。这些案例显示了在调整Agent与API和框架交互方面的机会，确保Agent不仅能检索正确数据，还能有效地处理并应用到实际场景中。改进这些计算的流畅性和深度，特别是在利用多个API时，可以在教育环境中带来更高的精确性和适应性，丰富学习体验并使Agent更能处理各种教育查询。

##### 商业数据分析 💸

>  *"给定这个去年销售数据的.csv文件，生成关于哪些产品需要扩大规模的见解。"*

\+ 提示执行

```
ref
```



在商业数据分析中，LLM Agent 可以通过处理大型数据集(如包含销售数据的CSV文件)来提供有价值的见解，发现趋势并提出战略建议。在这个例子中，**AgentA**(**sqlAgent绘图器langchain， gpt-4o-2024-05-13**)遇到了错误，将CSV文件误解为SQLite数据库，这突显了Agent在错误处理能力和适应不同文件格式方面的局限性。尽管Agent试图切换工具并纠正流程，但很明显需要在SQL和文件处理工具之间建立更无缝的集成以保持工作流的流畅性。同时，**AgentB**(**langchain Pandas DataFrame， gpt-4o-2024-08-06**)有效地分析了销售数据，根据销售收入识别出表现最好的产品如"笔记本电脑"和"智能手机"，并建议由于销售量高而扩大"耳机"和"键盘"的规模。然而，**AgentB**可以通过将销售模式与季节性或促销等外部因素联系起来，获得更深入的上下文理解。这些例子强调了Agent需要更好地处理复杂数据集、增强错误恢复能力，并提供更多上下文感知的分析，特别是在切换工具或处理不同数据格式时。改进这些领域将显著提升Agent提供更稳健、可操作见解的能力，特别是在复杂的商业场景中。

##### 社交媒体管理/内容创作 📸

> *"安排一周的每日 Instagram 帖子，使用相关标签和当前网红趋势来推广我们即将举行的促销活动。"*

\+ 提示执行

```
ref
```



在这个例子中，由于Agent的通用性质，安排一周的每日 Instagram 帖子并使用相关标签和网红趋势的任务并未完全实现。两个代理—**langchain google-serper search agent (gemini-1.5-pro-001)** 和 **langchain You.com Search (gpt-4o-mini-2024-07-18)**—都尝试创建内容并建议标签，但缺乏处理细致的社交媒体调度任务所需的特定功能。**gemini-1.5-pro-001** 代理不断循环询问有关促销的更多信息，而 **You.com** 代理则专注于为期一周的帖子日程的一般性建议，没有实时参与度洞察或基于平台特定趋势的自适应内容创作。

例如，**gemini-1.5-pro-001** 代理的输出重复询问促销详情，这表明在处理上下文方面存在局限性。此外，响应结构未考虑 Instagram 的独特功能，如最佳发布时间或 Story 集成。同时，**gpt-4o-mini-2024-07-18** 代理提供了一个不错的帖子日程，但没有充分利用网红数据或实时趋势来指导其内容建议。

这些代理虽然可以运作，但表明需要更专门的框架来适应社交媒体平台。集成与 Instagram、Twitter 或 Facebook 等平台直接连接，并整合最新社交媒体参与度分析的框架，将使代理能够生成更精确、更具平台特异性的建议。此外，代理可以从直接处理任务(如调度帖子)中受益，而不是仅生成内容想法，这将使它们在简化社交媒体策略方面更加有效。

##### 医疗诊断 👩‍⚕️

> *"根据这份包含患者病史的文件，与最新研究论文交叉参考，为慢性偏头痛推荐最新的治疗方案。"*

\+ 提示执行

```
ref
```



手动筛选医学研究不仅耗时，而且在数据跨越众多临床试验和不断发展的治疗方案时容易出现疏漏。LLM Agent提供了一个强大的替代方案，擅长梳理广泛的医学数据库，提取相关发现，并将其与特定患者病况联系起来。在慢性偏头痛等病例中，Agent可以迅速收集关于有效治疗的最新研究，如预防性治疗 erenumab 和提供长期头痛缓解的射频消融(RFA)。例如，在这个案例中，**langchain PubMed 生物医学文献工具(gpt-4o-mini-2024-07-18)** 代理成功检索到相关研究，根据最新发现提出具体的治疗方案。相比之下，**ArXiv 文章获取器(claude-3-haiku-20240307)** 由于 ArXiv 专注于预印本而非临床治疗而遇到困难。尽管如此，建议查看更适合的医学期刊(如 JAMA)的后备建议，显示了Agent在遇到限制时如何适应。加强与专业数据库的集成并改进多步查询处理可以释放更多潜力，使这些代理能够提供更快速、更准确和更具上下文相关性的医疗建议，最终推动自动化系统如何支持医疗决策的边界。

##### 体育数据分析 🏀

> *"根据球员个人数据、球队表现趋势和最近的交易新闻，预测丹佛掘金队赢得NBA冠军的概率。"*

\+ 提示执行

```
ref
```



在体育数据分析中，LLM Agent需要分析球员统计数据、球队表现和交易新闻来预测结果，比如丹佛掘金队赢得NBA冠军的可能性。**Agent A**(**langchain google-serper search agent， open-mixtral-8x22b**)收集了相关数据，但缺乏深入分析，忽略了关键洞察如球员受伤或其他球队的竞争情况。同样，**Agent B**(**anthropic web page reader， claude-3-5-sonnet-20240620**)强调了实时数据的重要性，但由于缺乏当前赛季的统计数据而无法提供详细的数据驱动预测。

这两个Agent都突显了需要更好地整合实时数据源和更先进的统计建模。改进这些代理处理多步推理和预测分析的方式将显著提升它们提供准确、可操作洞察的能力，使它们对依赖此类预测进行决策的球队、分析师和体育博彩者更有价值。

##### 旅行规划 ✈️

>  *"规划一次从旧金山到卡梅尔海滩的一日游。通过选择最省油的路线和最多景点来优化行程。"*

\+ 提示执行

```
ref
```



在旅行规划中，LLM Agent有潜力通过优化路线和建议相关停靠点来制定详细的行程。在规划从旧金山到卡梅尔海滩一日游的例子中，**Agent A**(**crewai AI Crew for Trip Planning， gemini-1.5-pro-002**)和**Agent B**(**langchain google-serper search agent， open-mixtral-8x22b**)都建议了省油路线和热门景点。然而，虽然**Agent B**高效地识别了主要地标并提供了清晰的路线，但响应缺乏基于实时条件(如交通或道路封闭)的动态调整。

此外，**Agent A**在工具之间的无缝过渡方面存在困难，这影响了充分整合相关行程数据的能力。这些例子指出了通过增强实时API集成和提高代理对变化的旅行条件的适应性，可以提供更个性化和准确的行程规划。更好地处理当前道路状况或用户特定偏好等动态因素将带来更丰富、更相关的旅行体验。

## 下一步计划和项目路线图

我们为 Agent Arena 制定了令人兴奋的路线图，计划了几项举措来增强和扩展平台的功能。我们设想 agent arena 将成为Agent开发者和提供商的中心枢纽。

对于有兴趣构建/使用Agent的开发者和用户来说，该平台将成为他们完善Agent技术栈的沙盒，提供适合其用例的正确提供商和框架。

通过提供系统化的方式来运行Agent、相互比较、查看基于用例的提供商高级分析，甚至查看类似用户的提示，我们希望为Agent构建社区提供价值。

为了实现这一愿景，我们制定了全面的功能开发和改进路线图。这些变更的总体主题将是改善竞技场对个别用户的个性化，同时扩展可用的分析功能。

##### 📈 增加平台上的 LLM 和框架提供商数量

Agent Arena 的主要目标之一是向用户展示他们可以构建的所有Agent组合，让他们能够确定哪些选项最适合他们的用例。虽然我们目前在每个类别中都提供主要的提供商，但我们希望扩大选择范围，纳入更多专注于特定任务的小众提供商。

##### 🧑‍💻 加入用户个性化

为了使平台尽可能实用，我们希望确保用户能够获得关于最新发布和最适合其用例的Agent的具体推荐。这将涉及我们学习他们对提供商和输出格式的偏好，使我们能够为他们推荐最佳Agent。

##### 🪜 启用多轮提示

大多数Agent任务都涉及Agent的多个推理和行动步骤。这需要跟踪任务上下文的状态。例如，看看以下任务:

> **任务:** "搜索标准普尔500指数中今年表现最好的5只股票，然后找出关于它们的最新新闻。"

这个任务需要Agent首先找到前5只股票，将其保存在后端'内存'中，然后调用另一组单独的工具来查找关于它们的最新新闻。这是一个多轮提示，其他示例可能会涉及5个以上的步骤。我们计划在未来几个月内为用户发布这项功能。

##### 🏋️‍♀️ 扩展平台的功能

平台的当前实现还有几个Agent用例领域未被探索。更具体地说，我们希望开始与Jira、Github、GSuite等API集成，使用户能够在他们的个人数据上实际运行Agent。虽然这将涉及许多安全和隐私考虑，但我们相信这是使平台对用户更有用的关键一步。

##### 📊 改进推荐算法

基于用户偏好和他们喜欢的提供商/框架，我们计划改进目标到更相关Agent的路由。此外，我们将包括两种不同的路由模式:一种更具探索性，另一种更专注于用户的偏好。

## 结论

Agent Arena 是一个评估和比较 LLM Agent的平台。通过提供全面的排名系统和测试各种框架Agent的工具，该平台使用户能够就其特定需求的最佳模型和工具做出明智的决定。随着计划中的持续改进和扩展，Agent Arena 将在塑造 LLM Agent评估的未来方面发挥关键作用。

我们邀请研究人员、开发者和 AI 爱好者探索 Agent Arena，为其成长做出贡献，并帮助塑造基于Agent的 AI 系统的未来。让我们一起推动 LLM Agent可能性的边界，释放 AI 驱动问题解决的新潜力。

------

我们希望您喜欢这篇博文。我们很乐意在 [Discord](https://discord.gg/grXXvj9Whz)、[Twitter (#GorillaLLM)](https://twitter.com/shishirpatil_/status/1661780076277678082) 和 [GitHub](https://github.com/ShishirPatil/gorilla/) 上听取您的意见。

#### 引用

如果您想引用 Agent Arena:

```
@inproceedings{agent-arena，
            title={Agent Arena}，
            author={Nithik Yekollu and Arth Bohra and Ashwin Chirumamilla and Kai Wen and Sai Kolasani
                    Wei-Lin Chiang and Anastasios Angelopoulos and Joseph E. Gonzalez and
                    Ion Stoica and Shishir G. Patil}，
            year={2024}，
            howpublished={\url{https://gorilla.cs.berkeley.edu/blogs/14_agent_arena.html}}，
}
```

___
