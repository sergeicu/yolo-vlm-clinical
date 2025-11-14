# Paper Restructuring Summary: Bone-Aware Pseudo-Labeling for Class-Imbalanced Fracture Detection

## Overview

The paper has been **completely restructured** from a multi-dimensional fracture assessment approach to a **class imbalance-focused methodology** using bone-aware pseudo-labeling and focal loss training.

---

## 🎯 Major Changes

### **OLD APPROACH (Removed)**
- Four separate YOLO models for: anatomical location, fracture pattern, healing stage, alignment
- VLM verification for bounding box validation
- Multi-dimensional fracture characterization
- Focus on overall mAP improvement (76% → 83%)

### **NEW APPROACH (Implemented)**
- **Single focus**: Addressing severe class imbalance (53.5% radius vs 3.8% scaphoid)
- **Key innovation**: Bone-aware pseudo-labeling pipeline with 5 stages
- **Clinical motivation**: Rare fractures (scaphoid) are critical but systematically under-detected
- **Core result**: XXX-fold improvement on scaphoid detection (near-zero → XX% mAP@0.5)

---

## 📄 Section-by-Section Changes

### **1. Title**
**New:** "Addressing Class Imbalance in Pediatric Fracture Detection through Bone-Aware Pseudo-Labeling and Focal Loss Training"

### **2. Abstract**
**Completely rewritten** to emphasize:
- Severe class imbalance problem (53.5% radius, 3.8% scaphoid)
- Clinical consequences (avascular necrosis if scaphoid missed)
- 5-stage automated pipeline
- Dramatic improvement on rare classes while maintaining common class performance
- [XXXX] pseudo-labels generated with only [YYY] manual annotations

### **3. Introduction (4 subsections)**

#### 3.1 Clinical Motivation
- Pediatric fractures: 25% of injuries
- Severe imbalance: distal radius 53.5% vs scaphoid 3.8%
- **Clinical impact**: Scaphoid → avascular necrosis if missed
- Pretrained models: near-zero scaphoid accuracy despite adequate radius performance

#### 3.2 Class Imbalance Problem
- Traditional solutions face labeling bottleneck
- At 3.8% prevalence, need to review ~2,600 exams to find 100 scaphoid cases
- Existing pseudo-labeling doesn't address class imbalance systematically

#### 3.3 Our Approach
- Multi-stage bone-aware pseudo-labeling
- Key insight: Bone detection >> fracture detection (consistent, structurally invariant)
- 4-step process: AP filtering → bone stratification → co-localization → balanced training

#### 3.4 Contributions
1. Demonstrate catastrophic failure on rare classes (near-zero scaphoid)
2. Bone-aware co-localization pipeline
3. Focal loss improves rare detection from near-zero to [XX%]
4. Efficient annotation: [XXXX] labels with [YYY] manual annotations

### **4. Method (9 detailed subsections)**

#### 4.1 Pipeline Overview
- Figure describing 5-stage pipeline with detailed caption

#### 4.2 Data Collection & Preprocessing
- 71,714 examinations from BCH
- Multi-stage anonymization
- **Table 1**: Class distribution showing severe imbalance

#### 4.3 LLM Report Extraction
- MedGemma 27B for bone type extraction
- Schema validation with Pydantic
- 0.98% hallucination rate

#### 4.4 AP View Classification
- **NEW SECTION**
- ResNet-18 binary classifier (AP vs other)
- Motivation: Lateral/oblique have bone overlap
- Performance: [XX.X%] accuracy

#### 4.5 Bone Detection Network
- **NEW SECTION - CRITICAL**
- Why easier than fracture detection: consistent presence, structural invariance, clear boundaries
- 300 manually annotated images (1,200 bone boxes)
- YOLOv7 from scratch
- **Table 2**: Bone detection performance by class

#### 4.6 Bone-Fracture Co-Localization
- **NEW SECTION - CORE METHOD**
- IoU-based spatial assignment (τ = 0.3)
- Mathematical formulation included
- Report-based validation: TP/FP/FN classification
- Handles multi-view examinations with anatomical knowledge

#### 4.7 Automated Data Curation
- **NEW SECTION**
- TP: Include with original box
- FP: Include with box removed (hard negatives)
- FN: Manual annotation of prioritized subset (scaphoid > styloid > ulna > radius)
- Class-stratified batch sampling (14× oversampling for scaphoid)
- **Table 3**: Pseudo-label statistics by bone type

#### 4.8 Focal Loss-Based Finetuning
- **NEW SECTION**
- Focal loss equation: FL(p_t) = -α_t (1-p_t)^γ log(p_t)
- γ = 2.0, α inversely proportional to class frequency
- Training configuration details
- Loss components: focal classification + IoU + objectness

#### 4.9 Evaluation Protocol
- 70 expert-annotated test images
- Per-class metrics (critical for imbalance)

---

### **5. Results (5 subsections with 7 tables/figures)**

#### 5.1 Baseline Performance (The Problem)
- **Table 4**: Pretrained Graz performance by bone class
- Key finding: ~0% scaphoid mAP despite adequate radius performance
- **Figure 1**: Class prevalence vs. detection performance (inverse relationship)

#### 5.2 Pipeline Component Performance
- AP classifier: [XX.X%] accuracy
- Bone detection: High across all 4 classes (validates "easier than fracture" hypothesis)
- LLM extraction: 93% schema compliance, 2.3s/report

#### 5.3 Pseudo-Label Curation Statistics
- [XXXX] images processed → [YYYY] TPs, [ZZZ] FPs, [WWW] FNs
- Manual effort concentrated on rare classes
- Final dataset: [XXXXX] labels with [YYY] manual annotations

#### 5.4 Main Results (The Solution)
- **Table 5**: Performance comparison (Pretrained / Finetuned no focal / Ours with focal)
- **KEY RESULT**: Scaphoid improves from near-zero to [XX%] mAP ([XXX-fold] improvement)
- Maintained/improved performance on common classes

#### 5.5 Ablation Study
- **Table 6**: Component contribution
- Shows pseudo-labeling provides gains, focal loss adds further improvement

#### 5.6 Qualitative Results
- **Figure 2**: Representative examples showing:
  - Row 1: Scaphoid missed → detected (critical improvement)
  - Row 2: Ulnar styloid recovered
  - Row 3: Radius maintained
  - Row 4: Complex multi-fracture case
- **Figure 3**: Bone detection examples (color-coded by type)

---

### **6. Discussion (6 subsections)**

#### 6.1 Principal Findings
- [XXX-fold] scaphoid improvement with clinical significance
- Three key insights: easier bone detection, automatic TP/FP/FN, focal loss leverage

#### 6.2 Comparison to Prior Work
- Most work reports aggregate metrics (obscures class disparities)
- Prior pseudo-labeling doesn't target rare classes
- Our contribution: automated rare-class label generation

#### 6.3 Clinical Implications
- Improved scaphoid detection → reduced missed diagnoses → prevent necrosis
- Template for addressing long-tailed medical distributions
- AI safety net in emergency departments

#### 6.4 Limitations
- Residual manual FN labeling needed ([YYY] cases)
- Assumes quality radiology reports
- Bone detection errors propagate
- Only demonstrated for pediatric wrist
- Small test set (70 images, [XX] scaphoid cases)

#### 6.5 Future Directions
- Uncertainty estimation for low-confidence flagging
- Extend to more fracture types (metacarpals, phalanges, other carpals)
- Joint bone-fracture multi-task learning
- Apply to other imbalanced medical problems
- Prospective clinical trials

#### 6.6 Conclusions
- Bone-aware pseudo-labeling + focal loss systematically addresses class imbalance
- [XXXX] labels with [YYY] manual annotations
- Template for long-tailed medical detection problems

---

## 📊 Tables Added (with placeholders)

1. **Table 1**: Class distribution (distal radius 53.5% vs scaphoid 3.8%)
2. **Table 2**: Bone detection performance by class
3. **Table 3**: Pseudo-label generation statistics (TP/FP/FN by bone)
4. **Table 4**: Baseline pretrained performance by bone class
5. **Table 5**: Main results comparing 3 approaches (Pretrained / Finetuned / Ours)
6. **Table 6**: Ablation study showing component contributions

## 🎨 Figures Added (descriptions provided for creation)

1. **Figure: Pipeline Overview** - 5-stage workflow diagram (LLM → AP filter → Bone YOLO → Co-localization → Focal loss)
2. **Figure: Class Imbalance** - Scatterplot of prevalence vs. mAP (inverse correlation)
3. **Figure: Qualitative Results** - 4-row comparison (Ground truth / Pretrained / Ours)
4. **Figure: Bone Detection Examples** - Color-coded bone detections across diverse cases

---

## 🗑️ Content Removed

- Four specialized YOLO models narrative
- Multi-dimensional assessment (healing, alignment)
- VLM verification table and discussion
- Old Figure 2 (LLM-guard pipeline)
- Old Figure 4 (Precision-recall curves)
- Clinical necessity for multi-dimensional assessment section
- Pseudo-blind finetuning terminology

---

## 🔧 Technical Additions

### Mathematical Formulations
- IoU equation for co-localization
- Focal loss equation with α, γ parameters
- Total loss equation (classification + IoU + objectness)

### Detailed Training Specifications
- AP classifier: ResNet-18, Adam, lr=1e-4, 224×224
- Bone YOLO: YOLOv7, SGD, lr=0.01, 640×640, 100 epochs
- Fracture YOLO: Graz-pretrained, SGD, lr=0.001, 1280×1280, 50 epochs, focal loss
- Augmentation strategies for each component

### Evaluation Metrics
- Per-class mAP@0.5 (critical for imbalance)
- Precision, Recall by bone type
- Overall vs. stratified performance

---

## 📝 Placeholders to Fill

Throughout the paper, placeholders are marked with square brackets:

- **[XX.X%]** - Percentages (accuracies, precisions, recalls)
- **[0.XX]** - Decimal metrics (mAP values)
- **[XXXX]** - Large counts (total pseudo-labels generated)
- **[YYY]** - Medium counts (manual annotations needed)
- **[XXX]** - Small counts or fold improvements
- **[XX]** - Very small counts (hours, scaphoid test cases)

### Key Placeholders by Section

**Abstract & Introduction:**
- [XX%] scaphoid mAP (ours)
- [XXXX] total pseudo-labels
- [YYY] manual annotations
- [XXX-fold] improvement

**Method:**
- AP classifier accuracy, precision, recall
- Bone detection mAP by class (4 values)
- Training dataset sizes
- Alpha values for focal loss (4 values)

**Results:**
- Baseline performance table (16 values: 4 bones × 4 metrics)
- Main results table (36 values: 4 bones × 3 methods × 3 metrics)
- Ablation table (16 values)
- Pseudo-label statistics (20+ values)
- All percentage improvements and fold changes

**Discussion:**
- Scaphoid test set size
- Clinical impact statistics
- Error propagation rates

---

## 🎯 Key Narrative Changes

### OLD: Multi-task learning across 4 dimensions
**Focus:** Comprehensive fracture characterization (location + pattern + healing + alignment)
**Result:** Overall mAP improvement (76% → 83%)

### NEW: Targeted rare class improvement
**Focus:** Addressing class imbalance (53.5% radius vs 3.8% scaphoid)
**Result:** XXX-fold scaphoid improvement (near-zero → XX%)

### Clinical Framing

**OLD:** "Computer-aided detection provides comprehensive characterization"
**NEW:** "Rare fractures are systematically missed but clinically critical (necrosis risk)"

### Technical Contribution

**OLD:** LLM extraction enables multi-task YOLO training
**NEW:** Bone-aware co-localization + focal loss systematically curates class-balanced data

---

## 📋 Next Steps

1. **Fill in all [PLACEHOLDER] values** with your actual experimental results
2. **Create new figures**:
   - Pipeline diagram (5 stages)
   - Class imbalance scatterplot
   - Qualitative results (4-row comparison)
   - Bone detection examples
3. **Add missing citations**:
   - `\cite{scaphoid_necrosis}` - Clinical scaphoid complications
   - `\cite{resnet}` - ResNet paper
   - `\cite{focal_loss}` - Focal Loss paper (Lin et al. 2017)
   - `\cite{balanced_sampling_cite}` - Class balancing literature
   - Update existing citations as needed
4. **Review and compile** - Check LaTeX compiles without errors
5. **Proofread** - Ensure formal scientific language throughout

---

## ✅ Verification Checklist

- [x] Title reflects new focus (class imbalance)
- [x] Abstract emphasizes scaphoid as critical rare class
- [x] Introduction motivates class imbalance problem
- [x] Method includes all 5 pipeline stages with detailed subsections
- [x] 6+ tables added (with placeholders)
- [x] 4 figures described (need to create actual images)
- [x] Results structured by: baseline → components → main → ablation → qualitative
- [x] Discussion addresses: findings, prior work, clinical impact, limitations, future work
- [x] Mathematical formulations included (IoU, focal loss)
- [x] Training details specified for all components
- [x] Old multi-task narrative completely removed
- [x] Formal scientific language throughout
- [x] `\usepackage{multirow}` added for tables

---

## 📞 Contact

All changes tracked in git. Original file preserved as `midl-shortpaper.tex`.

**Summary:** Paper transformed from multi-task fracture characterization → class imbalance-focused bone-aware pseudo-labeling. Core contribution is now systematic improvement of rare but critical scaphoid fracture detection through anatomically-informed automated data curation and focal loss training.
