# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Coursework for a Brain-Machine Interfacing course (Uni Ulm, Master KI). All work happens in Jupyter notebooks that analyze EEG datasets shipped as MATLAB `.mat` files. There is no application code, no tests, and no linter — the deliverables are the notebooks themselves.

- `exercise_1/` — EEG basics / ongoing EEG and evoked responses
- `exercise_2/` — P300 speller (uses stepwise regression for channel/feature selection)
- `exercise_3/` — motor imagery: spectrograms, ERD/ERS, periodogram features, LDA classification, CSP
- `final_project/` — P300, motor imagery, and SSVEP datasets plus a course-provided `explanationForP300.ipynb`

## Environment and commands

The environment is managed with [pixi](https://pixi.sh) (conda-forge, osx-arm64 only, Python 3.12). Dependencies live in [pixi.toml](pixi.toml): numpy, scipy, matplotlib, jupyterlab, mne, nb-clean. scikit-learn is not listed explicitly but is available (pulled in via mne) and used in the notebooks.

- `pixi install` — create/update the environment in `.pixi/`
- `pixi run jupyter lab` — start JupyterLab
- `pixi run python <script>` — run a script inside the environment
- `pixi run jupyter nbconvert --to notebook --execute --inplace <nb>.ipynb` — execute a notebook headlessly
- `pixi run nb-clean clean <nb>.ipynb` — strip outputs/metadata from a notebook

## Structure and conventions

Each exercise folder is fully self-contained: the notebook, its `.mat` data files, and its own copies of the helper modules. `loadmat.py` and `stepwisefit.py` are intentionally duplicated across folders (there is no shared package); notebooks do `sys.path.insert(0, '.')` and import them locally. When fixing a helper, check whether the same fix applies to the copies in the other folders.

- `loadmat.py` — replacement for `scipy.io.loadmat` that recursively converts MATLAB structs into nested Python dicts. Data access then looks like `lm.loadmat('MIs.mat')['data']['LH']`.
- `stepwisefit.py` — Python port of MATLAB's `stepwisefit` (stepwise regression), used for feature selection in the P300 work.

Data conventions that recur across notebooks: EEG arrays are typically shaped `(nbSamples, nbChannels, nbTrials)`; in `exercise_3` the C4 channel (index 5) is broken (all zeros) and is excluded from analysis.

`pixi.lock` is marked `merge=binary` in [.gitattributes](.gitattributes) — never hand-edit it; regenerate via pixi.

Commit messages follow the pattern `feat: <description>` (see git log).
