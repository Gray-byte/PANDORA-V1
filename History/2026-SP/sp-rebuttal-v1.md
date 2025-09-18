We thank the reviewers for their feedback. Supplementary experiment settings follow the main text.

## Threat Model & Contributions

#### Review#A-Related Threat Models
We compare our threat model (Sec. 3) with clean-label poisoning. Both preserve labels, but classical attacks assume free poisoned sample insertion and parameter access [4]. CDA-AL requires high-uncertainty samples, with PANDORA effective parameter-free..

[4] 2018-NIPS-Targeted Clean-Label Poisoning Attacks on Neural Networks

#### Review#C-Related Work and Contributions
Prior work modifies targets [1][2] or assumes free poisoned insertion [3], overlooking strict filtering in sensitive domains. PANDORA uses a stricter model: samples must show high uncertainty, targets stay unmodified, and effectiveness measured over updates. To our knowledge, it is the first to show poisoning persistence, crucial for domains like long-term malware detection.

#### Review#D-Code Update
[PANDORA Repository](https://anonymous.4open.science/r/PANDORA-Submit-version-D368)

## Attack Method

#### Review#A-Attack Formalization
Symbols follow the main text. The PANDORA attacker’s objective is:

$$\max_{ \{ D_{poi}^{n} \}_{n=1}^{T} } \sum_{n=1}^{T} \mathbb{I}[ f_{\bm{\theta}_{n}}(x_{tar}) \neq y_{tar} ]$$

- $x_{tar}$: target sample with ground-truth label $y_{tar}$  
- $T$: number of drift periods  
- $\mathbb{I}[\cdot]$: indicator, 1 if true, else 0  
- $D_{poi}^{n}$: poisoning data at cycle $n$  
- $f_{\theta_{n}}$: victim model updated with $D_{poi}^{n}$  

Thus, the attacker injects $D_{poi}^{n}$ each cycle, producing $f_{\theta}$ misclassifying $x_{tar}$.

#### Review#A-Problem-Space Perturbations
Table 1 shows cheap problem-space perturbations; other strategies are also viable. Our supplementary tests (AndroZoo) with six perturbation strategies preserved Drebin features and achieved a 90% ASR, demonstrating PANDORA’s robustness.

#### Review#A-Concept Drift Direction Misguidance
Problem-space may fail without high-uncertainty seeds. Thus our feature-space method (Sec. 4.3.2) generates high-uncertainty samples via Shapley attribution, introducing new features that mislead the victim model about the drift direction.

#### Review#A-Motivation for SHAP
Ledda et al.’s method requires gradients and is inapplicable to CDA-AL. Our model assumes no parameter access. We propose SHAP-based uncertainty maximization using only predicted scores.

#### Review#D-Objects of Uncertainty Quantification
The victim collects raw data then extracts features for quantification. Thus, if attackers alter raw data in the problem space (e.g., by inserting redundant code) without affecting features, the collected raw poisoned samples retain the same uncertainty after feature extraction.

#### Review#D-Problem-space vs Feature-space
Assuming attacker knowledge of feature construction is justified, as recent clean-label threat models make the same assumption [5]. As studies stress AI security should not rely on obscurity [6], CDA-AL cannot depend on hidden feature construction.

[5] 2025-Clean-Label Graph Backdoor Attack in the Node Classification Task

[6] 2025-The Pitfalls of “Security by Obscurity” and What They Mean for Transparent AI

## Evaluation

#### Review#C-Dataset Usage
APIGraph, a real-world long-span dataset, is the main benchmark for attacks, defenses, and factor analysis (Tables 4, 5, 7; Figs. 2–7). Synthetic datasets (BODMAS, MNIST, SPAM) with shorter drifts test multi-target and defenses. AndroZoo, similar to APIGraph but with different drift intensities, for supplementary analysis (Sec. 5.3.3).

#### Review#A-Attack Effectiveness
Each target has a baseline misclassification duration, measured in drift cycles. By injecting poisoned samples, PANDORA extends this duration for >80% of targets.

#### Review#B-F1 Stability
Fig. 5 presents the multi-target results, with no F1 drop in Fig. 7. Though only FNR is plotted, F1 remains stable at 0.88.

#### Review#A-PANDORA on Future and Past Knowledge
PANDORA preserves future learning (test F1 high, Figs. 4, 6, 7) and retains prior knowledge (training F1 0.95 under all attacks).

#### Review#C&D-Incomplete Test Data
With 30%, 50%, or 70% of test data, success averages 75.6% (APIGraph, single-target). Poisoned samples use high-uncertainty benign seeds found without full distribution knowledge.

#### Review#A-Unknown Labeling Budget
Without budget knowledge, attackers generate as many poisoned samples as resources allow. In the Unaware setting (Fig. 7), success approaches 90%.

#### Review#A-Unknown Uncertainty Strategy
Without uncertainty estimates, PANDORA transfers via confidence approximations. On APIGraph, attacks on HCL and CADE with UNCER achieve 75% ASR, as different strategies give similar rankings for sample selection.

#### Review#A-Labels of Poisoned Seeds
As malware families may share correlations (e.g., similar exploits), using them risks exposing target-related knowledge. Hence, poisoned samples are drawn only from benign data.

#### Review#A-Low ASR for Fobus
PANDORA targets drift samples, so ASR depends on drift intensity. In Fobus, failed attacks had 2.5× more highly similar samples (cosine >0.9), mainly from non-target malware (weak drift), consistent with PANDORA’s assumption.

#### Review#D-CDA-AL Variants
We evaluated CDA-AL variants with major query strategies [7]—uncertainty-diversity, deep Bayesian, density-based. PANDORA stayed effective, averaging 67% ASR， 0.91 F1 with stealth.

[7] 2021-A Survey of Deep Active Learning

#### Review#D-Weakened Attacker Models
We tested weaker attacker models with simpler encoders/classifiers. On APIGraph, multi-target success remained 82%, showing PANDORA’s robustness under limited capabilities.

#### Review#B-AndroZoo Supplementary Analysis
On AndroZoo, the multi-target ASR averaged 81%, confirming PANDORA’s effectiveness, though lower than the 97.5% on APIGraph, indicating that drift intensity and feature extraction affect attack performance.