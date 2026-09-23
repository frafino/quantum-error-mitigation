# Zero-Noise Extrapolation for Quantum Error Mitigation: Implementation and Noise Analysis

A Python and Qiskit implementation of methods presented in **[Digital zero noise extrapolation for quantum error mitigation](https://arxiv.org/abs/2005.10921)** by **Tudor Giurgica-Tiron, Yousef Hindy, Ryan LaRose, Andrea Mari, and William J. Zeng**.[^1]

My work consists of implementing the paper's unitary-folding and extrapolation techniques, applying them to Grover's algorithm, and comparing their behavior under different simulated noise models, measurement budgets, and circuit depths. The underlying methods and theoretical results are due to the paper's authors; this repository contains my implementation and numerical exploration of those methods.

## Overview

**Zero-noise extrapolation (ZNE)** estimates an ideal expectation value from measurements performed at several amplified noise levels. This project uses **unitary folding** to increase the number of noisy gate operations while preserving the circuit's ideal action. Classical extrapolation then estimates the value at zero noise.

The workflow is:

1. Construct and transpile a quantum circuit.
2. Generate folded circuits at selected noise-scale factors.
3. Simulate the circuits with a chosen noise model.
4. Fit the measured results and extrapolate to zero noise.
5. Compare the estimate with noiseless and unmitigated results.

## Implemented methods

### Noise scaling

- **Circuit folding (`circuit_folding`)**: inserts full-circuit inverse/forward pairs, with partial folding for intermediate scale factors.
- **Gate folding (`layer_folding`)**: inserts inverse/forward pairs around individual gates. Additional folds can be assigned from the left, from the right, or randomly.

### Extrapolation

- **Linear extrapolation**: fits a straight line and evaluates its intercept at zero noise.
- **Richardson extrapolation**: fits a polynomial of degree one less than the number of noise-scale points.
- **Exponential extrapolation**: fits an exponential decay model, with either a fixed or fitted asymptote.
- **Adaptive exponential extrapolation**: implements an adaptation of the paper's adaptive protocol, updating the noise-scale selection and measurement allocation from the estimated decay rate. The implementation includes a maximum scale factor and an early-stopping condition.

## Experiments and noise analysis

The main example is a **three-qubit Grover search** with two marked states, `101` and `110`. The measured quantity is the probability of obtaining either solution:

$$
E(\lambda)=P_{\lambda}(101)+P_{\lambda}(110).
$$

The notebook compares unmitigated results with ZNE estimates using:

| Noise model | Simulated effect |
| --- | --- |
| Depolarizing noise | Depolarizing errors on single- and two-qubit gates. |
| Thermal relaxation | Relaxation and dephasing determined by gate durations and T1/T2 times. |
| Readout noise | Asymmetric errors in measured classical bits. |
| Coherent unitary noise | Systematic rotation errors on `sx` and `sxdg` gates. |
| Combined noise | Thermal relaxation, readout errors, and coherent rotation errors. |

The analysis includes comparisons of adaptive and non-adaptive exponential extrapolation across measurement budgets, comparisons of extrapolation methods as the number of Grover iterations increases, and repeated simulations to examine variability. Errors are evaluated relative to noiseless reference results.

The repository also includes a **folder containing circuit diagrams and noise-analysis plots**, illustrating the original and folded circuits, extrapolation fits, and method comparisons.

## Repository contents

| Item | Description |
| --- | --- |
| Main implementation notebook | Folding routines, extrapolation methods, noise models, Grover simulations, and quantitative comparisons. |
| Presentation PDF | Overview of the paper's methods, circuit examples, and simulation results. |
| Auxiliary `ugly_or_unused` notebook | Exploratory code, including a GHZ example and additional simulation helpers; depends on definitions from the main notebook. |
| Circuit and noise-analysis plots folder | Saved figures illustrating circuit constructions and numerical analysis. |

## Running the project

Install the notebook, simulation, fitting, and circuit-drawing dependencies:

```bash
python -m pip install numpy scipy matplotlib qiskit qiskit-aer pylatexenc jupyter
jupyter notebook
```

Open the main implementation notebook and run its cells in order. The later comparison cells perform repeated simulations; reduce the repetition counts or measurement budgets for a shorter exploratory run. All experiments use local Qiskit Aer simulation and require no quantum-hardware account.

## Scope and interpretation

This project focuses on unitary folding and selected extrapolation methods from the paper. Parameterized noise scaling and general poly-exponential fitting are discussed in the presentation but are not implemented in the main notebook. The experiments explore the methods on Grover circuits rather than reproducing the paper's complete benchmark suite.

## Reference

[^1]: Tudor Giurgica-Tiron, Yousef Hindy, Ryan LaRose, Andrea Mari, and William J. Zeng. *Digital zero noise extrapolation for quantum error mitigation.* 2020 IEEE International Conference on Quantum Computing and Engineering (QCE), 2020. [arXiv:2005.10921](https://arxiv.org/abs/2005.10921) · [DOI: 10.1109/QCE49297.2020.00045](https://doi.org/10.1109/QCE49297.2020.00045).
