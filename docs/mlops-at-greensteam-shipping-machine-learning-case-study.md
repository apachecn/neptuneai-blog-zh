# 格林斯团队的 MLOps:运输机器学习[案例研究]

> 原文：<https://web.archive.org/web/https://neptune.ai/blog/mlops-at-greensteam-shipping-machine-learning-case-study>

[GreenSteam](https://web.archive.org/web/20220926102803/https://www.greensteam.com/) 是一家为海洋产业提供软件解决方案的公司，这些软件解决方案有助于减少燃料的使用。过量使用燃料不仅成本高昂，而且对环境有害，国际海事组织要求船舶运营商更加环保，到 2050 年将二氧化碳排放量减少 50%。

尽管我们不是一家大公司(50 人，包括业务人员、开发人员、领域专家、研究人员和数据科学家)，但我们在过去 13 年中已经开发了几款机器学习产品，帮助一些[大型航运公司做出明智的性能优化决策](https://web.archive.org/web/20220926102803/https://blog.greensteam.com/crucial-tools-for-shipping-companies-looking-to-thrive-post-2020)。

在这篇博文中，我想分享我们构建 MLOps 堆栈的旅程。具体来说，我们如何:

*   处理了**代码依赖**
*   走近**测试 ML 模型**
*   建立**自动化培训和评估渠道**
*   **部署和服务我们的模型**
*   设法在 MLOps 中保持**人在回路中**

## 新的开始

2019 年，创建了一个由一名数据科学家、另一名机器学习工程师和我组成的项目团队。我们的工作是建立一个新的机器学习模型，取代我们以前使用的一些模型。

我们像其他人一样开始。我们进行无休止的讨论，创建新的笔记本，重新培训，比较结果。这对于构建一个原型是有效的，但是我们需要更好地组织它。

随着格林斯团队的顾客越来越多:

*   我们需要**处理越来越多的数据**，
*   我们**发现了更多的边缘案例**，在这些案例中，我们的模型表现不如预期，
*   我们**观察到了一个数据漂移**，我们的模型还不足以处理它。

另一件困扰我们的事是对未来的规划。**我们知道我们的 ML 运营需要与公司一起成长**，我们需要一个可以处理它的机构。我们既想要一个健壮的模型，可以处理我们从客户那里获得的不同类型的数据，又想要一个可扩展的技术解决方案。

有时候，最好的前进方式是后退一步，这正是我们所做的。

我们决定从头开始，重新思考我们的整个机器学习基础设施和操作。

## 步骤 1:共享 ML 代码库

一开始，每个新想法都是一个新的笔记本。随着我们的实验，我们有越来越多的笔记本分散在我们的笔记本电脑上。在某个时候，我们开始忘记笔记本中所有的代码变化。合并它们以保留一些共同的代码库也是一件非常痛苦的事情。

是时候**将共享代码库的一部分提取到一个 Python 包**中，并为其创建一个 git 存储库。通过这样做，我们仍然可以在笔记本上试验我们的想法，同时对代码库中变化不快的部分进行版本控制。区别和合并脚本也比笔记本容易得多。

## 步骤 2:处理依赖性和再现性

在这个阶段，我们还注意到了可重复性的问题。虽然我们的笔记本电脑设置相似，但并不相同。

使用 [**【康达】**](https://web.archive.org/web/20220926102803/https://towardsdatascience.com/a-guide-to-conda-environments-bc6180fc533) **解决了一些问题，但这是一个脆弱的解决方案。**有时，有人会在他们的本地环境中安装一些东西，使其与我们认为的环境有所不同。它导致了一些小的但是令人恼火的“它在我的机器上工作”类型的错误。

随着我们的模型逐渐成熟，我们在更大的数据集上测试它，使用更强大的云机器，而不仅仅是我们的笔记本电脑。但是当你从本地环境转移到云端时，另一个问题就出现了。人们很容易忘记一些依赖关系，因此拥有可以信任的环境配置变得至关重要。

> “Docker 帮助我们做到了这一点，这是一个新时代的开始。它使我们能够拥有一个统一、密封的设置，无论是谁在哪里运行它。”

[](https://web.archive.org/web/20220926102803/https://neptune.ai/blog/data-science-machine-learning-in-containers)****码头工人帮了我们，这是一个新时代的开始**。它使我们能够拥有一个统一的、密封的设置，无论是谁在哪里运行它。对于设置，我指的是代码库、包版本、系统依赖、配置。设置环境所需的一切。**

 **此时，我们可以从容器内部启动 Jupyter，保存所有版本控制代码的散列并生成结果。这与在 git 中存储所有内容一起，极大地提高了我们的代码库和模型变更的可再现性和可追溯性。

## 步骤 3:如何处理不测试的测试

测试代码本身就是一个问题。

从一开始，我们就以高测试覆盖率为目标，总是使用类型提示并关注代码质量。但是对于机器学习代码，事情并不总是那么简单。

模型代码的某些部分很难进行单元测试。例如，为了正确测试它们，我们需要训练模型。即使你使用一个小的数据集，对于一个[单元测试来说也是相当长的。](https://web.archive.org/web/20220926102803/https://www.jeremyjordan.me/testing-ml/)

你还能做什么？

我们可以采用预先训练好的模型，但是对于多重测试，初始化它的开销使得测试套件足够慢，从而阻碍了我们频繁地运行它。

最重要的是，这些测试中的一些是古怪的，随机失败。原因很简单(也很难解决):你不能指望在一个小数据子样本上训练的模型像在整个真实数据集上训练的模型一样。

> “…我们决定放弃一些单元测试并运行冒烟测试…我们将监控管道中的任何问题，并尽早发现它们。”

为了解决所有这些问题，**我们决定放弃一些单元测试，运行** [**冒烟测试**](https://web.archive.org/web/20220926102803/https://www.guru99.com/smoke-testing.html) 。对于每个新的 pull 请求，我们将在类似生产的环境中，在完整的数据集上训练模型，唯一的例外是超参数被设置为可以快速获得结果的值。我们将监控这条管道的任何问题，并尽早发现它们。

在一天结束时，我们希望我们的测试尽早标记失败的代码，这个设置为我们解决了这个问题。

## 步骤 4:代码质量、类型检查和持续集成

既然我们可以在不同的执行环境(Docker)之间移动，并且已经建立了我们的测试，我们就可以添加持续集成来使检查和评审更加有效。

我们还**将所有代码检查移入 Docker** ，因此 flake8、black、mypy、pytest 的版本和配置也统一了。这帮助我们将本地开发设置与我们在 Jenkins 上使用的相统一。不再有不同版本的 linter 在本地和 Jenkins 显示不同结果的问题。

对于本地开发，我们有一个 Makefile 来构建映像，运行它，并对代码运行所有的检查和测试。对于代码审查，我们设置 Jenkins 作为 CI 管道的一部分运行相同的检查。

> “…在许多情况下，mypy 帮助找到单元测试没有覆盖的代码部分的问题。”

帮助我们晚上睡得好的另一个保障是使用[**【mypy】**](https://web.archive.org/web/20220926102803/https://mypy.readthedocs.io/en/stable/)。人们有时认为它“只是”一个检查类型的静态分析器(对于动态类型语言！).从我们的经验来看，在许多情况下，mypy 有助于发现单元测试没有覆盖的部分代码的问题。看到一个函数接收或返回一些我们不期望的东西是一个简单但强大的检查。

## 步骤 5:添加流程编排、管道，并从整体服务转向微服务

现在，我们需要针对不同场景中不同客户端的多个数据集来测试我们的模型。这不是您想要手动设置和运行的。

我们需要流程编排，我们需要自动化管道，我们需要轻松地将其插入到生产环境中。

将我们的 ML 代码 Dockerized 使得在不同的环境(本地和云)之间移动变得容易，但这也使得将它作为微服务直接连接到生产管道成为可能。

也正是格林斯 team **从气流切换到** [**Argo**](https://web.archive.org/web/20220926102803/https://argoproj.github.io/) 的时候，所以在我们的容器里插上几个 YAMLs 就完事了。

## 步骤 6:跟踪所有的模型版本和结果

现在，我们可以有效地扩展我们的模型构建操作，但是这引发了另一个问题:我们如何管理所有这些模型版本？

我们需要监控多个模型的结果，在多个数据集上用不同的参数和指标进行训练。我们想将所有这些模型版本相互比较。

经过一些研究，我们发现了两个解决方案可以帮助我们做到这一点，MLflow 和 Neptune。让我们相信 Neptune 的是，它更像是一个开箱即用的解决方案。它使我们能够在一个地方标记实验、保存超参数、模型版本、度量和绘图。

## 步骤 7:定制模型服务

让我们回到阿尔戈。

对于训练机器学习模型来说，这是一个非常低级的解决方案。为什么我们不使用一些对数据科学更友好的东西，例如一些一体化的 ML 平台？

一个显而易见的原因是我们已经在使用 Argo 运行其他进程，但对我们来说，更大的问题是模型服务和部署。

在标准的机器学习场景中:

*   从一个数据集开始，
*   将它推入预处理管道
*   训练你的模型，
*   将它包装在一些预测函数中，这些函数可以被腌泡或打包到 REST API 中，
*   为生产服务。

然而，在我们的案例中，我们的客户希望我们对不同种类和品种的船只进行预测。它们可以是慢速挖泥船、大型游轮、巨型油轮，仅举几例，通常在非常不同的条件下航行。在许多情况下，你不能轻易地将你从一艘船上的数据中学到的东西转移到另一艘船上。

因此，我们需要用不断增长的时间序列数据集为每种血管类型训练一个单独的模型。

此外，与传统的机器学习不同，在传统的机器学习中，模型预测单一事物，我们的目标是拥有一个预测船只行为的多种特征的模型。

这些预测然后被插入到几个服务中，帮助我们的客户计划诸如配平、速度、安排船体清洁以恢复污垢的影响等事情。底线是，这些事情都在很大程度上影响燃料消耗，我们的模型可以帮助做出更好的决定。

我们查看了现成的解决方案，如 SageMaker 或 Kubeflow，但不幸的是，我们没有发现它们能很好地服务于我们的用例。

> “我们最终将模型打包到微服务中，……使用 FastAPI 完成这项工作比我们想象的更容易。”

我们最终将模型打包到微服务中，因此如果需要，我们可以很容易地用另一个服务替换一个服务，只要它共享相同的接口。使用 FastAPI **来做这个 [**，结果比我们想象的更轻松。**](https://web.archive.org/web/20220926102803/https://testdriven.io/blog/fastapi-streamlit/)**

为了举例说明设置的灵活性，我们最近需要推出一个运行速度非常快的虚拟模型，只是为了测试我们的基础设施。将一个简单的 Scikit-learn 代码打包到一个我们可以插入的微服务中，包括代码审查在内，整个生产过程只花了四天时间。

## 步骤 8:人在回路模型审计

我们扩展了它，但是仍然存在一个瓶颈。我们如何确保模型提供良好的业务洞察力(而不仅仅是良好的机器学习指标)？

我们的设置假设每个经过训练的模型都经过了领域专家**的审核和批准。**在培训结束时，**我们会制作一份报告，这是一个静态网页，上面有描述结果的图表和统计数据。**

这不是很好的扩展。我们试图通过创建一套检查来自动拒绝可疑的结果，这些检查将查询模型以验证预测是否与我们从物理学和常识中预期的一致。这些检查有点作用，但有时在数据质量问题上失败了。垃圾进，垃圾出。

我们将继续寻找一个令人满意的解决方案，但由于质量如此重要，我们依赖于领域专家对每个训练模型结果的审核。

## 步骤 9:功能存储和微服务(进行中)

下一步是将我们学到的知识归纳到一个合适的微服务架构中，该架构可以轻松处理许多不同的模型。

我们现在的目标是确保我们的架构能够让我们轻松地将任何未来模型作为微服务插入:

*   我们**通过创建我们的特征库，将我们自己从数据管道**中分离出来，为模型预先计算特征。
*   我们的**模型作为独立的微服务**运行。
*   虽然我们仍然部分地以推模式运行，在这种模式下，经过训练的模型将结果发送到系统的其他部分，**我们的目标是使它成为拉模式，在这种模式下，当需要访问结果时，服务将调用我们的 API** 。

## 我们当前的 MLOps 堆栈(以及一些经验教训)

在这个项目上工作了一年多之后，我们学到了很多关于如何有效地解决类似的问题并将它们转化为可行的解决方案。当然，我们前面还有很长的路要走，但我们希望我们能够应对新的挑战。

我已经提到了许多优秀的工具，但为了让您全面了解我们目前使用的 MLOps 技术体系:

*   **架构**:微服务/混合
*   **管道**:阿尔戈
*   **云**:和
*   **环境管理** : Docker、Conda、Pip、诗歌、Makefiles
*   **源代码**:BitBucket.com 上的 git
*   **开发环境** : Jupyter 笔记本
*   **CI/CD**:gitop 使用 Jenkins 运行代码质量检查，在测试环境中使用类似生产的运行进行冒烟测试
*   **实验跟踪**:海王星
*   **模型测试和批准**:定制解决方案
*   **代码质量** : pytest，mypy，black，isort，flake8，pylint
*   **监视**:基巴纳，哨兵
*   **模型服务**:带有 FastAPI REST 接口的 Docker
*   **机器学习栈** : Scikit-learn，PyMC3，PyTorch，Tensorflow

对我来说，在这里学到的最大的教训是保持简单 ( [你不会需要它](https://web.archive.org/web/20220926102803/https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)！)，不要面向未来，但同时要保持足够的灵活性，这样你就可以很容易地改变未来的事情。我们不断地重构代码和实验。拥有一个统一的开发环境，测试到位，能够轻松地将组件插入或替换到现有的架构中，这使得事情变得更加容易。

### 提摩太的船

格林斯团队的机器学习工程师

* * *

**接下来的步骤**

## 如何在 5 分钟内上手海王星

##### 1.创建一个免费帐户

2.安装 Neptune 客户端库

[Sign up](/web/20220926102803/https://neptune.ai/register)

3.将日志记录添加到脚本中

##### 2\. Install Neptune client library

```py
pip install neptune-client

```

##### 3\. Add logging to your script

```py
import neptune.new as neptune

run = neptune.init_run("Me/MyProject")
run["parameters"] = {"lr":0.1, "dropout":0.4}
run["test_accuracy"] = 0.84
```

[Try live notebook](https://web.archive.org/web/20220926102803/https://colab.research.google.com/github/neptune-ai/examples/blob/master/how-to-guides/how-it-works/notebooks/Neptune_API_Tour.ipynb)

* * ***