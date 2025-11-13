# Critical Review: "Addressing Class Imbalance in Pediatric Fracture Detection through Bone-Aware Pseudo-Labeling and Focal Loss Training"

**Review for:** Medical Imaging with Deep Learning (MIDL) 2025
**Reviewer Perspective:** AI/ML researcher familiar with computer vision, object detection, and medical imaging

---

## Summary

This paper addresses severe class imbalance in pediatric wrist fracture detection, where rare but clinically critical fractures (scaphoid, 3.8% prevalence) suffer catastrophic failure in pretrained models (15.1% recall) compared to common fractures (distal radius, 53.5% prevalence, 80.5% recall). The authors propose a multi-stage pipeline combining:

1. LLM-based extraction of fracture bone types from radiology reports
2. AP view classification to filter anatomically informative images
3. Bone detection network to localize four anatomical regions
4. Co-localization of pretrained fracture predictions with bone regions, validated against reports to generate TP/FP/FN labels
5. Focal loss-based finetuning on curated dataset

**Main result:** Scaphoid recall improves 4.5× (15.1% → 67.4%) and precision 2.3× (33.3% → 77.3%) while all other bone classes also improve (macro-avg recall: 58.7% → 76.9%).

---

## Strengths

### 1. **Well-Motivated Clinical Problem** ⭐⭐⭐⭐⭐
- Class imbalance (14-fold difference) is a real, fundamental problem in medical AI
- Clinical consequences clearly articulated: scaphoid fractures → avascular necrosis if missed
- 5.3× performance gap between common/rare classes on pretrained model is striking
- Strong clinical framing with quantified impact (reducing missed diagnoses from 85% to 33%)

### 2. **Systematic Approach to Data Curation** ⭐⭐⭐⭐
- Novel insight: bone detection is easier than fracture detection (consistent presence, structural invariance)
- Automated TP/FP/FN labeling through co-localization + report validation is clever
- Multi-stage pipeline is well-designed and each component is justified
- Addresses annotation bottleneck (at 3.8% prevalence, need to review 2,600 exams for 100 scaphoid cases)

### 3. **Strong Empirical Results** ⭐⭐⭐⭐⭐
- 4.5× improvement on scaphoid recall is impressive and clinically meaningful
- **Critical finding**: All classes improve simultaneously (no performance sacrifice)
- Ablation comparing focal loss vs. balanced subset training is insightful (67.4% vs 44.2% scaphoid recall)
- Three-way comparison (Pretrained / Finetuned Scaphoid / Finetuned Balanced) tells a clear story

### 4. **Appropriate Evaluation Design** ⭐⭐⭐⭐
- Stratified test set (100 images per bone type) ensures fair evaluation
- Per-class metrics (not just overall mAP) are essential for imbalance problem
- Honest reporting of baseline catastrophic failure (15.1% scaphoid recall)

### 5. **Practical Impact** ⭐⭐⭐⭐
- Large dataset (71,714 examinations) demonstrates scalability
- Automated pipeline reduces manual annotation burden
- Clear path to clinical deployment (safety net for emergency departments)

---

## Weaknesses

### 1. **Incomplete Experimental Details** ⭐⭐ (Major Issue)

**Missing critical information:**
- AP classifier performance: All values are [XX.X%] placeholders
- Bone detection results: Table 2 completely placeholder (12 values missing)
- Pseudo-label statistics: Table 3 entirely placeholder (~25 values)
- Focal loss alpha weights: All 4 values are [X.X] placeholders
- Manual annotation counts: [YYY], [ZZ%], etc. all missing

**Impact:** Cannot assess:
- Whether AP classifier is reliable (if 70% accurate, introduces noise)
- Whether bone detection truly works well (claimed "high performance" unsubstantiated)
- Actual efficiency gains (how many manual annotations vs. pseudo-labels?)
- Whether focal loss weights are reasonable

**Recommendation:** These are not optional—they're core to the method. The paper should not be accepted until these experiments are completed and results reported.

### 2. **Test Set Size Concerns** ⭐⭐⭐ (Moderate Issue)

**Problem:**
- Paper states "70 expert-annotated images" in Method section (line 422)
- But Results section uses "400 images (100 per bone type)"
- **Inconsistency unresolved**

**Statistical power:**
- If truly 100 scaphoid test cases: adequate
- If only 70 images total (~18 scaphoid): very limited power for rare class
- Confidence intervals not reported—what is variance in scaphoid recall?

**Bootstrap analysis needed:**
- With small test sets, single metrics can be misleading
- Need error bars, confidence intervals, or statistical significance tests

### 3. **Limited Ablation Study** ⭐⭐⭐

**What's ablated:**
- Pretrained vs. Finetuned Scaphoid (balanced subset) vs. Finetuned Balanced (ours)

**What's missing:**
- Effect of AP filtering alone (how much does this help?)
- Effect of bone co-localization alone (vs. just using pretrained predictions directly)
- Effect of focal loss parameter γ (only 2.0 tested, what about 1.0, 3.0?)
- Effect of IoU threshold τ (empirically set to 0.3, but ablation would strengthen claim)
- Effect of different alpha weighting schemes (inversely proportional to frequency, but alternatives exist)

**Table 6 is entirely placeholder** - this is supposed to be the ablation study but contains no actual data.

### 4. **Bone Detection as Single Point of Failure** ⭐⭐⭐⭐ (Major Conceptual Issue)

**Pipeline dependency:**
1. Bone detection errors → incorrect co-localization → wrong TP/FP/FN labels → bad training data
2. Authors acknowledge this (line 516): "occasional mislocalization causes incorrect fracture-bone assignments"
3. But provide no quantification of error propagation

**Needed analysis:**
- What fraction of pseudo-labels are corrupted by bone detection errors?
- Does focal loss help or hurt when pseudo-labels are noisy?
- Sensitivity analysis: if bone detection degrades from 90% to 80% mAP, how much does final performance drop?

**Suggested mitigation:**
- Joint end-to-end training (mentioned in Future Work but should be baseline comparison)
- Confidence thresholding on bone detections
- Multiple hypothesis testing (if bone assignment is ambiguous, use both)

### 5. **Focal Loss Justification is Incomplete** ⭐⭐⭐

**What's presented:**
- Focal loss formula (Eq. 2)
- Parameters: γ = 2.0, α inversely proportional to class frequency
- Result: Better than balanced subset training (67.4% vs 44.2%)

**What's missing:**
- Why γ = 2.0? (Lin et al. used this for COCO, but medical images may differ)
- Actual α values not reported (all [X.X] placeholders)
- No comparison to simpler alternatives:
  - Class weighting without focal loss modulation (just α, no γ term)
  - Hard negative mining
  - SMOTE-style oversampling
  - Cost-sensitive learning

**Surprising claim:**
- "Focal loss on naturally-distributed data outperforms balanced subset" (line 551)
- But these aren't isolated variables! Finetuned Scaphoid uses:
  - 15K images (subset)
  - No focal loss
- Finetuned Balanced uses:
  - Full dataset (more data!)
  - Focal loss
- **Confound:** Is improvement from focal loss or more training data?

**Needed:**
- Finetuned Balanced WITHOUT focal loss (same data, standard cross-entropy)
- This would isolate focal loss contribution

### 6. **Evaluation Protocol Ambiguities** ⭐⭐⭐

**Test set composition unclear:**
- "100 images per bone type, each containing an isolated fracture of that bone" (line 507)
- Does this mean multi-fracture cases excluded?
- If so, how does model perform on realistic multi-fracture cases?
- Row 4 of qualitative results shows multi-fracture case, but this seems outside test distribution

**Acute-only filtering:**
- Abstract mentions "acute cases only: no cast, no metal" in dataset description
- But clinical deployment will see casts, healing fractures, metal artifacts
- Generalization to non-acute cases completely untested

**View-specific evaluation:**
- Test set is AP-only? Or includes all views?
- Clinical workflow includes lateral/oblique views—does model work on those?

### 7. **LLM Hallucination Rate Needs Context** ⭐⭐⭐

**Reported:**
- 0.98% hallucination rate (5 errors in 250 cases)

**Issues:**
- What counts as "hallucination"? (false positive fracture report?)
- What about false negatives? (missed fractures in report?)
- What about extraction errors? (bone type misidentification?)

**Critical question:**
- If LLM produces 1% hallucinations → 1% of pseudo-labels are wrong
- Combined with bone detection errors → cumulative error rate?
- How does noisy pseudo-label training affect focal loss optimization?

**Needed:**
- Precision/recall of LLM extraction (not just hallucination rate)
- Sensitivity analysis: train with synthetically corrupted labels to quantify robustness

### 8. **Limited Comparison to Prior Work** ⭐⭐⭐

**Baselines compared:**
- Graz pretrained (primary baseline)
- Manual finetuning on 214 expert-annotated images (mentioned but no results shown)

**Missing comparisons:**
- Other class imbalance techniques:
  - SMOTE (Synthetic Minority Oversampling)
  - Class-balanced loss (Cui et al. 2019)
  - Balanced group softmax (Li et al. 2020)
  - Two-stage detector with RoI resampling
- Other pseudo-labeling methods:
  - Self-training with confidence thresholding
  - Co-training with multiple models
  - MixMatch semi-supervised learning
- Other medical fracture detection papers (some citations missing)

**Discussion section comparison (lines 498-502):**
- References prior work but doesn't empirically compare
- "Our contribution is an automated pipeline..." but similar pipelines exist in semi-supervised learning

### 9. **Reproducibility Concerns** ⭐⭐⭐

**Good:**
- Detailed architectural descriptions
- Training hyperparameters specified
- Data anonymization protocol described
- Code promised at publication

**Missing:**
- Random seeds not mentioned
- Train/val/test split procedure (stratified? random? temporal?)
- Which Graz pretrained weights exactly? (checkpoint URL/version?)
- LLM prompts not shown (claimed "few-shot examples" but none provided)
- Flask labeling interface implementation details

**Dataset availability:**
- 71,714 examinations from single institution (BCH)
- Cannot be shared due to HIPAA
- Limits reproducibility and external validation

### 10. **Writing Quality Issues** ⭐⭐⭐⭐

**Generally good, but:**
- Numerous placeholder values disrupt reading flow
- Some redundancy between Abstract/Introduction/Results
- Figure captions are verbose (e.g., Fig 2 caption is 100+ words)
- Missing citations: `\cite{scaphoid_necrosis}`, `\cite{resnet}`, `\cite{focal_loss}`, `\cite{balanced_sampling_cite}`
- Notation inconsistency: sometimes "mAP@0.5", sometimes "mAP", sometimes just "accuracy"

---

## Technical Soundness

### ✅ Strengths:
1. **Co-localization via IoU is sound** (Eq. 1, τ=0.3)
2. **Focal loss formulation correct** (Eq. 2, standard from Lin et al.)
3. **Multi-stage pipeline logical** (each stage builds on previous)
4. **Evaluation metrics appropriate** (precision/recall per class)

### ⚠️ Concerns:

#### **1. Class-Stratified Sampling + Focal Loss: Redundancy?**
- Line 381: "class-stratified batch sampling... oversampling rare classes by up to 14×"
- This already addresses class imbalance during training
- Focal loss also addresses imbalance by down-weighting easy examples
- **Question:** Are both necessary? Ablation should isolate contributions

#### **2. IoU Threshold τ=0.3 is Low**
- Standard object detection uses τ=0.5 for TP/FP classification
- τ=0.3 means fracture box only needs 30% overlap with bone box
- Could this allow false positives? (e.g., radius fracture assigned to nearby ulna?)
- Justification: "empirically selected to balance sensitivity and specificity" (line 360)
- **Needed:** Show sensitivity analysis or precision-recall curve vs. τ

#### **3. Report-Level Labels for Image-Level Predictions**
- Reports describe findings across all views (AP, lateral, oblique)
- Co-localization uses single AP view
- Matching is approximate: "anatomical knowledge to resolve ambiguities" (line 369)
- **Risk:** Report says "scaphoid fracture" but visible only in scaphoid-specific view, not AP
- Then AP image gets FN label, but this is not a model error—it's a labeling limitation
- How often does this occur? No quantification provided

#### **4. Test Set Stratification May Overestimate Performance**
- "100 images per bone type, each containing an isolated fracture of that bone"
- This is NOT the natural distribution (which is 53.5% radius, 3.8% scaphoid)
- Model is trained on natural distribution but tested on balanced distribution
- **Metrics on natural distribution would be more clinically relevant**
- Example: In real deployment, 96% of scaphoid predictions might be false positives on non-scaphoid cases

#### **5. Focal Loss Alpha Calculation Needs Validation**
- Claimed: "α inversely proportional to class frequency" (line 410)
- If α_scaphoid = 1/0.038 = 26.3 and α_radius = 1/0.535 = 1.87, ratio is 14×
- This matches oversampling ratio (14× mentioned line 381)
- **But:** Focal loss α and oversampling are redundant imbalance corrections
- Optimal α when combined with oversampling is unknown (needs grid search)

---

## Missing Experiments (High Priority)

### 1. **Focal Loss Ablation on Same Data**
Train on full naturally-distributed dataset:
- Standard cross-entropy (no focal loss)
- Focal loss with γ=2.0
- Directly isolates focal loss contribution (currently confounded with dataset size)

### 2. **Natural Distribution Test Set**
Report performance on test set matching training distribution:
- 53.5% radius, 17.9% ulna, 12.9% styloid, 3.8% scaphoid
- More realistic clinical evaluation
- Would likely show higher FP rate for scaphoid

### 3. **Error Propagation Analysis**
Measure cumulative error from pipeline stages:
- AP classification errors → % of non-AP images included
- Bone detection errors → % of fractures assigned to wrong bone
- LLM extraction errors → % of incorrect TP/FP/FN labels
- End-to-end error rate

### 4. **Multi-Fracture Performance**
Test set includes "isolated fracture" cases (line 507):
- What about 2+ simultaneous fractures (common in trauma)?
- Table 1 shows "No Fracture" class (11.8%)—can model detect absence?

### 5. **Cross-Institution Validation**
All data from single institution (BCH):
- Evaluate on Graz dataset (public, used for pretraining)
- Evaluate on other pediatric hospital if available
- Domain shift is real concern for clinical deployment

---

## Missing Figures (Assumed to be Added)

Based on descriptions in text:

1. **Figure: Pipeline Diagram** (line 132) - CRITICAL
   - Must show 5 stages clearly
   - Should include example inputs/outputs at each stage

2. **Figure: Class Imbalance Scatterplot** (line 410) - IMPORTANT
   - X-axis: training prevalence (3.8% to 53.5%)
   - Y-axis: pretrained recall (15.1% to 80.5%)
   - Should show negative correlation visually

3. **Figure: Qualitative Results** (line 472) - IMPORTANT
   - 4 rows comparing Ground Truth / Pretrained / Ours
   - Critical for showing scaphoid missed→detected

4. **Figure: Bone Detection Examples** (line 481) - NICE TO HAVE
   - Shows bone YOLO works across diverse anatomies

**Suggestion:** Add precision-recall curves for each bone type (standard in detection papers)

---

## Suggested Improvements

### High Priority (Must Address for Acceptance)

1. **Complete all placeholder experiments**
   - AP classifier (3 values)
   - Bone detection (12 values)
   - Pseudo-label stats (25+ values)
   - Focal loss alphas (4 values)

2. **Resolve test set size inconsistency** (70 vs 400 images)

3. **Add focal loss ablation** (same data, with vs without focal loss)

4. **Add confidence intervals** on test metrics (bootstrap or cross-validation)

5. **Create all 4 figures** (especially pipeline diagram)

6. **Fix missing citations** (focal_loss, resnet, scaphoid_necrosis, balanced_sampling_cite)

### Medium Priority (Strengthen Contribution)

7. **Add error propagation analysis** (quantify compounding errors)

8. **Test on natural distribution** (not just stratified balanced set)

9. **Compare to standard imbalance baselines** (SMOTE, class-balanced loss, etc.)

10. **Report multi-fracture performance** (not just isolated single fractures)

11. **Hyperparameter sensitivity** (γ, τ, α values)

### Low Priority (Nice to Have)

12. **External validation** (Graz or other institution)

13. **Uncertainty quantification** (Monte Carlo dropout mentioned in Future Work)

14. **Computational cost analysis** (training time, inference speed)

15. **Qualitative failure case analysis** (what patterns does model still miss?)

---

## Specific Technical Questions for Authors

1. **Bone detection failures:** What happens when bone YOLO misses scaphoid entirely (0 detections)? Is entire image discarded or assigned as FN? How common is this?

2. **Report-image mismatch:** If report mentions "healing scaphoid fracture" but test set filtered to "acute only," is this image excluded or labeled as no-fracture? Please clarify filtering pipeline.

3. **Focal loss on imbalanced batches:** Class-stratified sampling creates balanced batches (line 381). But focal loss is designed for imbalanced batches. Isn't this contradictory? Should focal loss be applied to naturally-distributed batches?

4. **Manual FN annotation bias:** You prioritize scaphoid FNs for manual labeling (line 379). Could this introduce label noise asymmetry where scaphoid has cleaner labels than radius? How does this affect fair comparison?

5. **Pretrained model degradation:** Graz model was trained on mixed adult/pediatric data. Could scaphoid failure be domain shift (adult→pediatric anatomy) rather than class imbalance? How would you tease these apart?

6. **Clinical deployment:** In real emergency department, radiologist doesn't know bone type before viewing. But your pipeline requires bone detection first. If bone detection fails, entire pipeline fails. What's the fallback? Original Graz model?

---

## Comparison to MIDL Standards

**MIDL Full Paper requirements:**
- ✅ Medical imaging application (pediatric fracture detection)
- ✅ Novel methodology (bone-aware pseudo-labeling)
- ✅ Clinical motivation (avascular necrosis prevention)
- ⚠️ Experimental validation (**incomplete** - many placeholders)
- ⚠️ Reproducibility (**limited** - single institution, no public data)
- ✅ Writing quality (generally good, but placeholders disrupt)

**Comparison to recent MIDL papers:**
- Dataset size (71K exams) is large, comparable to top papers
- Results magnitude (4.5× improvement) is impressive
- But: Incomplete experiments weaken contribution
- Ablations are insufficient compared to typical MIDL standards

---

## Overall Assessment

### What This Paper Does Well:
1. **Important clinical problem** with clear motivation
2. **Novel approach** combining anatomical knowledge (bone detection) with LLM report extraction
3. **Strong empirical results** (4.5× scaphoid improvement without sacrificing other classes)
4. **Honest baseline comparison** (shows catastrophic pretrained failure)
5. **Scalable pipeline** (71K examinations processed)

### Critical Flaws:
1. **Incomplete experiments** (Tables 2, 3, 6 entirely placeholder; many [XX%] values)
2. **Missing focal loss ablation** (confounded with dataset size)
3. **Test set inconsistency** (70 vs 400 images unclear)
4. **Limited error analysis** (bone detection failure modes, error propagation)
5. **Insufficient baseline comparisons** (no SMOTE, class-balanced loss, etc.)

### Meta-Concern:
The paper reads like a **strong draft** that needs another experimental iteration. The story is compelling, the approach is sound, but the execution is incomplete. Many claims are unsubstantiated due to missing numbers.

**Example of issue:**
- Line 419: "bone YOLO achieved consistently high performance across all four anatomical classes"
- Table 2: [ALL VALUES ARE PLACEHOLDERS]
- Cannot verify claim!

This pattern repeats throughout. The authors clearly understand the problem and have designed a thoughtful solution, but the experimental validation is not finished.

---

## Recommendation

### **REJECT** (with encouragement to resubmit)

**Rationale:**
This paper has strong potential but is **not ready for publication** in its current form. The missing experimental results are not minor details—they're core to evaluating the method:

1. **Bone detection performance** (Table 2): If this is poor, entire pipeline fails
2. **Pseudo-label statistics** (Table 3): Need to assess annotation efficiency gains
3. **Focal loss ablation**: Current comparison confounds focal loss with dataset size

### Path to Acceptance:

**Required for resubmission:**
1. Complete ALL placeholder experiments (Tables 2, 3, 6; all [XX%] values)
2. Add focal loss ablation on same data (isolate focal loss contribution)
3. Resolve test set size inconsistency (70 vs 400 images)
4. Add confidence intervals on all test metrics
5. Create all 4 figures

**Strongly recommended:**
6. Add error propagation analysis
7. Compare to standard imbalance baselines (SMOTE, class-balanced loss)
8. Test on natural distribution (not stratified)
9. Report multi-fracture performance

**With these changes, this would be a strong accept.**

---

## Score Breakdown

| Criterion | Score | Weight | Weighted |
|-----------|-------|--------|----------|
| **Novelty** | 4/5 | 20% | 0.80 |
| **Technical Soundness** | 3/5* | 30% | 0.90 |
| **Experimental Validation** | 2/5* | 25% | 0.50 |
| **Clarity** | 4/5 | 10% | 0.40 |
| **Impact** | 4/5 | 15% | 0.60 |

**Total: 3.2/5.0**

*Soundness marked down for missing ablations; Validation marked down for incomplete experiments.

---

## Confidence in Review

**High (4/5)**

I am confident in this assessment. I have:
- Read the paper carefully, line by line
- Checked equations and methodology
- Identified specific missing experiments
- Compared to MIDL standards and recent papers
- Provided constructive feedback for improvement

I would be willing to review the revised submission if it addresses these concerns.

---

## Closing Remarks

**To the authors:**

You've identified an important problem (class imbalance in medical object detection) and designed a creative solution (bone-aware pseudo-labeling). The 4.5× improvement on scaphoid fractures is clinically meaningful and the approach is generalizable to other anatomical sites and rare pathologies.

However, the paper feels rushed. Many experiments are incomplete (placeholders) and critical ablations are missing. This makes it impossible to evaluate whether the gains truly come from your proposed method or from confounding factors (more training data, etc.).

**Please complete the experiments and resubmit.** With full results, proper ablations, and error analysis, this could be a strong contribution to MIDL.

The clinical impact is real—67.4% scaphoid recall could prevent avascular necrosis in many patients. This work is worth getting right.

Good luck with the revision!

---

**Reviewer Signature:** Anonymous NeurIPS-Style Reviewer
**Date:** [Generated for illustrative purposes]
**Recommendation:** Reject (Resubmit after major revisions)
