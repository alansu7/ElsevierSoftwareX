## 🧪 Model Fine-tuning (Colab + Unsloth + ORPO)

![ORPO Fine-tuning Workflow](../img/ORPO%20Fine-Tuning%20Workflow.png)

---

### **1. Environment Setup (Unsloth Framework)**

- **Install Unsloth nightly**:
  - Ensure the latest development version is installed with pip.
  - Avoid verbose notebook outputs using subprocess (e.g., `capture_output=True`).
- **Import FastLanguageModel**:
  - Load pre-trained models with 4-bit quantization for optimized memory usage.
  - RoPE scaling and dtype customization (Float16 / BFloat16) based on GPU type.
- **Integrate LoRA (Low-Rank Adaptation)**:
  - Enable parameter-efficient fine-tuning (PEFT).
  - Only 1-10% of model parameters are updated, drastically reducing memory footprint.
  - Optional: Gradient checkpointing, LoftQ, and RSLora for additional optimization.

---

### **2. Data Preparation**

- Prepare ORPO-style datasets with the following columns:
  - `Instruction`: e.g., `"What is 2+2?"`
  - `Accepted`: `"The answer is 4"`
  - `Rejected`: `"The answer is 5"`
- Format data using an Alpaca-style prompt template (`prompt`, `chosen`, `rejected`).
- Load and preprocess datasets from Hugging Face (`iecjsu/airlineORPO_all`).
- **Print and validate samples**:
  - Visualize prompt structure and check Accepted/Rejected responses using `pprint`.

---

### **3. Training the Model**

- **Trainer configuration**:
  - Create an **ORPOTrainer** instance from Hugging Face TRL.
  - Adjust hyperparameters (e.g., `batch_size`, `max_length`, `beta`, `fp16/bf16`, etc.).
- **Training process**:
  - Invoke `orpo_trainer.train()` to fine-tune the model.
  - Automatically logs loss, gradients, and learning rates.
  - Save checkpoints to `output_dir` after completion.

---

### **4. Inference**

- **Fast Inference with FastLanguageModel**:
  - Format inputs using Alpaca templates.
  - Utilize `FastLanguageModel.for_inference()` for efficient generation.
  - Decode outputs with `tokenizer.decode()` or stream tokens using **TextStreamer**.

---

### **5. Saving & Exporting the Model**

- **Option 1: Save LoRA adapter only**:
  - `model.save_pretrained("lora_model")`
  - Push adapter to Hugging Face with `push_to_hub()`.

- **Option 2: Merge & save full model**:
  - Save merged model in **float16** (`merged_16bit`) or **int4** (`merged_4bit`) formats.

- **Option 3: Export to GGUF / llama.cpp formats**:
  - Support for `q8_0`, `q4_k_m`, and `f16` quantization methods.
  - Save locally with `save_pretrained_gguf()` or push to Hugging Face via `push_to_hub_gguf()`.

---

### **6. Deploying the Model**

- **Local deployment**:
  - Integrate `.gguf` models into **GPT4All** or **llama.cpp** CLI workflows.
- **GPT4All**:
  - GUI-based interface for easy interaction.
- **llama.cpp**:
  - Command-line tool supporting custom parameters and batch processing.





