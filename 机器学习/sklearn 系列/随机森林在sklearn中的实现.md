# 随机森林在sklearn中的实现

## 随机森林概述

### 集成算法概述
集成学习(ensemble learning)是时下非常流行的机器学习算法,它本身不是一个单独的机器学习算法,而是通过在数据上构建多个模型,集成所有的模型的结果.基本上所有的机器学习领域都可以看到集成学习的身影，在
现实中集成学习也有相当大的作用，它可以用来做市场营销模拟的建模，统计客户来源，保留和流失，也可用来预测疾病的险和病患者的易感性。在现在的各种算法竞赛中，随机森林，梯度提升树（GBDT），Xgboost等集成算法的身影也随处可见，可见其效果之好，应用之广。

|                                        集成算法的目标                                         |
| ------------------------------------------------------------------------------------------- |
| 集成算法会考虑多个评估器的建模结果,汇总之后得到一个综合的结果,**以此来获取比单个模型更好的回归或分类表现** |

多个模型集成成为的模型叫做集成评估器（ensemble estimator），组成集成评估器的每个模型都叫做基评估器（base estimator）。通常来说，有三类集成算法：装袋法（Bagging），提
升法（Boosting）和stacking。

![](assets/20200331181058422_1173927189.png =800x)

装袋法的核心思想是构建多个**相互独立的评估器**，然后对其预测进行平均或多数表决原则来决定集成评估器的结
果。装袋法的代表模型就是随机森林。
提升法中，**基评估器是相关的**，是按顺序一一构建的。其核心思想是结合弱评估器的力量一次次对难以评估的样本
进行预测，从而构成一个强评估器。提升法的代表模型有Adaboost和梯度提升树。


### sklearn中的集成算法

- sklearn中的集成算法模块ensemble

|                  类                  |             类的功能              |
| ------------------------------------ | ------------------------------ |
| ensemble.AdaBoostClassifier         | AdaBoost分类                     |
| ensemble.AdaBoostRegressor          | AdaBoost回归                     |
| ensemble.BaggingClassifie           | 装袋分类器                        |
| ensemble.BaggingRegressor           | 袋装回归器                        |
| ensemble.ExtraTreeClassifier        | Extra-trees分类(超树,极端随机树)   |
| ensemble.ExtraTreesRegressor        | Extra-trees回归                  |
| ensemble.GradientBoostClassifier    | 梯度提升分类                     |
| ensemble.GradientBoostRegressor     | 梯度提升回归                     |
| ensemble.IsolationForest            | 隔离森林                          |
| **ensemble.RandomForestClassifier** | **随机森林分类**                  |
| **ensemble.RandomForestRegressor**  | **随机森林回归**                  |
| ensemble.RandomTreesEmbedding       | 完全随机树的集成                   |
| ensemble.VotingClassifier           | 用于不适估算器的软投票/多数规则分类器 |

集成算法中,有一半以上都是树的集成模型,可以想见决策树在集成中必定有很好的效果.

## RandomForestClassifier
```
class sklearn.ensemble.RandomForestClassifier (n_estimators=’10’, criterion=’gini’, max_depth=None,
min_samples_split=2, min_samples_leaf=1, min_weight_fraction_leaf=0.0, max_features=’auto’,
max_leaf_nodes=None, min_impurity_decrease=0.0, min_impurity_split=None, bootstrap=True, oob_score=False,
n_jobs=None, random_state=None, verbose=0, warm_start=False, class_weight=None)
```
随机森林是非常具有代表性的Bagging集成算法，它的所有基评估器都是决策树，分类树组成的森林就叫做随机森
林分类器，回归树所集成的森林就叫做随机森林回归器。这一节主要讲解RandomForestClassifier，随机森林分类
器

### 重要参数
#### 控制基评估器的参数
|          参数          |                                        含义                                         |
| --------------------- | ---------------------------------------------------------------------------------- |
| criterion             | 不纯度的衡量指标,有基尼系数和信息熵两种选择                                               |
| max_depth             | 树的最大深度,超过最大深度的树都会被剪枝                                                  |
| min_samples_leaf      | 一个节点在分之后每个子节点都必须包含至少min_samples_leaf个训练样本,否则不分枝               |
| min_samples_split     | 一个节点必须要包含至少min_samples_split个训练样本,这个节点才允许被分枝,否则分枝就不会产生    |
| max_features          | max_features限制分枝时考虑的特征个数,超过限制个数的特征都会被舍弃,默认值为总特征数的开平方取整 |
| min_impurity_decrease | 限制信息增益的大小,信息增益小于设定数值就不会分枝                                         |

#### n_estimators
这是随机森林的数量,即基评估器的数量,这个参数对随机森林模型的精确性影响是单调的,**n_estimators越大,模型的效果往往越好** . 但是相应的,任何模型都有决策边界,n_estimators到达一定程度之后,随机森林的精确性往往不再上升或开始波动.
并且，n_estimators越大，需要的计算量和内存也越大，训练的时间也会越来越长。对于这个参数，我们是渴望在训练难度和模型效果之间取得平衡


### 重要属性和接口
随机森林有三个非常重要的属性:`.estimators_`,`.oob_score_`以及`.feature_importance_`.

`.estmators_`是用来查看随机森林中所有树的列表的.

`.oob_score_`指的是袋外得分. 随机森林为了确保林中的每棵树都不尽相同,所以采用了对训练集进行有放回抽样的方式来不断组成新的训练集,在这个过程中,会有一些数据从来没被随机挑选到,他们就叫做`"袋外数据"`.这些袋外数据,没有被模型用来进行训练,sklearn可以帮助我们用他们来测试模型,测试的结果就有这个属性`obb_score`来导出,本质上还是模型的精确度.

而`.feature_importances_`和决策树中的`.feature_importances_`用法和含义都一致,是返回特征的重要性.

随机森林的接口和决策树完全一致,因此依然有四个常用的接口:`apply`,` fit`,`predict`和`score`.除此之外，还需要注意随机森林的predict_proba接口，这个接口返回每个测试样本对应的被分到每一类标签的概率，标签有几个分类就返回几个概率。如果是二分类问题，则predict_proba返回的数值大于0.5的，被分为1，小于0.5的，被分为0。传统的随机森林是利用袋装法中的规则，平均或少数服从多数来决定集成的结果，而sklearn中的随机森林是平均每个样本对应的predict_proba返回的概率，得到一个平均概率，从而决定测试样本的分类