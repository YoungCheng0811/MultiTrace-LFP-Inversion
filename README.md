# MultiTrace-LFP-Inversion

Implementation and lightweight supplementary materials for the manuscript:

**Multi-trace seismic acoustic impedance inversion with low-frequency prior
fusion**

## Method overview

The implementation stacks an aligned seismic window and low-frequency-prior
window into an input tensor with shape `[batch, 2, 5, time]`. A
ConvNeXt-ResUNet encoder-decoder preserves the five-trace dimension and
downsamples only along time. The network predicts an unconstrained residual,
which is transformed into a bounded residual update and added to the
normalized low-frequency prior:

```text
raw residual = G_theta(input)
bounded residual = alpha * tanh(raw residual)
prediction = clip(low-frequency prior + bounded residual, 0, 1)
```

For the Marmousi configuration, `alpha = 0.45`.

## Main features

- five-trace, two-channel seismic/prior input;
- temporal-only encoder downsampling;
- bounded residual prediction relative to the prior;
- composite supervised and seismic-consistency loss;
- unweighted overlap averaging for Marmousi;
- center-weighted five-trace fusion for the F3 diagnostic workflow;
- normalized PCC, MAE, RMSE, and R2 evaluation;
- fixed split manifests and a lightweight synthetic smoke test.

## Repository structure

```text
configs/      Frozen experiment and demonstration configurations
docs/         Method, preprocessing, commands, provenance, and limitations
examples/     Generated synthetic smoke-test arrays
manifests/    Split indices and expected external files
scripts/      Demo, training, inference, and evaluation entry points
src/          Model, loss, data, training, inference, and metric modules
```

## Installation

```bash
python -m venv .venv
# Linux/macOS
source .venv/bin/activate
# Windows PowerShell
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

An equivalent Conda specification is provided in `environment.yml`.

## What can be run now

The repository includes only newly generated synthetic arrays. It can run the
demo generator and smoke test without downloading third-party seismic data.

```bash
python scripts/generate_demo_data.py
python scripts/smoke_test.py
```

The smoke test reads the arrays, stacks the two channels, executes a forward
pass, verifies output shape and bounded residual addition, computes PCC, MAE,
RMSE, and R2, and writes runtime diagnostics under `outputs/`.

## External data preparation

Marmousi and F3 data are not redistributed. See `DATA_SOURCES.md` and
`manifests/expected_external_files.csv`. Place user-obtained files outside
version control and pass their locations through the configuration or command
line.

## Demo data

`scripts/generate_demo_data.py` creates:

- `examples/demo_seismic.npy`: `[5, 128]`;
- `examples/demo_lfm.npy`: `[5, 128]`;
- `examples/demo_target_ai.npy`: `[5, 128]`.

These arrays are not Marmousi, are not F3, do not represent a field survey, and
cannot reproduce any manuscript metric. They exist only to test the software
interface.

## Marmousi preprocessing

After acquiring the external arrays:

```bash
python scripts/preprocess_marmousi.py \
  --config configs/marmousi.yaml \
  --seismic data/marmousi_synthetic_seismic.npy \
  --impedance data/marmousi_Ip_model.npy \
  --output-dir processed_data/marmousi
```

The script applies the recorded orientation, clipping, low-pass filtering,
lateral smoothing, and split manifest. Review `docs/preprocessing_notes.md`
before running it because the benchmark prior is derived from the known
impedance model.

## Marmousi training

```bash
python scripts/train_marmousi.py \
  --config configs/marmousi.yaml \
  --data-dir processed_data/marmousi \
  --output-dir outputs/marmousi_training
```

This command starts training and is not part of the smoke test. It requires the
external data and appropriate compute resources.

## Marmousi inference

```bash
python scripts/infer_marmousi.py \
  --config configs/marmousi.yaml \
  --data-dir processed_data/marmousi \
  --checkpoint checkpoints/model.pth \
  --output outputs/marmousi_prediction.npy
```

No manuscript checkpoint is included.

## F3 preprocessing

The historical field workflow depends on third-party survey files and
well-seismic calibration products. The public package therefore provides the
required processed-file contract and validation logic, not the third-party
inputs. Prepare the files listed in `manifests/expected_external_files.csv`
using author-verified data access and calibration procedures.

## F3 inference

```bash
python scripts/infer_f3.py \
  --config configs/f3.yaml \
  --data-dir processed_data/f3 \
  --checkpoint checkpoints/fold_model.pth \
  --output outputs/f3_prediction.npy
```

The script uses the recorded center weights
`[0.35, 0.75, 1.0, 0.75, 0.35]`. Fold 1 uses W2 as the constraint well and W3
for diagnostic evaluation; Fold 2 reverses these roles. Checkpoints are not
included.

## Evaluation

```bash
python scripts/evaluate.py \
  --prediction outputs/prediction.npy \
  --target processed_data/target.npy \
  --data-range 10831.900512695312
```

PCC and R2 are calculated on flattened finite samples. MAE and RMSE are divided
by the specified impedance range when physical-domain arrays are supplied.
The boundary mask uses a temporal-gradient quantile of 0.85 and a dilation
radius of three samples.

## Expected shapes

| Item | Shape |
|---|---|
| Seismic section | `[traces, time]` |
| Low-frequency-prior section | `[traces, time]` |
| Input window | `[2, 5, time]` |
| Batched input | `[batch, 2, 5, time]` |
| Model output | `[batch, 1, 5, time]` |
| Marmousi processed section | `[2721, 701]` |
| Selected F3 interval | `[195, 226]` |

## Reproducibility scope

This repository provides the main implementation, configuration files,
data-split information, and a lightweight synthetic demonstration.
Reproduction of the full manuscript experiments requires the original
external datasets and computational resources. See `REPRODUCIBILITY.md` for
the exact scope and limitations.

## Data availability

See `DATA_AVAILABILITY.md` and `DATA_SOURCES.md`.

## Citation

Citation metadata are provided in `CITATION.cff`. Publication DOI, journal
volume, and repository URL are intentionally omitted until they exist.

## License

No license was found in the source project. See
`LICENSE_SELECTION_REQUIRED.md`. Public release should wait until the authors
select and add a license.

## Known limitations

- External datasets and checkpoints are not included.
- The complete manuscript experiment matrix was not rerun for this release.
- The field-data preprocessing chain requires author-controlled calibration
  products that cannot be redistributed here.
- The smoke test verifies interfaces only.
- Dataset download URLs and the software license require author confirmation.

## Contact

The corresponding-author contact should be added after the authors confirm
that the manuscript correspondence address is intended for public repository
use.

