# Figure Requirements for Restructured Paper

## Overview
The restructured paper requires **4 NEW figures** to be created. Below are detailed specifications for each figure.

---

## Figure 1: Pipeline Overview Diagram
**Filename:** `ims/fig_pipeline_NEW.png`
**Label:** `\ref{fig:pipeline}`
**Size:** Full width (1.0\linewidth)

### Description
Multi-stage flowchart showing the complete bone-aware pseudo-labeling pipeline.

### Content (Top to Bottom):

#### Stage 1: Report Processing (Top)
```
Radiology Report → [Remove PHI] → [LLM (MedGemma 27B)] → Structured Output
                    LLMguard         Chain-of-thought      {bone: radius,
                                    + Few-shot             fracture: yes,
                                    + Pydantic schema      ...}
```

#### Stage 2: Image Processing (Middle Left)
```
X-ray Images → [AP Classifier] → AP Views Only
                ResNet-18
```

#### Stage 3: Dual Detection (Middle Center)
```
AP Images → [Bone Detection YOLO] → 4 Bone Boxes (Radius, Ulna, Styloid, Scaphoid)
         ↓
         [Pretrained Graz Fracture YOLO] → Fracture Predictions
```

#### Stage 4: Co-Localization (Middle Right)
```
Bone Boxes + Fracture Boxes → [Compute IoU] → Assign Fracture to Bone
                                                      ↓
                          [Validate vs Report] → TP / FP / FN
                          (from LLM extraction)
```

#### Stage 5: Finetuning (Bottom)
```
Curated Dataset:                    [Manual Annotation]
- TPs (keep boxes)                         ↓
- FPs (remove boxes)              Small FN Subset
- FNs (prioritize rare)                    ↓
         ↓                                 ↓
    [Merge] ──────────────────────────────┘
         ↓
    [Focal Loss Training]
    - Class-stratified sampling
    - α weighted by 1/frequency
    - γ = 2.0
         ↓
    Finetuned YOLO (Improved Rare Class Detection)
```

### Visual Style
- Use distinct colors for each stage (gradient from blue → green → yellow → orange → red)
- Box shapes: Rectangle for processes, rounded rectangle for data, diamond for decisions
- Arrows should be thick and clearly directional
- Include small icons: 📄 for reports, 🖼️ for images, 🤖 for models, ✓/✗ for validation

---

## Figure 2: Class Imbalance Scatterplot
**Filename:** `ims/fig_class_imbalance_NEW.png`
**Label:** `\ref{fig:class_imbalance}`
**Size:** 0.6\linewidth

### Description
Scatterplot showing inverse relationship between training set prevalence and pretrained model performance.

### Axes
- **X-axis:** Training Set Prevalence (%)
  - Range: 0% to 60%
  - Ticks: 0, 10, 20, 30, 40, 50, 60
- **Y-axis:** Pretrained Model mAP@0.5
  - Range: 0.0 to [max value from results]
  - Ticks: 0.0, 0.2, 0.4, 0.6, 0.8, 1.0

### Data Points (4 bones)
Plot with large colored markers:
1. **Distal Radius**: (53.5%, [XX%]) - Blue circle
2. **Distal Ulna**: (17.9%, [XX%]) - Green square
3. **Ulnar Styloid**: (12.9%, [XX%]) - Orange triangle
4. **Scaphoid**: (3.8%, ~0%) - Red star (emphasize with larger size)

### Additional Elements
- **Trend line**: Dashed gray line showing negative correlation
- **Annotations**: Label each point with bone name
- **Highlight box**: Draw red box around scaphoid point with arrow pointing to it
- **Text annotation**: "Critical gap: 3.8% prevalence → near-zero detection"

### Style
- Clean, publication-quality matplotlib/seaborn style
- Grid lines (light gray, dashed)
- Legend in upper right
- Large, readable font (12pt for axis labels, 10pt for tick labels)

---

## Figure 3: Qualitative Results Comparison
**Filename:** `ims/fig_qualitative_NEW.png`
**Label:** `\ref{fig:qualitative}`
**Size:** Full width (1.0\linewidth)

### Description
4-row comparison showing ground truth, pretrained predictions, and our model's predictions on representative test cases.

### Layout
**4 Rows × 4 Columns:**

#### Row 1: Scaphoid Fracture (Rare, Critical)
| Column 1: Original Image | Column 2: Ground Truth | Column 3: Pretrained (Graz) | Column 4: Ours (Finetuned) |
|-------------------------|------------------------|----------------------------|---------------------------|
| Wrist X-ray (AP view)   | Green box on scaphoid  | No detections (missed!)    | Blue box on scaphoid ✓    |

#### Row 2: Ulnar Styloid Fracture (Rare)
| Column 1: Original Image | Column 2: Ground Truth | Column 3: Pretrained (Graz) | Column 4: Ours (Finetuned) |
|-------------------------|------------------------|----------------------------|---------------------------|
| Wrist X-ray (AP view)   | Green box on styloid   | No detections (missed!)    | Blue box on styloid ✓     |

#### Row 3: Distal Radius Fracture (Common)
| Column 1: Original Image | Column 2: Ground Truth | Column 3: Pretrained (Graz) | Column 4: Ours (Finetuned) |
|-------------------------|------------------------|----------------------------|---------------------------|
| Wrist X-ray (AP view)   | Green box on radius    | Red box on radius ✓        | Blue box on radius ✓      |

#### Row 4: Multi-Fracture Case (Complex)
| Column 1: Original Image | Column 2: Ground Truth     | Column 3: Pretrained (Graz)        | Column 4: Ours (Finetuned)    |
|-------------------------|----------------------------|------------------------------------|-------------------------------|
| Wrist X-ray with cast   | 2 green boxes:             | 3 red boxes:                       | 2 blue boxes:                 |
|                         | - Radius                   | - Radius ✓                         | - Radius ✓                    |
|                         | - Ulna                     | - Ulna ✓                           | - Ulna ✓                      |
|                         |                            | - Metacarpal (FALSE POSITIVE! ✗)   |                               |

### Color Coding
- **Ground truth boxes:** Thick green outline, no fill
- **Pretrained predictions:** Thick red outline, semi-transparent red fill (alpha=0.2)
- **Our predictions:** Thick blue outline, semi-transparent blue fill (alpha=0.2)

### Annotations
- Row labels on left: "Row 1: Scaphoid (rare)", "Row 2: Styloid (rare)", "Row 3: Radius (common)", "Row 4: Multi-fracture"
- Column headers at top: "Original", "Ground Truth", "Pretrained (Graz)", "Ours (Finetuned)"
- Add ✓ or ✗ symbols to indicate correct/incorrect detections
- Use red arrow pointing to missed scaphoid in Row 1, Column 3

### Style
- Gray background separator lines between rows
- Equal spacing between columns
- High-resolution X-ray images (de-identified)
- Box line width: 3px

---

## Figure 4: Bone Detection Examples
**Filename:** `ims/fig_bone_examples_NEW.png`
**Label:** `\ref{fig:bone_detection_examples}`
**Size:** 0.8\linewidth

### Description
Grid showing bone detection YOLO outputs across diverse patient presentations.

### Layout
**2 Rows × 4 Columns = 8 example images**

Each image shows an AP wrist X-ray with 4 colored bounding boxes:

#### Color Coding (Consistent Across All Images)
- **Distal Radius:** Red box
- **Distal Ulna:** Blue box
- **Ulnar Styloid:** Green box
- **Scaphoid:** Yellow box

### Image Selection Criteria
Choose 8 diverse cases:
1. Pediatric patient (young, small bones)
2. Adolescent patient (larger, more mature bones)
3. Left wrist
4. Right wrist
5. Image with cast/splint visible
6. Image with slight rotation
7. Image with fracture visible (fractures not labeled, just showing bone detection works)
8. High-quality, centered image

### Annotations
- Small legend in bottom-right corner showing color-to-bone mapping
- No bounding box labels (colors convey information)
- Row labels: "Diverse Patient Presentations"

### Style
- Clean, uniform spacing
- All images normalized to same brightness/contrast
- Box line width: 2px, solid
- No fill (hollow boxes)
- High detection confidence threshold (>0.8) for clean visualization

---

## Additional Figure Notes

### General Requirements
- **Resolution:** Minimum 300 DPI for publication
- **Format:** PNG with transparency where appropriate
- **File naming:** Use descriptive names as specified above
- **De-identification:** Ensure all X-rays are properly de-identified (no patient info visible)

### Software Recommendations
- **Pipeline diagram:** Draw.io, Lucidchart, or PowerPoint with export to PNG
- **Scatterplot:** Python (matplotlib/seaborn) or R (ggplot2)
- **Qualitative results:** Python (opencv/PIL) for overlaying boxes on X-rays
- **Bone detection examples:** Python (opencv/PIL) for box visualization

### Code Template for Bounding Boxes (Python)
```python
import cv2
import numpy as np

# Colors (BGR format for OpenCV)
COLORS = {
    'ground_truth': (0, 255, 0),      # Green
    'pretrained': (0, 0, 255),         # Red
    'ours': (255, 0, 0),               # Blue
    'radius': (0, 0, 255),             # Red
    'ulna': (255, 0, 0),               # Blue
    'styloid': (0, 255, 0),            # Green
    'scaphoid': (0, 255, 255)          # Yellow
}

def draw_box(image, box, color, label=None, thickness=3):
    """Draw bounding box on image"""
    x1, y1, x2, y2 = box
    cv2.rectangle(image, (x1, y1), (x2, y2), color, thickness)
    if label:
        cv2.putText(image, label, (x1, y1-10),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.5, color, 2)
    return image
```

---

## Placeholder Images

Until real figures are created, consider:
1. Using the existing `fig1.png`, `fig2.png`, `fig3.png`, `fig4.png` as temporary placeholders
2. The paper will compile with placeholder references (e.g., `ims/fig_pipeline_NEW.png`) even if files don't exist yet
3. LaTeX will show "File not found" warnings but will still generate PDF

---

## Priority Order

If creating figures sequentially:
1. **Pipeline diagram** (Figure 1) - Most important for understanding method
2. **Qualitative results** (Figure 3) - Most important for showing improvement
3. **Class imbalance plot** (Figure 2) - Motivates the problem
4. **Bone detection examples** (Figure 4) - Supporting evidence

---

## Questions to Clarify

Before creating figures, confirm:
- [ ] Which specific X-ray cases to use for qualitative examples?
- [ ] Exact mAP values for scatterplot data points?
- [ ] Preferred visual style (colors, fonts, layout)?
- [ ] Any institutional branding requirements (e.g., BCH colors)?

