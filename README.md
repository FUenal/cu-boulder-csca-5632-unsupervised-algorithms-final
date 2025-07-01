Unsupervised Clustering of Cybersecurity Incidents (VCDB)
Uncovering Hidden Threat Patterns with Unsupervised Learning
1. Project Overview

This project applies unsupervised learning techniques to real-world cybersecurity incident reports from the VERIS Community Database (VCDB). The primary goal is to move beyond simple descriptive statistics and identify distinct attack profiles or "threat archetypes" based on various incident characteristics such as actor types, actions taken, and target assets. By uncovering these hidden patterns, this work aims to enhance cyber threat intelligence, inform strategic defense planning, and optimize Security Operations Center (SOC) triage processes.

The absence of predefined labels for cybersecurity incident categories makes this a classic unsupervised learning problem, where clustering algorithms are ideal for discovering inherent groupings within the data.
2. Dataset

The dataset used for this project is the VERIS Community Database (VCDB), an open-source collection of real-world cybersecurity incidents. Each incident is described using the VERIS Framework, which provides structured metadata in JSON format.

Key characteristics of the dataset:

    Source: VCDB GitHub Repository

    Format: Individual JSON files, each representing a single incident.

    Content: Includes detailed metadata on:

        Actor types and motives (actor.external.variety, actor.external.motive)

        Action types (e.g., hacking, malware, social; action.hacking.variety, action.malware.variety)

        Asset types impacted (asset.assets)

        Industry of the victim (victim.industry)

        Reported losses or impact (impact.overall_rating)

    Suitability for Unsupervised Learning: The dataset's rich categorical nature and lack of pre-labeled incident categories make it an excellent candidate for unsupervised clustering, allowing for the discovery of emergent patterns.

3. Methodology

The project follows a structured machine learning pipeline:
3.1. Data Ingestion & Preprocessing

    Data Loading: JSON incident files are loaded and flattened into a Pandas DataFrame using json_normalize.

    Handling Nested Structures: Helper functions (flatten_list, extract_asset_variety) are used to parse complex nested lists and dictionaries within columns (e.g., asset.assets) into a flat list of relevant labels.

    One-Hot Encoding: Multi-label categorical features (e.g., actor.external.variety, action.hacking.variety, asset.variety_clean, victim.industry) are transformed into a sparse binary feature matrix using sklearn.preprocessing.MultiLabelBinarizer. Column prefixes are added (e.g., actor_, action_hacking_) to prevent naming conflicts and improve interpretability.

    Missing Data Handling: NaN values resulting from one-hot encoding (indicating the absence of a specific label) are explicitly filled with 0.

    Duplicate Removal: Identical incident profiles (rows) are identified and removed from the feature matrix to ensure that clustering results are not skewed by redundant data.

        Resulting Feature Matrix Shape: (4706, 910) unique incidents after de-duplication.

3.2. Exploratory Data Analysis (EDA)

Before clustering, a thorough EDA was performed to understand the data's distribution, identify common values, and assess data quality:

    Analysis of top categories for external actor types, hacking actions, asset types, and victim industries.

    Identification of significant proportions of "Unknown" values, highlighting data attribution challenges.

    Initial insights into prevalent attack vectors (e.g., web application exploitation, stolen credentials) and common targets (e.g., databases, end-user devices).

3.3. Dimensionality Reduction (PCA)

    Purpose: To reduce the high dimensionality (910 features) of the preprocessed data to a 2-dimensional space for visualization.

    Method: Principal Component Analysis (PCA) was applied.

    Note: While 2D PCA captures a limited amount of total variance in such high-dimensional data, it provides a qualitative view of potential cluster separation.

3.4. Clustering Algorithms

Two popular unsupervised clustering algorithms were employed:

    KMeans Clustering: A centroid-based algorithm that partitions data into k clusters, aiming to minimize the sum of squared distances between data points and their assigned cluster centroid.

    Agglomerative Clustering: A hierarchical clustering method that builds a hierarchy of clusters by progressively merging or agglomerating clusters.

3.5. Hyperparameter Optimization

    Optimal k Selection: The optimal number of clusters (k) for KMeans was determined by evaluating a range of k values (from 2 to 10) using:

        Elbow Method (Inertia): Observing the point where the decrease in inertia (sum of squared distances) begins to slow down.

        Silhouette Score: Measuring the cohesion and separation of clusters, with higher scores indicating better-defined clusters.

    Chosen k: Based on the combined analysis, k=4 was selected as a reasonable number of clusters.

4. Key Findings & Results

The project successfully identified four distinct cybersecurity incident archetypes through KMeans clustering (k=4). The Silhouette Score for KMeans was 0.0893, and for Agglomerative Clustering, it was 0.0766. The consistency between these scores suggests stable clustering patterns within the data.

Identified Threat Archetypes:

    Cluster 0: Credential/Data Theft by Organized Crime

        Characteristics: Dominated by actor_Unknown or actor_Organized crime, frequently involving action_hacking_Use of stolen creds to target asset_S - Database and asset_M - Documents.

        Insight: Represents common data exfiltration attempts, often with attribution challenges.

    Cluster 1: Web Application Exploitation & External Hacking

        Characteristics: High prevalence of asset_S - Web application and action_hacking_Exploit vuln. actor_Organized crime and actor_Unknown are significant.

        Insight: Highlights prevalent external attacks targeting web-facing services through vulnerability exploitation.

    Cluster 2: End-User Device Compromise

        Characteristics: Uniquely characterized by asset_U - Desktop or laptop, asset_U - Desktop, and asset_U - Laptop as primary targets.

        Insight: Suggests incidents stemming from malware, social engineering, or physical theft directly impacting end-user devices.

    Cluster 3: Diverse External Attacks, Often Activist-Related

        Characteristics: Features actor_Activist prominently, alongside actor_Unknown. Actions like action_hacking_Denial of service are more common here. Targets include asset_S - Web application and asset_S - Database.

        Insight: Indicates a broader range of external attacks, often with non-financial motives, characteristic of hacktivism.

5. Limitations

    "Unknown" Values: A significant portion of the dataset contains "Unknown" values for critical fields (e.g., actor types, specific actions). This data sparsity can limit the discriminative power of clusters, potentially grouping incidents based on the absence of information rather than specific patterns.

    Generalizability: The dataset exhibits a reporting bias, particularly towards U.S. public-sector institutions. Findings may not be universally applicable to all industries or geographical regions without further validation on more diverse data.

    Feature Engineering Sensitivity: Clustering results can be sensitive to feature selection and engineering. Different combinations or weighting of features might reveal alternative or more granular patterns.

6. Future Work

    Temporal Analysis: Incorporate incident dates (timeline.incident.year, timeline.incident.month) as features to enable time-series clustering. This would allow for the detection of trends in attacker behavior and the evolution of threat archetypes over time.

    Hybrid Supervised-Unsupervised Models:

        Cluster-based Prediction: Use the identified clusters as features in a supervised learning model to predict other incident attributes (e.g., impact.overall_rating, attribute.confidentiality.data_disclosure).

        Automated Triage: Train a classifier to automatically assign new, unlabeled incidents to the established threat archetypes, streamlining SOC triage.

    Advanced Clustering Techniques: Explore density-based algorithms (e.g., DBSCAN) for better outlier detection and the identification of irregularly shaped clusters, or model-based methods (e.g., Gaussian Mixture Models) to handle varying cluster densities.

    External Data Integration: Enrich incident data by mapping it to external threat intelligence feeds or the MITRE ATT&CK framework. This could lead to more granular and actionable clusters by providing a standardized context for attack techniques.

    Interpretability Tools: Employ Explainable AI (XAI) techniques (e.g., SHAP, LIME) to further dissect what specific features drive membership in each cluster, enhancing the transparency and trustworthiness of the archetypes.

7. Project Structure

.
├── csca-5632-unsupervised-algorithms-final-2.ipynb  # Main Jupyter Notebook with analysis
├── data/                                             # Directory for raw data (e.g., VCDB JSON files)
│   └── vcdb-json-1/                                  # VCDB dataset (as per Kaggle/local setup)
│       └── VCDB-master/
│           └── data/
│               └── json/
│                   └── validated/
│                       └── ... (json files)
├── README.md                                         # This README file
└── presentation_script.md                            # Script for video presentation (optional, if you want to include it)

8. How to Run/Reproduce

To run this project and reproduce the analysis:

    Clone the Repository:

    git clone [YOUR_GITHUB_REPO_URL]
    cd [YOUR_REPO_NAME]

    Download the Dataset:

        The project uses the VERIS Community Database (VCDB). You can download it from its GitHub repository:

        git clone https://github.com/vz-risk/VCDB.git data/vcdb-json-1/VCDB-master

        Ensure the JSON files are located at data/vcdb-json-1/VCDB-master/data/json/validated/ relative to your notebook.

    Set up the Environment:

        It's recommended to use a virtual environment.

    python -m venv venv
    source venv/bin/activate  # On Windows: `venv\Scripts\activate`
    pip install pandas scikit-learn matplotlib seaborn

    Run the Jupyter Notebook:

    jupyter notebook csca-5632-unsupervised-algorithms-final-2.ipynb

    Open the notebook in your browser and run all cells sequentially.

9. Deliverables

    Jupyter Notebook Report: csca-5632-unsupervised-algorithms-final-2.ipynb (This comprehensive notebook details the problem, EDA, model analysis, results, and conclusions).

    Video Presentation: [Insert Link to your MP4/YouTube video here]

    GitHub Repository: [Insert Link to your GitHub repo here]

10. References

    VERIS Community Database (VCDB): https://github.com/vz-risk/VCDB

    VERIS Framework: https://verisframework.org

    Scikit-learn Documentation: For clustering and preprocessing algorithms.

    Pandas Documentation: For data manipulation.

    Matplotlib & Seaborn Documentation: For data visualization.

11. License

This project is licensed under the MIT License - see the LICENSE file for details.
12. Author

Fatih Uenal

    Course: CU Boulder MSc Computer Science & AI

    GitHub: [Insert your GitHub profile link here]

    LinkedIn: [Optional: Insert your LinkedIn profile link here]
