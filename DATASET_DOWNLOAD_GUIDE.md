# PA-FORGE v0.4 — Dataset acquisition hub

## Scientific scope

The new `paforge datasets` interface downloads ORIGINAL publisher archives. It does **not** convert all datasets into photoacoustic raw detector arrays, train on them, or certify experimental calibration. Those processes require dataset-specific readers, acquisition geometry and held-out validation. In particular, images and reconstructed volumes **must not** be relabeled raw sensor time series.

Sources have different licenses. The downloader retains external files in separate directories, downloads nothing on installation, and requires explicit acceptance of responsibility for reviewing upstream terms before downloading. The program does not redistribute the archives.

## Install and run

```bash
python -m pip install -e '.[dev]'
paforge datasets list
paforge datasets inspect openqpai

# Single command: all supported sources, cautious starter profile.
paforge datasets download --all --profile starter --accept-licenses \
    --output data/external --max-file-gb 2 --max-total-gb 5

# Repeat to resume interrupted individual files.
paforge datasets download --all --profile starter --accept-licenses \
    --output data/external --max-file-gb 2 --max-total-gb 5

# Preflight plan without downloading; still needs internet for Zenodo/Dryad metadata.
paforge datasets download --all --profile full --dry-run \
    --max-file-gb 100 --max-total-gb 500

# Single dataset: entire Zenodo record (can be very large).
paforge datasets download --dataset synthetic-skin --profile full \
    --accept-licenses --max-file-gb 7 --max-total-gb 120

# Breast clinical image archive (large: not selected by default starter profile).
paforge datasets download --dataset breast-pact --profile full \
    --accept-licenses --max-file-gb 8 --max-total-gb 10

# Vascular Cambridge InSilico only (~433 MB); all Cambridge assets with --profile full.
paforge datasets download --dataset cambridge-vascular --profile starter \
    --accept-licenses
```

On Windows, use PowerShell with commands on one line instead of the Unix `\` line continuation.

## Dataset support (9 sources)

| ID | Data role | Method | Starter selection |
|---|---|---|---|
| `openqpai` | Experimental phantom raw IPASC waveforms, optical properties | Zenodo API | `invision.zip`, `DIS.zip`, mapping, README |
| `paiskintone` | Human multispectral raw acquisitions | Zenodo API | code + `SKIN42.tar.xz` (sample individual subject) |
| `synthetic-skin` | Synthetic pressure maps and acoustic sinograms | Zenodo API | 100-sample HDF5 subset, README |
| `breast-pact` | Human breast reconstructed PACT/URCT images; raw signals not confirmed | Dryad API | `README.md` only; `--profile full` for 5.98 GB `DATA.zip` |
| `pam3` | Reconstructed breast volumes; raw signals not confirmed | Zenodo API | Smallest available data archive <=1 GB if published; else docs only/no starter |
| `photiqa` | Reconstructed images and expert image-quality labels | Zenodo API | images, annotations, descriptor |
| `shanghaitech-vessel` | Vessel phantom dataset | Zenodo API | Small archive if available; potentially 7.7 GB full dataset |
| `cambridge-vascular` | Vessel masks/RSOM reconstructions, not verified raw traces | Publisher bitstream links | `InSilico.zip`; `--profile full` for all four files |
| `oadat` | Human forearm raw measurements and synthetic simulations | **Guided ETH Libdrive** | Official direct public share; cannot verify selective bulk automation |

**OADAT** public collection: https://libdrive.ethz.ch/index.php/s/i974KegIjhJJxkK (upstream reports ~842 GB if all files are downloaded). Its source is **CC BY-NC 4.0**. Selecting `--all` will document the access link, but will not silently attempt this enormous unverified bulk download.

Some sources have fewer or no small starter files. `--profile starter` therefore does not promise full datasets. Use `--profile full` with sufficient disk budget to request all discovered Zenodo/Dryad/Cambridge files.

## Download safeguards

- Upstream publisher API metadata drives actual Zenodo/Dryad filenames, URLs, byte counts and checksums.
- Streaming downloads write `*.part`; rerunning resumes using HTTP Range where supported. When Range is ignored, partial data is restarted, **not** duplicated.
- Published MD5/SHA-256 is checked before the final atomic rename. Note: MD5 verifies transfer integrity against the publisher's published digest, **not** cryptographic authenticity.
- When publishers don't supply a checksum (Cambridge bitstream links), basic archive magic is checked instead; this is **not** equivalent to a checksum.
- `--max-file-gb` and `--max-total-gb` prevent accidental huge transfers. Existing disk space is checked before downloads, with streaming caps for unknown exact sizes.
- Optional `--unpack` checks archive paths and rejects symlinks/special TAR files, with an expansion budget. Extraction can require substantially more disk than archive sizes.
- `data/external/download_report.json` records each source's actual status (`complete_selected`, `partial`, `guided_external_download`, `discovery_error`, etc.). Each dataset stays in its own subdirectory.
- No downloading occurs during package installation, UI startup, or basic simulator generation.

## Limitations and reproducibility

This release's actual external network download was **not** exercised in the provided sandbox (DNS/internet unavailable); tests use simulated HTTP payloads. Live metadata API interfaces may change or require authentication. Dryad in particular may return `401` for archive bytes; the report will record an error and users can continue at the official Dryad site. This is an integrated, locally tested downloader, **not** a guarantee that all publishers will serve every byte uninterrupted.

No external dataset bytes or pretrained real-data models are included in the release ZIP. No experimental simulation-to-real benchmark has been executed in this release.

### Reference landing pages

- OpenQPAIData: https://zenodo.org/records/14044853
- PAISKINTONE: https://zenodo.org/records/20068219
- Synthetic skin phantoms: https://zenodo.org/records/21785486
- Breast PACT/URCT: https://doi.org/10.5061/dryad.vmcvdnd55
- PAM3: https://zenodo.org/records/20612980
- PhotIQA: https://zenodo.org/records/15828190
- ShanghaiTech vessel: https://zenodo.org/records/10719987
- Cambridge vascular mesoscopy: https://doi.org/10.17863/CAM.78208
- OADAT: https://doi.org/10.3929/ethz-b-000551512
