📅 News

🚀 [04/10/2025] We update the training framework of Sptial-r1, and release [Sptial-R1-exp-1500](https://huggingface.co/RUBBISHLIKE/Sptial-R1-exp-1500), which is trained on SR dataset (91k). 1500 means the training steps. We will release final model after finshing training process.

🚀 [04/07/2025] We release checkpoint of [Spatial-R1-tiny](https://huggingface.co/RUBBISHLIKE/Spatial-R1-tiny)

🚀 [04/02/2025] We share the draft version of Spatial-R1 on arxiv.

🚀 [03/31/2025] We release evluation and training code.



# Spatial-R1
The first MLLM trained using GRPO for spatial reasoning in videos

data sample

{"problem_id": 1, "problem": "Question:\nHow many monitor(s) are in this room?", "data_type": "video", "problem_type": ["object_counting"], "solution": "<answer>7</answer>", "video_filename": "scannet/scene0653_00.mp4"}

prediction example:

predicted_answer: <think>There are three chairs in the room. Two are placed around the dining table, and one is placed near the window.</think> <answer>3</answer>

ground truth: 3

We curate a dataset tailored for video spatial reasoning based on ScanNet and train Qwen2-VL-7B using grpo method. It achieves promising gains in VSI-Bench. **Spatial-R1-Tiny** is trained on the tiny set of SR dataset, which contains 9k samples. 

🏆 Performance Comparison 

| Model                      | obj_appearance_order | object_abs_distance | object_counting | object_rel_distance | object_size_estimation | room_size_estimation | route_planning | object_rel_direction | Overall_acc |
| :------------------------- | :------------------- | :------------------ | :-------------- | :------------------ | :--------------------- | :------------------- | :------------- | :------------------- | :---------- |
| Qwen2.5-VL-7B(zero-shot) | 32.69                | 17.48               | 33.96           | 35.77               | 51.85                  | 36.60                | 29.38          | 37.73                | 34.43       |
| Qwen2.5-VL-7B(CoT)       | 30.42                | 12.10               | 15.84           | 31.83               | 19.12                  | 24.24                | 34.54          | 34.68                | 25.35       |
| Spatial-R1-Tiny      | 36.76                | 32.99               | 62.94           | 38.15               | 58.12                  | 31.04                | 28.87          | 32.72                | 41.81       |
|Sptial-R1-exp-1500    |57.28                 |29.78                |63.24            | 39.15               |60.76                   |45.07                 |28.87           |45.02                 |46.15        |   

## Training
```bash
git clone https://github.com/OuyangKun10/Spatial-R1.git
cd Spatial-R1/spatial-r1

# build environment
conda create -n spatial-r1 python=3.11 
conda activate spatial-r1
bash setup.sh

# qwen video extraction setting, e.g., max frames, resolutions
# Use the [decord] feature to improve speed
cd src/qwen-vl-utils
pip install -e .[decord]
cd ..
```
**Data Preparation**:

1. Download [videos](https://huggingface.co/datasets/RUBBISHLIKE/SR_dataset) of SR_dataset

2. Annotations to be uploaded

   
**Training script for Spatial-r1**
```bash
bash ./src/scripts/run_grpo_video.sh
```
## Evaluation

## Setup

1.  **Environment:** Python 3.8+, CUDA-enabled GPUs.
2.  **Install Libraries:**
    ```bash
    pip install torch pandas numpy pillow accelerate transformers sentencepiece decord flash-attn --no-build-isolation
    ```
3.  **Dataset:** VSI-Bench `.parquet` file and corresponding `.mp4` video files.
4.  **Utilities:** Ensure `qwen_vl_utils.py`, `util.py`, `vsibench_acc.py` are accessible.

## Usage

1.  **Configure:** Edit parameters in the `if __name__ == "__main__":` block:
    *   `parquet_file`, `video_dir`, `output_dir_base`
    *   `model_name` (path or HF ID)
    *   `gpu_ids`, `num_processes`
    *   `num_frames`, `fps`, `target_resolution`, `batch_size`
    *   `SELECTED_PROMPT_TYPE`
    *   Cognitive map settings (`use_cognitive_map`, `offload_cogmap`, paths, keys)
    *   `debug_mode`, `debug_size`
2.  **Run:**
    ```bash
    python eval_qwen.py
    ```

## Output

*   **Results:** `output_dir/model_timestamp/vsibench_results_*.jsonl` (Detailed predictions and metadata).
*   **Logs:** `output_dir/model_timestamp/vsibench_eval_*.log` (Configuration, progress, timings, and final accuracy metrics).

The script automatically calculates and prints/logs overall and per-task accuracy upon completion.

We will release the final version of **SR** dataset after all work is done.
