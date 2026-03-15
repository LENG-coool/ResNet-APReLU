# ResNet-APReLU: Enabling Activation Functions to Dynamically Adapt to Vibration Signals

## Preface

In intelligent industrial maintenance, fault diagnosis based on vibration signals and deep learning has become mainstream. In real-world applications, however, two challenges are common:

- For the same fault type, signal characteristics can vary significantly under different rotational speeds or loads (large intra-class variance).
- For different fault types, signal patterns can be highly similar under certain operating conditions (strong inter-class similarity).

Conventional CNNs or ResNets typically use fixed activation functions (such as ReLU or Leaky ReLU), applying the same nonlinear transformation to all inputs. Under complex operating conditions, this fixed strategy often struggles to balance robustness and discriminability.

This article introduces a paper published in *IEEE Transactions on Industrial Electronics (TIE)*: *Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis*. The study proposes APReLU, an adaptive activation function that equips residual networks with dynamic nonlinear modeling capability tailored to both individual samples and operating conditions.

<p align="center">
	<img src="/fig1.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Comparison of vibration signal waveforms under different rotational speeds and loads</p>

Traditional methods use a uniform nonlinear mapping for all signals, making it difficult to effectively aggregate same-class samples across multiple operating conditions and to sufficiently separate different classes. This directly limits the discriminative power of learned features.

## 1. Adaptively Parametric Rectifier Linear Unit (APReLU)

To address the above issues, the authors propose APReLU (Adaptively Parametric Rectifier Linear Units).

### 1.1 Core Idea

APReLU no longer relies on a fixed activation formula. Instead, it uses an internal subnetwork to dynamically generate the negative slope for the current input, enabling the activation function to vary with each sample.

<p align="center">
	<img src="/fig3(a).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Internal structure of the APReLU activation function</p>

### 1.2 Workflow

- **Feature aggregation**: Apply global average pooling (GAP) to the input feature map to extract global statistical information.
- **Nonlinear transformation**: Use two fully connected (FC) layers and a BN layer to learn the "feature-to-slope" mapping.
- **Dynamic slope generation**: Output a sample-adaptive negative slope through Sigmoid or a specific mapping function.

## 2. ResNet-APReLU Algorithm Architecture

The authors integrate APReLU into a deep residual network (ResNet), forming ResNet-APReLU:

- **Role of ResNet**: Mitigates gradient-related training issues in deep networks and supports deeper feature extraction.
- **Role of APReLU**: Introduces adaptive activation capability in each residual block, enabling the network to jointly learn both "fault categories" and the "optimal nonlinear transformation conditioned on operating conditions."

<p align="center">
	<img src="/fig3(c).png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">Overall architecture of ResNet-APReLU</p>

## 3. Experimental Validation

Experiments are conducted on a planetary gearbox dataset covering:

- 8 health states
- 3 rotational speeds
- 3 load levels

### 3.1 Accuracy Results

Under different noise conditions (SNR = 5 dB, 3 dB, and 1 dB), ResNet-APReLU outperforms methods using ReLU, PReLU, and others

### 3.2 Feature Visualization (t-SNE)


<p align="center">
	<img src="/fig5.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">t-SNE feature visualization comparison under a ConvNet architecture with different activation functions</p>

<p align="center">
	<img src="/fig6.png" style="width: 100%; margin: 0 auto; display: block;">
</p>
<p align="center" style="color: grey">t-SNE visualization of features extracted by ResNet-APReLU</p>

From the t-SNE results, the features learned by ResNet-APReLU form more compact clusters with clearer class boundaries and almost no overlap among different fault states.

## Paper Information

- **Title**: Deep Residual Networks With Adaptively Parametric Rectifier Linear Units for Fault Diagnosis
- **Journal**: IEEE Transactions on Industrial Electronics (TIE)
- **DOI**: 10.1109/TIE.2020.2972458
- **Link**: [https://ieeexplore.ieee.org/document/8998530](https://ieeexplore.ieee.org/document/8998530)
