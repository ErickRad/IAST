# IAST — Intelligent Agricultural images Segmentation Tool

**IAST** is a lightweight command-line application (CLI) developed for **Demetec Agro**. It focuses on agricultural computer vision by performing image segmentation using two classical computer vision techniques: **HSV color thresholding** and **K-Means clustering**.

## Features

* Load local images (optional webcam support)
* Segment **green** or **blue** regions
* Switch between **HSV** and **K-Means** segmentation
* Fine-tune HSV thresholds through command-line arguments
* Generate:

  * `*_mask.png` — Binary segmentation mask (0/255)
  * `*_overlay.png` — Segmentation overlay on the original image
* Display execution statistics:

  * Processing time
  * Percentage of segmented pixels

## Implemented Methods

### HSV Color Segmentation

Converts the input image to the **HSV** color space and applies thresholding based on configurable ranges of **Hue**, **Saturation**, and **Value**. This approach is well suited for detecting vegetation or blue agricultural materials such as tarps and field markers.

```bash
python segment.py --input samples/corn.jpg --method hsv --target green
```

Example with custom HSV thresholds:

```bash
python segment.py --input samples/field.jpg --method hsv --target green \
--hmin 35 --hmax 85 --smin 60 --smax 255 --vmin 40 --vmax 255
```

### K-Means Segmentation

Applies K-Means clustering to image pixels (RGB or HSV) to group regions with similar colors. The cluster closest to the selected target color is considered the segmented region.

```bash
python segment.py --input samples/soybean.jpg --method kmeans --k 3 --target green
```

## Limitations

Although IAST produces reliable results under controlled conditions, several real-world factors may reduce segmentation accuracy.

### Uneven Lighting

Significant illumination changes affect the **Value (V)** channel in the HSV color space. Overexposed images may cause colors to shift toward white, reducing segmentation quality.

### Low Color Saturation

Dry, yellowish, or unhealthy vegetation often exhibits low saturation, making it difficult to distinguish from soil using color-based segmentation alone.

### Visual Noise

Objects with colors similar to the target, such as tree trunks, wooden posts, or weathered plastic, may generate false positives. Morphological operations (e.g., `cv2.morphologyEx`) can help reduce these artifacts.

### Parameter Sensitivity

HSV segmentation requires manual threshold adjustment depending on camera characteristics, crop type, and lighting conditions. Small threshold changes may significantly alter the output.

### K-Means Variability

Since K-Means initializes cluster centers randomly, segmentation results may vary slightly between executions. Setting a fixed random seed (`np.random.seed()`) ensures reproducible results.

### Real-World Agricultural Conditions

Shadows, soil variability, and mixed vegetation reduce segmentation accuracy. The algorithm identifies dominant colors rather than specific plant species.

## Future Improvements

* Apply histogram equalization (`cv2.equalizeHist`) to improve illumination consistency.
* Add morphological post-processing (erosion and dilation) to reduce segmentation noise.
* Integrate lightweight deep learning models, such as compact CNNs or U-Net architectures, for semantic segmentation.

## Installation

### Clone the repository

```bash
git clone https://github.com/ErickRad/IAST.git
cd IAST
```

### Create and activate a virtual environment

**Linux/macOS**

```bash
python -m venv .venv
source .venv/bin/activate
```

**Windows**

```powershell
python -m venv .venv
.venv\Scripts\activate
```

### Install dependencies

```bash
pip install -r requirements.txt
```

## Project Structure

```text
IAST/
├── segment.py
├── segmentation/
│   ├── base_segmenter.py
│   ├── hsv_segmenter.py
│   └── kmeans_segmenter.py
├── outputs/
├── samples/
│   └── soybean.jpg
├── requirements.txt
└── README.md
```
