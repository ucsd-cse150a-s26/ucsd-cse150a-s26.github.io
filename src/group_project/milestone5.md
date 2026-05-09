# Milestone: Hidden Markov Model Implementation

## Introduction

Important instructions: Move your first project code to a branch titled 'first_project'. Then, proceed by working on the main branch for your final project.

In this milestone you will continue working on your main branch, and focus on working on your second model. You will also need to evaluate this model compare your performance. Include all responses to the below on your main branch in the README.md file.

## Overview

In the previous milestone, you tested for independence, conditionality, and conditional independence across your chosen dataset. You populated conditional probability tables (CPTs) using maximum likelihood estimation (MLE) and drafted a Bayesian network (BN) that encodes the dependency structure among your variables. Here we build on that foundation by introducing a related but structurally distinct class of probabilistic graphical model: the Hidden Markov Model (HMM).

A Bayesian network encodes conditional dependencies among variables in a directed acyclic graph (DAG), but it does not assume any temporal or sequential ordering among observations. An HMM, by contrast, is fundamentally a sequential model. It posits a series of hidden (latent) states that evolve over discrete time steps according to a transition probability matrix, where at each step the hidden state generates an observable output according to an emission probability distribution. Despite this structural difference, both models rely on the same probabilistic machinery you have already practiced: conditional probability and the chain rule. For the BN you used MLE for parameter estimation; for the HMM, because the hidden states are unobserved, parameter estimation is done via the EM algorithm.

The objective of this milestone is fourfold. First, you will identify one or more latent variables implied by the structure of your Bayesian network. Second, you will restructure your tabular dataset into a sequential format suitable for HMM training. Third, you will implement a basic discrete HMM whose parameters (transition matrix, emission matrix, and initial state distribution) are estimated via the EM algorithm. Fourth, you will perform inference on your fitted HMM using a method of your choice—e.g., the forward algorithm, the backward algorithm, the Viterbi algorithm, or another inference procedure—to compute quantities such as the likelihood of the observation sequence or the most likely hidden state sequence.

The previous milestone allowed flexibility in which model you implemented. For this milestone, you **MUST** implement an HMM for your data, as described above.

---

## Deliverables

For this milestone, each group will submit the following. Below is a detailed description of what to do and how to approach each deliverable.

1. **PEAS / Agent Analysis and Setup (Section 0) (2 pts):** Your PEAS description and task background (Section 0.1), plus your dataset exploration, variable overview with a figure, and explanation of variable interactions and model structure (Section 0.2).

2. **Latent variable identification (3 pts):** A written justification (minimum one paragraph) identifying the latent variable(s) in your dataset. Explain why the variable is latent, how it relates to the observed features, and which node(s) in your BN it corresponds to.

3. **Temporal data construction (4 pts):** A description and implementation of how you converted your tabular data into a sequential format. Include your ordering strategy, your discretization scheme, and a justification for both.

4. **HMM implementation and parameter estimation (8 pts):** implementation of a basic discrete HMM with EM-based parameter estimation. Your implementation should produce the estimated initial state distribution π, transition matrix A, and emission matrix B.

5. **Inference (7 pts):** Implement at least one inference method (e.g., forward, backward, or Viterbi) and use it to compute quantities such as observation-sequence likelihood or a decoded hidden state sequence.

6. **Evaluation and reflection (5 pts):** Evaluate the results (e.g., compare decoded states to ground truth if available, or use log-likelihood or another metric). Include a brief reflection (one to two paragraphs) comparing the HMM to your Bayesian network: what does the HMM capture that the BN does not, and what assumptions does the HMM impose that the BN does not require?

### Additional Notes (points to be subtracted)

Be sure to cite all work and all generative AI used in the process. You will not be deducted points for citing work and using these tools, but you may certainly be deducted points for not citing work!

**This milestone is worth: 30 points of 100 total project points**

Below is a detailed description of how to approach each deliverable.

---

## 0. PEAS / Agent Analysis and Setup (2 pts)

### 0.1 PEAS / Agent Analysis

Describe your agent in terms of PEAS and give a background of your task at hand. (You may reuse this from your project proposal. If you are doing a different model/dataset from your proposal, you must get approval first, and you should redefine your PEAS.)

- What problem are you solving?
- Why does probabilistic modeling make sense to tackle this problem?

### 0.2 Agent Setup, Data Preprocessing, Training Setup

Describe in detail the following points:

1. **Dataset exploration:** Give an exploration of your dataset, and highlight which variables are important. Give a brief overview of each variable and its role in your agent/model.
2. **Variable interactions and model structure:** Describe in detail how your variables interact with each other, and if your model fits a particular structure, explain why you chose that structure to model your agent. If it does not, further elaborate on why you chose that model based on the variables.

---

## 1. Latent Variables

### 1.1 Definition

A latent variable is a variable that is not directly observed in your dataset but whose existence is inferred from the statistical structure of the variables you can measure. Formally, if your dataset contains columns X₁, X₂, …, Xₙ, a latent variable Z has no corresponding column, yet it exerts a causal or generative influence on some subset of the Xᵢ. Its values must therefore be estimated (or decoded) from observed data rather than read directly from a table.

Consider a concrete analogy. You walk into a room and observe that several people are wearing coats, carrying umbrellas, and shaking water from their shoes. The variable "weather" does not appear anywhere in your observations, yet the pattern of evidence strongly implies a hidden state (e.g., "rainy"). Weather, in this scenario, is latent. It causally drives the things you observe, but it is absent from your measurements.

The key properties of a latent variable are as follows:

1. It is not directly recorded in the dataset (there is no column for it).
2. It has a causal or generative relationship with one or more observed variables.
3. Its value at any given observation must be inferred, and that inference carries uncertainty.

### 1.2 Latent Variables in the Context of HMMs

An HMM is constructed around the latent variable concept. The model assumes a sequence of hidden states Z₁, Z₂, …, Z_T that evolve over T time steps. At each step t, the hidden state Z_t emits an observation X_t that you can measure. The entire purpose of the model is to reason about the most likely sequence of hidden states given the sequence of observations. Because the hidden states are unobserved, parameters cannot be estimated by MLE directly; instead we use the EM algorithm to estimate parameters that make the observed sequence most probable under the assumed hidden-state structure.

### 1.3 Contrast with Observed Variables

An observed variable has a column in your dataset; you can read off its value for any row or time step. A latent variable has no such column. If you find yourself asking, "What generated these patterns in my data?" and the answer is a concept that is not explicitly measured, you have identified a candidate latent variable.

---

## 2. Identifying Latent Variables (3 pts)

### 2.1 From Your Data

There are four principal strategies for identifying latent variables from tabular data.

**Strategy 1: Unexplained correlation structure.** If several observed variables are correlated but share no direct causal link to one another, that pattern is a strong signal that a latent variable is driving them all. For example, if "customer complaint frequency," "return rate," and "negative review count" all covary across rows, a latent variable such as customer satisfaction may be generating all three. None of the three observed variables causes the others; they are all downstream effects of something unmeasured.

**Strategy 2: Clusters or regimes.** If your data appears to come from distinct groups or modes (e.g., two overlapping Gaussians, or rows that behave qualitatively differently), those groups may correspond to distinct values of a latent variable. Plotting distributions of your features and looking for multimodality is a practical first step.

**Strategy 3: The generative question.** Ask: "If I could see everything about the process that generated this data, what hidden process or state would I invoke?" This heuristic is the most broadly applicable. Medical data with symptoms suggests a latent disease state. Financial data with returns and volume suggests a latent market regime. Sensor readings from a machine suggest a latent operating condition (e.g., healthy, degrading, failing).

**Strategy 4: Conceptually real but unmeasurable quantities.** Variables such as mood, intent, health status, economic phase, or biological fitness are real and meaningful, but they often do not appear as a column in the dataset. If you can name such a concept and argue that it influences multiple observed columns, it is a candidate latent variable.

### 2.2 From Your Bayesian Network

This is considerably more direct, because the BN already encodes your assumptions about the generative structure. There are four indicators to look for.

**Indicator 1: Nodes with no corresponding column.** If your BN contains a node for a variable that does not exist as a column in your dataset, that node is latent by definition.

**Indicator 2: Root nodes or high-level parent nodes.** Nodes that sit near the top of the DAG, those with many children but few or no parents, often represent abstract, latent concepts. They are the causes that generate downstream observations.

**Indicator 3: Nodes you marginalize over.** If there is a node in your BN that you typically sum out when performing inference (because you never condition on it directly), that node is behaving as a latent variable.

**Indicator 4: Hidden common causes.** If two observed nodes are statistically dependent in your data but d-separated in your BN given all other observed nodes, a latent common cause may be missing from the graph. Conversely, if your BN explicitly includes a common parent that you do not observe, that parent is your latent variable.

**Mapping your BN to an HMM.** Examine your BN and ask which node(s) represent the state of the system. That node becomes the HMM hidden state. All nodes that are direct children of the state node (and that you actually measure) become your observations. The CPT of the state node given its previous value becomes your transition matrix, and the CPTs of the observation nodes given the state become your emission probabilities.

---

## 3. Creating Temporal Data from Tabular Data (5 pts)

A standard tabular dataset (rows as independent samples, columns as features) has no inherent sequence. HMMs, however, are fundamentally sequential models. Converting tabular data to a temporal format therefore requires imposing or discovering an ordering. Here we outline several strategies, each illustrated with one of the three toy datasets: California Housing, Iris, and Wine.

### 3.1 Strategy: Natural or Geographic Ordering

**Example: California Housing Dataset**

The California Housing dataset contains 20,640 samples with 8 features: `MedInc`, `HouseAge`, `AveRooms`, `AveBedrms`, `Population`, `AveOccup`, `Latitude`, and `Longitude`, plus a target variable `MedHouseVal`. Each row represents a census block group in California.

There is no explicit time column, but the geographic coordinates (`Latitude`, `Longitude`) provide a natural spatial ordering. One approach is to sort the data along a geographic transect, for example by `Latitude` from south to north. Under this ordering, each "time step" is the next census block moving northward, and the observation at each step is a vector of housing features.

The latent variable in this case might be **regional economic zone** (e.g., rural, suburban, urban-core, coastal-affluent). This variable is not measured directly, but it plausibly drives the co-occurrence patterns among median income, house age, average rooms, and median house value. A BN for this dataset might place economic zone as a parent node of `MedInc`, `HouseAge`, and `MedHouseVal`. In the HMM formulation, the economic zone becomes the hidden state, and the observed housing features become the emissions at each spatially ordered step.

To implement this:

1. Sort the dataset by `Latitude` (or by a combined spatial index such as a Hilbert curve).
2. Discretize continuous features into bins (e.g., `MedInc` into low/medium/high).
3. Define the number of hidden states K (e.g., K = 3 or K = 4 for economic zones).
4. Train the HMM on the resulting sequence.

### 3.2 Strategy: Categorical Grouping as Regime

**Example: Iris Dataset**

The Iris dataset contains 150 samples with 4 features: `SepalLength`, `SepalWidth`, `PetalLength`, and `PetalWidth`, plus a class label (`setosa`, `versicolor`, `virginica`). Each row represents a single flower measurement.

This dataset has no natural temporal ordering. However, the class label itself can serve as either a known or a latent state variable. If we treat the species label as latent (i.e., we remove it from the dataset and pretend we do not know which species each measurement belongs to), then the HMM's task is to infer species identity from the morphological measurements.

To create a sequence, one option is to sort the samples by a biologically meaningful axis, such as `PetalLength`, which increases monotonically from setosa through versicolor to virginica. Under this ordering, the sequence represents a gradient of increasing petal size, and the hidden state captures the species regime that generated each measurement.

The latent variable here is **species identity**. In the BN formulation, species is the parent node of all four morphological measurements. In the HMM, species becomes the hidden state with K = 3 states, and the discretized morphological features become the observations.

To implement this:

1. Remove the class label from the feature set.
2. Sort by `PetalLength` (or `PetalWidth`, or a principal component).
3. Discretize features into bins (e.g., short/medium/long for lengths, narrow/medium/wide for widths).
4. Define K = 3 hidden states corresponding to the (unknown) species.
5. Train the HMM and compare the decoded hidden state sequence against the true labels.

### 3.3 Strategy: Derived Ordering from Feature Gradients

**Example: Wine Dataset**

The Wine dataset contains 178 samples with 13 chemical features (e.g., `Alcohol`, `MalicAcid`, `Ash`, `Magnesium`, `Flavanoids`, `Proline`) and a class label indicating one of three cultivars. Each row represents a chemical analysis of a single wine sample.

As with Iris, there is no time column. However, chemical composition varies systematically with factors such as grape ripeness, fermentation duration, and terroir, all of which are latent. One can construct a temporal proxy by sorting samples along a chemical gradient. For instance, sorting by `Alcohol` content produces a sequence from lighter to heavier wines, and the hidden state at each step might represent the **cultivar** or, more interestingly, the **winemaking style** (e.g., light-bodied, medium, full-bodied) that generated the observed chemical profile.

Alternatively, if the data is treated as representing stages in a production process (e.g., samples taken at successive points during fermentation), the ordering becomes more naturally temporal. In practice, with the toy dataset, sorting by a dominant chemical feature is the most accessible approach.

To implement this:

1. Remove the class label.
2. Sort by `Alcohol` (or by the first principal component of the 13 features).
3. Discretize features into bins.
4. Define K = 3 hidden states.
5. Train the HMM and evaluate whether the decoded states recover cultivar identity.

### 3.4 Summary Table

| Dataset | Ordering Strategy | Latent Variable | Observations | Hidden States (K) |
|---|---|---|---|---|
| California Housing | Geographic (Latitude) | Regional economic zone | MedInc, HouseAge, AveRooms, etc. | 3–4 |
| Iris | Biological gradient (PetalLength) | Species identity | SepalLength, SepalWidth, etc. | 3 |
| Wine | Chemical gradient (Alcohol) | Cultivar / winemaking style | MalicAcid, Flavanoids, Proline, etc. | 3 |

### 3.5 When Temporal Conversion Is Not Appropriate

If your data has no ordering at all and no defensible proxy for one, an HMM is not the right model. HMMs are fundamentally sequential. In the absence of any sequence, a mixture model (e.g., Gaussian Mixture Model) may be more appropriate for latent variable discovery. For this milestone, however, you are expected to impose or discover a reasonable ordering and justify your choice.

---

## 4. HMM Implementation with EM (10 pts)

### 4.1 Model Components

A discrete HMM is defined by the following parameters:

- **State space:** A set of K hidden states s₁, s₂, …, s_K.
- **Observation space:** A set of M discrete observation symbols o₁, o₂, …, o_M.
- **Initial state distribution π:** A vector of length K where πᵢ = P(Z₁ = sᵢ).
- **Transition matrix A:** K × K matrix where Aᵢⱼ = P(Z_{t+1} = sⱼ | Z_t = sᵢ).
- **Emission matrix B:** K × M matrix where Bᵢₖ = P(X_t = oₖ | Z_t = sᵢ).

MLE estimates π, A, and B from the observed data. If the hidden states were known, the MLE estimates would be straightforward frequency counts (as you did for CPTs in the previous milestone). Because the states are latent, the EM algorithm iteratively estimates the hidden state assignments (E-step) and updates the parameters (M-step) until convergence.

### 4.2 Discretization

Because this milestone requires a basic discrete HMM, continuous features must be discretized. Common approaches include:

- **Equal-width binning:** Divide the feature range into b bins of equal width.
- **Equal-frequency binning** (quantile-based): Divide so each bin contains approximately the same number of samples.
- **Domain-informed thresholds:** Use meaningful cutpoints (e.g., income brackets, clinical thresholds).

The number of bins b controls the resolution of your observation space. Typical values range from 3 to 5 bins per feature. Note that if you have multiple observed features, you will need to encode them jointly (e.g., by concatenating bin labels) or use a single composite observation symbol.

### 4.3 Connection to Your Bayesian Network

Your BN from the previous milestone already encodes conditional dependencies that map naturally onto HMM components. The CPT of a parent node given its own prior state informs the structure of the transition matrix. The CPTs of child nodes given the parent inform the emission probabilities. You should explicitly document which BN nodes correspond to hidden states, which correspond to observations, and how the CPT values relate to (or initialize) the HMM parameters.

---

## 5. Inference on Your Fitted HMM

Once you have estimated the HMM parameters using EM, the next step is to **perform inference** on the fitted model. You may use any inference method appropriate to your task—for example:

- **Forward algorithm:** computes the probability of the observed sequence given the model (useful for model comparison or scoring).
- **Backward algorithm:** used in conjunction with the forward algorithm for smoothing or for computing posterior distributions over hidden states.
- **Viterbi algorithm:** computes the single most likely sequence of hidden states given the observations (decoding).
- **Other procedures:** any other inference method that yields interpretable quantities.

The quantity you compute (e.g., sequence likelihood, decoded state sequence, or posterior state probabilities) should **directly support the problem you set out to solve** in your PEAS / agent analysis (Section 0). For instance, if your agent is meant to classify or segment a sequence, the decoded hidden state sequence from Viterbi may be the main output; if your goal is to score or compare sequences, the forward likelihood may be more relevant. You should clearly state how the inference output you produce is used to address your original problem—whether for prediction, diagnosis, filtering, or another decision task—and, in your deliverables, justify your choice of inference method in light of that goal.