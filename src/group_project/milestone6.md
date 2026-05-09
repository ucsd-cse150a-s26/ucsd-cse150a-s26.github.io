# Group Project Milestone 6: Final Model Selection, Improvement, and Final Report Draft

## Introduction

**Important instructions: Move your second project code to a branch titled 'second_project'. Then, proceed by working on the main branch for your final project.**

In this milestone you will continue working on your main branch, and focus on working on your final model. You will also need to evaluate this model compare your performance. Include all responses to the below on your **main** branch in the **README.md file**.

### Additional Notes (points to be subtracted)

- Be sure to cite all work and all generative AI used in the process. You will not be deducted points for citing work and using these tools, but you may certainly be deducted points for not citing work!

**This milestone is worth: 30 points of 100 total project points**

---

## Overview

In this milestone, you will:

- Select your strongest probabilistic model from previous milestones (BN or HMM), or propose a new one with justification (unless you decide to try and do reinforcement learning).
- You should improve the metrics of the selected model in a meaningful way.
- Submit a complete draft of your final report in `README.md`.

This submission functions as a draft. You will receive feedback and resubmit a revised final version at the end of the quarter.

You may either:

- Select your Bayesian Network (Milestone 4) and improve its metrics
- Select your Hidden Markov Model (Milestone 5) and improve its metrics
- Propose a new probabilistic model (must justify) and evaluate it
- Try to do reinforcement learning and evaluate it

---

## Git Instructions

- Move previous work to:
  - `first_project` (BN)
  - `second_project` (HMM)
- Complete this milestone on the `main` branch.

Your `main` branch must contain:

- Final model implementation
- A clean, structured `README.md` (see below)

---

## Milestone Objectives

You must:

- Choose your best model
- Improve it technically
- Compare performance to earlier versions
- Submit a complete final report draft

Caveat: If you did not implement a Bayesian Network in a previous milestone, you must implement one in this milestone. You may still choose to further improve your HMM or attempt reinforcement learning, but a Bayesian Network must be implemented and evaluated here if it was not completed earlier.

---

## Final Report Draft Structure (README.md)

Organize your report using the following sections:

---

### 1. Motivation, Problem Statement & PEAS (3 pts)

- What real-world or theoretical problem are you solving?
- Why is uncertainty modeling important here?
- What are the limitations of non-probabilistic approaches?

Explain your agent using PEAS:

- Performance measure
- Environment
- Actuators
- Sensors

---

### 2. Dataset & Preprocessing (3 pts)

- Dataset source, size, and key features
- Task definition
- Data types (categorical, continuous, mixed)
- Preprocessing steps (cleaning, discretization, temporal ordering if applicable)
- Justification for design decisions

---

### 3. Model Choice & Methodology (10 pts total)

#### Model Selection (4 pts)

- Which model are you using (BN, HMM, or new)?
- Why is it your strongest model?
- How does it improve upon previous milestones?

Your writeup should have summaries of your models and a clear comparison of them.

#### Methodology (6 pts)

- Formal description of the model structure (include diagram)
- How parameters are computed:
  - CPT estimation (MLE)
  - EM updates (if HMM)
  - Relevant formulas
- Assumptions and simplifications made
- Inference procedure used
- Evaluation strategy

#### Model Improvements

Propose technically grounded improvements based on:

- Structure
- Parameter estimation
- Preprocessing
- Assumptions
- Identified weaknesses

Be specific.

Do not include any results, interpretations, or analysis in this section. These will be addressed later in Sections 4 and 5.

---

### 4. Evaluation & Results (12 pts)

#### Training & Implementation (3 pts)

- Training procedure
- Hyperparameters
- Convergence criteria (if applicable)
- Link to clean, documented code (or key snippet)

If using libraries (pgmpy, hmmlearn, etc.), explain what they do and cite them.

Provide:

- Quantitative metrics (accuracy, F1, log-likelihood, etc.)
- Baseline comparison
- Comparison to previous milestones
- Visualizations if helpful

Interpret your results:

- Where does the model perform well?
- Where does it fail?
- Why?

---

### 5. Reflection (2 pts)

Briefly compare:

- Final model vs. earlier model(s)
- What each captures
- Key assumptions and trade-offs
- Weaknesses or limitations of your models
- Potential extensions or next steps

You must clearly identify your strongest overall model and justify why it is the strongest (empirically and conceptually).

In addition, discuss the implications of your results. What do they suggest about the problem domain and the role of uncertainty modeling in this task? When is your probabilistic approach especially valuable, and when might it struggle?

Conclude with a short final paragraph summarizing your best model and what you learned.

---

### 6. Citations & AI Disclosure (Required)

Cite:

- All libraries
- External resources
- Any generative AI tools used

Failure to cite may result in point deductions.