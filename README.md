# Adversarial Distance Metrics in Clustering-Based Decision Systems

Code for the extended version of the SECRYPT 2026 paper *Adversarial Distance Metrics: A Threat to
Fairness in Clustering-Based Decision Systems* by Shahzad Ahmad, Stefan Rass and Enes Sovtic.

Clustering-based decision systems take their distance function as a configuration parameter. An
insider who controls this parameter can replace the Euclidean distance by an ε-semimetric and dictate
the clusters, while data and code stay unchanged. The extended version states the guarantee of the
construction, evaluates it in closed form, attacks the three clustering algorithms of the conference
paper and eight library implementations, measures the fairness impact on Adult, COMPAS and German
Credit, and tests the proposed defences.

This repository holds one Jupyter notebook with all code. It reproduces every table, the figure and
every number in the text of the extended version. Everything runs in Python. MATLAB is not needed.

## Contents

| Path | Content |
|---|---|
| `Adversarial_Distance_Metrics_Extended.ipynb` | The notebook. Run it from top to bottom. |
| `requirements.txt` | Python packages. |
| `data/artifacts/As_m12_double.mat` | The matrix that the MATLAB code of the conference paper saved for its Adult experiment. |
| `data/` | The three datasets. The notebook downloads them on first use. |
| `results/` | JSON results and the figure of a full run of the notebook. The notebook overwrites them when it runs. |

## Quick start

```bash
git clone <URL of this repository>
cd <repository folder>
python -m venv .venv
.venv\Scripts\activate          # Windows
source .venv/bin/activate       # macOS and Linux
pip install -r requirements.txt
jupyter lab Adversarial_Distance_Metrics_Extended.ipynb
```

Then choose *Kernel → Restart Kernel and Clear Outputs of All Cells*, followed by *Run → Run All Cells*.
Clearing first matters because the notebook in this repository already holds the outputs of the reference
run, and some front ends keep old output lines next to new ones. The first cell also installs missing
packages into the running kernel, so an existing Jupyter installation works as well.

Two switches at the top of the notebook control a run.

| Switch | Effect |
|---|---|
| `QUICK = True` | Runs every code path with smaller sizes in about two minutes. Results go to `results_quick/`. |
| `RECOMPUTE = False` | Loads the JSON files in `results/` instead of recomputing them. All tables appear in seconds. |

## What the notebook reproduces

| Notebook section | Paper | Result file |
|---|---|---|
| 3. Construction | Sect. 4: lifting, conference algorithm, closed form (Proposition 1) | |
| 5. Experiment X1 | Sect. 4.5, Tables 1 and 2, Fig. 1: accuracy and construction time | `results/x1.json`, `results/fig1_time.png` |
| 6. Experiment X2 | Sect. 5, Tables 3 and 4: three conference algorithms, eight library implementations | `results/x2.json` |
| 7. Experiment X3 | Sect. 6, Tables 5 and 6: fairness impact on real data | `results/x3.json` |
| 8. Experiment X4 | Sect. 7.2, Table 7: statistical tests against six legitimate metrics | `results/x4.json` |
| 9. Numbers in the text | every number in the running text | `results/numbers.json` |
| 10. Check | comparison with the reference run | |

## Runtime and memory

A full run takes 25 to 45 minutes: about 25 minutes on our two-core Linux machine and 43 minutes on a
Windows laptop. Most of it is the conference algorithm at m = 70 in X1, which we keep for the timing
comparison. The closed form at m = 150 needs about 3 GB of
memory.

## Data

| File | Source | Licence |
|---|---|---|
| `adult.data` | UCI Machine Learning Repository, Adult (Becker and Kohavi, 1996), https://doi.org/10.24432/C5XW20 | CC BY 4.0 |
| `german.data` | UCI Machine Learning Repository, Statlog German Credit (Hofmann, 1994), https://doi.org/10.24432/C5NC77 | CC BY 4.0 |
| `compas-scores-two-years.csv` | ProPublica, https://github.com/propublica/compas-analysis (Larson et al., 2016) | see the ProPublica repository |

The notebook downloads each file from its source on first use and checks its SHA-256 against the file
used in the paper. Without network access, place the files in `data/` by hand. `.gitignore` keeps the
three datasets out of the repository.

## Reproducibility notes

**MATLAB random numbers.** For positive seeds, MATLAB's `rng(seed)` and NumPy's `RandomState(seed)`
produce the same Mersenne Twister stream. The notebook therefore reproduces the lifted vectors of the
conference code exactly, and its Python version of the conference algorithm matches the saved MATLAB
matrix to a relative error of about 1e-14. MATLAB's normal generator differs from NumPy's, so the
synthetic points differ from the conference draws.

**Seeds and threads.** Every experiment fixes its seeds. The k-medoids pipeline calls FasterPAM with
BUILD initialisation and one thread, so its output does not depend on thread scheduling.

**Check against the reference run.** Section 10 compares every number in the text with the run that
produced the paper. Values that depend on rounding (tiny relative errors, condition-number estimates,
counts of complex values) can change with the BLAS and LAPACK build. Timings depend on the machine,
so even the run stored in `results/` differs slightly from the timings in the paper, which come from an
earlier run on the same machine. All other values should agree exactly. A difference there usually means a different data file or a
NumPy version whose random generators produce other streams.

**Windows.** joblib asks Windows for the number of physical cores through `wmic`, which recent Windows
versions no longer ship. The notebook seeds joblib's cache and avoids the harmless traceback that the
failed lookup would print. With MKL, scikit-learn also warns that KMeans can leak memory. KMeans runs
only on tiny inputs here, so the notebook silences this warning.

**Tested with** Python 3.13, NumPy 2.5, SciPy 1.18, scikit-learn 1.9, pandas 3.0, matplotlib 3.11 and
kmedoids 0.5.5 on Linux, and with Python 3.12, NumPy 1.26, SciPy 1.14, scikit-learn 1.5, pandas 2.2,
matplotlib 3.9 and kmedoids 0.5.5 on Windows. The two runs agree on every value that does not depend on
rounding or timing.

## Citation

If you use this code, please cite the conference paper.

```bibtex
@inproceedings{ahmad2026secrypt,
  author    = {Ahmad, Shahzad and Rass, Stefan and Sovtic, Enes},
  title     = {Adversarial Distance Metrics: A Threat to Fairness in Clustering-Based Decision Systems},
  booktitle = {Proceedings of the 23rd International Conference on Security and Cryptography ({SECRYPT} 2026)},
  editor    = {De Capitani di Vimercati, Sabrina and Samarati, Pierangela},
  publisher = {SCITEPRESS},
  address   = {Porto, Portugal},
  DOI       = {10.5220/0015144800004103}
  year      = {2026}
}
```

## Contact

Shahzad Ahmad, LIT Secure and Correct Systems Lab, Johannes Kepler University Linz, shahzad.ahmad@jku.at
