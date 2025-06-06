# Human Preference Modeling and Optimization in Human-Robot Collaboration

## Human Preference Modeling with Decision Field Theory (DFT)

In our experimental setup, the internal preference state \${P}\_k \in \mathbb{R}^5\$ characterizes human worker $k$'s subjective evaluation over five robot options, each described by:

* Energy consumption
* Safety
* Intelligence
* Reliability
* Pace

### Data Collection via GUI

Participants interact with a graphical user interface (GUI) to express preferences under different task scenarios:

* **High urgency**: e.g., producing 100 parts in 1 day
* **Low urgency**: e.g., producing 100 parts in 7 days

These preferences are fit using a multi-alternative **Decision Field Theory (DFT)** model, yielding a personalized preference vector \${P}\_k\$.

### Mapping Preferences to Production Behavior

We define a response function:

```math
y_k = r_k({P}_k),
```

where $y_k$ represents the expected production capability contributed by human $k$. The most preferred robot is selected using \$\arg\max\_j P\_{k,j}\$, and \$y\_k\$ is assigned based on empirical data. This allows for systematic, interpretable integration into optimization.

---

## Optimization Problem Formulation

We minimize the total production cost:

```math
\min_{x} \sum_{i=1}^{5} f_i(x_i) + \sum_{k=1}^{2} g_k(y_k)
```

Where:

* $f_i(x_i) = x_i^\top \Lambda_i x_i$: cost for autonomous agent $i$
* $g_k(y_k) = y_k^\top \Gamma_k y_k$: cost for human worker $k$
* $\Lambda_i, \Gamma_k$: diagonal matrices with positive entries

### Chance-Constrained Resource Limitation

The system is constrained by:

```math
\mathbb{P} \left( \sum_{i=1}^{5} A_i x_i + \sum_{k=1}^{2} B_k y_k + c \leq 0 \right) \geq \delta,
```

Where:

* $A_i\in\mathbb{R}^{3 \times 3}$, $B_k \in \mathbb{R}^{4 \times 3}$: resource consumption
* $c \in \mathbb{R}^3$: negative of total available resource

---

## Experimental Design

* **Participants**: 8 individuals, each session lasting \~1 hour
* **Interface**: GUI to assess preferences across different stakes and robot attributes
* **Modeling**: DFT parameters ($\phi_1,\phi_2,\epsilon,\tau,\beta$) estimated via Apollo choice modeling software.

Participants validated whether the system-predicted robot matched their preference. This setup is both human-centric and scalable, and it enables risk-aware pairing of humans and robots.

GUI:
![Sample Survey Problem](https://github.com/user-attachments/assets/c7b1d31d-744e-4be8-8109-b1cf687ac4bd)

---
## Cognitive Modeling with DFT

DFT accounts for key cognitive effects:

* Similarity
* Attraction
* Compromise

These are embedded into the GUI and verified via surveys. Results reveal the influence of:

* Sensitivity ($\phi_1$)
* Memory decay ($\phi_2$)
* Cognitive noise ($\epsilon$)

The resulting $E(P_k)$ and $P_k$ values are then mapped to human responses $y_k$.

Prediction Satisfaction:
![User Satisfaction Based on Human-Centric Predictions](https://github.com/user-attachments/assets/4443ffa5-e9d8-4b57-9325-a0d7ff86375d)

---

## Optimization vs. Evaluation Phases

* **Optimization Phase**: Predicts mean and covariance of $y_k$ for planning
* **Evaluation Phase**: Samples $y_k$ from the distribution to assess empirical performance

---

## Comparison Settings

We simulate three modeling strategies:

* **Our Algorithm**: Our method using both mean and covariance of $y_k$
* **Baseline 1**: Uses only the mean of $y_k$, ignores uncertainty
* **Baseline 2**: Naive baseline using previous $y_k$ as input

---

## Evaluation: Constraint Conservativeness

To approximate the chance constraint, a deterministic linear inequality is used. We test four values of \$\delta\$: {0.6, 0.7, 0.8, 0.9}. For each, we evaluate the empirical satisfaction rate over 1000 samples.

### Summary Table

| Metric        | δ = 0.6 | δ = 0.7 | δ = 0.8 | δ = 0.9 | BL1   | BL2   |
| ------------- | ------- | ------- | ------- | ------- | ----- | ----- |
| Rate ↑        | 81.3%   | 88.7%   | 91.2%   | 94.1%   | 43.6% | 27.4% |
| Penalty ↓     | 23.6    | 13.7    | 8.2     | 6.9     | 123.8 | 180.7 |
| Cost ↓        | 24.6    | 26.1    | 28.6    | 29.3    | 22.4  | 40.1  |
| Performance ↓ | 48.2    | 39.8    | 36.8    | 35.5    | 146.2 | 220.8 |

---

## Performance Index

Defined as:

```math
\text{Performance Index} = \text{Cost} + \gamma \cdot \text{Penalty}
```

Where:

```math
\text{Cost} = \sum_i x_i^\top \Lambda_i x_i + \sum_k y_k^\top \Gamma_k y_k
```

```math
\text{Penalty} = \left\| \max \left( 0, \sum_i A_i x_i + \sum_k B_k y_k + c \right) \right\|_2
```

> Note: $\max(\cdot)$ is element-wise.

$\gamma = 100$. Index computed for 1000 sampled realizations of $y_k$ using fixed $x^*$.

### Boxplot: Total Performance Comparison

![Performance Boxplot](fig/performance_boxplot.png)


---

## Adaptability to Human Behavior Types

We model two human behavior types:

* **Lazy**: Decrease effort when robots are more active
* **Hardworking**: Increase effort when robots are active

### Scenarios Tested: Work share between humans and robots and associated system cost across different human types.

* Both humans tend to be in less productive mode
  ![neither](https://github.com/user-attachments/assets/51060c89-2082-4f47-90c0-1a2f918782d5)

* Both humans tend to be in productive mode
  ![both](https://github.com/user-attachments/assets/d13951ab-8b38-41c5-b51d-e5401f7c07b7)

* Mixed cases 
![either](https://github.com/user-attachments/assets/31ab517a-3577-42ea-ac7a-834ad900ccdb)

The optimization algorithm remains unchanged.

