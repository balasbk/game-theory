

## Installing PRL with Pypi (Python 3)
PRL is available in the PyPi repository and it can be installed with
```sh
pip3 install prl
```

and then it can be imported in python with

```python
import prl
```

## How to use the PRL module
It is possible to run PRL using the provided python script `run_prl.py`.

### Configuration file
In order to correctly run the script the configuration file must be initialized. An example of configuration file is provided in `config/config.json`

```json
{
    "algorithm" : "PRL",
    "feat_gen" : "GenHPolyF",
    "feat_gen_params" : [3],
    "pref_generator" : "macro",
    "columns_budget" : 1000,
    "iterations" : 10,
    "solver" : "LinProg",
    "solver_params" : [0]
}
```
The meaning of each configuration attribute is described in the following:
* `algorithm`: it selects the PRL variation. Actually two PRL variations are implemented:
  * `PRL`: which is the standard algorithm as presented in the paper mentioned above;
  * `PRL_ext`: that is slight different from PRL, in the sense that the budget of columns is not fixed, but at each iterations `columns_budget` number of new columns are generated. This variation guarantees that regardless of the initial budget at some iteration the number of columns will be enough to guarantee the convergence to the optimum (as the number of iterations increases);
  * `KPRL`: which is similar to the standard PRL but instead of generating preference-feature pairs as columns, it generates preference-kernel pairs.
  
* `feat_gen`: it indicates which feature generator will be used by PRL. Feature generators are implemented in the script `genF.py` and at the moment the following feature generators scheme are implemented:
  * `GenLinF`: generates linear features, i.e., it randomly picks a feature from the input ones;
  * `GenHPolyF`: generates homogeneous polynomial features of the specified degree;
  * `GenDPKF`: generates dot-product polynomials features of the specified degree. It mainly differs from `GenHPolyF` on the weighting scheme of the features;
  * `GenConjF`: it assumes binary/boolean input vectors and it generates conjunctive (logical AND) features of the specified arity;
  * `GenRuleF`: generates conjunctions of logical rules over the input attributes. The generated rules has a form like `age >= 10 and height <= 160`. The arity of the conjunction is a parameter of the generator;
  * `GenRuleEqF`: like `GenRuleF`, but the only relation considered is the equality (`==`).

* `feat_gen_params`: ordered list of parameters for the selected feature generator. For more details, please refer to the documentation of the generators;

* `kernel_gen`: (Only for KPRL) it indicates which kernel generator will be used by KPRL. Kernel generators are implemented in the script `genK.py` and at the moment the following kernel generators scheme are implemented:
  * `GenKList`: generates a kernel from the provided list of kernel functions, i.e., it randomly picks a kernel from the provided list;
  * `GenHPK`: generates an homogeneous polynomial kernel function of one of the specified degrees;

* `kernel_gen_params`: (Only for KPRL) ordered list of parameters for the selected kernel generator. For more details, please refer to the documentation of the generators;
* `pref_generator`: it indicated which preference generator will be used by PRL. The possible preference generation schemes are:
  * `macro`: a macro preference describes preferences like
    y_i is preferred to `y_j` for the instance `x_i`, where `(x_i, y_i) in X x Y`, while `(x_i, y_j) not in X x Y`. This kind of preferences are suitable for label ranking tasks;
  * `micro`: a micro preference describes preferences like
    `(x_i, y_i)` is preferred to `(x_j, y_j)`, where `(x_i, y_i) in X x Y`, while  `(x_j, y_j) not in X x Y`. This kind of preferences are suitable for instance ranking tasks.

* `columns_budget`: the number of columns of the matrix game;
* `iterations`: number of iterations of PRL;
* `solver`: the algorithm for solving the game. Up to now the available algorithms are `FictitiousPlay`, `AMW` and `LinProg`;
* `solver_params`: it is the ordered list of parameters of the solver. For more details, please refer to the documentation of the solvers.

Inside the `config` folder an example of configuration file which uses `KPRL` is also provided.

### Run PRL

```sh
python3 r.py -s 1 -v  dataset/oh_tic-tac-toe.svmlight
```
which runs PRL using 80% of the dataset as training set and the rest as test set, using 1 as pseudo-random seed and a verbose output.

### Evaluation
The evaluation is computed in terms of *accuracy*, *balanced accuracy* and it also shows the *confusion matrix*.

