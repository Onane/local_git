# 模型评估与选择
#### 评估方法
-  留出法
   - 划分训练集与测试集，注意数据分布保持一致，常用比例7:3、8:2
- 交叉验证法
   - 常称 k折交叉验证
   - 当k=m时，得到特例 留一法
- 自助法
  - 有放回采样，适合小数据集
  - lim|x->∞  `(1-1/m)**m` -->1/e ≈ 0.368
  - 改变了数据集的分布，会引入估计偏差
#### 性能度量
**衡量模型泛化能力的评价标准**
- 回归
  - 均方误差 mean squared error
  - 1/m Σ (y-y`)²
- 分类
  - 错误率
    - 分类错误的样本数占样本总数的比例
    - 1/m ∏ (y!=y`)
  - 精度
    - 分类正确的样本数占样本总数的比例
    - 1/m ∏ (y=y`)

混淆矩阵

真实情况||预测结果
-|-|-
-|正例|反例
正例|TP(真正例)|FN(假反例)
反例|FP(假正例)|TN(真反例)

- 查准率
  - 预测为正的样本中有多少是正样本
  - P=TP/(TP+FP)
- 查全率
  - 真正的正样本中有多少被预测出来
  - R=TP/(TP+FN)

P-R曲线
- 将预测概率排序，x轴查全率，y轴查准率
- 平衡点BEP(Break-Event Point)
  - P=R时的取值
- Fβ-Score
  - (1+β²)*P*R/((β²*P)+R)
  - β大于1时查全率影响更大，β小于1时查准率影响更大
- F1度量(F1-Score，β=1) 
  - 2*P*R/(P+R)
  - 2*TP/(样本数+TP-TN)

宏
- 多个混淆矩阵分别求出查准率等后再求平均

微
- 多个混淆矩阵先平均再求查准率等
  
ROC(受试者工作特征，Receiver Operating Characteristic)
- TPR(真正例率，y轴)
  - TP/(TP+FN)
- FPR(假正例率，x轴)
  - FP/(TN+FP)
- FNR(假反利率)
  - 1-TPR
- AUC
  - ROC曲线下面积

##### 代价敏感错误率与代价曲线

**为权衡不同类型错误所造成的不同损失，可为错误赋予'非均等代价'(unequal cost)**

二分类代价矩阵

真实类别|-|预测类别
-|-|-
-|第0类|第1类
第0类|0|cost01
第1类|cost10|0

错误率
- ``1/m(Σ ∏(y!=y`)*cost01+Σ ∏(y!=y`)*cost10)``

代价曲线
- x轴是取值为[0,1]的正例概率代价
  - p*cost01/(p*cost01+(1-p)*cost10)
  - p是样例为正例的概率
- y轴是取值为[0,1]的归一化代价
  - (FNR * p * cost01+FPR * (1-p) * cost10) / (p * cost01+(1-p) * cost10)
- 取ROC曲线上的一个点，绘制(0,FPR)->(1,FNR)线段下的面积即表示了该条件下的期望总体代价
- 将ROC上每一个点都绘制后，所有线段的下界围成的面积即为在所有条件下学习器的期望总体代价

#### 比较检验
##### 假设检验
***思想：若在测试集上观察到学习器A比B好，则A的泛化性能是否在统计意义上优于B，以及这个结论的把握有多大***

**单个学习器**

根据测试错误率估推出泛化错误率的分布
- 泛化错误率为ε的学习器被测得测试错误率为ε^的概率
  - P(ε^;ε)=``(m,ε^*m)*ε**(ε^m) * (1-ε)**(m-ε^*m)``
  - 解∂P(ε^;ε)/∂ε = 0 可知，P(ε^;ε)在ε=ε^时最大，符合二项分布

二项检验(binomial test)
- 已知：测试错误率为0.3
- 假设：泛化错误率是否不大于0.3
- 在α的显著度下，假设'ε<=ε0'不能被拒绝，即能以1-α的置信度认为，学习器的泛化错误率不大于ε0，否则该假设可被拒绝，即在α的显著度下认为学习器的泛化错误率大于ε0

t检验(t-test)
- 通过k折交叉验证，得到k个测试错误率，可认为其为泛化错误率的独立采样
- 变量t= k**1/2 *(μ-ε0)/σ  服从自由度为k-1的t分布
- 对假设'μ=ε0'和显著度α，可计算出当测试错误率均值为ε0时，在1-α概率内能观测到的最大错误率，即临界值
- 若|μ-ε0|位于临界值范围[-α/2,α/2]内，则不能拒绝假设'μ=ε0'，即可认为泛化错误率为ε0，置信度为1-α；否则可拒绝该假设，即在该显著度下可认为泛化错误率与ε0有显著不同

双边t检验的常用临界值

α|-|-|k|-|-|
-|-|-|-|-|-|
-|2|5|10|20|30
0.05|12.706|2.776|2.262|2.093|2.045
0.10|6.314|2.132|1.833|1.729|1.699

**两个学习器**

基本思想：若两个学习器的性能相同，则他们使用相同的训练集得到的训练错误率也应相同

交叉验证t检验
- 根据两个学习器分别对相同训练集得出的测试错误率求差值，根据差值来对“学习器A与B性能相同”这个假设做t检验。
- 为缓解测试错误率的非独立性，可以采用‘5 X 2 交叉验证’,即5次2折交叉验证，μ=第一次2折的错误率差值的均值，σ则需取每一次2折的错误率差值去计算。
- 变量服从自由度为5的t分布，其双边检验的临界值tα/2,5 当α=0.05时为2.5706，α=0.1时为2.0150

McNemar检验
- 列连表

算法A|-|算法B
-|-|-
-|正确|错误
正确|e00|e01
错误|e10|e11

- 假设两学习器性能相同，则e01=e10，变量|e01-e10|应服从正态分布，且均值为1，方差为e01+e10。
- 因此变量(|e01-e10|-1)**2 / (e01+e10)服从自由度为1的卡方分布，即标准正态分布变量的平方。给定显著度α，当以上变量小于临界值α卡方时，不能拒绝原假设。其临界值当α=0.05时为3.8415，α=0.1时为2.7055

**多个学习器**

Friedman(F检验)检验与Nemenyi后续检验
- 如遇多个学习器性能比较，一是两两比较，另一种即是基于算法排序的Friedman检验
  - 原始Friedman检验：假设“所有算法的性能相同”，对每个学习器在每个训练集上的测试性能排序，然后计算平均序值,在N个数据集上比较k个算法，ri表示第i个算法的平均序值，不考虑平分序值情况，则ri服从正态分布
  - 均值=(k+1)/2，方差=(k**2-1)/12，在k和N都较大时，变量 Γχ=12N * (Σ(ri **2 -k(k+1)(k+1)/4))/k(k+1)  服从自由度为k-1的卡方分布
  - 现多使用变量ΓF=(N-1)Γχ / (N(k-1)-Γχ),ΓF服从自由度为k-1和(k-1)(N-1)的F分布,F检验的常用临界值可查看西瓜书p43
- 假设被拒绝，需后续检验进一步区分算法，常用的有Nemenyi后续检验
  - 计算出平均序值差别的临界值域CD=qα*(k(k+1)/6N)**1/2
  - 若两个算法的平均序值之差超出了临界值域CD，则以相应的置信度拒绝“两个算法性能相同”
  - Friedman检验图

#### 偏差与方差

**“偏差-方差分解”(bias-variance decomposition)是解释学习算法泛化性能的一种重要工具**

***思想：试图对学习算法的期望泛化错误率进行拆解***

为便于讨论，假定噪声期望为0，通过多项式展开合并，可对算法的期望泛化误差进行分解
- 泛化误差=偏差+方差+噪声
- 偏差
  - 度量了学习算法的期望预测与真实结果的偏离程度，即刻画了算法本身的拟合能力
- 方差
  - 度量了同样大小的数据集的变动所导致的学习性能的变化，即刻画了数据扰动所造成的影响
- 噪声
  - 表达了在当前任务上任何学习算法所能达到的期望泛化误差的下界，即刻画了学习问题本身的难度
- 偏差-方差冲突：偏差-方差窘境(bias-variance dilemma)
  - 训练不足时，学习器拟合能力不够，数据扰动不足以使学习器产生显著变化，此时偏差主导泛化错误率
  - 训练充足时，学习器学到了数据扰动，此时方差主导泛化错误率
  - 若训练集自身的、非全局的特征被学习，则发生过拟合