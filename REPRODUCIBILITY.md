# Reproducibility scope

This repository provides the verified ConvNeXt-ResUNet implementation,
composite loss, preprocessing functions, data-split indices, inference
functions, evaluation metrics, and configuration records associated with the
manuscript.

Directly runnable without external data:

- synthetic demonstration generation;
- module import and shape checks;
- a forward pass through the same model class;
- bounded residual and metric checks.

Requires external data:

- Marmousi preprocessing, training, inference, and evaluation;
- Netherlands F3 preprocessing and fold-wise inference.

Not provided:

- third-party raw data;
- trained checkpoints;
- full prediction arrays;
- manuscript figures;
- intermediate field-data products.

The repository has not been used to rerun the complete manuscript experiment
matrix in a clean environment. Full numerical reproduction therefore requires
the external datasets, the documented preprocessing products, suitable
computational resources, and retraining. The smoke test is an interface test,
not a scientific validation.

The Marmousi split is trace-level. The six withheld traces are excluded from
the training-well list, but the released record does not claim an
output-window-buffered independent validation design.

