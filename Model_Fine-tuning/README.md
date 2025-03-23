## 🧪 Model Fine-tuning (Colab + Unsloth + ORPO)

![ORPO Fine-tuning Workflow](../img/ORPO%20Fine-Tuning%20Workflow.png)

---
🚀 We recommend using Colab to run Unsloth, which helps simplify setup and saves some extra steps. You can access our Colab notebook here:
👉[Colab Notebook Link](https://drive.google.com/file/d/1_W-koRTRdwlDyEqEvGcgVH3h7sO4EpsK/view?usp=drive_link)
### 🔄 Customizing the Base Model

To change the base model, go to Hugging Face Models, select your desired model, copy its model name (for example, "TheBloke/Mistral-7B-Instruct-v0.2-GPTQ"), and replace the original model name in the model_name field of the following code:

![model_name](../img/model_name.png)
```python
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name = "unsloth/Mistral-Nemo-Instruct-2407-bnb-4bit",
    max_seq_length = max_seq_length,
    dtype = dtype,
    load_in_4bit = load_in_4bit,
)
```
To load a custom dataset, go to Hugging Face Datasets, copy the dataset name (for example "lvwerra/stack-exchange-paired"), and replace the "iecjsu/airline-sentiment-ORPO-train" string inside load_dataset("...") as shown below:

![Data_name](../img/Data_name.png)
```python
from datasets import load_dataset
dataset = load_dataset("iecjsu/airline-sentiment-ORPO-train")["train"]
dataset = dataset.map(format_prompt)
```
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
```python
# Save to 16bit GGUF
if True: model.save_pretrained_gguf("Mistral-Nemo-IT-2407-ORPOall-f16", tokenizer, quantization_method="f16")
if True: model.push_to_hub_gguf("Mistral-Nemo-IT-2407-ORPOall-f16", tokenizer, quantization_method="f16", token="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")
```
Use `model.save_pretrained_gguf()` to export your fine-tuned model in GGUF format.

- The `first argument` specifies the custom name for your model.
- The `second argument` defines the quantization format (e.g., `"f16"`, `"q4_k_m"`, `"q8_0"`), where a larger number indicates more parameters retained.

Then, use `model.push_to_hub_gguf()` to upload the model to your Hugging Face repository.

- The `first argument` is your custom model name.
- The `second argument` is the GGUF format as above.
- The `third argument` is your personal Hugging Face Access Token.
---

Finally, download the exported GGUF file from Hugging Face to your local machine and import it into GPT4ALL for KNIME integration.
![model_name](../img/HG_model.png)
> ⚠️ Make sure the downloaded GGUF model is placed in the same directory specified in your GPT4ALL model path settings.
![GPT4ALL](../img/GPT4ALL.png)
