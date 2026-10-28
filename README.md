# Black-Box Optimsation Project

## Project Overview

This project applies Bayesian Optimisation to maximise 8 different black-box functions using a Gaussian Process surrogate model and an Expected Improvement acquisition function. The aim is to efficiently maximise the output values of each function, using the techniques mentioned to guide query selection. Queries are limited to 1 per function per week, giving a total of 13 queries per function overall.

### Why is it relevant in real-world ML? What's the high-level idea?

The goal of this project is to optimise 8 unknown functions by intelligently selecting the most informative points to evaluate, rather than exhaustively searching the entire input space. Bayesian optimisation is widely used in machine learning for tasks such as hyperparameter tuning and engineering optimisation, where function evaluations are computationally expensive.

### How would this BBO capstone project support you in your current or future career?

This project develops practical skills in Bayesian optimisation, Gaussian Processes and Python, while demonstrating the ability to solve real-world optimisation problems efficiently. These skills are valuable for careers in data science, machine learning and artificial intelligence, where optimisation techniques are commonly used to improve model performance and support data-driven decision-making.

--------

## Inputs/Outputs

There are 8 unknown functions of varying dimensionality and starting dataset size. Each dataset grows by 1 data point each week, allowing the model to be updated with new information to inform its next decision. The functions are as follows:

*D refers to the number of dimensions of the input variables for that function, with all input values for all functions lying between 0 and 1.*

- Function 1 - 2D, 10 initial input-output pairs, 1D output Y
- Function 2 - 2D, 10 initial input-output pairs, 1D output Y
- Function 3 - 3D, 15 initial input-output pairs, 1D output Y
- Function 4 - 4D, 30 initial input-output pairs, 1D output Y
- Function 5 - 4D, 20 initial input-output pairs, 1D output Y
- Function 6 - 5D, 20 initial input-output pairs, 1D output Y
- Function 7 - 6D, 30 initial input-output pairs, 1D output Y
- Function 8 - 8D, 40 initial input-output pairs, 1D output Y

Each query must be submitted in the format ***x<sub>1</sub>-x<sub>2</sub>-x<sub>3</sub>-...-x<sub>n</sub>*** with each variable specified to **exactly 6 decimal places**. The output returned is a single continuous value, which varies depending on the function queried. In the initial datasets, function 1 has outputs that lie closely to 0 with the best output being **7.71 x 10<sup>-16</sup>** and the worst output being **-3.61 x 10<sup>-3</sup>**. Function 5 has a much wider range, with the best and worst outputs being **1088.86** and **0.11**.

--------

## Technical Approach

Each function is modelled using a Gaussian Process surrogate model to fit the data with an Expected Improvement acquisition function (*xi*) to guide where the best improvement may be found. This is coupled with a visualisation for each function to provide better interpretability of the model and increase confidence in query decisions (as regions of improvement become tighter, model uncertainty around selected inputs decreases). 

The exploration/exploitation balance is controlled by *xi*, which is initialised at 0.01. This provides a good balance between exploration/exploitation, and can be increased or decreased (between 0.001 and 0.05). For functions that return promising improvements this is left unchanged at *xi* = 0.01.

### Manual queries were used at 3 points in the optimisation process. 
- In round 11, F5's inputs were pushed to the upper boundary after noticing that with high *x<sub>2</sub>*, *x<sub>3</sub>*, *x<sub>4</sub>* (all >0.95), increasing *x<sub>1</sub>* led to increased outputs. This resulted in the highest output for that function of ***8662***, which the optimiser had not been able to improve upon. It is unknown if the optimiser would have converged on this value within the query budget without manual intervention.
- In round 8, F4 had been suggesting query points outside of the previously discovered region of ~(0.4, 0.4, 0.4, 0.4), leading to outputs much smaller than previous queries had given. The manual query at this point aimed to refine the current best query (*query 1*), but did not improve upon it. The kernel type was switched from RBF to Matern with nu = 2.5 the following week, which led to this behaviour subsiding. A new best was found in round 12 (***0.570***).
- In round 13, F1 had been struggling to refine around the best point even with *xi* lowered to 0. A manual query was used by combining coordinates of the two previous queries. The magnitude of the output was greater than the previous best (***2.5 x 10<sub>-5</sub>***), but was negative (***-0.00398***). If one of the coordinates were pushed slightly less, it is likely that this would have become the new best output.
