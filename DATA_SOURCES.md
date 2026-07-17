# External data sources

## Marmousi benchmark

- Manuscript reference: Versteeg, R., 1994. The Marmousi experience: Velocity
  model determination on a synthetic complex data set. The Leading Edge 13(9),
  927-936. https://doi.org/10.1190/1.1437051.
- Data version used by the project: a 2721-trace by 701-sample acoustic
  impedance section and aligned synthetic post-stack seismic section.
- Official dataset URL: TODO - Author must confirm the official dataset URL
  before release.
- Expected local inputs:
  `data/marmousi_synthetic_seismic.npy` and
  `data/marmousi_Ip_model.npy`.

## Netherlands F3 Block

- Manuscript reference: Silva, R.M., Baroni, L., Ferreira, R.S., Civitarese,
  D., Szwarcman, D., Brazil, E.V., 2019. Netherlands Dataset: A New Public
  Dataset for Machine Learning in Seismic Interpretation. arXiv:1904.00770.
- Dataset record cited by the manuscript:
  https://doi.org/10.5281/zenodo.1422787.
- Data version used by the project: the selected 2150-2600 ms interval with
  195 traces and 226 time samples, derived from the third-party survey.
- Official download URL beyond the cited dataset record: TODO - Author must
  confirm the exact download page and version before release.
- Expected processed inputs are listed in
  `manifests/expected_external_files.csv`.

## Suggested local layout

```text
data/
  marmousi_synthetic_seismic.npy
  marmousi_Ip_model.npy
processed_data/
  marmousi/
  f3/
```

The original datasets are not included because they are third-party materials.
Users must obtain them from official sources and comply with the applicable
license or terms of use. This repository does not grant redistribution rights
for either dataset.

