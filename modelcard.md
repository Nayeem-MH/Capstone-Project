# Model Card

### **Overview**

    Name: GP-EI sequential black-box optimisation 
    Type: Bayesian optimisation using a Gaussian Process surrogate model and an Expected Improvement acquisition function
    Version: V1.0 
    Scope: Eight separate optimisation problems (F1-F8), with input dimensions from 2D to 8D, all inputs in [0, 1]

### **Intended use**

*Suitable for:*

- Maximising expensive or unknown functions with a very small query budget (roughly 13 evaluations per function)
- Low to moderate-dimensional problems (2D-4D here), where a GP can learn something useful from few points
- Learning and demonstrating the exploration-exploitation trade-off

*Should be avoided for:*

- High-dimensional problems with few queries (F7 at 6D and F8 at 8D were already at the edge of what this approach can do)
- Very rough, discontinuous or heavily noisy functions (F4), where GP smoothness assumptions are weak
- Functions whose outputs span many orders of magnitude without a transform (F1)
- Safety-critical or high-stakes decisions, where a proper uncertainty analysis and validation would be needed

### **Details of approach** 

Technique: In each round, a GP was fitted to all earlier observations of a function, and the next query was the point that maximised Expected Improvement. For functions 1 and 2, a Matern kernel was used with an anisotropic length scale. All other functions used the RBF kernel with a fixed length scale. Noise, smoothness(where applicable) and exploration parameters were initialised at 10<sup>-10</sup>, 2.5 and 0.01 respectively.

How the approach evolved:

- **Rounds 1-4:** broad exploration. Queries were spread widely across each space to find promising regions.
- **Rounds 5-9:** targeted exploitation. Where trends were clear, queries concentrated there. For F5, x1 was pushed upwards with the other inputs near 1. For F6 and F7, queries refined around the best points (F6: x3≈0.66, x4≈0.8-0.85, low x5; F7: x6≈0.65).
- Exploration was kept for difficult functions. F1 (outputs ≈0 nearly everywhere) and F4 (very rough) stayed cautious or exploratory, because the surrogate gave little to exploit.

### **Performance**

Metrics used:

- Best observed output per function (the quantity being maximised)
- Improvement over round 1 (best value versus the first query)

| Function | Initial query (R1) | Initial output | Best query | Best round | Best output |
|:---:|:---:|:---:|:---:|:---:|:---:|
| F1 | [0.937078, 0.684657] | -5.60e-69 | [0.366681, 0.475455] | R10 | 2.50e-05 |
| F2 | [0.784471, 0.945866] | -0.000776 | [0.708638, 0.510785] | R13 | 0.7296 |
| F3 | [0.387781, 0.383893, 0.466712] | -0.005551 | [0.313581, 0.474345, 0.485191] | R7 | -0.003100 |
| F4 | [0.435953, 0.415935, 0.389022, 0.424633] | 0.4567 | [0.389907, 0.406702, 0.418185, 0.413754] | R12 | 0.5697 |
| F5 | [0.278685, 0.815061, 0.977174, 0.897284] | 1661.41 | [0.999999, 0.999999, 0.999999, 0.999999] | R11 | 8662.41 |
| F6 | [0.484288, 0.365614, 0.495586, 0.638325, 0.188988] | -0.4766 | [0.379808, 0.287508, 0.660125, 0.776426, 0.078124] | R12 | -0.0416 |
| F7 | [0.080871, 0.374942, 0.294087, 0.148619, 0.357409, 0.659524] | 1.9768 | [0.161621, 0.187075, 0.503918, 0.254813, 0.334677, 0.663204] | R12 | 3.2488 |
| F8 | [0.107783, 0.152192, 0.201339, 0.074624, 0.811699, 0.315491, 0.261762, 0.653263] | 9.9369 | [0.119143, 0.135448, 0.151215, 0.240738, 0.752919, 0.368148, 0.224161, 0.437327] | R6 | 9.9672 |

### **Assumptions and limitations**

- The functions are smooth and stationary, so nearby inputs give similar outputs and the behaviour is similar across the domain.
- Outputs are low-noise and deterministic.
- A good optimum exists within the [0, 1] domain and can be reached within the query budget.

#### **Constraints and failure modes:**

- Sparse data in high dimensions: 9 points in 8D (F8) leave the surrogate very uncertain almost everywhere.
- Rough functions: F4 swings from +0.46 to -0.98 across small moves, so the GP smoothness assumption fails and EI can be misled.
- Scale problems: F1's outputs span 10<sup>-11</sup> to 10<sup>-143</sup>, so a standard GP learns almost nothing without a transform.
- Exploitation drift: Later rounds concentrate near the current best, which risks a local optimum and leaves boundaries and other regions unexplored (F2's second region is one example).
- No ground truth: It is impossible to verify how close any result is to the true maximum.

### **Ethical considerations**

The project has no personal data, so the main ethical concerns are honesty and reproducibility.

- **Transparency supports reproducibility.** Anyone can rerun the strategy if given the full query history, the model settings (kernel, noise, acquisition parameters), the EI optimiser and seeds, and knowledge of any manual overrides.
- **Honest reporting.** Stating what worked, what didn't (F1, F4), and where judgement replaced the algorithm lets others judge how far the results can be trusted.
- **Real-world adaptation.** The same approach is used in settings like hyperparameter tuning, experiment design and engineering optimisation. There, undocumented assumptions could lead to poor decisions or wasted resources.
- **Appropriate caution.** Results from about 13 queries should not be presented as general findings, and anyone adapting this approach should validate it against their own problem.
