## 🧪 Model Fine-tuning (Colab + Unsloth + ORPO)

![ORPO Fine-tuning Workflow](../img/ORPO%20Fine-Tuning%20Workflow.png)

---

### 1. Environment Setup (Unsloth)

- Install Unsloth nightly build, uninstall previous versions, and suppress verbose outputs in the notebook.
- Import `FastLanguageModel` from Unsloth.
- Load the base model in **4-bit quantization** with RoPE scaling enabled and dtype set (float16 for T4/V100 or bfloat16 for A100).
- Apply **LoRA** adapters for parameter-efficient fine-tuning (PEFT), using ~1-10% trainable parameters.
- Optional memory optimizations include gradient checkpointing, LoftQ, and RSLora.

---

### 2. Data Preparation (ORPO Dataset)

- Prepare an ORPO-style dataset containing:
  - `Instruction`: The task instruction.
  - `Accepted`: The preferred response.
  - `Rejected`: The non-preferred response.
  
- Format into Alpaca-style schema (`prompt`, `chosen`, `rejected`).
- Load dataset from Hugging Face: `iecjsu/airlineORPO_all`.
- Inspect sample data using `pprint()` to validate the formatting.
- Import **PatchDPOTrainer** to enhance reward modeling statistics in the training pipeline.

---

### 3. Model Fine-tuning (ORPOTrainer)

- Configure **ORPOTrainer** with the following settings:
  - `beta`: Preference ranking strength (e.g., 0.1).
  - `max_length`, `max_prompt_length`, `max_completion_length`: Control input-output token lengths.
  - `batch_size`: Set for T4 compatibility.
  - `fp16` / `bf16`: Enable mixed precision.
  - `optim`: Use `adamw_8bit` optimizer for memory efficiency.
  - `max_steps`: 60 steps for testing (or `num_train_epochs=1` for full training).
  - Logging enabled at custom intervals.

- Start the fine-tuning process with `orpo_trainer.train()`.

---

### 4. Inference (FastLanguageModel)

- Switch model to inference mode with `FastLanguageModel.for_inference()`.
- Format prompts in Alpaca style and generate outputs via `model.generate()`.
- Optionally use **TextStreamer** to stream tokens in real-time to the console.

---

### 5. Saving & Exporting the Model

- Save the LoRA adapter locally (`save_pretrained()`) or push to Hugging Face (`push_to_hub()`).
- Merge and export the model in:
  - **float16** (`merged_16bit`)
  - **int4** (`merged_4bit`)
  - **LoRA-only** weights.
  
- Export to **GGUF** format for llama.cpp or GPT4All deployment:
  - Supported quantization methods: `q8_0`, `q4_k_m`, `q5_k_m`, `f16`.

---
