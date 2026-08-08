# Full Fine-Tuning vs. Parameter-Efficient Adaptation for Arabic Sentiment Classification

> A controlled comparison of Full Fine-Tuning, Frozen Backbone, LoRA, and QLoRA for Arabic sentiment classification across different training-set sizes.

This repository contains the dataset preparation, experimental code, and results used to compare four adaptation strategies for Arabic binary sentiment classification using **CAMeLBERT-Mix**.

The study evaluates how the methods behave when the amount of labeled training data changes from the complete training dataset to substantially reduced-data settings.

The following adaptation strategies are evaluated:

- Full Fine-Tuning
- Frozen Backbone
- Low-Rank Adaptation (LoRA)
- Quantized Low-Rank Adaptation (QLoRA)

Each method is evaluated using the same experimental framework across three training-data conditions:

- Full training dataset
- 100 samples per class
- 25 samples per class


## Study at a Glance

| Setting | Description |
|---|---|
| **Task** | Arabic Binary Sentiment Classification |
| **Dataset** | Hotel Arabic Reviews Dataset (HARD) |
| **Pre-trained Model** | CAMeLBERT-Mix |
| **Methods** | Full Fine-Tuning, Frozen Backbone, LoRA, QLoRA |
| **Training-Data Levels** | Full Dataset, 100/Class, 25/Class |
| **Random Seeds** | 5 |
| **Primary Performance Metric** | Macro-F1 |
| **Additional Metrics** | Accuracy, Macro Precision, Macro Recall |
| **Efficiency Metrics** | Peak GPU Memory, Training Time, Trainable Parameters |
| **Total Experimental Runs** | 60 |


## Dataset

The experiments use the **Hotel Arabic Reviews Dataset (HARD)** for Arabic sentiment classification.

HARD contains hotel reviews written in both Modern Standard Arabic and dialectal Arabic. The balanced version used in this study contains positive and negative reviews.

The original rating labels are mapped into two sentiment classes:

- Ratings **1 and 2 → Negative**
- Ratings **4 and 5 → Positive**

### Dataset Distribution

| Sentiment | Number of Reviews |
|---|---:|
| Negative | 52,849 |
| Positive | 52,849 |
| **Total** | **105,698** |

The complete dataset is divided using a **stratified 80/10/10 split** to preserve the class distribution across the training, validation, and test sets.

### Data Split

| Split | Negative | Positive | Total |
|---|---:|---:|---:|
| Training | 42,279 | 42,279 | **84,558** |
| Validation | 5,285 | 5,285 | **10,570** |
| Test | 5,285 | 5,285 | **10,570** |
| **Total** | **52,849** | **52,849** | **105,698** |

The validation and test sets remain fixed throughout the experiments so that all adaptation methods are evaluated using the same data partitions.


## Training-Data Conditions

To examine the effect of labeled-data availability, the four adaptation methods are evaluated at three training-data levels.

| Training Condition | Negative | Positive | Total Training Samples |
|---|---:|---:|---:|
| **Full Dataset** | 42,279 | 42,279 | **84,558** |
| **100 / Class** | 100 | 100 | **200** |
| **25 / Class** | 25 | 25 | **50** |

The reduced-data subsets are sampled from the original training split while the validation and test sets remain unchanged.


## Experimental Design

The experimental framework combines **four adaptation strategies** with **three training-data levels**, resulting in **12 experimental conditions**.

Each condition is repeated across **five random seeds**, producing a total of:

**4 methods × 3 data levels × 5 seeds = 60 experimental runs**

<p align="center">
  <img src="figures/experimental_design.png" width="850">
</p>

All methods are evaluated using the same:

- Dataset partitions
- Pre-trained model
- Tokenization procedure
- Training configuration
- Validation set
- Test set
- Random seeds

This controlled setup allows the effect of the adaptation strategy and training-set size to be compared under consistent experimental conditions.


## Pre-trained Model

The experiments use **CAMeLBERT-Mix** as the pre-trained Arabic language model.

CAMeLBERT-Mix was selected because its pre-training data covers multiple varieties of Arabic, including Modern Standard Arabic, dialectal Arabic, and classical Arabic, making it suitable for the linguistic variation found in HARD hotel reviews.

The model is used as the common backbone for all four adaptation strategies.


## Adaptation Methods

Four strategies are compared under the same experimental framework.

| Method | Trainable Components | Quantization | Main Configuration |
|---|---|---|---|
| **Full Fine-Tuning** | Entire model | None | All model parameters updated |
| **Frozen Backbone** | Classification head only | None | Pre-trained encoder frozen |
| **LoRA** | Low-rank adapters | None | r=16, α=32, dropout=0.1 |
| **QLoRA** | Low-rank adapters | 4-bit NF4 | r=16, α=32, dropout=0.1 |

For LoRA and QLoRA, the adapters are applied to the **query** and **value** attention projection modules.

QLoRA additionally uses 4-bit NF4 quantization and a quantization-compatible optimizer.


## Training Configuration

The same general training configuration is maintained across the adaptation methods to support a controlled comparison.

| Hyperparameter | Value |
|---|---:|
| Epochs | 10 |
| Learning Rate | 2 × 10⁻⁵ |
| Training Batch Size | 32 |
| Evaluation Batch Size | 64 |
| Weight Decay | 0.01 |
| Warm-up Ratio | 0.1 |
| Mixed Precision | FP16 |
| Maximum Sequence Length | 128 tokens |

The maximum sequence length was selected from the token-length distribution of the training dataset and then fixed across all experiments.


## Evaluation

The comparison considers three complementary aspects of model adaptation.

### Classification Performance

The following metrics are reported:

- Accuracy
- Macro Precision
- Macro Recall
- Macro-F1

**Macro-F1** is used as the primary classification metric.

### Computational Efficiency

The computational cost of each adaptation strategy is evaluated using:

- Number of trainable parameters
- Peak GPU memory
- Training time

### Stability

Each experimental condition is repeated using five random seeds:

`42, 123, 456, 789, 2024`

Results are summarized using the **mean**, **sample standard deviation**, and **95% confidence intervals** across runs.


## Results

### Performance Across Training-Set Sizes

The following figure summarizes the change in Macro-F1 as the amount of training data is reduced.

<p align="center">
  <img src="figures/macro_f1_trends.png" width="800">
</p>

The experiments show a clear change in the relative behavior of the adaptation methods as the amount of labeled training data decreases.

At the full-data level, Full Fine-Tuning, LoRA, and QLoRA achieve closely grouped Macro-F1 performance, whereas the Frozen Backbone performs substantially lower.

Under the reduced-data conditions, Full Fine-Tuning retains considerably stronger classification performance, while the parameter-efficient approaches experience larger performance degradation.


### Performance–Cost Trade-Off

Performance alone does not capture the computational differences between the adaptation strategies. The following figures compare Macro-F1 against peak GPU memory under the two reduced-data conditions.

#### 100 Samples per Class

<p align="center">
  <img src="figures/tradeoff_100_per_class.png" width="720">
</p>

#### 25 Samples per Class

<p align="center">
  <img src="figures/tradeoff_25_per_class.png" width="720">
</p>

These comparisons illustrate the trade-off between predictive performance and GPU-memory requirements as the amount of labeled training data decreases.


## Key Findings

The experiments highlight several differences between the adaptation strategies:

- **Full-data:** Full Fine-Tuning, LoRA, and QLoRA achieve closely comparable classification performance.
- **Reduced-data:** Full Fine-Tuning retains substantially stronger Macro-F1 as the training set is reduced to 100 and 25 samples per class.
- **Parameter efficiency:** LoRA and QLoRA require only a small fraction of the trainable parameters required by Full Fine-Tuning.
- **GPU memory:** LoRA and QLoRA reduce peak GPU-memory requirements relative to Full Fine-Tuning.
- **Frozen Backbone:** Training only the classification head provides the lowest adaptation cost but results in substantially lower classification performance.
- **Overall trade-off:** The preferred adaptation strategy depends on both labeled-data availability and computational constraints.


## Repository Structure

```text
.
├── data/
│   └── ...
│
├── code/
│   └── ...
│
├── figures/
│   ├── experimental_design.png
│   ├── macro_f1_trends.png
│   ├── tradeoff_100_per_class.png
│   └── tradeoff_25_per_class.png
│
└── README.md
```

### `data/`

Contains the dataset files used for the experiments.

### `code/`

Contains the complete experimental pipeline, including:

- Data preparation and splitting
- Reduced-data sampling
- CAMeLBERT-Mix loading
- Full Fine-Tuning
- Frozen Backbone
- LoRA
- QLoRA
- Evaluation
- Computational-cost measurements
- Statistical analysis
- Result visualization

### `figures/`

Contains the figures generated from the experimental results and used for visualizing performance and computational trade-offs.


## Reproducing the Experiments

The experiments were developed in **Google Colab** using a CUDA-enabled GPU environment.

Install the required libraries before running the experiment:

```bash
pip install transformers
pip install datasets
pip install accelerate
pip install peft
pip install bitsandbytes
pip install scikit-learn
pip install scipy
```

The experimental code then performs the complete pipeline from dataset preparation to final evaluation.

For every adaptation strategy, experiments are conducted across:

```text
Full Dataset
├── Seed 42
├── Seed 123
├── Seed 456
├── Seed 789
└── Seed 2024

100 Samples / Class
├── Seed 42
├── Seed 123
├── Seed 456
├── Seed 789
└── Seed 2024

25 Samples / Class
├── Seed 42
├── Seed 123
├── Seed 456
├── Seed 789
└── Seed 2024
```

The same procedure is repeated for:

```text
Full Fine-Tuning
Frozen Backbone
LoRA
QLoRA
```

producing **60 experimental runs** in total.


## Dataset Citation

If you use the HARD dataset, please cite the original dataset paper:

```bibtex
@incollection{elnagar2018hard,
  title     = {Hotel Arabic-Reviews Dataset Construction for Sentiment Analysis Applications},
  author    = {Elnagar, Ashraf and Khalifa, Yasmin S. and Einea, Anas},
  booktitle = {Intelligent Natural Language Processing: Trends and Applications},
  series    = {Studies in Computational Intelligence},
  volume    = {740},
  pages     = {35--52},
  publisher = {Springer International Publishing},
  year      = {2018},
  doi       = {10.1007/978-3-319-67056-0_3}
}
```


## Citation



## License

This repository is provided for academic and research purposes.
