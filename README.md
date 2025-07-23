# ImageJ Macro for Semi-Automated Calcium Imaging Analysis in Neuro-Immune Research

## Overview

This ImageJ macro was developed during a research technician role in the Sensory Neurophysiology Lab at Queen Mary University of London to solve a key research bottleneck. It automates the analysis of calcium imaging data from fluorescence microscopy experiments, specifically tailored for investigating neuro-immune interactions in chronic pain models.

The primary challenge was the time-consuming manual analysis of multi-frame image stacks from experiments involving primary mouse dorsal root ganglia (DRG) neurons. This macro was created to standardize and accelerate the process, **reducing analysis turnaround time by 50%** and freeing up ~2 hours per experiment for other critical research activities.

## Application in Chronic Pain Research

This tool was applied to a project investigating how inflammatory mediators from fibromyalgia and rheumatoid arthritis patients affect neuronal signaling.

* **Goal:** To quantify calcium dynamics in mouse DRG neurons and supporting cells when exposed to human neutrophils, their extracellular vesicles (EVs), and fibroblast lysates.
* **Technique:** Time-series confocal imaging was used to record fluorescence changes in Fluo-4AM stained cells across different experimental phases.
* **Experimental Workflow Supported:**
    1.  **Baseline Recording**: Measure resting calcium levels with NaCl solution.
    2.  **Intervention**: Apply patient-derived samples (neutrophils, EVs, etc.).
    3.  **Maximal Stimulation**: Apply high KCl solution to confirm cell viability and measure maximal response.

## System Architecture and Key Features

The macro is designed for an interactive yet automated workflow, directly addressing the needs of the neuro-immune pain project.

## System Architecture

### Core Components

```
┌─────────────────────────────────────────────────────────┐
│                    Main Processing Loop                  │
├─────────────────────────────────────────────────────────┤
│  1. Image Stack Loading (.zip files)                    │
│  2. Brightness/Contrast Adjustment                      │
│  3. ROI Management (Creation/Loading)                   │
│  4. Multi-Measure ROIs across all frames                │
|  5. Time Frame Verification for Quality Control.         |
│  6. Time Series Analysis                                │
│  7. Data Export (CSV format)                            │
│  8. Batch Processing Support                            │
└─────────────────────────────────────────────────────────┘
```

### Key Features

- **Batch Processing**: Analyze multiple image stacks sequentially
- **ROI Reusability**: Save and reload regions of interest across experiments
- **Interactive Workflow**: User-guided process with clear prompts
- **Time Frame Analysis**: Separate analysis of baseline, intervention, and KCl response periods
- **Automated Measurements**: Extract fluorescence intensity data from all ROIs
- **Excel Integration**: Export results in CSV format for further analysis

## Installation and Requirements

### Prerequisites
-   **ImageJ** or **Fiji** (Fiji is recommended)
-   **Input Format**: Time-series image stacks (e.g., multi-frame TIFFs) packaged in .zip format.

### Installation Steps
1. Download the `imagej-calcium-imaging-macro.ijm` file
2. Select the .ijm: /Applications/Fiji.app/Plugins/Macros/Run...’

Alternatively,
1. Download the `imagej-calcium-imaging-macro.ijm` file
2. Place it in your ImageJ/Fiji plugins folder:
   - **Windows**: `C:\Program Files\Fiji.app\plugins\`
   - **macOS**: `/Applications/Fiji.app/plugins/`
   - **Linux**: `~/Fiji.app/plugins/`
3. Restart ImageJ/Fiji
4. The macro will appear in the Plugins menu

## Usage Instructions

### Quick Start Guide

1. **Launch the Macro** (Same as Step 1 in Installation Steps)
	 - Open ImageJ/Fiji
	 - Select the .ijm: /Applications/Fiji.app/Plugins/Macros/Run...’
   - Or, navigate to `Plugins > Process Zip File with ROIs`

2. **Load Image Stack**
   - Select your .zip file containing the calcium imaging time series
   - Wait for images to load completely

3. **Adjust Display Settings**
   - The macro will prompt you to adjust brightness/contrast
   - Use the slider to optimize cell visibility
   - Click "OK" when satisfied

4. **Define Regions of Interest (ROIs)**
   - **New Analysis**: Draw ROIs around individual neurons
     - Use oval/ellipse tool for cell bodies
     - Press 'T' to add each ROI to the manager
   - **Repeat Analysis**: Load previously saved ROI .zip file

5. **Run Measurements**
   - The macro automatically measures all ROIs across the time series
   - Results include mean fluorescence intensity per frame

6. **Time Frame Analysis**
   - Define KCl application start frame
   - Specify intervention/condition time window
   - Review baseline period if needed

7. **Export Results**
   - Save measurements as CSV file
   - Import into Excel or other analysis software

### Detailed Workflow Diagram

```
Start
  │
  ├─→ Load .zip Image Stack
  │     └─→ Brightness/Contrast Adjustment
  │
  ├─→ ROI Management
  │     ├─→ Create New ROIs
  │     │     └─→ Draw → Press 'T' → Save
  │     └─→ Load Existing ROIs
  │           └─→ Review → Modify if needed
  │
  ├─→ Multi-Measurement
  │     └─→ Extract Intensity Data
  │
  ├─→ Time Frame Definition
  │     ├─→ Baseline Period
  │     ├─→ Intervention Period
  │     └─→ KCl Response Period
  │
  └─→ Export Results
        └─→ CSV File → Excel Analysis
```

## Best Practices

### ROI Selection Guidelines
- **Cell Bodies**: Use oval/ellipse tool to outline soma
- **Avoid Empty Areas**: Ensure ROIs contain cellular structures
- **Prevent Overlap**: Keep ROIs separate to avoid signal contamination
- **Consistent Size**: Maintain similar ROI sizes for comparable measurements
- **Background ROI**: Include a background ROI for baseline correction

### Data Quality Considerations
- **Motion Correction**: Apply drift correction before analysis if needed
- **Photobleaching**: Monitor for signal decay over time
- **Focus Stability**: Ensure consistent focus throughout acquisition
- **Frame Rate**: Use appropriate sampling rate for calcium dynamics (typically 1-10 Hz)

## Troubleshooting

### Common Issues and Solutions

| Issue | Possible Cause | Solution |
|-------|----------------|----------|
| "No images loaded" error | Corrupted .zip file | Re-export images from acquisition software |
| ROIs not visible | Display settings | Adjust brightness/contrast, check "Show All" in ROI Manager |
| Measurements empty | No ROIs selected | Ensure ROIs are added to manager before measurement |
| Macro stops unexpectedly | Memory issues | Increase ImageJ memory allocation (Edit > Options > Memory & Threads) |

### Error Messages

- **"Failed to retrieve current directory"**: Check write permissions in working directory
- **"No ROIs to save"**: Ensure ROIs are properly added to ROI Manager
- **"Start frame greater than end frame"**: Verify frame numbers are entered correctly

## Data Analysis Tips

### Post-Processing in Excel
1. **Calculate ΔF/F₀**: Normalize fluorescence changes to baseline
   ```
   ΔF/F₀ = (F(t) - F₀) / F₀
   ```
   where F₀ is mean baseline fluorescence

2. **Response Amplitude**: Measure peak response during KCl application

3. **Time to Peak**: Calculate response kinetics

4. **Statistical Analysis**: Compare responses across conditions/groups

### Example Analysis Pipeline
```
Raw Data (CSV)
    ↓
Baseline Correction
    ↓
ΔF/F₀ Calculation
    ↓
Peak Detection
    ↓
Statistical Tests
    ↓
Publication Figures
```

## Technical Details

### File Structure
- **Input**: `.zip` files containing multi-frame TIFF stacks
- **ROI Files**: `.zip` files with ROI coordinates
- **Output**: `.csv` files with intensity measurements

### Measurement Parameters
- **Metric**: Mean gray value per ROI per frame
- **Bit Depth**: Supports 8-bit, 16-bit, and 32-bit images
- **Channels**: Single channel (grayscale) fluorescence images

### Memory Management
- Images loaded sequentially to minimize RAM usage
- ROI processing in batches for large datasets
- Automatic garbage collection between file processing

## Contributing and Support

### Reporting Issues
When reporting problems, please include:
- ImageJ/Fiji version
- Operating system
- Error messages (exact text)
- Sample data (if possible)

### Feature Requests
Suggestions for improvements are welcome, particularly for:
- Additional measurement parameters
- Export format options
- Automation features
- Analysis integration

## References

### Calcium Imaging Methodology
1. Grienberger, C., & Konnerth, A. (2012). Imaging calcium in neurons. *Neuron*, 73(5), 862-885.
2. Bootman, M. D., Rietdorf, K., Collins, T., Walker, S., & Sanderson, M. (2013). Loading fluorescent Ca2+ indicators into living cells. *Cold Spring Harbor Protocols*.

### ImageJ Resources
- [ImageJ User Guide](https://imagej.nih.gov/ij/docs/guide/)
- [Fiji Wiki](https://imagej.net/Fiji)
- [ImageJ Macro Language](https://imagej.nih.gov/ij/developer/macro/macros.html)

## License

This macro is provided as-is for research purposes. Users should validate results independently before publication.

---

*Developed for the Neurobiology/Neuroengineering Group*  
*Last Updated: [Current Date]*  
*Version: 1.0*
