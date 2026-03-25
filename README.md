# Financial Fraud Detection with Graph Analytics

## Project Overview

This project builds an **end-to-end fraud detection system** using unsupervised learning and graph analytics to identify suspicious transactions and uncover coordinated fraud rings.

Unlike traditional models that analyze transactions individually, this project focuses on **network-level fraud detection**, enabling the discovery of hidden relationships between entities such as cards, devices, and email domains.

This solution leverages the **IEEE Fraud Detection Dataset** from [Kaggle](https://www.kaggle.com/competitions/ieee-fraud-detection/data), which contains over 100,000 transactions with rich behavioral and identity features.

---

## Business Problem

Financial fraud often occurs in **coordinated groups (fraud rings)** rather than isolated transactions. Detecting these patterns is critical for reducing financial losses and improving risk management. Traditional transaction-level models miss the coordinated nature of fraud rings where multiple cards, devices, and email addresses work together to exploit the system.

---

## Solution Approach

### 1. Anomaly Detection (Unsupervised Learning)

* Used **Isolation Forest** to detect suspicious transactions
* Identified top ~2% anomalies as potential fraud cases
* Generated **fraud risk scores** for each transaction

---

### 2. Feature Engineering

The feature engineering pipeline creates sophisticated behavioral and network features:

* **Velocity Features**: Transaction frequency, rolling statistics (mean/std of last 5 transactions)
* **Amount Deviation**: Deviation from user's average transaction amount
* **Temporal Features**: Hour and day-of-week patterns, daily transaction frequency per card
* **Identity-based Features**: 
  * Email domain frequency (cards sharing email domains)
  * Device sharing frequency (cards using same device)
  * Device and email diversity per card (how many different devices/emails per card)
* **Encoding**: Frequency encoding for high-cardinality variables and label encoding for categorical features
* **Processed Output**: ~1.1GB engineered dataset ready for modeling

---

### 3. Graph Analytics (Core Innovation)

* Built a network connecting:

  * Cards
  * Email domains
  * Devices
* Applied graph analysis to:

  * Detect **fraud rings (communities)**
  * Identify **fraud hubs (highly connected nodes)**
  * Analyze relationships between suspicious entities

---

## Key Results

* **Flagged ~2,000 suspicious transactions** (~2% of dataset) using Isolation Forest
* **Identified multiple fraud rings** through network community detection
* **Discovered high-risk fraud hubs** (cards, emails, devices acting as central nodes)
* **Enabled network-level fraud detection** revealing coordinated fraud patterns beyond individual anomalies
* Generated **anomaly scores** and **node risk rankings** for prioritized investigation

---

## Tech Stack

* **Data Processing**
  * pandas (3.0.1) - Data manipulation & feature engineering
  * numpy (2.4.3) - Numerical computations
  * scipy (1.17.1) - Scientific computing utilities
* **Machine Learning**
  * scikit-learn (1.8.0) - Isolation Forest anomaly detection
* **Graph Analytics**
  * NetworkX (3.0+) - Network construction, community detection, centrality measures
* **Visualization**
  * matplotlib (3.10.8) - Graph and statistical visualizations
  * seaborn (0.13.2) - Enhanced statistical plots
* **Statistics & Analysis**
  * statsmodels (0.14.6) - Statistical modeling
  * joblib - Parallel processing
* **Development**
  * Jupyter (notebooks) - Interactive analysis and documentation

---

## Project Structure

```
fraud-detection-project/
│
├── notebooks/
│   ├── 01_EDA.ipynb                    # Exploratory Data Analysis
│   ├── 02_feature_engineering.ipynb    # Feature creation & preprocessing
│   ├── 03_modeling.ipynb               # Isolation Forest anomaly detection
│   └── 04_graph_analysis.ipynb         # Fraud ring detection & network analysis
│
├── data/
│   ├── test_transaction.csv            # Raw transaction data
│   ├── test_identity.csv               # User identity & device data
│   ├── processed_fraud_data.csv        # Engineered features (output from 02)
│   ├── fraud_unsupervised_results.csv  # Fraud flags & scores (output from 03)
│   ├── fraud_nodes.csv                 # Node risk scores (output from 04)
│   ├── fraud_communities.csv           # Fraud ring statistics (output from 04)
│   ├── column_summary.csv              # Data profile information
│   └── dataset_link.txt                # Kaggle dataset source
│
├── env/                                # Python virtual environment
├── requirements.txt                    # Project dependencies
└── README.md                           # This file
```

---

## Sample Insights

* **Behavioral Patterns**: Transactions with unusual amounts, high frequency, or unusual timing show higher fraud likelihood
* **Device & Email Sharing**: Multiple cards sharing the same device or email domain indicate coordinated fraud (fraud rings)
* **Fraud Hubs**: Highly connected nodes (cards/emails/devices) in the transaction network indicate central orchestrators of fraud rings
* **Community Detection**: Large graph communities reveal organized fraud operations where multiple entities collaborate
* **Risk Layering**: Combining anomaly scores with network centrality measures provides comprehensive fraud risk assessment

---

## Workflow & Notebook Execution

The project follows a sequential pipeline:

1. **01_EDA.ipynb**: Understand data characteristics, distributions, and missing patterns
   - Input: `test_transaction.csv`, `test_identity.csv`
   
2. **02_feature_engineering.ipynb**: Create behavioral, temporal, and identity-based features
   - Input: Raw transaction + identity data
   - Output: `processed_fraud_data.csv` with engineered features
   
3. **03_modeling.ipynb**: Apply Isolation Forest to detect individual fraud anomalies
   - Input: `processed_fraud_data.csv`
   - Output: `fraud_unsupervised_results.csv` with fraud flags and anomaly scores
   
4. **04_graph_analysis.ipynb**: Build fraud network and detect coordinated fraud rings
   - Input: Fraud-flagged transactions from step 3
   - Output: `fraud_nodes.csv`, `fraud_communities.csv` with risk rankings and community statistics

---

## Technical Deep Dive

### Anomaly Detection (Isolation Forest)
* **Algorithm**: Isolation Forest with 200 estimators
* **Contamination Rate**: 0.02 (assumes ~2% fraud in dataset)
* **Output**: Binary fraud flags + anomaly scores (negative = more suspicious)
* **Rationale**: Unsupervised approach works well with unlabeled data

### Graph Analytics (Network Analysis)
* **Network Construction**: Weighted multigraph with cards, emails, and devices as nodes
* **Edge Weights**: Frequency of shared transactions between entities
* **Community Detection**: Greedy modularity optimization algorithm
* **Centrality Measures**:
  - **Degree Centrality**: Connection count (how many other entities connected)
  - **Betweenness Centrality**: Bridge role (connecting different fraud groups)
  - **Combined Risk Score**: Integrates both measures for node-level risk ranking

---

## Key Learnings & Insights

* **Behavioral + Relational Analysis**: Fraud detection is most effective when combining transaction-level anomalies with relationship patterns
* **Graph Methods Excel at Coordination Detection**: Network analysis reveals fraud rings that transaction-level models miss entirely
* **Unsupervised Learning Advantage**: Without labeled data, anomaly detection + graph analysis provides powerful fraud signals
* **Feature Engineering is Critical**: Rich behavioral features (velocity, deviation, identity sharing) enable better anomaly detection
* **Scalability Limitations**: Full network analysis on 100K+ transactions requires careful memory management and efficient algorithms

---

## Future Improvements

* **Advanced Community Detection**: Implement Louvain method for better community resolution on large networks
* **Temporal Dynamics**: Track how fraud rings evolve over time with sliding window analysis
* **Real-time Pipeline**: Deploy Kafka + Spark for streaming fraud detection
* **Interactive Dashboard**: Build Power BI/Grafana dashboard for investigator exploration of fraud rings
* **Explainability**: Add SHAP values to explain anomaly scores and feature contributions
* **Supervised Enhancement**: If labeled data becomes available, train semi-supervised models
* **Graph Embedding**: Use Node2Vec or GraphSAGE for representation learning on fraud network
* **Ensemble Methods**: Combine multiple anomaly detection algorithms (Isolation Forest, LOF, Autoencoder)

---

## Installation & Setup

### Prerequisites
- Python 3.8+ (tested with 3.11)
- pip or conda package manager
- ~5GB disk space (for raw data + processed outputs)

### Quick Start

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/fraud-detection-project.git
   cd fraud-detection-project
   ```

2. **Create and activate Python virtual environment** (recommended)
   ```bash
   # Using venv
   python -m venv env
   
   # Activate (Windows)
   env\Scripts\activate
   
   # Activate (macOS/Linux)
   source env/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Download dataset**
   - Follow instructions in `data/dataset_link.txt`
   - Download `test_transaction.csv` and `test_identity.csv` from Kaggle
   - Place in `data/` folder

5. **Run notebooks in order**
   ```bash
   jupyter notebook
   ```
   - Execute **01_EDA.ipynb** - Exploratory analysis (~5-10 min)
   - Execute **02_feature_engineering.ipynb** - Feature creation (~10-15 min)
   - Execute **03_modeling.ipynb** - Anomaly detection (~5 min)
   - Execute **04_graph_analysis.ipynb** - Network analysis (~5-10 min)

### Output Files Generated
- `processed_fraud_data.csv` - Engineered features
- `fraud_unsupervised_results.csv` - Fraud flags and anomaly scores
- `fraud_nodes.csv` - Node-level risk rankings
- `fraud_communities.csv` - Community (fraud ring) statistics

---

## Data Source

Dataset: [IEEE Fraud Detection Competition on Kaggle](https://www.kaggle.com/competitions/ieee-fraud-detection/data)

**Dataset Features**:
- **100,000+ transactions** with ~400 features
- **Transaction features** (V1-V339): Anonymized numerical features from credit card network
- **Identity features** (DeviceType, RealState, DayOfWeek, etc.): Device and behavioral data
- **Sparse data**: Many identity features have 90%+ missing values
- **Unlabeled**: No fraud labels provided (unsupervised approach required)


