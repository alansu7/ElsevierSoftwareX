![KNIME workflow for the development of KLSAS](../img/KNIME%20workflow%20for%20the%20development%20of%20KLSAS.png)
## 🏭 Phase 3: KLSAS Deployment & Configuration (GPT4ALL + KNIME)

The final phase deploys the fine-tuned model into **KLSAS**, a no-code sentiment analysis pipeline powered by **KNIME** and integrated with **GPT4ALL**. Below is a detailed configuration for each node within the KLSAS workflow, including prompt generation, model integration, and performance evaluation.

---

### **1️⃣ Prompt Preparation (KNIME)**
![Prompt Preparation](../img/Prompt%20Preparation.png)
### 🎯 Purpose:
Pipeline for generating structured prompts from raw text reviews.

### ⚙️ What It Does:
- Ingests **150 airline reviews** from a CSV.
- Performs **stratified sampling** by sentiment labels to ensure balanced representation.
- Appends a **instruction**:
  > `Classify the following text using one of these labels: neutral, positive, and negative:`
- Handles **missing values** in instructions column.
- Concatenates the instruction with each text input to create a **standardized prompt**.

✅ Enables consistent prompt formatting for multi-class sentiment classification (`neutral`, `positive`, `negative`).
| **Node**               | **Configuration**                                                                              |
|------------------------|------------------------------------------------------------------------------------------------|
| **CSV Reader**         | Source: Local Mountpoint<br>Path: `your_computer/filename.csv`                                |
| **Row Sampling**       | Method: Absolute = 150<br>Stratified Sampling: by label<br>Random seed = 100                  |
| **Table Creator**      | Column: `instruct`<br>Row0: `"Classify the following text using one of these labels: neutral, positive, and negative:"` |
| **Column Appender**    | Action: Generate new RowIDs                                                                   |
| **Missing Value**      | Fill method: Previous Value for `instruct` column                                              |
| **String Manipulation**| Expression: `join($instruct$, "\"", $text$, "\" ")` <br> Output column: `prompt`              |

---

### **2️⃣ Model Selection (Local GPT4ALL LLM Connector)**

![Local GPT4ALL LLM Connector.png](../img/Local%20GPT4ALL%20LLM%20Connector.png)

### 🎯 Purpose:
Directly connects to a locally deployed GPT4ALL model (`.gguf`) without requiring credentials or API endpoints.

### ⚙️ What It Does:
- Uses the **Local GPT4All LLM Connector** node to load a `.gguf` model from local disk.
- No need for authentication or API key — the model runs entirely offline.
- Configures key inference parameters like **max response tokens** and **temperature**.

✅ Supports fully offline LLM inference within KNIME using a locally deployed model.
| **Node**                     | **Configuration**                                                                                  |
|-----------------------------|----------------------------------------------------------------------------------------------------|
| Local GPT4All LLM Connector | **Model path**: `E:\LLMmodel\models\qwen2-7b-instruct-q4_0.gguf`<br>**Maximum response length (token)**: `250`<br>**Context length**: `2048`<br>**Temperature**: `0.2`<br>**Top-k sampling**: `20`<br>**Top-p sampling**: `0.15`<br>**Prompt batch size**: `128`<br>**Device**: `cpu` (*use `cuda` if GPU is available*) |
> 📌 **Note:** Ensure your selected `.gguf` model is compatible with **GPT4ALL** and that the file path is valid on your machine.
---
### **3️⃣ Model Inference**
![Model Inference](../img/Model%20Inference.png)
### 🎯 Purpose:
Uses the fine-tuned model to classify sentiment based on input prompts.

### ⚙️ What It Does:
- Sends each **prompt** to the LLM via the **LLM Prompter Node**.
- Retrieves and stores model **predictions** in the response column.

✅ Provides fully automated **sentiment classification outputs** directly inside KNIME.
| **Node**            | **Configuration**                             |
|---------------------|-----------------------------------------------|
| **LLM Prompter**    | Input column: `prompt`<br> Output: `Response` |

---

### **4️⃣ Performance Evaluation**
![Model Performance Evaluation.png](../img/Model%20Performance%20Evaluation.png)
### 🎯 Purpose:
Evaluates model predictions and generates key performance metrics.

### ⚙️ What It Does:
- Normalizes and cleans raw model outputs.
- Maps responses to target sentiment classes (with fallback to `"unclassified"`).
- Computes **confusion matrix** and derives **recall/accuracy** per label.
- Compares predictions to ground truth (`label vs response`).
- Visualizes results with KNIME’s native reporting tools.

✅ Ensures **interpretability** with detailed error analysis and robust accuracy assessment.

| **Node**              | **Configuration**                                                                 |
|-----------------------|-----------------------------------------------------------------------------------|
| **Column Filter**     | Keep columns: `text`, `label`, `prompt`                                           |
| **Joiner**            | Join on: `RowID` (left and right tables)                                          |
| **Column Expressions**| Clean-up: `removeChars(lowerCase(column("Response")))`                            |
| **Rule Engine**       | Rule mapping:<br>`*neutral* => neutral`<br>`*positive* => positive`<br>`*negative* => negative`<br>Fallback: `unclassified` |
| **Scorer**            | Visualization: Confusion Matrix<br> Input: `label` (actual), `response` (predicted) |
| **Column Resorter**   | Order: `text`, `label`, `instruct`, `Response`, `response`                        |
| **Table Viewer**      | Output columns: `label`, `response` + RowIDs                                      |

---

### 🎯 **End Goal**

- Deploy an ORPO-fine-tuned LLM locally via **GPT4ALL API** and seamlessly integrate it into KNIME visual workflows.
- Achieve automated sentiment classification with full interpretability via confusion matrix and structured reporting.

---

💡 **Tip:** Make sure the GPT4ALL local server is running and listening on port `4891` to ensure smooth integration with the KNIME OpenAI connector.

---


