# Proactive Detection of Zero-Day Attacks with a Hybrid BERT and Graph Neural Network Architecture

[![Python 3.9](https://img.shields.io/badge/python-3.9-blue.svg)](https://www.python.org/downloads/release/python-390/)
[![PyTorch 2.0](https://img.shields.io/badge/PyTorch-2.0-red.svg)](https://pytorch.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Official code repository for the paper: **"Proactive Detection of Zero-Day Attacks with a Hybrid BERT and Graph Neural Network Architecture"** (Submitted to *Scientific Reports*).

## 📖 Overview

Zero-day attacks exploit previously unknown vulnerabilities, making them notoriously difficult for traditional, signature-based Intrusion Detection Systems (IDS) to detect. This repository introduces a novel, hybrid AI-driven framework that bridges the gap between semantic threat intelligence and real-time network behavior.

The framework consists of two core modules:
1. **ThreatOrator**: A domain-adapted BERT model with a specialized domain-masking mechanism designed to extract semantic intelligence from unstructured textual sources (e.g., CVE databases, Exploit-DB, security advisories).
2. **GraphAEGIS**: An attention-based Graph Neural Network (GNN) that uses dynamic neighbor sampling and edge-feature augmentation to identify complex structural anomalies in network traffic flows.

These modalities are combined using an **Adaptive Weighted Fusion Algorithm** to achieve multi-dimensional, proactive zero-day attack detection.

---

## 🏗️ Architecture

* **Textual Modality (ThreatOrator):** Processes vulnerability descriptions and generates contextual embeddings.
* **Structural Modality (GraphAEGIS):** Converts NetFlow data into directed graphs (Nodes = IPs, Edges = Communications) and extracts structural embeddings.
* **Hybrid Fusion:** Dynamically gates and combines text and graph features to make a final classification, achieving **>99% accuracy** while maintaining a near-zero false positive rate.

---

## 📊 Datasets

The model is trained and evaluated on two benchmark intrusion detection datasets, alongside a custom unstructured text corpus:
* **[UNSW-NB15](https://research.unsw.edu.au/projects/unsw-nb15-dataset):** A comprehensive dataset containing 9 distinct attack families.
* **[NF-UNSW-NB15-V2](https://staff.itee.uq.edu.au/marius/NIDS_datasets/):** A modernized NetFlow-based variant of the UNSW-NB15 dataset.
* **Threat Intelligence Corpus:** 45,200 curated documents from NVD, MITRE, Exploit-DB, and US-CERT (Restricted to pre-2015 publications to strictly prevent temporal data leakage).

---

## ⚙️ Installation & Requirements

This code was tested on a high-performance computing setup (NVIDIA Tesla V100 GPU, 128GB RAM). 

**1. Clone the repository:**
```bash
git clone [https://github.com/moazanbasoud/Zero-day-Attack.git](https://github.com/moazanbasoud/Zero-day-Attack.git)
cd Zero-day-Attack
2. Create a virtual environment and install dependencies:Bashconda create -n zeroday python=3.9 -y
conda activate zeroday
pip install torch==2.0.0+cu118 torchvision torchaudio --index-url [https://download.pytorch.org/whl/cu118](https://download.pytorch.org/whl/cu118)
pip install torch_geometric
pip install transformers scikit-learn pandas numpy nltk spacy imbalanced-learn
python -m spacy download en_core_web_sm
🚀 Usage1. Data PreprocessingBefore training, you must construct the network graphs and align them with the textual embeddings.Bash# Preprocess the UNSW-NB15 dataset and build the GraphAEGIS structures
python src/data_preprocessing/build_graphs.py --dataset unsw-nb15

# Tokenize and pre-compute ThreatOrator text embeddings
python src/data_preprocessing/process_text.py --corpus_path data/threat_intel/
2. Training the ModelsYou can train the components independently (for ablation) or train the full hybrid framework end-to-end.Train the Hybrid Framework:Bashpython src/train.py --model hybrid --dataset unsw-nb15 --epochs 200 --batch_size 64 --lr 1e-4
Train Baseline/Standalone Models:Bashpython src/train.py --model graphaegis --dataset unsw-nb15
python src/train.py --model threatorator --dataset unsw-nb15
3. Zero-Day Evaluation (LOGO Protocol)To rigorously test the model's ability to detect entirely unseen threats, we use the Leave-One-Group-Out (LOGO) protocol. To run the evaluation excluding the 'Worms' or 'Backdoors' attack families from the training set:Bashpython src/evaluate_zeroday.py --exclude_family Worms --checkpoint weights/hybrid_best.pt
📈 Key ResultsOur hybrid framework significantly outperforms state-of-the-art baselines (including recent models like BS-GAT and standard GraphSAGE) across multiple metrics.DatasetAccuracyPrecisionRecallF1-ScoreFPRUNSW-NB1599.63%99.17%97.44%98.80%0.0224NF-UNSW-NB15-V299.72%99.40%97.94%99.01%0.0269Proactive Detection: Achieves >92% detection accuracy within the first 5 network packets.Unseen Attacks: Improves detection rates of novel/unseen attack families (zero-day proxy) by over 27% compared to standalone GNN baselines.📁 Repository StructurePlaintextZero-day-Attack/
├── data/                   # Raw datasets and threat intelligence corpus
├── src/                    # Source code
│   ├── models/             # PyTorch model definitions (ThreatOrator, GraphAEGIS, Hybrid)
│   ├── data_preprocessing/ # Graph construction and NLP tokenization scripts
│   ├── train.py            # Main training loop
│   ├── evaluate.py         # Standard evaluation metrics
│   └── evaluate_zeroday.py # LOGO protocol for zero-day evaluation
├── weights/                # Saved model checkpoints
├── requirements.txt        # Python dependencies
└── README.md               # Project documentation
📜 CitationIf you find this code or our research helpful in your work, please cite our paper:Code snippet@article{tariq2026proactive,
  title={Proactive Detection of Zero-Day Attacks with a Hybrid BERT and Graph Neural Network Architecture},
  author={Tariq, Ahmed Hasham Ibn E and Rehman, Attique Ur and Akhtar, Faijan and Hayat, Mohd Ammar Bin and Aneeq, Muhammad and Qadri, Syed Furqan and Tariq, Moazan Basoud Ibn E and Muaad, Abdullah Y. and Heyat, Md Belal Bin},
  journal={Scientific Reports (Under Review)},
  year={2026}
}
🤝 Contact & LicenseLicense: This project is licensed under the MIT License - see the LICENSE file for details.Contact: For any questions or issues regarding the code, please open an issue in this repository or contact the authors directly as listed in the manuscript.
