# Introduction
Molecular property prediction is essential for drug discovery in order to help researchers screen novel compounds. Neural networks, especially transformer models, are nowadays widely employed for this task by representing compounds as SMILES (Simplified Molecular Input Line Entry System) strings. Various research groups have written transformer architectures for predicting properties such as solubility and lipophilicity, but accurately predicting continuous values remains a challenge. Accurate prediction requires both pretraining on large chemical datasets and fine-tuning on a specific task. \\

In our work, we compared different fine-tuning approaches ansd selection strategies with [large-scale chemical language model](https://huggingface.co/ibm-research/MoLFormer-XL-both-10pct) for the prediction of lipophilicity. We evaluated these methods on the lipophilicty dataset of [MoleculeNet](https://moleculenet.org/datasets-1) and compared their effectiveness and identified the most promising strategy. This report outlines the fine-tuning and data selection techniques and a performance analysis of each approach.

---

# Methods  

Here, we fine-tune MoLFormer-XL, a pre-trained chemical language model, for lipophilicity prediction. Two architectures were explored: MoLFormerWithRegressionHead, which directly fine-tunes a regression head, and MoLFormerMLMWithRegressionHead, which undergoes unsupervised masked language modeling (MLM) fine-tuning before regression. To improve model interpretability, influence functions were applied to estimate how individual training points affect predictions, using Hessian Vector Products (HVP) to approximate the Hessian inverse efficiently. Data splitting was performed using Butina clustering instead of random sampling to ensure diverse molecular representations in training, validation, and test sets, leveraging Tanimoto similarity for cluster assignments. Additionally, three Parameter-Efficient Fine-Tuning (PEFT) methods were tested: 
- BitFit, which fine-tunes only bias terms.
- LoRA, which optimizes low-rank decompositions of weight updates.
- IA3, which introduces learned scaling vectors for key, value, and feedforward layers while freezing pre-trained weights.
All methods were trained with a learning rate of 0.0001, batch size of 128, weight decay of \(10^{-5}\), with PEFT methods running for 50 epochs and Butina clustering for 30 epochs.

---

# Results

This study explored different fine-tuning strategies for MoLFormer-XL in lipophilicity prediction. Direct regression fine-tuning used the AdamW optimizer with MSE loss, but results showed potential overfitting, as validation loss plateaued while the R² score stagnated. To enhance model understanding of SMILES, unsupervised Masked Language Modeling (MLM) fine-tuning was applied before regression, leading to improved R² scores and lower test loss compared to direct regression. Influence functions were used to identify high-impact training points, revealing a trend where higher lipophilicity values had greater influence, likely due to data imbalance. Adding these influential points to the training set further improved performance, achieving an R² score of 0.73 and test loss of 0.39. The Butina clustering method revealed structural patterns within the dataset, but model performance stagnated after 13 epochs, resulting in an R² score of 0.44. The influence-based approach, which incorporated additional high-impact training points, showed higher performance, though information leakage may have played a role. Among Parameter-Efficient Fine-Tuning (PEFT) methods, LoRA outperformed BitFit and IA3, achieving an R² of 0.697 with stable loss reduction and well-aligned predictions. BitFit performed poorly (R² = 0.4987), while IA3 failed to capture underlying patterns (R² = 0.0622). Overall, the combination of MLM pre-training, influence-based data selection, and LoRA yielded the best results for fine-tuning MoLFormer-XL.
