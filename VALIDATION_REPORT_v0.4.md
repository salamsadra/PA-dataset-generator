# PA-FORGE v0.4 — Validation and deployment report

**Date:** 2026-10-28

## What was actually executed

- `PYTHONPATH=. python -m pytest -q`: **59 passed** (Python 3.13 test runtime).
- CLI smoke: `python -m paforge.cli datasets list` and `datasets download --help` work; tests cover their output.
- Offline source smoke: `datasets download --all --dry-run` correctly lists Cambridge as planned, OADAT as guided and seven API-based sources as discovery errors because sandbox DNS/internet is unavailable. It creates no data folder during dry run.
- HTTP interface tests use mocked publisher responses; tests verify Zenodo metadata, Dryad metadata, safe names and HTTPS URLs, proper resume using HTTP Range, restart if Range ignored, checksum corruption detection, incomplete transfers and `.part` retention, disk-budget handling, and prevention of ZIP path traversal.
- ZIP and HDF5 signature guards reject fake HTML content for checksum-free Cambridge assets.
- Original suite (numerical solver, five anatomical profiles, vascular generation, noise-OFF invariance, synthetic export, reconstruction and experimental workflow) continues passing.

## What was NOT executed

- No original 20 GB, 97 GB, 101 GB, 842 GB, etc. dataset archive was downloaded.
- No live Zenodo/Dryad/Cambridge byte transfer was tested from this environment, so runtime HTTP headers, redirects, rate limiting, publisher authentication and file permissions remain unverified.
- No dataset ingestion into canonical detector arrays; no real human model training; no empirical physical device calibration; no independent real acquisition evaluation; and no cross-device generalization result.
- OADAT ETH collection selective automated access remains unverified. It is **intentionally not automatically downloaded**, especially given total size around 842 GB and noncommercial licensing.

## Dataset download readiness

| Source | Download integration | Actual archive tested? |
|---|---|---|
| OpenQPAIData | Zenodo metadata API + resumable verified bytes | No |
| PAISKINTONE | Zenodo API, subject-level starter selection | No |
| Synthetic Skin | Zenodo API, starter HDF5 subset | No |
| Breast PACT/URCT | Dryad API; 5.98 GB image ZIP on full plan | No |
| PAM3 | Zenodo API; full record selection | No |
| PhotIQA | Zenodo API; image/annotation ZIPs | No |
| ShanghaiTech vessel | Zenodo API; full record | No |
| Cambridge vascular | Official bitstream URLs, no publisher checksum verified | No |
| OADAT | Guided ETH share; no safe auto subset manifest | No |

## Reviewer-level scientific boundary

Acquisition integration is a **data engineering milestone**, not an experimental validation claim. Even once transfer succeeds, PA-FORGE's current optical and acoustic approximations remain in need of independent numerical and physical calibration. A Q1 manuscript should use explicit train/validation/test group splits and publish comparisons against held-out experimental measurements.
