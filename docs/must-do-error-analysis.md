# 将模型投入生产之前，必须进行 5 项误差分析

> 原文：<https://web.archive.org/web/https://neptune.ai/blog/must-do-error-analysis>

深度学习时代的繁荣始于 2012 年，当时 [Alex Krizhevsky](https://web.archive.org/web/20221208053917/https://qz.com/1307091/the-inside-story-of-how-ai-got-good-enough-to-dominate-silicon-valley/) 创建了一个卷积神经网络，将图像分类的准确率提高了 10%以上。这一巨大成功很快被其他研究领域所效仿，很快其他企业——企业集团和初创企业——希望将这一前沿技术应用到自己的产品中:银行现在使用 ML 模型来检测欺诈；自动驾驶采用传感器结果，自动做出自信的决策。

然而，从研究到生产的快速转变往往会导致一些经常被忽视但却是必须考虑的差距。许多公司仅仅根据实验室结果，甚至简单的准确性来判断他们的机器学习模型，以加快展示他们的产品线的步伐，这实际上可能导致巨大的性能差距，甚至未知的偏差。

本文深入探讨了机器学习(ML)模型表面精度性能之外的现实场景，这是人们在将其投入生产之前应该考虑的。具体来说，虽然特定模型的训练精度可能看起来令人信服，但分析不同的数据集分布是否会保持性能是很重要的。我们提供了**五个必做的分析，以确保您的模型在上线时能如预期一样运行。**

## 错误分析:初步知识

在探索为什么每个[错误分析](https://web.archive.org/web/20221208053917/https://www.analyticsvidhya.com/blog/2021/08/a-quick-guide-to-error-analysis-for-machine-learning-classification-models/)是不同的并且必须完成之前，我们首先必须正确理解 ML 模型的核心概念，以实现其内在的约束。

在传统的工程环境中，我们希望设计将输入映射到指定输出的数学系统——这种数学建模并不总是可行的，尤其是当系统未知且过于复杂而难以找到时。这就是机器学习(特别是监督学习)发挥重要作用的时候。我们不是创建模型，而是基于一组已知的输入和输出来学习它。

为了充分训练和[评估 ML 模型](/web/20221208053917/https://neptune.ai/blog/the-ultimate-guide-to-evaluation-and-selection-of-models-in-machine-learning)，典型的设置是将可用数据集分成训练集和验证集，其中在模型设置期间仅看到训练集，而验证仅用于评估。我们通常基于验证集的性能，作为我们的模型在真实世界设置下推广的基准。因此，了解验证数据集的构造方式以及模型投入生产时可能出现的潜在分布变化非常重要，因为所有的误差分析都围绕这一概念进行。

## 错误分析 1:训练和验证数据集的大小

如前一节所述，模型的表现完全取决于训练和验证数据集。这导致每个人在生产之前必须进行的第一个也是最重要的错误分析:确定训练和验证数据集的大小是否足够。我们可以用一个狗和猫的图像识别模型来说明这一点的重要性。

假设我们希望创建一个 ML 模型，它可以确定图像中的动物是狗还是猫。为了训练模型，我们需要收集一组图像来进行训练和验证。现在考虑这样一种情况，有 50 种狗，然而我们只有 40 种用于训练和验证的图像。我们的模型可能在训练和验证上都表现得非常好，但我们永远不会知道它对 10 种未知物种的概括能力有多强，当模型投入生产进行真实世界测试时，这些物种是有效的例子。

这个案例听起来可能过于极端，导致人们质疑它是否真的会在现实中发生。然而，许多真实世界的数据包含更复杂的分布，超出了我们的知识范围——一个模型可能不会暴露出一个重要的特征，而我们甚至不会注意到！下面进一步描述了当训练/验证集太小时可能导致的问题。

*   **当训练集太小的时候**:我们可以把训练集想象成整个真实世界数据集的一个小群体。因此，如果我们希望 ML 模型是稳健的，我们会希望这个小群体从真实世界数据可能具有的所有可能性中提取样本。如果没有，模型将只学习可用数据的少量分布，使其在测试中几乎不可推广。

*   **当验证集过小时**:当验证集过小时，可能会出现不同的问题。一个小的验证集可能意味着不是所有的可能性都被测试过，而不是不可推广。这可能会给人以错误的印象；如果学习了当前的分布，我们可以看到一个非常好的结果，反之亦然。

“太小”的确切原因是一个困难的问题，因为答案基于许多未知因素，如任务的难度和数据集分布的复杂程度。但是，根据经验，如果影像数据集包含的影像少于几千幅，或者如果回归数据集包含的条目少于几千条，则模型通常不能很好地进行概化。如果你的 ML 模型是基于深度学习的(需要神经网络)，由于深度学习模型有大量的参数可用，你肯定需要更多的数据。

### 解决办法

解决方法很简单—**增加数据集的大小**。考虑你的模型将要应用到什么样的现实场景中。在相同的设置下收集更多的数据，并将它们放入训练和验证数据集中。虽然看起来很简单，但这实际上是许多公司(如谷歌、脸书)一直试图通过他们庞大的客户数据库实现的:他们不断收集所有数据(搜索历史的图像)，因为他们具体意识到数据集大小增加的好处是机器学习。

之后，考虑训练集和验证集的平衡。80/20 是标准的划分，但是根据数据集的大小，您可以减少验证集以使训练更加稳健，反之亦然，以便更好地了解模型的执行情况。

## 错误分析 2:数据集的平衡和每类的准确性

第二个错误分析挖掘数据集的内容，以找到标签的平衡。我们可以回到上面提到的狗/猫分类任务来说明这个问题。

考虑一个数据集，在 1000 幅图像中，990 幅是狗，剩下的 10 幅是猫。如果一个模型学会将所有东西都归类为狗(这是非常不准确的，基本上是无用的)，它将在整个数据集上获得 99%的准确率。如果不深入研究错误的形成，许多人会直截了当地认为模型训练有素，可以投入生产了。

这个问题可以很容易地通过研究每个类的样本数量来规避。在最佳情况下，我们希望每个单独的类拥有大致相同数量的数据。如果这样的设置是不可能的，那么我们至少应该关注每个类有足够数量的数据(几百个)。

然而，即使数据集完全平衡，网络也可能在某些类上表现得更好，而在其他类上表现得更差，或者如果您的任务是二元分类，则在检测阳性方面表现得比阴性更好。要意识到这一点，我们不能只直接看准确度，而是要通过不同的度量来看每个类的准确度和真/假阳性/阴性。以下是一些可用于准确性之外的进一步分析的指标。

![Error analysis 2: Balance of a dataset and accuracy per class](img/405bb5c568aaa4fe31678d59cff8950a.png)

*   **F1 得分**:对于二元分类，我们可以计算 F1 得分(上面的公式)，这可以让我们了解我们给定模型的精度和召回率。请注意，我们还可以专门取出精度，并逐个召回进行评估。根据手头的任务，我们可能希望最大限度地提高精确度，或者回忆并放弃另一个。例如，如果您的目标是为您的产品找到一组目标客户，您可能希望召回量大一些，这样您就不会错过潜在客户，但是如果您找到的客户不完全是您的目标客户，精确度可能会受到影响。

![Example of a Confusion Matrix on Iris Dataset to show the percentages of predictions for each label](img/3e9a25a88e52a357b910161fe9765031.png)

*Example of a confusion matrix on iris dataset to show the percentages*
*of predictions for each label | [Source](https://web.archive.org/web/20221208053917/https://scikit-learn.org/stable/auto_examples/model_selection/plot_confusion_matrix.html)*

*   **混淆矩阵:**(上图)显示每个类别被分类为所有类别的百分比。伴随着颜色编码，当我们试图理解哪一类更容易出错以及错误是如何产生的时候，这个矩阵就变成了一个很好的可视化工具。
*   **ROC 曲线:**如果我们在做分类，可以通过改变阈值来区分阳性和阴性来构造一条曲线。这将使我们更好地了解哪个阈值更好。

### 解决办法

如果您遇到一个数据少得多或者性能比其他类差得多的类，那么增加这个特定类的数据量是明智的。如果无法获得真实世界的数据，也可以使用数据扩充等技巧作为替代(关于数据扩充的更多内容将在后一节中讨论)。此外，我们还可以对数据应用随机权重采样，或者直接对每个样本硬编码采样权重，以应对数据集的不平衡。

## 错误分析 3:细粒度的错误分类错误

既然您可能计算的所有数字都显示了良好的结果，那么仍然有必要对细粒度的分类结果进行一些粗略的定性分析，以准确理解是什么导致了错误。

通常，一些错误可能非常明显，但仅仅从类信息中是看不出来的。例如，在我们的狗/猫问题中，我们可能会注意到，通过简单地可视化所有错误分类的猫，所有的白猫都被错误分类了，然而猫的整体类别表现得非常好。这种错误分析变得至关重要，因为白猫可能是现实世界案例的主要部分(再次是验证和现实世界测试之间的分布差异问题)。然而，由于 labels 本身根本不能反映这样的问题，研究这个问题的唯一方法是通过人在回路中的定性分析。

### 解决办法

细粒度错误分类的解决方案非常类似于[不平衡数据集](/web/20221208053917/https://neptune.ai/blog/how-to-deal-with-imbalanced-classification-and-regression-data)的解决方案，其中直接且唯一的解决方案是向数据集添加更多内容。然而，为了定位一个特定的细粒度类，我们应该只添加那些性能很差的子类。

## 错误分析 4:调查过度拟合的程度

当模型学习仅出现在训练集中的模式/数据分布时，会发生过度拟合。当将模型转移到测试时，使用这些模式会降低模型的性能。

过度拟合可能以两种方式出现，一种更常见，另一种不太常见。下面描述了这两个问题及其各自的解决方案。

### 过度适应训练集

当网络从训练集中学习到不适用于验证集的东西时，就会发生这种情况。在每个时期之后，借助于绘制用于训练和验证的损失曲线，该问题很容易观察到。如果在验证变得停滞后，训练损失继续下降(导致性能差距扩大)，则过度拟合正在发生。

### 解决办法

训练集过拟合有多种解决方案，下面我们列出了 6 种可行的解决方案。

*   **提前停止:**我们在每个历元/a 一定数量的迭代之后绘制训练和验证损失。如果验证损失在一定数量的时期内停止下降，我们就完全停止训练。这可以防止模型在训练集中学习不需要的模式。
*   **降低 ML 模型的复杂性:**当一个模型太复杂(参数太多)时，它很可能会学习模式，特别是当手头的任务实际上很简单时(例如，如果我们正在学习一个函数 y = wx，但我们有 100 个参数{w1，w2 …，w100}，那么很可能 w2 到 w100 实际上学习的是数据中的噪声，而不是真实的函数)。如果您正在训练神经网络，这相当于减少层的深度和宽度。如果你正在训练决策树/森林，那将会限制树的深度或数量。
*   **添加 L1/L2 正则化:**这些正则化防止特定参数过大，过拟合时经常出现这种情况。具体来说，L1 正则化旨在最小化每个权重的绝对值，而 L2 正则化则最小化每个权重的平方值。因此，平方曲线会将所有值推到接近 0 的状态。本质上，这两个正则化确保了每个因素对最终预测的贡献是相似的。

![Comparison of a neural network with and without dropout. Picture retrieved from the original dropout paper from JMLR](img/6f1aba14afb201a3ed903f2d726a5a08.png)

*Comparison of a neural network with and without dropout | Picture retrieved*
*from the original dropout paper from [JMLR](https://web.archive.org/web/20221208053917/https://jmlr.org/papers/v15/srivastava14a.htm)*

*   **加入退出项:**对于一个神经网络，我们可以随机地退出某些神经元，不考虑它们的输出。这将使我们的模型对输入数据的微小变化不太敏感，从而防止过度拟合。

![Visualisation of a data augmentation technique called “mixup”](img/030d817b42f758cc36ef745fcddc14c9.png)

*Visualization of a data augmentation technique called “mixup” | Source: Author*

*   **数据扩充:**增加更多的训练数据将使得某个模式不太可能存在于训练集中而不存在于验证集中。数据扩充是一种综合当前可用条目中的数据并将其用作训练的附加数据的技术。例如，我们可以裁剪、旋转和移动图像，并将它们视为“新”图像，以添加到训练中，并使模型更加健壮。一些更复杂和最新的增强技术还包括 mixup、cutout、cutmix 等。
*   **集成学习:**如果计算资源可用，人们可以训练多个模型，并将它们的预测组合在一起，以做出最终的预测。不太可能所有模型都过度适应相同的模式。因此，预测将更加平滑，进一步防止过度拟合。事实上，集成模型有多种方式，从简单的打包和提升到更复杂的深度模型组合——所有这些都取决于手头案例所用的场景和模型。

### 过度适应验证集

这不是一个常见的问题，因为实际上只有训练数据用于调整模型。但是，如果您基于验证执行早期停止，仍然有可能您实际上为该特定集合而不是为整个真实总体选择了最佳模型。

### 解决办法

防止这种情况的一种方法是通过交叉验证等方法采用多重验证。交叉验证将整个数据集分成多个片段，每次我们取其中一个片段用于验证，其余的用于训练。此外，我们还应该确保验证集足够大，并且当我们手动增加验证集的大小时，精确度保持大致相同。

## 错误分析 5:模仿模型将被应用的场景

最后，在您可以使用培训和验证集进行所有详细分析之后，是时候模拟您的模型将如何应用的场景并测试实际结果了。

我们可以通过狗/猫的分类问题再次说明这一点。假设你正在为 iPhone 用户创建一个给狗和猫分类的 app 你根据从网上获得的一堆狗和猫的图片，经过仔细分析，训练了你的模型。对于这最后一部分，您可能希望通过在不同的 iPhone 相机上进行测试来模拟真实场景。换句话说，我们希望确保在测试集上进行评估时，您训练和验证的模型之间的分布差距很小。

### 解决办法

由于这是误差分析的最后阶段，并且假设其他一切都表明您的模型具有巨大的潜力，您可以潜在地将您的模型投入生产，但添加在线学习机制以继续模型的微调。

在线学习是一种学习机制，通过将新的测试数据添加到训练中来不断改进模型。随着添加越来越多的数据来微调模型，准确性可能会增加，因为模型将开始更符合您最终目标的测试分布。许多应用程序(如 Siri、面部识别)实际上都采用了这种方案，这就是当你继续使用手机时，它们如何慢慢适应你的外观和声音。

## 失败的案例

只有上述错误分析的理论支持，人们实际上可能会怀疑这种数据分析的实用性和必要性:如果没有这些修复，机器学习模型真的会出错吗？要回答这个，我们其实可以深挖一个 AI 工具失败的两个经典案例:[2014 年亚马逊 AI 招聘的偏差](https://web.archive.org/web/20221208053917/https://www.reuters.com/article/us-amazon-com-jobs-automation-insight-idUSKCN1MK08G)和 2019 年 [FB 广告](https://web.archive.org/web/20221208053917/https://www.nytimes.com/2019/03/19/technology/facebook-discrimination-ads.html)。

### 亚马逊 AI 招聘

为了加快招聘过程，亚马逊在 2014 年致力于开发一种人工智能驱动的招聘工具，以审查和筛选简历。然而，在投入生产一年后，他们在 2015 年意识到性别偏见的严重问题，尽管性别不是投入因素的一部分，特别是对于软件开发人员或其他技术角色。

#### 这是怎么发生的？

这个问题的答案实际上深深植根于模型数据集最初是如何收集的:使用过去 10 年的简历，这些简历最终以男性为主。事实上，美国顶级科技公司技术岗位的男女员工比例约为 3:1。

![Tech Industry is dominated by men](img/1a32ef9cd493268adb3300570bd75708.png)

*Tech Industry is dominated by men | [Source](https://web.archive.org/web/20221208053917/https://www.reuters.com/article/us-amazon-com-jobs-automation-insight-idUSKCN1MK08G)*

正因为如此，这种模式天生就给人一种错误的印象，即男性候选人更受青睐，因此更多地与男性相关的词汇联系在一起，从而摧毁了包含“女子象棋俱乐部队长”等词汇的简历的价值。

#### 如何才能避免这种情况？

实际上，我们可以将这种情况追溯到错误 2 和错误 5，在这两种情况下，数据集中的严重不平衡(男女比例)以及未能模拟真实的评估场景。因此，为了在生产之前避免这些严重的错误，必须考虑数据集的平衡，并且潜在地对特定类别的样本/扩充增加权重，以避免这样的问题。

### 脸书广告偏见

类似地，脸书推出了一个机器学习和推荐系统，但这种推荐最终将女性排除在某些广告之外，反之亦然，这是由于一般人群的一般化和相关的刻板印象，正如南加州大学的研究所表明的。

#### 如何才能避免这种情况？

同样，通过大量测试来平衡和发现细粒度错误，同时通过在实际投入大规模生产之前拥有一组测试用户来模拟现实世界中可能发生的场景，通过详细和严格的错误分析，这样的问题不太可能发生。

## 尾注

现在你知道了！在你可以说你的模型已经完全准备好投入生产之前，你必须做五种不同的误差分析。这里有一些其他有用的文章，你可以进一步深入研究，使你的机器学习模型在生产中有效和值得尊敬:

请记住，在测试阶段小心谨慎可能会节省很多精力！