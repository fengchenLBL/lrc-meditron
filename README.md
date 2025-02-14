# Meditron Setup and Inference Guide

This guide provides step-by-step instructions to set up a Conda environment for Meditron, install Meditron, and run inference using the `inference_pipeline.sh` script.

## Request a GPU node with four A40 (46GB), A100 (80GB), or H100 (80GB) GPUs
* How to specify desired GPU card(s)?
  * via SLURM: https://scienceit-docs.lbl.gov/hpc/systems/einsteinium/
  * via LRC Open OnDemand (OOD) jupyter server: https://lrc-ondemand.lbl.gov/

## 1. Setup Conda Environment for Meditron
To ensure a clean and isolated environment, create a Conda environment before installing dependencies:

```bash
conda create --name meditron python=3.10 -y
conda activate meditron
```

## 2. Install Meditron
Clone the Meditron repository and install required dependencies:

```bash
git clone https://github.com/epfLLM/meditron.git
cd meditron
pip install --upgrade pip
pip install -r requirements.txt
```

## 3. Run Inference with Meditron-7B on A40 or A100 GPUs
Navigate to the evaluation directory and run the inference pipeline:

```bash
cd evaluation
bash inference_pipeline.sh -b medmcqa -c meditron-7b -s 5 -m 1
```

### What This Command Does:
1. **Downloads the `medmcqa` dataset** automatically.
2. **Downloads the Meditron-7B model weights** from Hugging Face if not already available locally.
3. **Runs inference using the vLLM engine** to generate responses based on the dataset.

### Example output: [medmcqa-meditron-7b-5-shot.jsonl.tar.gz](medmcqa-meditron-7b-5-shot.jsonl.tar.gz)

## 4. Running Meditron-70B on A100 or H100 (80GB) GPUs

Meditron-70B can now successfully run on **H100 (80GB) GPUs** after reducing `max_num_seqs` from **1024 to 512** to prevent Out-of-Memory (OOM) errors.

### Run Inference with Meditron-70B on A100 or H100 GPUs:
```bash
cd evaluation
bash inference_pipeline.sh -b medmcqa -c meditron-70b -s 5 -m 1
```
### Example output: [medmcqa-meditron-70b-5-shot.jsonl.tar.gz](medmcqa-meditron-70b-5-shot.jsonl.tar.gz)

### GPU Memory Optimization:
`max_num_seqs` is now **adjusted** based on available GPU memory:
- **>96GB** → `max_num_seqs = 1024`
- **>80GB** → `max_num_seqs = 512`


## 5. Troubleshooting

### a) Model or Dataset Not Found
If the script fails due to missing model or dataset: 
* Replace `inference_pipeline.sh` with this file [inference_pipeline.sh](inference_pipeline.sh)

### b) `use_beam_search` Error
If you see an error related to `use_beam_search`:
* Replace `inference.py` with this file [inference.py](inference.py)
* Or, edit `inference.py` and **comment out** this parameter:

```python
# use_beam_search=True  # Comment out or remove this line
```

### c) `Evaluation` Error
If you see an error related to `evaluate.py`:
* Replace `evaluate.py` with this file [evaluate.py](evaluate.py)

### d) Out-of-Memory Issues for 7B model
Reduce the batch size:
```bash
bash inference_pipeline.sh -b medmcqa -c meditron-7b -s 5 -m 1 -d 8
```

Monitor GPU usage with:
```bash
nvidia-smi -l
```

### e) Out-of-Memory Issues for 70B model on A100 GPUs
Meditron-70B previously encountered **Out-of-GPU Memory** errors on A100 GPUs (80GB). However, it now runs successfully on **H100 GPUs** after reducing `max_num_seqs` to **512**.

## 6. Conclusion
Following these steps, you can successfully set up Meditron, download necessary datasets, and run inference using **Meditron-7B (on A40, A100)** and **Meditron-70B (on H100 GPUs)**. If you encounter any issues, refer to the troubleshooting section or adjust the pipeline parameters accordingly.

