# PureVolatility
复刻东吴证券《“波动率选股因子”系列研究（一）：寻找特质波动率中的纯真信息——剔除跨期截面相关性的纯真波动率因子》

## 目录

- [背景](#背景)
- [样本空间基本参数设置](#样本空间基本参数设置)
- [传统特质波动率因子的构造和评价](#传统特质波动率因子的构造和回测)
- [正交换手率以后的特质波动率因子的构造与评价](#正交换手率以后的特质波动率因子的构造与评价)
- [剔除跨期截面相关性的特质波动率因子的构造与评价](#剔除跨期截面相关性的特质波动率因子的构造与评价)
- [对特质波动率因子和本文复刻方法的思考](#对特质波动率因子和本文复刻方法的思考)
- [项目负责人](#项目负责人)

## 背景

### 选择复现这篇研报的原因
学术界研究特质波动率因子的原因在于Ang等人提出的特质波动率异象：有效市场假说和经典的多因子模型认为特质波动率与资产收益正相关，但是由于定价效率和套利限制等问题的存在，特质波动率带来的风险往往不能及时低被准确定价，
于是出现了特质波动率越小资产收益越大的异象。

自己对特质波动率的探究起源于在本科老师带领下探究特质波动率与套利限制的关系。当时的项目是运用Fama-French三因子和五因子模型以及GARCH模型构造股票月度特质波动率，然后运用添加交互项的滚动回归模型探究套利限制对特质波动率
的线性和非线性影响。在这个契机下，我选择复现这一篇特质波动率因子因子面板上自相关对特质波动率因子的影响的研报，加深我对特质波动率因子的理解

### 影响特质波动率异象的因素（不完全）
- 套利限制：有一些存在套利限制的股票，由于无法被充分套利，特质波动率异象无法被消除。
- 市场交易行为：由于市场参与者的有限注意力，存在特质波动率异象的股票很难及时被发现并套利（即市场定价效率不充分）；有时候由于市场震荡带来的噪声也会带来特质波动率异象。因此学术界普遍认为特质波动率因子与换手率因子相关。
- 公司基本面因素：公司的新闻或者是基本面数据带来的特质风险

## 样本空间基本参数设置
- 时间区间：2018年1月1日至2022年12月31日
- 样本：时间区间内全体A股，剔除ST、*ST，一字板股票
- 对因子缺失值的处理：直接剔除样本
- 回归模型回溯期：过去20日

## 传统特质波动率因子的构造和评价
### 构造方法
- 用Fama-French三因子模型滚动20日回归，求模型残差的标准差作为当日的特质波动率因子值
- 由于A股市场当中每一个样本都具有个体固定效应和截面异方差问题，因此需要选用异方差稳健标准误来缓解异方差问题
- 稳健性检验：以t+1期的收益率数据作为因变量对t期的三因子滚动20日回归（即假设下一日收益率为当日的预期收益率）

## 正交换手率以后的特质波动率因子的构造与评价
- 用传统特质波动率因子对过去20日换手率回归，取残差作为剔除换手率因子的特质波动率因子

## 剔除跨期截面相关性的特质波动率因子的构造与评价
若因子具有较强的跨期截面相关性，则会导致我们在t 时刻选股时，虽然表面上只参考
了t-1 时刻的因子值，却不可避免地连带使用了t-2 时刻、t-3 时刻、甚至t-4 时刻的因子
信息，而这些早期的因子值，无疑被多次重复使用了。这就意味着我们每一期获得的因
子信息，其实都不够纯净
### 确定特质波动率因子自相关的阶数
- 由于机能限制，不得不在定阶方面进行简化：将每个截面上的特质波动率等权加总，作为当日的市场特质波动率。计算市场特质波动率序列的自相关系数，发现序列在30阶有显著的自相关。于是暂定自回归的滞后阶数为30阶
- 构造正交换手率之后的特质波动率因子滞后30阶的自回归模型，取模型残差作为剔除自相关性的特质波动率因子


## 对特质波动率因子和本文复刻方法的思考
- 特质波动率异象的三个来源（有学术文献支撑）：套利限制、换手率（交易流动性）和财务基本面事件
- 在使用特质波动率因子套利的时候，需要仔细思考策略的逻辑——想赚哪一个来源的alpha：财务基本面？套利限制？然后仔细的排除其他影响特质
波动率因子的变量，比如本研报当中排除的换手率和序列相关性。只有精细地排除其他变量的干扰，才能让输入模型的数据和模型更直观的反映策略的逻辑
从而用简单的模型和思路达到最好的效果

### 接下来对特质波动率因子的改进
- 寻找一个更合适的模型来刻画这个序列相关性，排除不平稳序列和高阶自相关对特质波动率的干扰（考虑模型的garch残差？或者门槛自回归模型？）
- 寻找正交其他噪声来源的变量
- 正交的时候改用rolling OLS的残差，现在用全序列的OLS会引入未来信息
- 如果接着采用自回归类的时间序列模型的话，需要对滞后参数进行调整


### 对回测的改进
- 由于设备机能限制，无法运行全市场的事件驱动回测框架进行回测，接下来会考虑去工位借助云资源完善这个回测框架
- 对调仓方法的改进：目前只是简单做了买入并持有五年的回测，而且还是基于五年内平均的因子大小分组，而不是t0的调仓。未来需要设置为周频或者月频进行测试（依据：如果策略的alpha来源是基本面的话，基于2020-2023的数据和中信建投的研报，事件效应一般可以持续至少一周）


## 项目负责人
陈瑭澳222040009 Github：algo23TangaoChen


