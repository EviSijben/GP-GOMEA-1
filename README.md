# Genetic Programming - GOMEA, Standard & Semantic
Implementations of the **Gene-pool Optimal Mixing Evolutionary Algorithm for Genetic Programming** (GP-GOMEA), **standard tree-based GP**, and **Semantic Backpropagation-based GP**.
This code uses **C++** under the hood for speed, and has a **Python 3** interface to **scikit-learn**.

## Algorithms
By estimating interdependencies between model components, and appropriately mixing them, GP-GOMEA is especially proficient in finding models in the form of small symbolic expressions, that can often be interpreted. Semantic Backpropagation-based GP works well when high accuracy is demanded, at the cost of producing very complex models, which are hard or impossible to interpret. Standard GP is a good baseline to compare the other methods with.

This code also implements the Interlaved Multistart Scheme (IMS), that makes GP more robust w.r.t. setting a specific population size. Setting the population size is tricky: if too small, GP will find models with poor accuracy; If too big, GP will waste computation time. The IMS starts and executes multiple evolutionary runs in an interleaved fashion, with subsequent runs using bigger population sizes. It terminates ongoing runs that perform poorly, according to some heuristics. The IMS is largely inspired from the Parameter-free Genetic Algorithm of Harik and Lobo: https://dl.acm.org/citation.cfm?id=2933949

It is also possible to run GP to optimize multiple objectives at once (in which case NSGA-II is used: https://doi.org/10.1007/3-540-45356-3_83). **Note**: Multi-objective versions of GP-GOMEA and IMS have yet to be implemented.

## Related research work
If you use our code for academic purposes, please support our research by citing the paper that most applies, from the following:
* M. Virgolin, T. Alderliesten, C. Witteveen, and P.A.N. Bosman. *Scalable Genetic Programming by Gene-pool Optimal Mixing and Input-space Entropy-based Building Block Learning*. In Proceedings of the Genetic and Evolutionary Computation Conference - GECCO-2017, pages 1041-1048, ACM, 2017. (The source code for this paper is available on Peter's website: https://homepages.cwi.nl/~bosman/source_code.php)
* M. Virgolin, T. Alderliesten, A. Bel, C. Witteveen, and P.A.N. Bosman. *Symbolic Regression and Feature Construction with GP-GOMEA applied to Radiotherapy Dose Reconstruction of Childhood Cancer Survivors*. In Proceedings of the Genetic and Evolutionary Computation Conference - GECCO-2018, pages 1395-1402, ACM, 2018.
* M. Virgolin, T. Alderliesten, P.A.N. Bosman. *Linear Scaling with and within Semantic Backpropagation-based Genetic Programming for Symbolic Regression*.  In Proceedings of the Genetic and Evolutionary Computation Conference - GECCO-2019, pages 1084-1092, ACM, 2019.
* M. Virgolin, T. Alderliesten, C. Witteveen, P.A.N. Bosman. *Improving Model-based Genetic Programming for Symbolic Regression of Small Expressions*. Preprint on arXiv https://arxiv.org/abs/1904.02050 2019, accepted in Evolutionary Computation.
* M. Virgolin, T. Alderliesten, P.A.N. Bosman. *On Explaining Machine Learning Models by Evolving Crucial and Compact Features*. Swarm and Evolutionary Computation, v. 53, p. 100640, 2020.
* M. Virgolin, A. De Lorenzo, E. Medvet, F. Randone. "Learning a Formula of Interpretability to Learn Interpretable Formulas". Parallel Problem Solving from Nature - PPSN-XVI, pages 79-93, Springer, 2020.

## Installation

### Native
This code can be compiled on Linux, we tested on Ubuntu and Fedora (kudos to [@abouter](https://github.com/abouter) for helping out).
There are a few steps to follow:
* Inspect and potentially edit the `deps_ubuntu` (or `deps_fedora`) file to align it to your system (no change should be needed). This installs the dependencies that are needed for GP-GOMEA.
* Run `chmod +x deps_ubuntu; sudo ./deps_ubuntu` (or `chmod +x deps_fedora; sudo ./deps_fedora)` to install the needed dependencies on your system.

The project is built using CMake (kudos to [@EviSijben](https://github.com/EviSijben)). Run `make` (or `make debug` for a debug build). To test that everything works fine, run `python3 test.py`. You can use Ninja to speed up builds, by prefixing the make command with `GEN=ninja` (e.g. `GEN=ninja make release`).

### Docker
This code can also be compiled and run inside a [Docker](https://www.docker.com/why-docker) container (kudos to [@roy-tc](https://github.com/roy-tc) for providing this!):

```bash
docker build -t gp-gomea .
```

This will:
* Install dependencies (implementation derived from on `deps_ubuntu`).
* Build the project.
* Test that everything is fine using `python3 test.py`.

You can run the container in interactive mode using `docker run -it gp-gomea` and issue for instance `python3 test.py` or execute your own script.

## Using the Python interface
See `test.py`. 
For example:
```python
from pyGPGOMEA import GPGOMEARegressor as GPGR
from sklearn.datasets import load_boston
import numpy as np

X, y = load_boston(return_X_y=True)
model = GPGR( gomea=True, ims='5_1', generations=10, seed=42 )
model.fit(X, y)
print('Model found:', model.get_model())
print('RMSE:', np.sqrt( np.mean( np.square( model.predict(X) - y ) ) ))
```
Take a look at `test.py` for more details.

## Do you want to run a C++ executable instead?
After running `make`, you will find a C++ executable called `main` in `build/release/src/` that you can run using a parameter setting file, for example, `gpgomea --file params_sgp.txt`.

### Datasets
For the C++ executable, datasets must be organized as follows. Each row is an example, and each column is a feature, with exception for the last column, which is the target variable. Values should be separated by spaces. Do not include any textual header.
You can find examples at: https://goo.gl/9D2z3b 
