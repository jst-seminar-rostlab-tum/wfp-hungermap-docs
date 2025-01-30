---
id: graph-plotting-utility
title: Graph Plotting Utility
slug: /graph-plotting-utility
---

# Graph Plotting Utility

**Author:** `Muhammed Emre Bayraktaroglu`

This documentation provides an overview of the **Graph Plotting Utility**, which is designed to generate and encode food consumption-related graphs for different countries. The utility processes country-level predictions and outputs visualizations as base64-encoded images.

---

## Overview

The **Graph Plotting Utility**:
- Generates **Food Consumption Score (FCS)** and **Reduced Coping Strategy Index (rCSI)** graphs.
- Applies confidence intervals for visual clarity.
- Encodes graphs as base64 strings for seamless integration.
- Cleans up temporary image files after encoding.

---

## Requirements and Setup

Before using this utility, ensure you have the following:
- Python 3.8+ installed.
- `matplotlib` installed for graph generation.

### Directory Structure

The script assumes the following folder structure:
```plaintext
project_root/
├── plots/  # Stores temporary image files before encoding
│   ├── country_FCS.png
│   ├── country_RCSI.png
```
After execution, the `plots` folder will be removed, leaving only base64-encoded images.

---

## Key Features

#### Graph Generation
The script generates FCS and rCSI visualizations based on country-specific predictions.

#### Base64 Encoding
The generated graphs are converted into base64 strings for easy embedding.

#### Temporary File Cleanup
Ensures that all intermediate image files are deleted after encoding.

---

## Function(s)

### **Function:** `plot_fcs(country_data)`
Generates and encodes an **FCS graph** for a given country.

#### **Process:**
1. Extracts **FCS data** (dates, values, confidence intervals) from `country_data`.
2. Plots a time-series graph with confidence shading.
3. Saves the graph as a temporary PNG file.
4. Encodes the image in base64 format.
5. Deletes the temporary image file.

#### **Exceptions Handled:**
- `KeyError`: If necessary data is missing.
- `FileNotFoundError`: If the image file cannot be located post-generation.
- `PermissionError`: If the script lacks the necessary permissions to delete files.

---

### **Function:** `plot_rcsi(country_data)`
Generates and encodes an **rCSI graph** for a given country.

#### **Process:**
1. Extracts **rCSI data** (dates, values, confidence intervals) from `country_data`.
2. Plots a time-series graph with confidence shading.
3. Saves the graph as a temporary PNG file.
4. Encodes the image in base64 format.
5. Deletes the temporary image file.

#### **Exceptions Handled:**
- `KeyError`: If necessary data is missing.
- `FileNotFoundError`: If the image file cannot be located post-generation.
- `PermissionError`: If the script lacks the necessary permissions to delete files.

---

### **Function:** `generate_graphs(data)`
Processes a dataset to generate and encode FCS and rCSI graphs for multiple countries.

#### **Process:**
1. Iterates through a list of country dictionaries.
2. Calls `plot_fcs()` and `plot_rcsi()` for each country.
3. Stores the resulting base64-encoded images in the dataset.
4. Cleans up the `plots` directory after processing.

#### **Exceptions Handled:**
- `OSError`: If the script encounters issues removing the `plots` directory.

---

### **Function:** `base64_encode_image(image_path)`
Encodes an image file as a base64 string.

#### **Process:**
1. Reads the image file in binary mode.
2. Encodes the file using base64.
3. Returns the encoded string.

---

## Example Usage

To execute the script, call `generate_graphs()` with a dataset:
```python
country_data_list = [
    {"country_name": "Country A", "fcs_prediction": [...], "rcsi_prediction": [...]},
    {"country_name": "Country B", "fcs_prediction": [...], "rcsi_prediction": [...]},
]
generate_graphs(country_data_list)
```

If no data is available for a country, the script will print:
```plaintext
No FCS data available for Country A
No RCSI data available for Country A
```

Upon successful encoding, it prints:
```plaintext
FCS Graph saved: plots/Country_A_FCS.png
RCSI Graph saved: plots/Country_A_RCSI.png
```

---

## What Happens Under the Hood?

1. **Data Extraction:** The script retrieves FCS and rCSI predictions from country data.
2. **Graph Generation:** Uses Matplotlib to generate visual representations.
3. **Base64 Encoding:** Converts the images into a string format for easy transfer.
---

## Conclusion

The **Graph Plotting Utility** is an efficient tool for visualizing food consumption trends across different countries. 

