# Introduction
Molecular property prediction is essential for drug discovery in order to help researchers screen novel compounds. Neural networks, especially transformer models, are nowadays widely employed for this task by representing compounds as SMILES (Simplified Molecular Input Line Entry System) strings. Various research groups have written transformer architectures for predicting properties such as solubility and lipophilicity, but accurately predicting continuous values remains a challenge. Accurate prediction requires both pretraining on large chemical datasets and fine-tuning on a specific task. \\

In our work, we compared different fine-tuning approaches ansd selection strategies with [large-scale chemical language model](https://huggingface.co/ibm-research/MoLFormer-XL-both-10pct) for the prediction of lipophilicity. We evaluated these methods on the lipophilicty dataset of [MoleculeNet](https://moleculenet.org/datasets-1) and compared their effectiveness and identified the most promising strategy. This report outlines the fine-tuning and data selection techniques and a performance analysis of each approach.

Here’s your summary with LaTeX equations reformatted for a Markdown-friendly environment:  

---

### Methods  

This study fine-tunes MoLFormer-XL, a pre-trained chemical language model, for lipophilicity prediction. Two architectures were explored: MoLFormerWithRegressionHead, which directly fine-tunes a regression head, and MoLFormerMLMWithRegressionHead, which undergoes unsupervised masked language modeling (MLM) fine-tuning before regression. To improve model interpretability, influence functions were applied to estimate how individual training points affect predictions, using Hessian Vector Products (HVP) to approximate the Hessian inverse efficiently. Data splitting was performed using Butina clustering instead of random sampling to ensure diverse molecular representations in training, validation, and test sets, leveraging Tanimoto similarity for cluster assignments. Additionally, three Parameter-Efficient Fine-Tuning (PEFT) methods were tested: (1) BitFit, which fine-tunes only bias terms, (2) LoRA, which optimizes low-rank decompositions of weight updates, and (3) IA3, which introduces learned scaling vectors for key, value, and feedforward layers while freezing pre-trained weights. All methods were trained with a learning rate of 0.0001, batch size of 128, weight decay of \(10^{-5}\), with PEFT methods running for 50 epochs and Butina clustering for 30 epochs.  

#### Reformatted Equations  

- Influence function:  
  \[
  \mathcal{I}_{\text{up,params}}(z) = -H_{\hat{\theta}}^{-1} \nabla_{\theta} L(z, \hat{\theta})
  \]  
  where \( H_{\hat{\theta}} \) is the Hessian matrix.  

- Hessian approximation:  
  \[
  \hat{H}\theta = \frac{1}{n} \sum_{i=1}^{n} \nabla^2_\theta L(z_i, \hat{\theta})
  \]  

- Tanimoto similarity:  
  \[
  T(A, B) = \frac{|A \cap B|}{|A| + |B| - |A \cap B|}
  \]  
  where \( A \) and \( B \) are Morgan fingerprints of molecules \( X \) and \( Y \).
