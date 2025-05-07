## Association Rule 关键定义

- **Itemset**：一组一个或多个 items（项）的集合。  
  - **k-itemset**：包含 k 个 items 的 itemset，例如 X = {x₁, ..., x_k}。

- **Support**（支持度）：  
  - **Absolute Support (Count)**：包含该 itemset 的 transaction 数量。  
  - **Relative Support**：包含该 itemset 的 transaction 占总 transaction 的比例。  
    - 记作 $sup(X)$，即 transaction 包含 itemset X 的概率。  
    - $sup(X) = \frac{count(\{X\})}{count(Total)} $
      - $sup(X)$ 频率
      - $P(X)$ 概率

- **Frequent Itemset**：  
  - 如果 itemset X 的 support 不小于最小支持度阈值（minsup），则称 X 为 frequent itemset。

- **Association Rule**（关联规则）：  
  - 形式为 $X \rightarrow Y$，其中 X 和 Y 都是非空 itemset，且 $X \cup Y = \emptyset$。
  - 描述了“如果 X 出现，则 Y 也很可能出现”的关系。

- **Support of Rule**（规则的支持度）：  
  - $sup(X \rightarrow Y) = P(X \cup Y)$  = $\frac{count(\{X, Y\})}{count(Total)} $
    * $X \cup Y $ 看成一个事件，就是 X， Y 同时发生

- **Confidence**（置信度）：  
  - $conf(X \rightarrow Y) = P(Y|X) = \frac{sup(X \cup Y)}{sup(X)}$  
    - 表示在包含 X 的 transaction 中，同时也包含 Y 的概率。
    - 就是算一个条件概率

- **Downward Closure Property**（向下封闭性质）：  
  - 任意 frequent itemset 的所有子集也一定是 frequent 的。

- **Apriori Principle**：  
  - 如果一个 itemset 是 infrequent 的，则它的所有超集也一定是 infrequent 的。

## Apriori 算法总结

- **核心思想**：
  - 基于 **Downward Closure Property**（向下封闭性质）：任意 frequent itemset 的所有子集也一定是 frequent 的。
  - 通过逐层生成候选项集（candidate itemsets）并筛选频繁项集（frequent itemsets），减少搜索空间。

- **算法步骤**：
  1. **生成频繁 1-项集**：
     - 扫描数据库，计算每个单项的支持度（support），筛选出频繁 1-项集。
  2. **生成候选 k-项集**：
     - 通过频繁 (k-1)-项集的自连接（self-join）生成候选 k-项集。
     - 剪枝（pruning）：移除包含 infrequent 子集的候选项集。
  3. **筛选频繁 k-项集**：
     - 扫描数据库，计算候选项集的支持度，筛选出频繁 k-项集。
  4. **重复步骤 2 和 3**，直到无法生成新的频繁项集。
  5. **生成关联规则**：
     - 对每个频繁项集生成所有可能的规则（rules），并计算置信度（confidence）。
     - 筛选出满足最小置信度阈值的规则。

- **优点**：
  - 简单易懂，基于直观的频繁项集生成过程。
  - 利用剪枝策略显著减少候选项集数量。

- **缺点**：
  - 需要多次扫描数据库，计算开销较大。
  - 候选项集数量可能呈指数增长，尤其在数据稠密或最小支持度较低时。

- **改进方法**：
  - **FP-Growth**：通过构建 FP 树避免候选项集生成。
  - **Eclat**：使用垂直数据格式加速计算。

## 实例说明

以下是一个具体的例子，展示如何使用 Association Rule：

- **场景**：超市的市场篮子分析
  - 数据集包含顾客的购物记录，每条记录是顾客一次购物中购买的商品集合。
  - 目标：发现商品之间的关联关系，以优化商品摆放和促销策略。

- **步骤**：
  1. **生成频繁项集**：
     - 假设最小支持度阈值（minsup）为 50%。
     - 数据示例：
       ```
       Transaction ID | Items Bought
       1             | {Milk, Bread, Butter}
       2             | {Milk, Bread}
       3             | {Milk, Butter}
       4             | {Bread, Butter}
       5             | {Milk, Bread, Butter}
       ```
  

### 规则 $Milk \rightarrow Bread$
1. **支持度计算**：
   - $sup(Milk \rightarrow Bread) = \frac{\text{包含 } \{Milk, Bread\} \text{ 的 transaction 数量}}{\text{总 transaction 数量}}$
   - 包含 $\{Milk, Bread\}$ 的 transactions 是 {1, 2, 5}，数量为 3。
   - 总 transaction 数量为 5。
   - $sup(Milk \rightarrow Bread) = \frac{3}{5} = 60\%$

2. **置信度计算**：
   - $conf(Milk \rightarrow Bread) = \frac{sup(Milk \cup Bread)}{sup(Milk)}$
   - $sup(Milk) = \frac{\text{包含 } \{Milk\} \text{ 的 transaction 数量}}{\text{总 transaction 数量}} = \frac{4}{5} = 80\%$
   - $conf(Milk \rightarrow Bread) = \frac{60\%}{80\%} = 75\%$

### 规则 $Milk, Bread \rightarrow Butter$
1. **支持度计算**：
   - $sup(Milk, Bread \rightarrow Butter) = \frac{\text{包含 } \{Milk, Bread, Butter\} \text{ 的 transaction 数量}}{\text{总 transaction 数量}}$
   - 包含 $\{Milk, Bread, Butter\}$ 的 transactions 是 {1, 5}，数量为 2。
   - 总 transaction 数量为 5。
   - $sup(Milk, Bread \rightarrow Butter) = \frac{2}{5} = 40\%$

2. **置信度计算**：
   - $conf(Milk, Bread \rightarrow Butter) = \frac{sup(Milk \cup Bread \cup Butter)}{sup(Milk \cup Bread)}$
   - $sup(Milk \cup Bread) = \frac{\text{包含 } \{Milk, Bread\} \text{ 的 transaction 数量}}{\text{总 transaction 数量}} = \frac{3}{5} = 60\%$
   - $conf(Milk, Bread \rightarrow Butter) = \frac{40\%}{60\%} = 66.7\%$

- 规则 $Milk \rightarrow Bread$ 的支持度为 60%，置信度为 75%。
- 规则 $Milk, Bread \rightarrow Butter$ 的支持度为 40%，置信度为 66.7%。

## Lift 公式定义与使用

- **定义**：
  - Lift 衡量两个事件（itemsets）之间的相关性，表示它们是否独立或存在正/负相关关系。
  - 公式：
    $$
    \text{Lift}(X \rightarrow Y) = \frac{P(X \cup Y)}{P(X) \cdot P(Y)} = \frac{conf(X \rightarrow Y)}{P(Y)}
    $$
  - 解释：
    - 如果 $\text{Lift}(X \rightarrow Y) = 1$，则 X 和 Y 是独立的。
    - 如果 $\text{Lift}(X \rightarrow Y) > 1$，则 X 和 Y 是正相关的。
    - 如果 $\text{Lift}(X \rightarrow Y) < 1$，则 X 和 Y 是负相关的。

## Chi-Square 定义与使用

- **定义**：
  - Chi-Square Test（卡方检验）是一种统计方法，用于检验两个变量之间是否存在显著的相关性。
  - 通过比较观测值（Observed Value, $O$）与期望值（Expected Value, $E$）的差异，判断变量是否独立。

- **公式**：
  $$
  \chi^2 = \sum \frac{(O - E)^2}{E}
  $$
  - $O$：观测值。
  - $E$：期望值，通常根据独立假设计算：
- **期望值 (Expected Value) 的计算**：
  1. **独立性假设**：
     - 假设两个变量 $A$ 和 $B$ 是独立的，则联合概率 $P(A \cup B)$ 可以表示为边缘概率的乘积：
       $$
       P(A \cup B) = P(A) \cdot P(B)
       $$
   2. 展开
       $$
       \frac{count(A \cup B)}{Total} = \frac{count(A)}{Total} \cdot \frac{count(B)}{Total}
       $$
       $$
       E= count(A \cup B) = \frac{count(A) \cdot count(B)}{Total}
       $$

  - 期望值的计算基于独立性假设，反映了在变量独立情况下的理论频数。
  - 通过比较观测值与期望值的差异，可以判断变量之间是否存在显著的相关性。
- **解释**：
  - 如果 $\chi^2 = 0$，表示观测值与期望值完全一致，变量独立。
  - 如果 $\chi^2 > 0$，表示观测值与期望值存在差异，变量可能相关。
  - 需要结合自由度（Degrees of Freedom, $df$）和显著性水平（Significance Level, $\alpha$）查表判断是否显著。
- **示例**：
  - 假设有以下数据：
    ```
    |            | Buy B | Not Buy B | Total |
    |------------|-------|-----------|-------|
    | Buy A      | 40    | 10        | 50    |
    | Not Buy A  | 60    | 90        | 150   |
    | Total      | 100   | 100       | 200   |
    ```
  - 计算期望值：
    $$
    E(\text{Buy A, Buy B}) = \frac{50 \times 100}{200} = 25
    $$
  - 计算 $\chi^2$：
    $$
    \chi^2 = \frac{(40 - 25)^2}{25} + \frac{(10 - 25)^2}{25} + \frac{(60 - 75)^2}{75} + \frac{(90 - 75)^2}{75} = 20
    $$
  - 根据自由度 $df = (2-1)(2-1) = 1$ 和显著性水平 $\alpha = 0.05$ 查表，判断是否显著相关。
  

- **与 Lift 的区别**：
  | **特性**            | **Chi-Square Test**                          | **Lift**                                |
  |---------------------|---------------------------------------------|-----------------------------------------|
  | **目的**            | 检验变量是否显著相关                       | 衡量变量之间的相关性强度               |
  | **结果范围**        | $\chi^2 \geq 0$                             | $0 < \text{Lift} < \infty$             |
  | **是否方向敏感**    | 不敏感，只判断是否相关                     | 敏感，可区分正相关和负相关             |
  | **适用场景**        | 需要显著性检验的场景                       | 需要衡量相关性强度的场景               |
  | **对 Null Transactions** | 可能受影响                              | 可能受影响                              |

- **使用场景**：
  - **Chi-Square Test**：
    - 用于判断两个变量是否显著相关，例如商品 A 和商品 B 是否存在购买关联。
    - 在医疗数据中，判断某种症状与疾病是否相关。
  - **Lift**：
    - 用于衡量相关性强度，例如商品 A 和商品 B 的购买关联强度。
    - 在推荐系统中，用于评估推荐规则的有效性。





