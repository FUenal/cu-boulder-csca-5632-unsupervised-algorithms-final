# Unsupervised Clustering of Cybersecurity Incidents (VCDB)  
**Uncovering Hidden Threat Patterns with Unsupervised Learning**

---

## 1. Project Overview

This project applies unsupervised learning techniques to real-world cybersecurity incident reports from the **VERIS Community Database (VCDB)**. The primary goal is to move beyond simple descriptive statistics and identify distinct attack profiles or "threat archetypes" based on various incident characteristics such as actor types, actions taken, and target assets.

By uncovering these hidden patterns, this work aims to:
- Enhance cyber threat intelligence
- Inform strategic defense planning
- Optimize SOC triage processes

The absence of predefined labels makes this a classic **unsupervised learning problem**, well suited to clustering algorithms.

![](<./pic1.png>)

---

## 2. Dataset

The dataset used is the **VERIS Community Database (VCDB)**, an open-source collection of structured cybersecurity incident data.

**Key characteristics:**
- **Source:** [VCDB GitHub Repository](https://github.com/vz-risk/VCDB)
- **Format:** JSON files, each representing a single incident
- **Content:**
  - `actor.external.variety`, `actor.external.motive`
  - `action.hacking.variety`, `action.malware.variety`
  - `asset.assets`
  - `victim.industry`
  - `impact.overall_rating`

The dataset’s rich categorical structure makes it ideal for unsupervised clustering.

---

## 3. Methodology

### 3.1. Data Ingestion & Preprocessing
- **Flattening JSON** into a Pandas DataFrame using `json_normalize`
- **Helper Functions:** `flatten_list`, `extract_asset_variety` for parsing nested lists/dicts
- **MultiLabelBinarizer** used for one-hot encoding multi-label categorical features
- **Missing Data:** NaNs from one-hot encoding filled with 0
- **Duplicates Removed:** 5205 duplicate incidents were removed

📊 **Final Shape:** 4706 unique incidents, 910 features

---

### 3.2. Exploratory Data Analysis (EDA)
- Frequency analysis of actors, actions, asset types, industries
- Identification of widespread "Unknown" values
- Early detection of attack patterns: e.g., stolen creds, web exploits, hacktivism

![](<./pic1.png>)
---

### 3.3. Dimensionality Reduction (PCA)
- **Purpose:** Visualize high-dimensional data
- **Method:** PCA to reduce 910 features to 2D
- Note: PCA captures only a portion of variance but is effective for cluster visualization

![](<./pic2.png>)

---

### 3.4. Clustering Algorithms
- **KMeans Clustering** (k=4)
- **Agglomerative Clustering** (k=4)

![](<./pic3.png>)

---

### 3.5. Hyperparameter Optimization
- **Optimal k:** Evaluated using:
  - **Elbow Method**
  - **Silhouette Score**
- **Chosen k = 4**

---

## 4. Key Findings & Results

KMeans clustering successfully identified **four threat archetypes**.  
📈 **Silhouette Scores:**
- KMeans: `0.0893`
- Agglomerative: `0.0766`

---

### Identified Clusters:

#### 🟦 Cluster 0: Credential/Data Theft by Organized Crime
- **Top Attributes:** `actor_Unknown`, `actor_Organized crime`, `action_hacking_Use of stolen creds`, `asset_S - Database`, `asset_M - Documents`
- **Insight:** Common data exfiltration with attribution challenges

---

#### 🟨 Cluster 1: Web Application Exploitation
- **Top Attributes:** `asset_S - Web application`, `action_hacking_Exploit vuln`, `actor_Organized crime`
- **Insight:** Targeted external attacks on web-facing services

---

#### 🟥 Cluster 2: End-User Device Compromise
- **Top Attributes:** `asset_U - Desktop or laptop`, `asset_U - Laptop`, `asset_U - Desktop`
- **Insight:** Compromise via malware, social engineering, or theft

---

#### 🟩 Cluster 3: Activist-Inspired External Attacks
- **Top Attributes:** `actor_Activist`, `action_hacking_Denial of service`, `asset_S - Database`
- **Insight:** Broader range of attacks often with non-financial motives

---

## 5. Limitations

- **"Unknown" Values:** High proportion reduces cluster clarity
- **Bias Toward U.S. Public Sector:** Generalizability may be limited
- **Feature Sensitivity:** Clustering depends on how features are engineered

---

## 6. Future Work

- **🕒 Time-Based Clustering:** Add incident timestamps to detect evolving threats
- **🔀 Hybrid Models:** Combine unsupervised clustering with supervised prediction
- **🔍 Advanced Clustering:** Try DBSCAN, GMM, or hierarchical Bayesian models
- **🧠 Explainability:** Use SHAP/LIME for cluster interpretability
- **🔗 External Data:** Enrich with MITRE ATT&CK mappings or threat intel feeds

---

## 7. Project Structure

```

.
├── csca-5632-unsupervised-algorithms-final-2.ipynb  # Main notebook
├── data/
│   └── vcdb-json-1/
│       └── VCDB-master/
│           └── data/json/validated/
├── README.md
└── presentation\_script.md  # Optional

````

---

## 8. How to Run/Reproduce

```bash
# Clone the repository
git clone https://github.com/FUenal/cu-boulder-csca-5632-unsupervised-algorithms-final/tree/main?tab=readme-ov-file
cd [YOUR_REPO_NAME]

# Download VCDB dataset
git clone https://github.com/vz-risk/VCDB.git data/vcdb-json-1/VCDB-master

# Set up the environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt

# Launch notebook
jupyter notebook csca-5632-unsupervised-algorithms-final-2.ipynb
````

> Ensure JSON files are located at:
> `data/vcdb-json-1/VCDB-master/data/json/validated/`

---

## 9. Deliverables

* 📓 **Notebook:** `csca-5632-unsupervised-algorithms-final-2.ipynb`
* 🎥 **Video Presentation:** \[Insert Link]
* 📁 **GitHub Repository:** \[Insert Link](https://github.com/FUenal/cu-boulder-csca-5632-unsupervised-algorithms-final)

---

## 10. References

* [VCDB GitHub](https://github.com/vz-risk/VCDB)
* [VERIS Framework](https://verisframework.org)
* [Scikit-learn](https://scikit-learn.org)
* [Pandas](https://pandas.pydata.org)
* [Matplotlib](https://matplotlib.org)
* [Seaborn](https://seaborn.pydata.org)

---

## 11. License

This project is licensed under the **MIT License** — see `LICENSE` for details.

---

## Author 

**Fatih Uenal**

🎓 MSc Computer Science & AI, CU Boulder

🌐 Webpage: https://dataiq.netlify.app/

🔗 LinkedIn: https://www.linkedin.com/in/fatih-uenal/

📊 Kaggle Notebook: https://www.kaggle.com/code/fatihuenal/csca-5632-unsupervised-algorithms-final-2



