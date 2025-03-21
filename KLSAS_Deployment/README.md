## 🏭 Phase 3: KLSAS Deployment & Configuration (GPT4ALL + KNIME)

The final phase deploys the fine-tuned model into **KLSAS**, a no-code sentiment analysis pipeline powered by **KNIME** and integrated with **GPT4ALL**. Below is a detailed configuration for each node within the KLSAS workflow, including prompt generation, model integration, and performance evaluation.

---

### **1️⃣ Prompt Preparation (KNIME)**

| **Node**               | **Configuration**                                                                              |
|------------------------|------------------------------------------------------------------------------------------------|
| **CSV Reader**         | Source: Local Mountpoint<br>Path: `your_computer/filename.csv`                                |
| **Row Sampling**       | Method: Absolute = 150<br>Stratified Sampling: by label<br>Random seed = 100                  |
| **Table Creator**      | Column: `instruct`<br>Row0: `"Classify the following text using one of these labels: neutral, positive, and negative:"` |
| **Column Appender**    | Action: Generate new RowIDs                                                                   |
| **Missing Value**      | Fill method: Previous Value for `instruct` column                                              |
| **String Manipulation**| Expression: `join($instruct$, "\"", $text$, "\" ")` <br> Output column: `prompt`              |

---

### **2️⃣ Model Selection (KNIME + GPT4ALL)**

| **Node**                        | **Configuration**                                                                                     |
|----------------------------------|-----------------------------------------------------------------------------------------------------|
| **Credentials Configuration**    | Define `username` & `password` <br> Save password (weak encryption)                                |
| **OpenAI Authenticator**         | API Key: from `credentials`<br> Base URL: `http://localhost:4891/v1` (GPT4ALL local API)           |
| **OpenAI Chat Model Connector**  | Model ID: `Mistral-Nemo-Instruct-2407.gguf` or `Mistral-Nemo-IT-2407-ORPOall-f16`<br> Max tokens: 100<br> Temperature: 0.2 |

---

### **3️⃣ Model Inference**

| **Node**            | **Configuration**                             |
|---------------------|-----------------------------------------------|
| **LLM Prompter**    | Input column: `prompt`<br> Output: `Response` |

---

### **4️⃣ Performance Evaluation**

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


