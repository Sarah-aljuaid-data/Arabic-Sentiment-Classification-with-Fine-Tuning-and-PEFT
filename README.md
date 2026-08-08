# PEFT-for-Arabic-Sentiment-Classification

## Description

This repository contains the implementation of a comparative study of model adaptation strategies for Arabic sentiment classification.

The study compares Full Fine-Tuning, Frozen Backbone, LoRA, and QLoRA using CAMeLBERT-Mix as the pretrained language model. The methods are evaluated under full-data and reduced-data conditions to compare their classification performance and computational efficiency.

## Dataset

T## Dataset

The experiments use the Hotel Arabic Reviews Dataset (HARD) for binary Arabic sentiment classification. Reviews with ratings 1 and 2 are labeled as negative, while ratings 4 and 5 are labeled as positive. Neutral reviews are excluded.

The resulting dataset contains 105,698 reviews, equally distributed between the two sentiment classes.

| Class | Number of Reviews |
|------|------------------:|
| Negative | 52,849 |
| Positive | 52,849 |
| **Total** | **105,698** |

The dataset is divided using a stratified 80/10/10 split:

| Split | Percentage | Number of Samples |
|------|-----------:|------------------:|
| Training | 80% | 84,558 |
| Validation | 10% | 10,570 |
| Test | 10% | 10,570 |
| **Total** | **100%** | **105,698** |

In addition to the full training set, two reduced-data conditions are evaluated to examine model adaptation under limited training data:

| Training Condition | Samples per Class | Total Training Samples |
|-------------------|------------------:|-----------------------:|
| Full Dataset | 42,279 | 84,558 |
| 100 per Class | 100 | 200 |
| 25 per Class | 25 | 50 |

The reduced-data subsets are sampled from the training split only, while the validation and test sets remain fixed across experiments.
## Methods

Four model adaptation strategies are compared:

- **Full Fine-Tuning:** All model parameters are updated during training.
- **Frozen Backbone:** The pretrained encoder is frozen and only the classification head is trained.
- **LoRA (Low-Rank Adaptation):** Low-rank trainable matrices are introduced while the pretrained backbone remains frozen.
- **QLoRA (Quantized Low-Rank Adaptation):** LoRA is combined with 4-bit quantization to reduce memory requirements during training.

All methods use CAMeLBERT-Mix as the pretrained backbone and are evaluated under the same experimental framework.

## Evaluation

The methods are evaluated using the following classification and computational metrics:

- Accuracy
- Macro-F1
- Macro Precision
- Macro Recall
- Training time
- Peak GPU memory usage
- Inference time

Experiments are repeated across five random seeds, and the results are summarized using the mean, sample standard deviation, and Student's *t*-based 95% confidence interval.

## Results

The experiments compare the performance and computational efficiency of the four adaptation strategies across the full training dataset and the two reduced-data conditions.

The main comparison focuses on how the amount of available training data affects the relative performance of Full Fine-Tuning, Frozen Backbone, LoRA, and QLoRA.

## Repository Contents

The repository includes:

- **`PEFT_for_Arabic_Sentiment_Classification.ipynb`** — Main notebook containing data preparation, model training, evaluation, statistical analysis, and result visualization.
- **`HARD Dataset.zip`** — Dataset used in the experiments.
- **`README.md`** — Project description and repository documentation.

## Reproducibility

The experiments are implemented in Python using PyTorch and the Hugging Face ecosystem.

The notebook contains the complete experimental pipeline, including:

- Dataset preprocessing and splitting
- Reduced-data sampling
- Full Fine-Tuning
- Frozen-Backbone training
- LoRA
- QLoRA
- Model evaluation
- Statistical analysis
- Result visualization

## Dataset Citation

If you use the HARD dataset, please cite:

Elnagar, A., Khalifa, Y. S., & Einea, A. (2018).  
Hotel Arabic-Reviews Dataset Construction for Sentiment Analysis Applications.  
In *Intelligent Natural Language Processing: Trends and Applications*,  
Studies in Computational Intelligence, Vol. 740, pp. 35–52.  
Springer International Publishing.  
DOI: 10.1007/978-3-319-67056-0_3

## License

Please refer to the original HARD dataset source for its dataset usage and licensing conditions.
