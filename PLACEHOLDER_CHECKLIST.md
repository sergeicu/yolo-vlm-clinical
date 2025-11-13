# Placeholder Values Checklist

This document lists **all placeholder values** that need to be filled in with actual experimental results.

---

## Quick Statistics

- **Total Placeholders:** ~120+
- **Locations:** Abstract, Introduction, Method, Results, Discussion
- **Categories:** Performance metrics, Dataset statistics, Training parameters

---

## 📝 Placeholder Format Guide

- `[XX%]` or `[XX.X%]` = Percentage values (accuracies, improvements)
- `[0.XX]` = Decimal metrics (mAP, precision, recall between 0 and 1)
- `[X.X]` = Decimal parameters (alpha weights, fold improvements)
- `[XXXX]` = Large integer counts (thousands: total labels, images)
- `[YYY]` = Medium integer counts (hundreds: manual annotations)
- `[XXX]` = Small integer counts (tens to hundreds: improvements, samples)
- `[XX]` or `[ZZ]` = Very small counts (hours, epochs, test cases)
- `[NNN]` = Variable count (depends on experiment)

---

## 🎯 Critical High-Priority Values (Need First)

These appear in Abstract and define the paper's main contribution:

### **Scaphoid Performance (The Main Result)**
- [ ] Pretrained scaphoid mAP@0.5: **~0.00** (confirmed)
- [ ] Our finetuned scaphoid mAP@0.5: **[XX%]** → e.g., 0.65 = 65%
- [ ] Fold improvement: **[XXX-fold]** → e.g., "∞-fold" or "65% from zero"

### **Dataset Statistics**
- [ ] Total pseudo-labels generated: **[XXXX]** → e.g., 8,500
- [ ] Manual annotations needed (FN subset): **[YYY]** → e.g., 450
- [ ] Efficiency ratio: XXXX/YYY = e.g., 19× fewer manual labels

---

## 📊 Section-by-Section Checklist

### **Abstract (11 placeholders)**

| Line | Placeholder | Description | Example Value |
|------|-------------|-------------|---------------|
| 72 | [XX\%] | Our scaphoid mAP@0.5 | 0.65 (65%) |
| 72 | [XXXX] | Total pseudo-labels | 8,500 |
| 72 | [YYY] | Manual annotations | 450 |

---

### **Introduction (4 placeholders)**

| Section | Placeholder | Description | Example Value |
|---------|-------------|-------------|---------------|
| 1.4 Contributions | [XX\%] | Our scaphoid mAP | 0.65 |
| 1.4 Contributions | [XXXX] | Total pseudo-labels | 8,500 |
| 1.4 Contributions | [YYY] | Manual annotations | 450 |

---

### **Method Section (28 placeholders)**

#### 4.4 AP View Classifier (6 values)
- [ ] Training images (AP): **[XXX]** → e.g., 500
- [ ] Training images (non-AP): **[YYY]** → e.g., 800
- [ ] Training epochs: **[ZZ]** → e.g., 20
- [ ] Test set size: **[NNN]** → e.g., 200
- [ ] Test accuracy: **[XX.X%]** → e.g., 94.5%
- [ ] Precision for AP class: **[XX.X%]** → e.g., 92.3%
- [ ] Recall for AP class: **[XX.X%]** → e.g., 96.1%

#### 4.5 Bone Detection Network (5 values)
- [ ] Annotation time: **[XX] hours** → e.g., 15 hours
- [ ] Bone detection mean mAP@0.5: **[0.XX]** → e.g., 0.91
- [ ] Table 2 (4 bones × 3 metrics = 12 values):
  - Distal Radius: mAP **[0.XX]**, Precision **[0.XX]**, Recall **[0.XX]**
  - Distal Ulna: mAP **[0.XX]**, Precision **[0.XX]**, Recall **[0.XX]**
  - Ulnar Styloid: mAP **[0.XX]**, Precision **[0.XX]**, Recall **[0.XX]**
  - Scaphoid: mAP **[0.XX]**, Precision **[0.XX]**, Recall **[0.XX]**

#### 4.7 Data Curation (5 values)
- [ ] Total AP images processed: **[XXXX]** → e.g., 12,000
- [ ] Total TP labels: **[YYYY]** → e.g., 7,200
- [ ] Total FP labels: **[ZZZ]** → e.g., 1,800
- [ ] Total FN detected: **[WWW]** → e.g., 2,500
- [ ] Manual FN annotations: **[YYY]** → e.g., 450

- [ ] Table 3 (4 bones × 5 metrics = 20 values):
  - Distal Radius: Total **[XXXX]**, TP **[XXXX]**, FP **[XXX]**, FN **[XXX]**, FN Manual **[XX]**
  - Distal Ulna: Total **[XXXX]**, TP **[XXXX]**, FP **[XXX]**, FN **[XXX]**, FN Manual **[XX]**
  - Ulnar Styloid: Total **[XXXX]**, TP **[XXXX]**, FP **[XXX]**, FN **[XXX]**, FN Manual **[XXX]**
  - Scaphoid: Total **[XXXX]**, TP **[XXX]**, FP **[XXX]**, FN **[XXX]**, FN Manual **[XXX]**
  - **TOTAL ROW**: Total **[XXXXX]**, TP **[XXXXX]**, FP **[XXXX]**, FN **[XXXX]**, FN Manual **[YYY]**

#### 4.8 Focal Loss (4 alpha weights)
- [ ] Alpha for scaphoid: **[X.X]** → e.g., 14.0 (inversely proportional to 3.8% prevalence)
- [ ] Alpha for ulnar styloid: **[X.X]** → e.g., 4.1
- [ ] Alpha for distal ulna: **[X.X]** → e.g., 3.0
- [ ] Alpha for distal radius: **[X.X]** → e.g., 1.0 (baseline)

**Formula for alpha:** α_class = (1 / prevalence) / (1 / max_prevalence)
- Scaphoid: (1/0.038) / (1/0.535) = 26.3 / 1.87 = 14.1
- Styloid: (1/0.129) / (1/0.535) = 7.75 / 1.87 = 4.1
- Ulna: (1/0.179) / (1/0.535) = 5.59 / 1.87 = 3.0
- Radius: (1/0.535) / (1/0.535) = 1.87 / 1.87 = 1.0

---

### **Results Section (52+ placeholders)**

#### 5.1 Baseline Performance

- [ ] Table 4 - Pretrained Performance (4 bones × 4 metrics = 16 values):

| Bone Type | Prevalence | mAP@0.5 | Precision | Recall |
|-----------|------------|---------|-----------|--------|
| Distal Radius | 53.5% | **[0.XX]** → e.g., 0.72 | **[0.XX]** | **[0.XX]** |
| Distal Ulna | 17.9% | **[0.XX]** → e.g., 0.51 | **[0.XX]** | **[0.XX]** |
| Ulnar Styloid | 12.9% | **[0.XX]** → e.g., 0.28 | **[0.XX]** | **[0.XX]** |
| Scaphoid | 3.8% | ~0.00 ✓ | ~0.00 ✓ | ~0.00 ✓ |
| **Overall** | --- | **[0.XX]** → e.g., 0.38 | **[0.XX]** | **[0.XX]** |

#### 5.2 Pipeline Components (3 values)
- [ ] AP classifier test accuracy: **[XX.X%]** → e.g., 94.5%
- [ ] AP classifier precision: **[XX.X%]** → e.g., 92.3%
- [ ] AP classifier recall: **[XX.X%]** → e.g., 96.1%
- [ ] Bone YOLO mean mAP@0.5: **[0.XX]** → e.g., 0.91
- [ ] Bone YOLO scaphoid mAP: **[XX%]** → e.g., 87%

#### 5.3 Pseudo-Label Stats (confirms values from Method)
- [ ] AP images processed: **[XXXX]**
- [ ] TP labels: **[YYYY]**
- [ ] FP labels: **[ZZZ]**
- [ ] FN labels: **[WWW]**
- [ ] Scaphoid FNs manually labeled: **[XX%]** → e.g., 85%
- [ ] Radius FNs manually labeled: **[YY%]** → e.g., 10%
- [ ] Total training labels: **[XXXXX]** → e.g., 11,500
- [ ] Manual annotations: **[YYY]** → e.g., 450

#### 5.4 Main Results Table

- [ ] Table 5 - Performance Comparison (4 bones × 3 methods × 2 metrics = 24 values):

**mAP@0.5:**
| Bone Type | Pretrained (Graz) | Finetuned (No Focal) | Ours (+Focal Loss) |
|-----------|-------------------|----------------------|--------------------|
| Distal Radius | [0.XX] → 0.72 | [0.XX] → 0.78 | **[0.XX]** → 0.81 |
| Distal Ulna | [0.XX] → 0.51 | [0.XX] → 0.64 | **[0.XX]** → 0.68 |
| Ulnar Styloid | [0.XX] → 0.28 | [0.XX] → 0.52 | **[0.XX]** → 0.61 |
| Scaphoid | ~0.00 | [0.XX] → 0.42 | **[0.XX]** → 0.65 |
| **Mean** | [0.XX] → 0.38 | [0.XX] → 0.59 | **[0.XX]** → 0.69 |

**Recall:**
| Bone Type | Pretrained (Graz) | Finetuned (No Focal) | Ours (+Focal Loss) |
|-----------|-------------------|----------------------|--------------------|
| Distal Radius | [0.XX] → 0.78 | [0.XX] → 0.82 | **[0.XX]** → 0.85 |
| Distal Ulna | [0.XX] → 0.56 | [0.XX] → 0.68 | **[0.XX]** → 0.72 |
| Ulnar Styloid | [0.XX] → 0.31 | [0.XX] → 0.58 | **[0.XX]** → 0.67 |
| Scaphoid | ~0.00 | [0.XX] → 0.47 | **[0.XX]** → 0.71 |
| **Mean** | [0.XX] → 0.41 | [0.XX] → 0.64 | **[0.XX]** → 0.74 |

#### 5.4 Narrative (3 values)
- [ ] Scaphoid mAP improvement: **[XX%]** → e.g., 65%
- [ ] Fold improvement: **[XXX-fold]** → e.g., "∞" or 65÷0.01 = 65× if baseline was 0.01
- [ ] Ulnar styloid absolute gain: **[XX%]** → e.g., +33% (from 28% to 61%)

#### 5.5 Ablation Study

- [ ] Table 6 - Component Contributions (4 bones × 3 configs = 12 values + 4 gains):

| Configuration | Radius | Ulna | Styloid | Scaphoid |
|--------------|--------|------|---------|----------|
| Pretrained (Graz) | [0.XX] → 0.72 | [0.XX] → 0.51 | [0.XX] → 0.28 | ~0.00 |
| + Pseudo-labels (no focal) | [0.XX] → 0.78 | [0.XX] → 0.64 | [0.XX] → 0.52 | [0.XX] → 0.42 |
| + Focal loss (ours) | [0.XX] → 0.81 | [0.XX] → 0.68 | [0.XX] → 0.61 | [0.XX] → 0.65 |
| *Gain (ours - pretrained)* | [+0.XX] → +0.09 | [+0.XX] → +0.17 | [+0.XX] → +0.33 | [+0.XX] → +0.65 |

- [ ] Scaphoid improvement from pseudo-labels: **[+XX%]** → e.g., +42% (from 0 to 0.42)
- [ ] Scaphoid improvement from focal loss: **[+XX%]** → e.g., +23% (from 0.42 to 0.65)

---

### **Discussion Section (6 placeholders)**

#### 6.1 Principal Findings
- [ ] Scaphoid fold improvement: **[XXX-fold]** → same as above
- [ ] Our scaphoid mAP@0.5: **[XX\%]** → e.g., 65%

#### 6.3 Clinical Implications
- [ ] Scaphoid proportion of wrist injuries: **[XX%]** → e.g., 5-10% (cite literature)
- [ ] Our scaphoid recall: **[XX%]** → e.g., 71%

#### 6.4 Limitations
- [ ] Manual FN annotations: **[YYY] cases** → e.g., 450
- [ ] Total labels generated: **[XXXX]** → e.g., 8,500
- [ ] LLM extraction errors for complex cases: **[XX%]** → e.g., 7%
- [ ] Bone YOLO mean mAP: **[XX%]** → e.g., 91%
- [ ] Scaphoid test cases: **[XX]** → e.g., 12

#### 6.6 Conclusions
- [ ] Scaphoid fold improvement: **[XXX-fold]**
- [ ] Total labels: **[XXXX]**
- [ ] Manual annotations: **[YYY]**

---

## 🔢 Summary by Category

### Performance Metrics (mAP, Precision, Recall)
- **Baseline table:** 16 values
- **Main results table:** 24 values
- **Ablation table:** 16 values
- **Component performance:** 5 values
- **Total:** ~61 performance values

### Dataset Statistics
- **Pseudo-label generation:** 25 values (Table 3)
- **AP classifier data:** 3 values
- **Bone detection data:** 1 value
- **Processing stats:** 8 values
- **Total:** ~37 dataset values

### Training Parameters
- **Focal loss alphas:** 4 values
- **Classifier training:** 3 values
- **Bone YOLO training:** 1 value
- **Total:** ~8 parameter values

### Clinical/Discussion Values
- **Improvements/gains:** 8 values
- **Clinical context:** 2 values
- **Limitations:** 3 values
- **Total:** ~13 narrative values

---

## 📥 Recommended Data Collection Order

### Phase 1: Baseline (Run First)
1. Test pretrained Graz model on 70 test images
2. Compute per-class mAP, precision, recall → Fill Table 4
3. **Validates main hypothesis:** Scaphoid ≈ 0%

### Phase 2: Pipeline Components
4. Train AP classifier → Fill 4.4 values
5. Train bone detection YOLO → Fill Table 2
6. Run LLM extraction on all reports (already done?)

### Phase 3: Pseudo-Labeling
7. Run co-localization pipeline → Count TPs, FPs, FNs
8. Manually annotate FN subset → Fill Table 3

### Phase 4: Finetuning
9. Train with pseudo-labels (no focal loss) → Fill "Finetuned (No Focal)" column
10. Train with pseudo-labels + focal loss (compute alphas first) → Fill "Ours" column

### Phase 5: Analysis
11. Compute ablation study → Fill Table 6
12. Calculate all improvements/gains → Fill narrative values
13. Select qualitative examples → Create Figure 3

---

## ✅ Quick Verification

Before submission, ensure:
- [ ] **Zero placeholders remain** (search for `[` in .tex file)
- [ ] **All tables sum correctly** (e.g., Table 3 TOTAL row matches column sums)
- [ ] **Gains are consistent** (e.g., Ours - Pretrained = values in Table 6 bottom row)
- [ ] **Narrative matches tables** (e.g., "[XX%] improvement" in text matches table difference)
- [ ] **Figures reference correct values** (e.g., scatterplot uses Table 4 baseline values)

---

## 📝 Template for Results File

Create a separate file (e.g., `RESULTS.txt`) with this format:

```
# BASELINE PERFORMANCE (Table 4)
Distal Radius: mAP=0.72, Precision=0.75, Recall=0.78
Distal Ulna: mAP=0.51, Precision=0.54, Recall=0.56
Ulnar Styloid: mAP=0.28, Precision=0.31, Recall=0.31
Scaphoid: mAP=0.00, Precision=0.00, Recall=0.00
Overall: mAP=0.38, Precision=0.40, Recall=0.41

# AP CLASSIFIER
Train AP: 500 images
Train non-AP: 800 images
Test size: 200 images
Epochs: 20
Test accuracy: 94.5%
Precision: 92.3%
Recall: 96.1%

# BONE DETECTION (Table 2)
Annotation time: 15 hours
Distal Radius: mAP=0.95, Precision=0.96, Recall=0.97
Distal Ulna: mAP=0.93, Precision=0.94, Recall=0.95
Ulnar Styloid: mAP=0.89, Precision=0.90, Recall=0.91
Scaphoid: mAP=0.87, Precision=0.88, Recall=0.89
Mean: mAP=0.91

# PSEUDO-LABELS (Table 3)
...
[Continue for all sections]
```

Then use find-and-replace to fill placeholders systematically.

---

## Need Help?

If uncertain about:
- **Reasonable values**: Check similar papers in medical object detection
- **Calculation errors**: Verify sum/average formulas
- **Missing experiments**: Prioritize based on "Phase" order above

