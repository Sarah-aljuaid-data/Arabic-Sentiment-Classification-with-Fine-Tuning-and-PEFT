# PEFT-for-Arabic-Sentiment-Classification
## Dataset

The experiments are conducted using the Hotel Arabic Reviews Dataset (HARD).

For binary sentiment classification, reviews with ratings 1 and 2 are labeled as negative, while reviews with ratings 4 and 5 are labeled as positive. Neutral reviews are excluded.

The dataset is divided using a stratified 80/10/10 split:
- 80% for training
- 10% for validation
- 10% for testing

A fixed split seed of 42 is used to ensure reproducibility.



Three training-data conditions are evaluated:
- Full training dataset
- 100 samples per class
- 25 samples per class

In addition to the full training dataset, two reduced-data conditions are evaluated:
- 25 samples per class
- 100 samples per class

The reduced-data subsets are sampled from the training split only, while the validation and test sets remain unchanged across all experiments.

## Methods

Four model adaptation strategies are compared:

- **Full Fine-Tuning:** All model parameters are updated during training.
- **Frozen Backbone:** The pretrained backbone is frozen, and only the classification head is trained.
- **LoRA:** Low-Rank Adaptation is applied to selected attention modules while keeping the pretrained backbone frozen.
- **QLoRA:** LoRA is combined with 4-bit quantization to reduce memory requirements during training.
