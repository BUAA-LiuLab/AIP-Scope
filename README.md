# AIP-Scope

**An integrated Windows workspace for mass-spectrometry data assessment, DIA-PASEF isolation-window evaluation, and precursor re-identification.**

[Software releases](https://github.com/BUAA-LiuLab/AIP-Scope/releases) · [User guide (PDF)](https://github.com/BUAA-LiuLab/AIP-Scope/blob/main/AIP-Scope_User_Guide.pdf) · [Report an issue](https://github.com/BUAA-LiuLab/AIP-Scope/issues)

AIP-Scope brings **AIP-MSRefine**, **AIP-WES**, and **AIP-reID** into one desktop workspace. Users can launch each module, inspect analysis outputs, and optionally ask questions about selected results through a configured model service.

This repository is intended for distributing the packaged Windows applications and user documentation. This README describes how to use the software; no development environment or source build is required.

## Modules

| Module | Purpose | Main inputs | Main outputs |
| --- | --- | --- | --- |
| **AIP-MSRefine** | Assess acquisition and identification quality with metrics, tables, and diagnostic figures. | Bruker DIA-PASEF `.d` data and a matching Spectronaut or DIA-NN TSV export. | `manifest.json`, metrics, figures, tables, and an HTML report. |
| **AIP-WES** | Evaluate isolation-window coverage and load distribution; generate a candidate scheme using the current reference-calibrated workflow. | Bruker DIA-PASEF `.d` data and a matching identification report. | `result.json`, comparison figures, metrics, and exportable window settings. |
| **AIP-reID** | Generate a predicted library, filter candidates using a fresh first-pass search and a frozen Transformer, and perform a final DIA-NN search. | One Bruker `.d` acquisition, a protein FASTA database, and an external DIA-NN installation. | `result.json`, first-pass and final identification reports, CSV/HTML summaries, and execution records. |

### Versions described in this README

| Component | Interface | Analysis Core |
| --- | --- | --- |
| AIP-Scope | 1.4.0 | — |
| AIP-MSRefine | 0.1.2 | 0.1.2 |
| AIP-WES | 0.5.7 | 0.5.1-ref2 |
| AIP-reID | 0.2.3 | 0.2.2 |

Check the release notes for the versions included in the package you download.

## System requirements

- **Windows x64.** Windows 10 or 11 is recommended. The launcher uses .NET Framework 4.5 or later.
- **The complete extracted application folder.** The packaged modules include their Python and scientific runtimes; a separate Python installation is not required.
- **Writable local storage with sufficient free space.** RAW extraction, spectral-library prediction, and searches create intermediate files. Storage and memory requirements depend on the dataset.
- **For new AIP-reID searches:** a complete **DIA-NN 2.3.2** installation, including its required vendor dependencies. Obtain DIA-NN from its [official project](https://github.com/vdemichev/DiaNN). Select `diann.exe` in AIP-reID; keep the installation's `timsdata.dll` and contaminant FASTA available.
- **For GPU inference:** a compatible NVIDIA GPU and driver. CPU inference is also supported; select the device in AIP-reID settings.
- **For optional Results Q&A:** an OpenAI-compatible model service configured with your own connection details. This service is not required to run the analysis modules or view saved results.

The current desktop data workflows support Bruker DIA-PASEF acquisitions. AIP-WES requires a fixed, repeating window scheme. Check the module instructions before using other acquisition types or report formats.

## Download and launch

1. Open [Releases](https://github.com/BUAA-LiuLab/AIP-Scope/releases) and select the Windows application package. Follow that release's download and extraction instructions. If a package is supplied in numbered archive parts, download every part into the same folder before extraction.
2. Extract the complete package to a writable local directory, such as `C:\AIP\`.
3. Keep the module folders and their runtime resources together, including `_internal`, `_gui_internal`, model files, and profiles where present. Copying an executable by itself is insufficient.
4. Double-click **`AIP-Scope.exe`**.
5. Select **Launch module** for AIP-MSRefine, AIP-WES, or AIP-reID. Each module opens in its own window.

The relevant application layout is:

```text
AIP-Suite_20261028/
  AIP-Scope.exe
  docs/
  modules/
    AIP-MSRefine/
      AIP-MSRefine.exe
      AIP-MSRefine-Core.exe
    AIP-WES/
      AIP-WES.exe
      AIP-WES-Core.exe
    AIP-reID/
      AIP-reID.exe
      AIP-reID-Core.exe
```

Additional runtime files are omitted from this overview. Preserve the complete extracted package. Closing the launcher does not stop analyses already running in module windows.

## Run an analysis

All three modules start with default analysis settings. Import a saved parameter file explicitly to restore a previous configuration. AIP-reID remembers the external `diann.exe` path independently until it is replaced.

Use a new output directory for each analysis. Keep Bruker `.d` folders intact, including `analysis.tdf`, `analysis.tdf_bin`, and acquisition metadata. If input files have moved, update their paths in the interface before running.

### AIP-MSRefine

1. Select one or more supported `.d` folders in **Configuration**.
2. Choose the identification-report format and the matching TSV export.
3. Set a new output directory and validate the inputs.
4. Start the analysis and follow the execution log on **Run / Output**.
5. Inspect metrics, tables, and figures on **Results**. Use **Open artifact** to view an original output externally.

Reports must contain the fields required by the selected parser. A minimal quantification table is insufficient; the current MSRefine interface does not accept a DIA-NN Parquet report as its identification input.

### AIP-WES

1. Select the RAW `.d` acquisition and its identification report.
2. Use **Check report** to inspect the report. The program matches the report run to the RAW basename automatically.
3. Choose **Evaluate current windows only** or **Evaluation and optimization**, then set a new output directory.
4. Select **Run analysis**.
5. Compare the current and candidate schemes in **Results**, and export the selected isolation-window settings when needed.

In Core 0.5.1-ref2, the optimization option uses a bundled S3 reference profile after fitting the current data. The exported window table must be reviewed in the instrument software; it is not a complete acquisition method.

### AIP-reID

1. On **Data**, select one RAW `.d` acquisition and the protein FASTA database.
2. On **External Software**, select the full path to **`diann.exe`** from the DIA-NN 2.3.2 installation.
3. On **Setting**, choose a new output directory, thread count, inference device, and search parameters.
4. Review **Summary**, validate the inputs, and start the search.
5. Follow the stages on **Output**. On completion, **Results** displays the first-pass and final counts and provides access to the saved reports.

Default search settings are:

| Parameter | Default |
| --- | --- |
| Peptide length | 7–30 amino acids |
| Precursor charge | 1–4 |
| Precursor m/z | 300–1800 |
| Fragment m/z | 200–1800 |
| Final precursor FDR threshold | 0.01 (1%) |

The first-pass report threshold of 0.5 and candidate-selection threshold of 0.2 are intermediate search settings; they are distinct from the final identification threshold. Use a short output path, such as `C:\AIP_runs\reID_001`, because DIA-NN creates nested intermediate-file paths.

## Open existing results

Saved analyses can be inspected without starting a new run.

| Module | Action | File to select |
| --- | --- | --- |
| AIP-MSRefine | **Results → Load manifest** | The output folder's `manifest.json` |
| AIP-WES | **Results → Load result.json** | The output folder's `result.json` |
| AIP-reID | **Results → Load result** | The output folder's normalized `result.json` |

Keep each result file with its companion figures, tables, reports, and subfolders. For reID, use the normalized `result.json` to load the interface. RAW acquisitions, FASTA databases, and example datasets are separate from the program download unless a release explicitly includes them.

## Optional Results Q&A

1. Open **Model settings** in AIP-Scope and configure the API base URL, model ID, and API key.
2. Load a result on the corresponding module card.
3. Select **Include in answers** for the results you want to discuss.
4. Ask a question and inspect the selected evidence and source references.

For example: “What are the first-pass and final reID precursor counts?” or “How do the current and candidate WES window metrics compare?”

The configured service receives selected summary facts and conversation text. RAW files, identification tables, libraries, and images are not uploaded by this feature. The Agent explains recorded outputs; it does not start analyses or modify results. Source citations and exported chat receipts support review of its answers.

## Interpreting results

- **MSRefine:** quality scores summarize diagnostic metrics; they are not FDR estimates or additional identifications.
- **WES:** candidate-window scores describe the fitted distribution and modeled coverage/load. A better score alone does not demonstrate an experimental identification gain. Review the recorded fit diagnostics.
- **reID:** precursor and peptide counts use the selected precursor FDR threshold. Protein-group counts additionally require `PG.Q.Value <= 0.01`; separately labelled precursor counts apply this additional filter too. A net count increase is not the number of uniquely recovered precursors.
- **Version and protocol matter:** saved results retain the version that produced them. The current desktop reID route uses one fresh first-pass search and differs from the frozen workflow used for the manuscript figures. Exact reproduction of those figures has not been established for Core 0.2.2; a completed desktop run should be interpreted using its own inputs, parameters, and recorded results.

## Documentation and troubleshooting

The [User guide (PDF)](https://github.com/BUAA-LiuLab/AIP-Scope/blob/main/AIP-Scope_User_Guide.pdf) provides illustrated usage instructions. Consult the release notes and module quick-start files for version-specific changes; individual interface updates may postdate guide screenshots.

| Situation | What to check |
| --- | --- |
| A module does not open | Confirm that the complete package was extracted and its runtime folders remain beside the executables. |
| The RAW and report do not match | Select the report for the same acquisition and inspect its run names and required columns. |
| reID cannot locate DIA-NN | Select the actual `diann.exe` file from a complete DIA-NN 2.3.2 installation. |
| A saved result opens without figures | Keep its companion output folders together and check relocated artifact paths. |
| A search stops with a path-length error | Use a shorter local output path and start a fresh run in a new directory. |
| Results Q&A is unavailable | Configure the model service and test the connection in Model settings. |

For help, open an [issue](https://github.com/BUAA-LiuLab/AIP-Scope/issues) with the module and version, Windows version, input/report format, relevant log message, and steps to reproduce the problem.

## Third-party components

The application packages contain third-party runtime components. Their license notices are retained with the distribution. DIA-NN is obtained separately and is governed by its own terms. This README does not assign an open-source license to the AIP applications.
