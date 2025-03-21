## 🗂️ Phase 1: Data Preparation (ORPO Dataset Generation with KNIME)

---

### **Step 1: CSV Reader Input Data **
- **Input:** `Tweets.csv` from Kaggle Airline Sentiment Dataset.
- **Function:** Reads the dataset and extracts two columns:  
  `text` (tweet content) and `airline_sentiment` (label: positive, neutral, negative).

---

### **Step 2: Neutral Sentiment Split (Fig.7)**

- **Row Filter (KNIME Node):** Extracts rows where `airline_sentiment = neutral`.
- **Column Appender (Branch A):** Adds `"positive"` as `rejected`.
- **Column Appender (Branch B):** Adds `"negative"` as `rejected`.
- **Concatenate Node:** Merges both neutral branches.
- **Missing Value Node:** Fills empty values if needed.

➡️ **Output:** Neutral samples paired with both `"positive"` and `"negative"` rejected labels.

---

### **Step 3: Instruction Injection & Input Construction (Fig.8)**

- **Table Creator:** Creates system instruction:  
  `"Classify the input using one of these labels: neutral, positive, and negative."`

- **Column Appender:** Appends `instruction` and `text` (as `input`) fields to dataset.

➡️ **Output:** Dataset containing `instruction`, `input`, `accepted`, and `rejected`.

---

### **Step 4: Column Renamer, Resorter & Shuffle**

- **Column Renamer:** Standardize column names.
- **Column Resorter:** Ensure order as:  
  `instruction`, `input`, `accepted`, `rejected`
- **Shuffle Node:** Randomize rows.

---

### **Step 5: JSON Conversion & Export (Fig.9)**

- **Concatenate Node:** Merge datasets for neutral, positive, and negative sentiments.
- **Table to JSON:** Convert to JSON format.
- **JSON Writer:** Export as `orpo_train.json`.

➡️ **Final Output:** ORPO-ready dataset for model fine-tuning.

---

### 🎯 **JSON Output Example**

```json
{
  "instruction": "Classify the input using one of these labels: neutral, positive, and negative.",
  "input": "@USAirways Do you have a phone Nbr for Refund Dept?",
  "accepted": "neutral",
  "rejected": "positive, negative"
}
