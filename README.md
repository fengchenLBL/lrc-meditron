# Meditron Setup and Inference Guide

This guide provides step-by-step instructions to set up a Conda environment for Meditron, install Meditron, and run inference using the `inference_pipeline.sh` script.

## Request a GPU node with four A100 (or H100) GPUs with 80GB memory
* How to specify desired GPU card(s)?
  * https://scienceit-docs.lbl.gov/hpc/systems/einsteinium/

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

## 3. Run Inference with Meditron-7B
Navigate to the evaluation directory and run the inference pipeline:

```bash
cd evaluation
bash inference_pipeline.sh -b medmcqa -c meditron-7b -s 5 -m 1
```

### What This Command Does:
1. **Downloads the `medmcqa` dataset** automatically.
2. **Downloads the Meditron-7B model weights** from Hugging Face if not already available locally.
3. **Runs inference using the vLLM engine** to generate responses based on the dataset.

## 4. Troubleshooting

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

### c) Out-of-Memory Issues for 7B model
Reduce the batch size:
```bash
bash inference_pipeline.sh -b medmcqa -c meditron-7b -s 5 -m 1 -d 8
```

Monitor GPU usage with:
```bash
nvidia-smi -l
```
### d) Out-of-Memory Issues for 70B model
* 70B model: __Out of GPU Memory__ when running it on a node with four A100 GPUs with 80GB memory

## 5. Conclusion
Following these steps, you can successfully set up Meditron, download necessary datasets, and run inference using Meditron-7B. If you encounter any issues, refer to the troubleshooting section or adjust the pipeline parameters accordingly. However, Meditron-70B had Out-of-Memory Issues when running on NVIDIA A100(80GB)

