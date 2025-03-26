# KLSAS
This project presents an innovative pipeline combining **Odds Ratio Preference Optimization (ORPO)**, **Unsloth**, and the **KNIME Analytics Platform** to efficiently fine-tune large language models (LLMs) for multi-class sentiment analysis with minimal resource consumption. The workflow is structured as follows:

### 1. 🔍 Data Preparation
- Leveraged the public "Tweets.csv" dataset (Kaggle) by extracting key fields: `text` (tweets) and `airline_sentiment` (positive, negative, neutral labels).
- Converted data into ORPO-compatible format with `instruction`, `input`, `accepted`, and `rejected` fields to enable preference-based learning and improve multi-class discrimination capability.

### 2. ⚙️ Model Fine-tuning (ORPO + Unsloth)
- Adopted **Mistral-Nemo-Instruct-2407** as the base model, fine-tuned using the **Unsloth** framework with ORPO methodology.
- Applied optimization techniques including **LoRA (Low-Rank Adaptation)**, **4-bit quantization**, and **gradient checkpointing** to enhance memory efficiency and reduce training time while preserving model performance.

### 3. 🚀 KLSAS System Deployment (KNIME)
- Integrated the ORPO fine-tuned model into a no-code environment using the **KNIME Analytics Platform** to build the **Knime LLM-based Sentiment Analysis System (KLSAS)**.
- Developed a modular visual workflow for tasks such as data ingestion, model inference, and performance evaluation, ensuring accessibility for non-technical users.

### 4. 📊 Experimental Validation
- Conducted experiments on real-world data, achieving **88% recall for neutral sentiment** classification.
- Outperformed baseline methods including BERT and traditional machine learning models (e.g., TF-IDF + Logistic Regression) in both accuracy and resource efficiency.

### ✨ Highlights
- Unified the LLM fine-tuning pipeline by merging **instruction-tuning** and **preference alignment** using ORPO.
- Delivered a practical, enterprise-ready sentiment analysis system with scalable deployment on the **KNIME** platform.
- Reduced computational overhead while enhancing accuracy for nuanced, multi-class sentiment tasks.

---

## 📦 System Requirements & Dependencies

Before running the KLSAS workflow, ensure the following tools and platforms are installed and up-to-date:

| **Tool**        | **Description**                                                                 | **Download / Link**                                                                                     |
|------------------|---------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| **KNIME**         | Visual workflow platform used to build and deploy the KLSAS system.              | 🔗 [KNIME (Latest Version)](https://www.knime.com/)                                                      |
| **GPT4ALL**       | Offline LLM runtime for loading `.gguf` models used during KNIME integration.    | 🔗 [GPT4ALL (Latest Version)](https://gpt4all.io/index.html?ref=localhost)                              |
| **Unsloth**       | Memory-efficient fine-tuning framework for LLMs, used in Phase 2.                | 🔗 [Unsloth GitHub](https://github.com/unslothai/unsloth?tab=readme-ov-file)                             |
| **Hugging Face**  | LLM model hub and hosting platform for downloading pretrained/fine-tuned models.| 🔗 [Hugging Face](https://huggingface.co/)                                                               |

---


## KLSAS - Workflow Overview

![Project Workflow](img/Workflow%20for%20Prompt-Based%20Sentiment%20Classification%20with%20LLMs.png)

---

## 🔄 Simplified Project Workflow

The system is structured into three key phases to streamline LLM fine-tuning and deployment for multi-class sentiment analysis:

### **Phase 1: Data Preprocessing (KNIME)**
- **Input:** Raw Twitter dataset (`Twitter.csv`)
- **Process:** Utilizing KNIME, data is split into training and testing sets.
- Data points are converted into a prompt-based format:
  - `Instruction`: Defines the classification task
  - `Input`: Raw tweet text
  - `Accepted`: Correct sentiment label
  - `Rejected`: Incorrect sentiment labels
- **Output:** A structured dataset (`ORPO-ready.csv`) ready for model fine-tuning.
&nbsp;

🔗 **For the detailed workflow, see** [Phase 1: Data Preprocessing(KNIME)](./Data_Preprocessing)

---

### **Phase 2: Model Fine-tuning (Colab + Unsloth + ORPO)**

- **Environment:** Google Colab with the Unsloth optimization framework.
- **Base Model:** Mistral-Nemo-Instruct-2407.
- **Technique:** Fine-tuning via ORPO to unify supervised fine-tuning and alignment into one stage.
- **Result:** An optimized fine-tuned model (**FT-LLM**) for multi-class sentiment classification.
&nbsp;

🔗 **For the detailed workflow, see** [Phase 2: Model Fine-tuning (Colab + Unsloth + ORPO)](./Model_Fine-tuning)

---

### **Phase 3: KLSAS Deployment (KNIME)**

- **Deployment:** Integration of FT-LLM into **KNIME Analytics Platform** via a no-code workflow.
- **Application:** Real-time sentiment analysis on customer feedback and online reviews.
- **Outputs:** Model-generated sentiment labels (positive, neutral, negative) and comprehensive performance metrics.
&nbsp;

🔗 **For the detailed workflow, see** [Phase 3: KLSAS Deployment (KNIME)](./KLSAS_Deployment)

---

🎯 **Objective:** Deliver an enterprise-ready, low-code sentiment analysis solution by combining ORPO fine-tuning with KNIME visual workflows.
