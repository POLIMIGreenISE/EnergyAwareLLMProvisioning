# Energy-Aware Provisioning of Language Models in Information System Workflows

Given a batch of requests from several clients, the provider selects which language models to load on its GPUs and assigns every request to one of them. The objective is to minimize the total energy of the period under quality, budget and capacity constraints. The problem is formulated as a capacitated facility-location MILP (FL-MILP) and compared with six baselines.

## Notebooks

Run them in this order, from the root of the repository:

- `01_build_request_pool.ipynb`: request pool, 5,042 requests from seven public datasets.
- `02_build_model_pool.ipynb`: model pool, 197 candidates and the core of 9 models with energy, quality and price.
- `03_length_estimator.ipynb`: output-length estimator and the estimates for every request.
- `04_length_estimator_timing.ipynb`: time of the length estimation.
- `05_routing_pipeline.ipynb`: one batch, end to end: parameters, FL-MILP, baselines, checks.
- `06_experiments.ipynb`: experiment campaign, thesis figures and tables.

Descriptive analyses: `analysis_request_pool.ipynb`, `analysis_model_pool.ipynb`, `analysis_length_profile.ipynb`.

The saved outputs of every notebook are those of the runs reported in the thesis. The notebooks were run on Google Colab with the data on Google Drive, so the paths printed in the saved outputs refer to that folder.

## Data and results

- `data/request_pool/`: request pool and its metadata (source datasets pinned to the commits in `01_build_request_pool.ipynb`).
- `data/model_pool/`: model pool v1.3 (used by the pipeline) with its core, the ML.ENERGY batch curve and the source snapshots; v1.2 is kept for `analysis_model_pool.ipynb` and differs only in the price snapshot.
- `data/request_estimates/`: estimated output lengths and the timing of the estimator. The weights of the classifier are not included (about 250 MB): `03_length_estimator.ipynb` writes them to `data/request_estimates/length_classifier/`, which `04_length_estimator_timing.ipynb` needs.
- `data/length_profile/`: tables of `analysis_length_profile.ipynb`.
- `results/pipeline/`: outputs of `05_routing_pipeline.ipynb` (N = 200, seed 42).
- `results/experiments/`: `runs.csv`, `methods.csv`, `models.csv`, summary tables, `logs/` and `thesis_figures/`.

Prices come from an OpenRouter snapshot of 20 October 2026; energy, throughput and power from ML.ENERGY (`mlenergy-data==0.4.0`); quality from the Open LLM Leaderboard.

## Setup

```bash
pip install -r requirements.txt
```

The notebooks were run on Google Colab (GPU for `03` and `04`; CPU for the others). On Colab, clone the repository and move into it before running a notebook:

```python
!git clone https://github.com/POLIMIGreenISE/EnergyAwareLLMProvisioning.git
%cd EnergyAwareLLMProvisioning
```

Notebooks `01`, `02`, `03` and `analysis_length_profile` need a Hugging Face token: on Colab the secret `HF_TOKEN`, elsewhere the environment variable `HF_TOKEN`. The token must have access to the gated repositories `meta-llama/Llama-3.1-8B-Instruct` and `lmsys/lmsys-chat-1m`.

`06_experiments.ipynb` reads `05_routing_pipeline.ipynb` and runs it once per configuration. Finished runs are read from `results/experiments/runs.csv` and skipped, so the figures and tables can be rebuilt without running the campaign again: run the setup cells, skip the two run cells, then run "Results by experiment" and "Thesis figures and tables".
