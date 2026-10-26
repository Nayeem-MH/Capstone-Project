# Datasheet for dataset

### **Motivation:** why did you create this data set? What task does it support?

This dataset was created to be the starting point of a black-box optimisation project, where the aim is to maximise the output of 8 unknown black-box functions using 1 query per week per function. Each round's inputs and outputs were appended to the original dataset and used to update the optimiser for future queries.

The dataset was used to guide the Gaussian Process surrogate model along with an Expected Improvement acquisition function in order to select queries. The initial dataset was provided by Emeritus, and is maintained/updated by me.

### **Composition:** what does it contain? What is the size and format and are there any gaps?

The initial inputs and outputs are in separate .npy files for each function. They vary in size, ranging from 10 initial points to 40.

| Function | Dimensions (number of inputs) | Initial dataset size |
|:--------:|:-----------------------------:|:--------------------:|
| F1       | 2                             | 10                   |
| F2       | 2                             | 10                   |
| F3       | 3                             | 15                   |
| F4       | 4                             | 30                   |
| F5       | 4                             | 20                   |
| F6       | 5                             | 20                   |
| F7       | 6                             | 30                   |
| F8       | 8                             | 40                   |

All valid inputs are between 0 and 1, and are specified to 6 decimal places (e.g. 0.123456).

Are there any gaps?

- Coverage is sparse in higher dimensions (for example, 9 points in 8D for F8).
- Queries cluster near the best-known points, because of the EI strategy, so some regions and boundaries are unexplored.
- F1's outputs are effectively zero everywhere, so they carry little information (outputs range from order of 10-3 to 10-143).
- F4 is very rough, with outputs ranging from +0.46 to -14.5 across queries.
- There is no knowledge of what the underlying functions are, so the true optimum of each function is unknown.

### **Collection process:** how were the queries generated? What strategy did you use? Over what time frame?

Each round, a Gaussian Process surrogate was fitted to all previous observations for a function. The next query was then chosen by maximising Expected Improvement.

Strategy: The early rounds were broad exploration. Later rounds moved towards exploitation for functions with clear trends (F5, F6, F7), while the rough or uninformative functions (F1, F4) stayed more exploratory or cautious.

Time frame: 13 weeks, 1 query per function each week.

### **Preprocessing and uses:** have you applied any transformations? What are the intended and inappropriate uses?

The initial data was not transformed, but was structured into tables for easy lookup.

Intended uses:

- Teaching and learning about sequential Bayesian optimisation
- Reviewing and reproducing the search strategy
- Benchmarking other strategies on the same query history

Inappropriate uses:

- Drawing conclusions about the true functions' shapes or optima, because the sample is tiny and biased towards the areas that EI explored
- Treating the data as an unbiased or space-filling sample
- Comparing strategies without accounting for the fact that these queries depended on earlier results

### **Distribution and maintenance:** 

Where will it be available? GitHub repository 

Terms of use: For academic and educational use only.

Who maintains it? I maintain it.

Will it be updated? Yes, it grows by one query per function each round.
