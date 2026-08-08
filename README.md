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
