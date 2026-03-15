# ResNet-APReLU：让激活函数“动态适配”振动信号

## 前言

在工业智能运维领域，利用深度学习处理振动信号进行故障诊断已成为主流。然而，实际应用中常见两类挑战：

- 同一种故障在不同转速或负载下，信号特征差异显著（类内差异大）。
- 不同故障在某些工况下，信号表现高度相似（类间相似性强）。

传统 CNN 或 ResNet 通常采用固定激活函数（如 ReLU、Leaky ReLU），对所有输入执行相同的非线性变换。面对复杂工况时，这种固定策略往往难以兼顾鲁棒性与判别性。

本文介绍一篇发表于 *IEEE Transactions on Industrial Electronics (TIE)* 的论文：*Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis*。该研究提出了自适应激活函数 APReLU，为残差网络提供了面向样本与工况的动态非线性建模能力。

<p align="center">
  <img src="/fig1.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">不同转速/负载下的振动信号波形对比</p>

传统方法对所有信号使用一致非线性映射，难以将多工况下的同类样本有效聚合，也难以将异类样本充分分离，这会直接制约特征学习的判别能力。

## 1. 自适应参数化线性单元（APReLU）

为解决上述问题，作者提出 APReLU（Adaptively Parametric Rectifier Linear Units）。

### 1.1 核心思想

APReLU 不再依赖固定激活公式，而是通过内置子网络为当前输入动态生成负向斜率，实现“激活函数随样本而变”。

<p align="center">
  <img src="/fig3(a).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">APReLU 激活函数内部结构图</p>

### 1.2 工作流程

- **特征聚合**：对输入特征图进行全局平均池化（GAP），提取全局统计信息。
- **非线性转换**：通过两个全连接层（FC）与 BN 层学习“特征-斜率”映射关系。
- **动态斜率生成**：通过 Sigmoid 或特定映射输出样本自适应负向斜率。

## 2. ResNet-APReLU 算法架构

作者将 APReLU 集成到深层残差网络（ResNet）中，形成 ResNet-APReLU：

- **ResNet 的作用**：缓解深层网络训练中的梯度问题，支持更深层特征提取。
- **APReLU 的作用**：在每个残差块中引入自适应激活能力，使网络同时学习“故障类型”与“工况相关的最优非线性变换”。

<p align="center">
  <img src="/fig3(c).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">ResNet-APReLU 完整网络架构图</p>

## 3. 实验验证

研究在行星齿轮箱数据集上进行，覆盖：

- 8 种健康状态
- 3 种转速
- 3 种负载

### 3.1 准确率结果

在不同噪声条件（SNR = 5 dB、3 dB、1 dB）下，ResNet-APReLU 相比 ReLU、PReLU 等方法表现更优：

- ResNet-APReLU 平均测试准确率达到 **97.51%**。
- 相比 PReLU 版 ResNet，准确率提升约 **3.98%**。
- 相比 ReLU 版 ResNet，准确率提升约 **5.27%**。

### 3.2 特征可视化（t-SNE）


<p align="center">
  <img src="/fig5.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">不同激活函数在 ConvNet 架构下的 t-SNE 特征可视化对比</p>

<p align="center">
  <img src="/fig6.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">ResNet-APReLU 提取特征的 t-SNE 可视化</p>

从 t-SNE 可视化结果可见，ResNet-APReLU 学习到的特征聚类更紧凑、类别边界更清晰，不同故障状态之间几乎无重叠。

## 论文信息

- **标题**：Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis
- **期刊**：IEEE Transactions on Industrial Electronics (TIE)
- **DOI**：10.1109/TIE.2020.2972458
- **链接**：(https://ieeexplore.ieee.org/document/8998530)[https://ieeexplore.ieee.org/document/8998530]