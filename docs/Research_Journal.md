# Planet Bros Research Journal

---

# PB-0001
**Date:** 4 August 2026

## Milestone
Planet Bros GitHub repository created.

## What we did
- Created GitHub repository.
- Configured Google Colab.
- Created the project journal.
- Agreed to build an open, reproducible exoplanet search project.

## What we learned
Every scientific project starts with careful preparation.

## Next step
Mission 001 - First Light
Recover our first known exoplanet from public TESS data.

---

## Why this matters
Today Planet Hunting Bros became a real scientific project rather than just an idea.

(Date to be completed...)

# PB-0002

Date: 5 August 2026

**Session Summary**
Today Planet-Hunting-Bros successfully established its first complete astronomy analysis pipeline.

**Milestones Achieved**
Created the first Google Colab notebook (PB-0002-First-Light.ipynb).
Installed the Lightkurve astronomy package.
Successfully queried NASA's TESS archive.
Retrieved the observation catalogue for TOI-700 (216 available observations).
Downloaded the first TESS light curve.
Successfully plotted our first real stellar light curve.

**Lessons Learned**
Python packages sometimes need to be installed before they can be imported.
A search result is a list of available observations rather than the observations themselves.
When multiple observations exist, an individual one can be selected using indexing (e.g. search_result[0]).
A light curve represents a star's brightness measured over time.

**Challenges**
Learned to navigate GitHub and Google Colab from an Android tablet.
Samsung keyboard occasionally duplicated variable names while typing code.
Lightkurve initially reported multiple available observations; resolved by selecting the first observation explicitly.

**Reflections**
Today marked the first successful retrieval and plotting of astronomical data by Planet-Hunting-Bros. For the first time we visualised real measurements collected by NASA's TESS spacecraft.
Jake became noticeably more engaged after seeing the light curve appear on screen. He immediately began looking for possible dips in the data, intuitively understanding that small decreases in brightness could indicate planets. This was an encouraging moment and reinforced the aim of making this a father-and-son scientific adventure.
Although no planet search has begun yet, today's work established the complete technical foundation upon which the remainder of the project will be built.

**Next Session Objectives**
Learn how to clean and prepare a light curve.
Understand how astronomers identify transit signals.
Plot improved versions of the data.
Begin examining real transit-like features.
Mission Status: ✅ PB-0002 — First Light complete.

**PB-0003 – Survey Pipeline Complete**
Date: 7 August 2026

**Objective**
Expand the Planet-Hunting Bros project from analysing a single artificial light curve to conducting an automated survey of 100 simulated stars.

**Work completed**
Successfully generated artificial stellar light curves with realistic random noise.
Randomly inserted planetary transits into approximately half of the simulated stars.
Improved the transit detector from identifying a single low point to recognising a sustained sequence of low-brightness measurements.
Measured transit start time, end time and duration.
Built an automated survey capable of analysing 100 stars.
Recorded the hidden ground truth (planet_truth) and compared it against the detector's results.
Achieved 100% accuracy in the initial simulated environment.
Investigated and resolved several notebook issues, including runtime resets, variable persistence, execution order and NameError exceptions.

**Scientific observations**
The detector currently performs perfectly because the simulated universe closely matches the assumptions built into the algorithm.
The next stage is to evaluate the detector using a more detailed scientific scorecard (true positives, false positives, true negatives and false negatives) before making the simulated observations progressively more realistic.

**Memorable moments**
Jake became fascinated by the randomly generated light curves and began identifying which stars contained planets.
He nicknamed broad transit events "Jupiters".
Before bed he asked:
"What if there were two dips?"
This naturally leads towards future work on multi-planet systems.

**Next session**
PB-0004 – Detector Evaluation
Objectives:
Build a scientific scorecard.
Calculate:
True Positives
False Positives
True Negatives
False Negatives
Introduce more challenging simulated observations.
Continue preparing the pipeline for analysis of genuine NASA TESS light curves.

# PB-0004 — Building a Periodic Transit Detector

## Objective

Move beyond detecting isolated dips and begin building a detector capable of identifying and vetting **repeating transit signals** in increasingly realistic synthetic light curves.

The main goal was not simply to recover simulated planets, but to understand how ordinary stellar variability and noise could imitate or obscure transit signals, and to develop independent tests for separating genuine periodic signals from false positives.

---

## Making the Synthetic Stars More Realistic

Before developing the periodic search, the synthetic light curves were made progressively more difficult.

Earlier simulations had relatively simple noise around a stable stellar baseline. PB-0004 introduced more realistic sources of variation so that the detector would have to distinguish transit-like signals from changes intrinsic to the star and from observational noise.

The test light curves included natural-looking brightness variability in addition to random noise and injected transit signals.

This exposed an important problem: slow changes in stellar brightness could distort the local baseline and either hide genuine transit signals or create features that appeared unusually deep.

Rather than compensating for this by simply making the detector more permissive, a preprocessing stage was developed to address the underlying problem.

---

## Light-Curve Cleaning and Detrending

A detrending stage was developed to remove slow stellar brightness variation while preserving short-duration transit signals.

The cleaning process was tested visually and quantitatively rather than simply assumed to work correctly.

Particular attention was paid to the edges of the light curve, where rolling or smoothing operations can behave poorly because fewer neighbouring observations are available.

**Edge protection** was introduced and tested to prevent these regions from generating artificial detections.

The resulting preprocessing workflow became:

**raw synthetic light curve**  
→ remove/limit slow brightness variation  
→ preserve short transit-scale features  
→ protect problematic edges  
→ search the cleaned light curve for transit signals

This represented an important step toward separating three different components of the data:

1. Long-timescale stellar variability
2. Short-timescale observational/random noise
3. Short-duration transit-like dimming

The detector was therefore tested against cleaned but deliberately imperfect light curves rather than idealised flat stars.

---

## Periodic Test Universe

A synthetic population of **1,000 light curves** was used:

- 500 stars containing injected periodic transit signals
- 500 control stars containing no injected planet

The detector was tested without access to the injected orbital periods during the search.

This allowed detection performance and false-positive behaviour to be measured against known ground truth.

---

## First Blind Period Search

An initial homemade period-search method folded a light curve across a grid of trial periods and searched for unusually deep phase bins.

For an example planet:

- Injected period: ~3.496 days
- Recovered period: ~3.211 days

The result demonstrated that simple phase-bin scoring was too sensitive to noise.

Rather than tuning the method until it reproduced the known answer, the failed approach was retained as evidence that a more appropriate periodic-search technique was required.

---

## Box Least Squares (BLS)

The detector was upgraded to use **Box Least Squares (BLS)**, an algorithm designed specifically to search for repeating box-shaped transit signals.

For the same example planet:

- Injected period: ~3.496 days
- Blind BLS recovery: ~3.480 days
- Period error: ~0.46%

Phase-folding the light curve using only the BLS-recovered period concentrated the transit points around phase zero, providing an independent visual confirmation that the recovered period represented the injected orbit.

---

## The Innocent-Star Test

BLS was then deliberately applied to a control star containing no planet.

As expected, BLS still returned a "best" period.

This demonstrated an important principle:

> A best-fitting BLS period is not, by itself, evidence for a planet.

A search algorithm will always identify the best solution available to it, even when every possible solution is produced by noise.

The detector therefore needed to measure not only the best BLS solution, but how convincing that solution was compared with the behaviour of stars known to contain no planet.

---

## Population BLS Test

BLS was run across all **1,000 synthetic stars**.

### Median maximum BLS power

- Planet population: **0.00009044**
- Control population: **0.00000700**

The planet and control populations showed strong separation, although some overlap remained.

The strongest innocent star produced a higher BLS score than the weakest genuine planet, confirming that no simple BLS threshold could perfectly separate the populations.

This overlap became particularly useful because the difficult innocent stars provided realistic false positives against which later vetting stages could be developed.

---

## ROC / Threshold Analysis

A full BLS threshold sweep was performed rather than selecting a threshold by eye.

### Best balanced threshold

- Planet recovery: **493 / 500 (98.6%)**
- False positives: **8 / 500 (1.6%)**

### 95% recovery configuration

- Planet recovery: **476 / 500 (95.2%)**
- False positives: **3 / 500 (0.6%)**

### 99% recovery configuration

- Planet recovery: **495 / 500 (99.0%)**
- False positives: **21 / 500 (4.2%)**

Approximate ROC AUC:

**0.9982**

The conservative **99% recovery configuration** was selected for further development.

The philosophy is to preserve potentially genuine signals during early screening and allow later, independent vetting stages to remove false positives.

At this stage it is considered more acceptable to investigate additional innocent stars than to discard genuine planet signals unnecessarily.

The ROC result applies only to the synthetic population used in this experiment and should not be interpreted as expected performance on real TESS data.

---

## Gate 2 — Repeated-Transit Coherence

The **516 stars** surviving the BLS gate consisted of:

- 495 genuine planet stars
- 21 innocent stars

A second test measured whether points predicted by BLS to occur during transit were consistently below the surrounding baseline.

Median coherence:

- Planets: **0.962**
- Innocents: **0.796**

Median repeated-transit depth:

- Planets: **0.001638**
- Innocents: **0.000675**

This showed that the BLS false positives tended, as a population, to have both shallower and less coherent apparent transits.

Rather than choosing thresholds visually, a two-dimensional coherence/depth threshold search was performed.

A conservative rule was identified:

**Reject when coherence < 0.78 AND median depth < 0.00080**

This removed:

- **8 / 21 false positives**
- **0 / 495 genuine planets**

This left **13 particularly convincing false positives** for further investigation.

These became informally known during development as the **"Dirty Thirteen."**

---

## Gate 3 — Individual Transit Depth Consistency

The remaining false positives were tested for consistency between their individual predicted transits.

For each candidate, the depth of every predicted transit was measured separately.

The coefficient of variation (CV) of those depths was then calculated.

A low CV indicates that the individual apparent transits have similar depths, while a high CV indicates inconsistent events.

### Population comparison

Median depth CV:

- Genuine planets: **0.155**
- Dirty Thirteen: **0.322**

The false-positive population was generally less consistent than the genuine planet population.

However, significant overlap remained.

One innocent star achieved a depth CV of approximately **0.07**, with three remarkably similar apparent transit depths despite containing no injected planet.

This demonstrated another important principle:

> Noise can occasionally produce a periodic signal that is coherent, repeatable and remarkably planet-like.

Conversely, some genuine simulated planets showed relatively poor depth consistency.

Depth CV therefore contained useful information but could not safely be used as an aggressive standalone rejection criterion.

---

## Gate 3 Threshold Battlefield

A full sweep of possible depth-CV rejection thresholds was performed.

The detector measured how many of the remaining 13 false positives could be rejected while allowing losses of:

- 0 genuine planets
- 1 genuine planet
- 2 genuine planets
- 5 genuine planets
- 10 genuine planets

The conservative zero-loss configuration was:

**Reject if depth CV > 0.940**

This removed:

- **1 / 13 remaining false positives**
- **0 / 495 surviving genuine planets**

More aggressive thresholds produced only modest improvements while beginning to discard genuine planet signals.

The conservative threshold was therefore retained.

This leaves **12 difficult false positives** for further investigation.

---

## Current Detector Funnel

The developing detector now follows approximately:

**Synthetic light curve**  
→ stellar variability/noise  
→ detrending and cleaning  
→ edge protection  
→ BLS periodic search  
→ high-recall BLS significance gate  
→ repeated-transit coherence  
→ repeated-transit depth  
→ individual-transit depth consistency  
→ further vetting

The control population has progressed:

**500 innocent stars**  
→ **21 after the BLS gate**  
→ **13 after Gate 2**  
→ **12 after Gate 3**

Meanwhile:

**495 / 500 injected planets** survive the initial high-recall BLS gate.

The conservative Gate 2 and Gate 3 rules have so far removed additional false positives while sacrificing:

**0 / 495 surviving planets**

---

## Key Lessons

1. Detecting an isolated dip is much easier than demonstrating genuine periodicity.

2. Natural stellar brightness variation can obscure genuine transit signals or create misleading features.

3. Preprocessing is itself part of the detector. Removing stellar variability without removing or manufacturing transit signals must be tested and validated.

4. Edge effects created by cleaning algorithms can generate artificial behaviour and therefore require explicit protection.

5. BLS can recover an injected orbital period accurately without being given the true period.

6. BLS always returns a best solution, even for stars containing no planet.

7. Population-level control tests are essential for interpreting detection scores.

8. A very strong detection score does not guarantee that a signal is genuine.

9. Independent vetting stages can remove false positives without simply making the original detection threshold increasingly aggressive.

10. False positives can occasionally look remarkably planet-like across several different measurements.

11. Thresholds should be determined from measured performance rather than selected because they produce desirable results.

12. Preserving genuine candidates is currently considered more important than aggressively eliminating every false positive during early screening.

13. A failed test is useful if it reveals what the detector does not yet understand.

14. Synthetic performance must not be confused with expected performance on real astronomical data.

---

## Next Step — Gate 4

Investigate the remaining **12 hard false positives**.

The aim will be to identify additional properties capable of distinguishing these signals from genuine periodic transits using evidence that is meaningfully different from the existing BLS, coherence and depth-consistency measurements.

Any proposed Gate 4 metric will again be tested against the genuine planet control population before being adopted.

The detector will continue to be developed and validated on synthetic data before being applied to real TESS light curves.

---

## Session Checkpoint

A PB-0004 checkpoint containing the important intermediate results from Gates 1–3 was saved to persistent storage.

The checkpoint was then successfully reloaded in a separate test before ending the session.

This allows development to resume from Gate 4 without requiring the computationally expensive earlier population searches to be repeated.

---

## Status

**PB-0004 — Gates 1–3 complete**

- BLS high-recall survivors: **495 planets + 21 false positives**
- Gate 2 false positives remaining: **13**
- Gate 3 false positives remaining: **12**
- Additional genuine planets lost during Gates 2–3: **0**
- Checkpoint: **saved and reload verified**

**Next session: Gate 4 — interrogate the remaining 12 false positives.**


# PB-0005 — Gate 4: Reality Check, Robustness & Blind Validation
**Date:** 14 August 2026

## Objective

Continue development of the Planet Hunting Bros detector by investigating whether
additional measurements could distinguish genuine simulated planets from the
false positives that survived Gates 1–3.

The priority remained conservative:

**Reject useful numbers of false positives without unnecessarily rejecting
genuine planets.**

---

## Starting Population

The existing PB-0004 control population was used.

After Gates 1–3:

- Genuine planets available for testing: **495**
- Surviving false positives / impostors: **12**

Gate 4 was deliberately investigated separately rather than immediately adding
another hard rejection rule to the detector.

---

## Gate 4 — Coherence Reconnaissance

Transit coherence was compared between the genuine planets and the 12 surviving
impostors.

### Genuine planets

- Median coherence: **0.9623**
- Range: **0.6833 – 1.0000**

### Impostors

- Median coherence: **0.8058**
- Range: **0.683 – 0.887**

The distributions showed useful separation, although there was overlap.

A threshold battlefield was therefore performed to determine how many impostors
could be rejected at different acceptable levels of genuine-planet loss.

### Coherence battlefield

- Allow 0 planets lost:
  - Reject **1/12 impostors**
  - Keep **495/495 planets**
  - Threshold: coherence < **0.683**

- Allow ≤2 planets lost:
  - Reject **4/12 impostors**
  - Keep **493/495 planets**
  - Threshold: coherence < **0.777**

- Allow ≤5 planets lost:
  - Reject **6/12 impostors**
  - Keep **490/495 planets**
  - Threshold: coherence < **0.799**

Conclusion:

**Coherence contained genuine discriminatory information, but was not sufficiently
powerful by itself to form an aggressive safe rejection rule.**

---

## Gate 4 — BLS Power Reconnaissance

BLS power was then investigated independently.

### Genuine planets

- Median BLS power: approximately **9.06 × 10⁻⁵**
- Range: approximately **2.00 × 10⁻⁵ – 3.35 × 10⁻⁴**

### Impostors

- Median BLS power: approximately **2.30 × 10⁻⁵**
- Range: approximately **2.07 × 10⁻⁵ – 3.34 × 10⁻⁵**

The impostors clustered strongly at the low-power end of the genuine-planet
distribution.

However, BLS power alone could not reject any impostors while maintaining
zero planet loss.

Examples:

- Allow 0 planets lost → **0/12 impostors rejected**
- Allow ≤2 planets lost → **6/12 rejected**
- Allow ≤5 planets lost → **7/12 rejected**
- Allow ≤10 planets lost → **8/12 rejected**

Conclusion:

**Power was informative, but unsafe as a standalone hard veto.**

---

## Gate 4 — The "Double Whammy"

Coherence and BLS power were then combined.

Instead of rejecting an object for having either suspicious characteristic,
an object was rejected only when it had:

**LOW coherence AND LOW BLS power**

This produced a dramatic improvement.

### Initial combined result

Rule:

**Reject if coherence < 0.825 AND power < 2.3166451 × 10⁻⁵**

Result:

- Impostors rejected: **6/12**
- Genuine planets retained: **495/495**
- Genuine planets lost: **0**

More aggressive versions could reject:

- **7/12** impostors with ≤1 planet lost
- **9/12** impostors with ≤2 planets lost
- **10/12** impostors with ≤5 planets lost
- **11/12** impostors with ≤10 planets lost

This was the strongest apparent Gate 4 result so far.

However, rather than accepting the excellent result immediately, the rule was
stress-tested.

---

## Robustness Stress Test

The initial Double Whammy rule was tested across **5,000 perturbed trials**.

Fixed rule:

- coherence < **0.825**
- AND power < **2.3166451 × 10⁻⁵**

### Result

Planet losses:

- Median: **1**
- Mean: **0.919**
- Worst: **3**
- Zero-loss trials: **29.4%**

Impostors removed:

- Median: **5/12**
- Mean: **5.01/12**
- Range: **2–8/12**
- Trials removing ≥5 impostors: **68.8%**
- Trials removing ≥6 impostors: **33.6%**

Conclusion:

The original 6/12, zero-planet-loss result was real for the original population,
but **was not sufficiently robust to measurement/population perturbation to be
adopted as a hard rejection criterion**.

This was an important negative result.

---

## Robustness Optimisation

A grid of **100 candidate combined rules** was tested to identify rules achieving
at least **95% zero-planet-loss trials**.

**29 rules** reached this safety target.

### Best robust-safe rule identified

- coherence < **0.816**
- AND power < **2.11 × 10⁻⁵**

Performance:

- Zero-planet-loss trials: **95.4%**
- Mean planets lost: **0.047**
- Worst planets lost: **2**
- Median impostors removed: **1/12**
- Mean impostors removed: **1.33/12**
- Trials removing ≥3 impostors: **10.6%**
- Trials removing ≥4 impostors: **1.7%**

Several still more conservative rules produced zero-loss rates approaching
98%.

This demonstrated the fundamental Gate 4 trade-off:

**High false-positive rejection was possible, but not while maintaining the
very high level of planet protection required for a discovery-oriented
detector.**

Gate 4 was therefore reinterpreted primarily as a conservative false-positive
flag rather than an aggressive execution rule.

---

## Frozen Gate 4 Rule

A deliberately conservative rule was selected and frozen before blind testing:

**FLAG if:**

- coherence < **0.825**
- AND
- BLS power < **2.05 × 10⁻⁵**

No further optimisation was permitted after seeing the blind-test results.

---

## Blind Synthetic Validation

A completely fresh synthetic population was generated using a new fixed random
seed.

Blind population:

- **500 genuine planets**
- **100 impostors**

The frozen Gate 4 rule was applied before examining the labels.

### Result

Genuine planets:

- Kept: **500/500**
- Flagged: **0/500**
- Planet retention: **100.00%**

Impostors:

- Caught: **19/100**
- Missed: **81/100**
- Impostor rejection: **19.00%**

### Interpretation

The frozen Gate 4 rule successfully generalised to a fresh synthetic population
without rejecting a single genuine simulated planet.

The 19% false-positive rejection rate is deliberately modest compared with the
initial Double Whammy result, but provides useful additional filtering while
remaining highly conservative.

This test represents **synthetic validation only**.

The blind population was generated from assumed distributions informed by the
existing simulation framework. It therefore does NOT yet demonstrate equivalent
performance on real TESS observations.

---

## Gate 4 Status

**PROVISIONALLY VALIDATED — SYNTHETIC**

Frozen rule:

**coherence < 0.825 AND BLS power < 2.05 × 10⁻⁵**

The rule will NOT be further optimised against the PB-0004 synthetic population.

Future changes must be justified by independent evidence rather than improving
performance against data already examined.

---

## Checkpoint

A final project checkpoint was created:

**PB0004_FINAL_CHECKPOINT.pkl**

The checkpoint contains:

- PB-0004 control populations
- Gate 4 control objects
- Frozen Gate 4 thresholds
- Blind-validation results
- Project provenance / checkpoint information

The file was successfully uploaded to:

**Google Drive → PlanetHuntingBros → Data**

The previous Gate 3 checkpoint was retained as an earlier recovery point.

---

# Next Step — PB-0005: TESS Reality Check

Synthetic detector development is now sufficiently mature to begin testing
against real astronomical observations.

The next stage will construct a small independent control sample from real
TESS data containing approximately 10–20 carefully selected targets, including:

- Confirmed transiting planets
- Known false positives / eclipsing binaries
- Stars without known transit detections

The existing detector and frozen Gate 4 rule will be applied without knowledge
of the target classifications where practical.

Only after scoring will the known classifications be compared with the
detector output.

The purpose of this stage is NOT to discover a new planet.

The purpose is to answer a more fundamental question:

**Does the detector behaviour developed in simulation survive contact with
real TESS light curves?**

If it does, the next phase can begin:

**systematic analysis of under-covered TESS targets for genuine new candidates.**

---

## End-of-session status

**Gates 1–3:** Complete  
**Gate 4:** Developed, stress-tested and frozen  
**Synthetic blind validation:** Complete  
**Final PB-0004 checkpoint:** Saved  
**Next milestone:** Real TESS validation

### Planet Hunting Bros

**The synthetic proving ground is complete.**

**Next: real starlight.** 🔭🪐

## PB-0006 — TESS Reality Check: TOI-700 Sector 1

**Objective:**  
Test the Planet Hunting Bros detection and vetting workflow against real TESS photometry, rather than simulated light curves.

**Target:** TOI-700  
**TESS Sector:** 1

### Method

A Box Least Squares (BLS) search was performed on the cleaned Sector 1 light curve to identify transit-like periodic signals.

Several distinct BLS peaks were recovered. Candidate #5, with a period of approximately **6.898 days**, was selected for detailed investigation.

The candidate was then subjected to a series of increasingly strict vetting tests.

### Gate 1 — BLS Candidate Identification

Candidate #5 was identified at:

- Period: **6.897980 days**
- BLS depth: approximately **0.001122**
- Four predicted transit events within the Sector 1 observations

The signal initially appeared sufficiently interesting to investigate further.

### Gate 2 — Individual Event Inspection

The four predicted transit events were examined individually.

Measured depths:

1. **0.000598**
2. **0.000062**
3. **0.002181**
4. **0.000859**

All four predicted epochs showed some downward flux behaviour, but the measured depths varied substantially.

The third event, centred near **TESS time 1340.091**, was dramatically deeper than the others and became a major source of suspicion.

### Gate 3 — Dominant-Event Removal and Depth Consistency

The strong event near **1340.091** was masked and the BLS search repeated.

The original ~6.898-day signal fell to approximately **26.2% of its original BLS power**, demonstrating that the candidate was heavily dependent on this single event.

A depth-consistency test was also performed.

Results:

- Mean measured depth: **0.000925**
- Depth coefficient of variation: **0.843**
- Weighted common depth: **0.000737**
- Chi-square: **12.839**
- Degrees of freedom: **3**
- Reduced chi-square: **4.280**

Diagnostic:

**CAUTION — noticeable depth inconsistency.**

This was inconsistent with the behaviour expected from a clean sequence of similar planetary transits.

### Gate 4 — Phase-Folded Shape Test

The complete light curve was folded at the candidate period of approximately **6.898 days**.

A weak depression was visible near the predicted transit centre, but the folded signal was not clean or compelling. The result remained strongly influenced by the unusually deep 1340 event.

Candidate #5 was therefore retained only for further testing rather than accepted as a credible transit signal.

### Gate 5 — Phase Fold Without the 1340 Event

The dominant event centred at **1340.091454** was removed completely and the light curve was folded again at the same candidate period and epoch.

A small residual depression remained near phase zero.

This showed that the original signal was not *entirely* produced by the 1340 event, but the surviving feature was considerably weaker.

### Gate 6 — Residual Dip Significance

The remaining phase-zero depression was quantified after removal of the dominant event.

Results:

- In-transit points: **130**
- Out-of-transit points: **835**
- Out-of-transit median flux: **0.999998**
- In-transit median flux: **0.999510**
- Residual depth: **0.000488**
- Depth uncertainty: **0.000234**
- Significance: **2.09 sigma**

This residual signal is too weak to support a serious transit candidate.

## Conclusion

**Candidate #5 — REJECTED**

The ~6.898-day BLS signal initially resembled a possible repeating transit signature, but detailed vetting showed that it was dominated by a single unusually deep event near TESS time 1340.091.

After that event was removed, the original BLS signal lost approximately three quarters of its power. The remaining folded depression had a significance of only **2.09 sigma**.

PB-0006 therefore successfully demonstrated an important capability of the Planet Hunting Bros workflow:

> A strong-looking BLS peak should not be treated as evidence of a planet until the individual events producing it have survived independent vetting.

This experiment established a useful preliminary vetting sequence:

**BLS detection → individual event inspection → dominant-event removal → depth consistency → phase-fold inspection → residual significance**

### Final status

**REJECTED — likely false-positive / non-coherent transit-like signal.**

The candidate was not a planet detection, but PB-0006 was a successful test of the project's ability to reject an initially convincing signal in real TESS data.

---

## PB-0006 Continuation — Positive Control Validation (LHS 3844)

**Objective:**  
Verify that the Planet Hunting Bros workflow can recover a genuine exoplanet signal using a blind search, providing a positive control alongside the rejected TOI-700 Candidate #5.

### Target

- **Star:** LHS 3844
- **TIC:** 410153553
- **TESS Sector:** 1

### Blind BLS Recovery

The saved LHS 3844 TESS light curve was searched using Box Least Squares (BLS) without supplying the known planet's orbital period or transit timing.

The blind search independently identified a strong periodic signal with the following parameters:

- **Recovered period:** 0.462822 days
- **Recovered transit epoch:** 1325.727138
- **Recovered transit depth:** 0.003910
- **Usable TESS data points:** 18,275

The BLS periodogram showed a dominant peak at the recovered period together with a series of related harmonic peaks.

### Phase-Fold Test

The light curve was then phase-folded using only the period and epoch recovered by the blind BLS search.

The resulting folded light curve showed a clear, coherent transit centred close to phase zero.

The transit produced an approximately **0.39% decrease in stellar brightness**, consistent with the depth independently recovered by BLS.

Unlike TOI-700 Candidate #5, the signal did not depend upon one unusually deep isolated event. Repeated events aligned when folded at the recovered period, producing a persistent transit-shaped feature.

### PB-0006 Reality Check

The experiment therefore produced two contrasting outcomes:

**TOI-700 Candidate #5**

Initially convincing BLS signal → individual-event inspection → dominant event identified → dominant event removed → signal weakened substantially → residual significance only **2.09 sigma** → **REJECTED**

**LHS 3844**

Known genuine transit signal → blind BLS search → period independently recovered → phase folding → coherent repeated transit → **RECOVERED**

### Conclusion

PB-0006 demonstrated both rejection of a weak transit-like false candidate and blind recovery of a known genuine TESS transit signal.

This is an important validation of the Planet Hunting Bros workflow. The pipeline is not simply rewarding strong-looking BLS peaks, nor is the subsequent vetting procedure automatically destroying transit signals.

Instead, the tests demonstrate the principle that will guide future candidate searches:

> **A candidate should survive because the signal remains coherent under increasingly hostile tests, not because the original detection looked convincing.**

### Final status

**PB-0006 — PASSED**

The reality-check experiment successfully demonstrated preliminary discrimination between a non-coherent transit-like signal and a genuine repeating transit signal in real TESS photometry.

**Next:** expand the positive-control tests to additional known TESS planets before applying the validated workflow to new candidate searches.


### PB-0007 — Blind Control Batch: Setup / Commissioning

A reusable blind-control workflow was created to test the Planet Hunting Bros detection and vetting pipeline against multiple real TESS datasets without consulting the expected result during analysis.

The initial PB-0007 framework successfully:

- located the four previously downloaded TESS control datasets;
- loaded a target anonymously;
- applied a fixed cleaning and detrending procedure;
- performed a blind BLS search over periods of 0.2–15 days.

During commissioning, the first anonymously selected target produced a strongest BLS solution at **14.818691 days**, with a transit epoch of **1340.093495**.

The recovered epoch was almost identical to the unusually deep event near **TESS time 1340.091** investigated during PB-0006. This indicated that the initial blind-control pool probably included the dataset already examined in PB-0006.

The result was therefore **not treated as a new blind-control result**.

**Methodological decision:** Before PB-0007 continues, the previously investigated PB-0006 dataset will be excluded from the blind pool. A genuinely unused control will then be selected and analysed without consulting its known planetary properties.

**Status:** PB-0007 framework operational; commissioning test excluded from scientific scoring.

**Next:** Begin the first genuine blind control.

## PB-0007 — Blind Control #1: Successful Recovery of WASP-18 b

**Date:** 19 August 2026  
**Experiment:** PB-0007 — Blind Control Batch  
**Control:** BC-001  
**TESS Sector:** 2  

### Objective

Test the Planet Hunting Bros detection and vetting workflow against an anonymous real TESS light curve whose identity was deliberately hidden during analysis.

The purpose of the experiment was to determine whether the workflow could recognise and correctly classify a genuine transit signal without prior knowledge of the target or expected planetary parameters.

### Blind Analysis

BC-001 was selected from the control dataset with its target identity hidden.

After quality filtering and preparation, the light curve contained **18,299 usable observations** spanning approximately **27.406 days**.

Visual inspection showed numerous regularly repeating deep flux reductions, but the identity and known classification of the target remained hidden.

A blind Box Least Squares (BLS) search recovered the strongest periodic signal at:

- **Period:** 0.941517 days
- **Transit duration:** 0.0800 days (~1.92 hours)
- **BLS depth:** 0.009340 (~0.934%)
- **BLS power:** 0.066383

The phase-folded light curve produced a clear, coherent and strongly repeated transit-like profile.

### Odd/Even Transit Test

Odd and even events were analysed independently.

- **Odd depth:** 0.009639
- **Even depth:** 0.010206
- **Absolute difference:** 0.000566
- **Fractional difference:** 0.057 (5.7%)

**Result: PASS**

Odd and even transit depths were closely matched, providing no strong evidence for alternating primary and secondary eclipses at the recovered period.

### Individual Transit Consistency

The recovered ephemeris predicted **28 usable individual transit events** within the Sector 2 observations.

Results:

- **Events showing a dip:** 100.0%
- **Events within 30% of median depth:** 85.7%
- **Median individual depth:** 0.010160
- **Mean individual depth:** 0.009554
- **Depth MAD:** 0.000146
- **Depth CV:** 0.172

Although several individual events showed shallower measured depths, all 28 predicted events contained a detectable flux reduction.

**Final pre-reveal diagnostic: STRONG PASS**

### Blind Verdict

Before the target identity was revealed, the Planet Hunting Bros verdict was locked as:

**STRONG PLANET CANDIDATE**

Only after this classification had been recorded was the target identity examined.

### Identity Reveal

BC-001 was revealed as:

**TIC 100100827 — WASP-18**

The recovered signal corresponds to the known confirmed transiting exoplanet **WASP-18 b**.

Published values for WASP-18 b give an orbital period of approximately **0.94145 days**, extremely close to the **0.941517-day** period recovered independently by the blind Planet Hunting Bros analysis.

The recovered transit depth of approximately **0.934%** is likewise consistent with the known transit signal.

### Result

**BLIND CONTROL #1 — SUCCESS**

PB-0007 successfully recovered a genuine known planetary transit from anonymous real TESS photometry and classified the signal as a strong planet candidate before the identity of the target was known.

This does **not** constitute an independent discovery or confirmation of WASP-18 b. Instead, it provides an important validation of the developing Planet Hunting Bros workflow: when presented with a real planetary signal without prior knowledge of its identity or expected parameters, the pipeline was capable of detecting the periodicity and producing the correct broad classification.

Further blind controls — including confirmed planets, eclipsing binaries, false positives and apparently quiet stars — will be required before the performance of the workflow can be assessed robustly.

### Milestone

PB-0007 / BC-001 marks the first successful fully blind recovery of a confirmed exoplanet by the Planet Hunting Bros workflow.

**Target identity hidden → signal detected → diagnostics passed → verdict locked → identity revealed.**

**Blind verdict:** STRONG PLANET CANDIDATE  
**Reality:** CONFIRMED EXOPLANET — WASP-18 b

**Believe — but verify.**

**Verified.**

# PB-0007 — Blind Control BC-002: Full Vetting and Successful Identification

**Date:** 21 August 2026  
**Project:** Planet Hunting Bros  
**Status:** COMPLETE — BLIND CONTROL PASSED

## Objective

Continue blind-control testing of the Planet Hunting Bros detection and vetting pipeline using BC-002.

The identity of BC-002 was deliberately hidden throughout the analysis. The purpose was to determine whether the PHB pipeline could detect and appropriately vet a genuine transit-like signal without prior knowledge of the target or its known classification.

A blind verdict was locked before revealing the target identity.

---

## Initial Detection

The PHB pipeline identified a strong periodic transit-like signal in BC-002.

Two clearly observed transit events were investigated individually.

Approximate measured depths:

- Transit 1: 0.00232 (~0.232%)
- Transit 2: 0.00293 (~0.293%)

The two events were broadly consistent in depth and morphology.

A period ambiguity remained between approximately:

- 5.659 days
- 11.318 days

The expected intermediate event under the shorter-period hypothesis occurred during a TESS data gap, preventing the ambiguity from being resolved from this sector alone.

---

## False-Positive Vetting

### Secondary Eclipse Test

No significant secondary eclipse was detected for the testable short-period hypothesis.

Measured secondary signal:

- ~16 ppm
- ~0.32 sigma

The longer-period secondary test was untestable because of missing TESS coverage.

Result:

**No detected secondary eclipse capable of rejecting the candidate.**

---

## Odd/Even Transit Test

Only even-parity events were observed because the intervening event fell inside the TESS coverage gap.

The pipeline correctly returned:

**UNTESTABLE**

rather than incorrectly interpreting missing data as an odd/even pass.

---

## Centroid Motion Test

Flux-weighted centroid behaviour was measured independently around both observed transits.

Results:

- Transit 1 shift: ~0.00209 pixels (~0.044 arcsec), 0.37 sigma
- Transit 2 shift: ~0.00184 pixels (~0.039 arcsec), 0.47 sigma

Result:

**No obvious centroid shift detected.**

---

## Difference Imaging

Independent in-transit and out-of-transit pixel images were constructed for both events.

Initial missing-light centroid estimates showed some positional disagreement:

- Transit 1: (5.970, 4.848)
- Transit 2: (5.621, 4.826)
- Initial separation: ~0.350 pixels

This was NOT treated as sufficient evidence to reject the candidate.

---

## Bootstrap Difference-Centroid Test

1,000 bootstrap resamples were performed for each transit to estimate the stability of the difference-image centroids.

The robust bootstrap median positions were separated by only approximately:

**0.135 pixels**

This demonstrated that the apparently large initial transit-to-transit centroid disagreement was substantially affected by measurement uncertainty.

Both transit centroid distributions instead showed a broadly similar positional displacement.

---

## Catalogue and Gaia Investigation

The difference-centroid positions were compared with the catalogue target position.

Both events appeared somewhat displaced from the nominal catalogue coordinate, initially raising a possible contamination concern.

A blind Gaia neighbourhood search was therefore performed.

A bright Gaia source was identified approximately 4–5 arcsec from the nominal catalogue position and close to the inferred missing-light region.

Initially this appeared to be a possible contaminating source.

Further investigation showed that this bright Gaia source was most likely the true Gaia counterpart of BC-002 itself rather than an independent contaminant.

This was an important pipeline lesson: an apparent contaminant must not be assumed to be a separate astrophysical source without first establishing its relationship to the target.

---

## Genuine Neighbour Dilution Test

After removing the likely target Gaia counterpart, the nearest genuine neighbouring source was approximately:

- Separation: ~19.8 arcsec
- Magnitude difference: ΔG ~+8.74

The neighbour was then tested to determine whether an eclipse occurring on that star could reproduce the observed diluted TESS transit depth.

Required intrinsic eclipse depth:

**~823%**

This is physically impossible.

Result:

**The nearest genuine Gaia neighbour cannot produce the observed signal.**

---

## Target Geometry Test

The nominal catalogue position, Gaia counterpart, measured TPF photocentre and transit missing-light positions were compared.

The bright Gaia source was closer to the actual TPF light photocentre than the nominal catalogue position, supporting its identification as the target counterpart rather than a contaminant.

The missing-light positions were also broadly compatible with this source.

---

## Final Systematics Stress Test

Before revealing BC-002, the difference-centroid analysis was repeated using multiple reasonable analysis choices, including:

- standard transit window
- tighter transit window
- wider transit window
- closer baseline regions
- mean rather than median difference images

Transit 1 remained spatially robust.

Transit 2 showed greater sensitivity to analysis choices, but the two events remained broadly consistent.

Final robust transit-to-transit centroid separation:

**~0.352 pixels (~7.4 arcsec)**

The pipeline therefore returned a cautious rather than definitive spatial verdict.

---

## Locked Blind Verdict

Before revealing the identity of BC-002, the PHB verdict was recorded as:

**PASS / SURVIVES VETTING**

More specifically:

**Credible on-target transit-like candidate with residual centroid/systematics uncertainty.**

The signal was NOT declared a validated planet.

However, the available evidence did not justify rejecting BC-002 as an obvious eclipsing binary, contaminating neighbour or instrumental false positive.

The blind analysis was then frozen.

---

# Identity Reveal

After all analysis and the blind verdict were locked, the hidden target identity was revealed.

**BC-002 = TIC 259377017**

The target is:

**TOI-270 / L 231-32**

TOI-270 is a known multi-planet system containing three transiting planets.

The blind PHB analysis had therefore independently detected and successfully retained genuine planetary transit signals without knowing that the target was a known planetary system.

---

## Outcome

**BLIND CONTROL SUCCESSFUL**

PB-0007 demonstrated that the developing Planet Hunting Bros pipeline can:

- detect genuine transit-like events
- interrogate individual events
- recognise period/coverage degeneracy
- refuse conclusions when tests are unobservable
- search for secondary eclipses
- perform odd/even vetting
- perform centroid-motion analysis
- construct difference images
- bootstrap difference-centroid measurements
- investigate Gaia contamination
- perform quantitative dilution tests
- identify misleading apparent contaminants
- stress-test results against reasonable analysis choices
- retain uncertainty rather than forcing binary PASS/FAIL conclusions
- reach a defensible blind classification before target identity is known

Most importantly, the pipeline did not simply identify a transit and assume "planet".

It repeatedly attempted to falsify the planetary interpretation.

BC-002 survived.

Only then was its identity revealed as the known TOI-270 planetary system.

---

## Key Lesson

PB-0007 reinforced a central Planet Hunting Bros principle:

**A candidate does not survive because we want it to be a planet, and it does not die because one diagnostic looks suspicious. Every conclusion must be earned by the evidence.**

This blind control substantially increased confidence in the methodology while also identifying areas requiring further development, particularly rigorous pixel-level localisation and centroid uncertainty analysis.

The next objective is to repeat blind testing across additional known planets and astrophysical false positives before applying the mature pipeline at scale to neglected TESS targets.

---

**PB-0007 COMPLETE**

✋️*✋️ **Believe — but be critical.**

# PB0008 — Synthetic End-to-End Gate Commissioning

**Status:** Completed — commissioning run exposed an experimental-design flaw  
**Outcome:** Pipeline mechanics commissioned; final scientific classification invalidated  
**Successor:** PB0009

## Objective

PB0008 was designed as the first extended end-to-end commissioning run of the
Planet Hunter Bros candidate-vetting pipeline.

A synthetic transit-like candidate was passed sequentially through the developing
gate architecture, with each gate intended to test a different potential
false-positive or instrumental explanation.

The principal goals were to:

- verify gate execution and record keeping;
- commission diagnostic thresholds and reporting;
- test independence between evidence streams;
- test the early catalogue-context firewall;
- test late identity reveal;
- exercise final evidence synthesis;
- identify architectural weaknesses before using real TESS targets.

## Synthetic Candidate

The primary science data were generated as a deliberately clean,
planet-like synthetic transit.

The simulated signal exhibited broadly:

- repeatable transit-like events;
- approximately stable transit depth;
- no deliberately injected secondary eclipse;
- no deliberately injected odd/even depth difference;
- stable aperture behaviour;
- target-centred difference-image behaviour;
- negligible transit-correlated centroid motion;
- robustness under several reasonable preprocessing variants.

Nearby synthetic sources were also introduced so that contamination and dilution
diagnostics could be exercised.

## Gate Commissioning

The PB0008 run exercised the developing G01–G16 architecture.

Among the notable results:

- the synthetic transit was successfully detected and characterised;
- multiple transit-shape and consistency diagnostics passed;
- aperture-depth stability passed;
- nearby-source screening passed;
- dilution feasibility identified neighbouring sources capable of reproducing
  the observed depth;
- difference-image localisation nevertheless placed the injected missing light
  close to the target;
- centroid behaviour showed no persuasive transit-correlated displacement;
- the signal survived all tested preprocessing variants.

One particularly useful result was the combination:

**G11 FAIL — dilution physically feasible**

while

**G12 PASS — missing light localised near target**

and

**G13 PASS — no significant centroid shift**

This demonstrated that the gates were capable of retaining apparently conflicting
pieces of evidence rather than simply cancelling one another or treating the
pipeline as a PASS/FAIL vote count.

## G03 Firewall Test

PB0008 also commissioned an early masked catalogue-context gate.

To test the firewall, a deliberately secret synthetic identity was created:

- object identity: `SECRET_TEST_OBJECT`
- private object class: `ECLIPSING_BINARY`
- private catalogue: `SECRET_TEST_CATALOGUE`

G03 was designed to expose only a masked contextual class while preventing the
underlying identity and catalogue information from entering the blind science
workflow.

The firewall itself worked as intended.

During the session, the firewall test was further separated from the main science
record using a deep copy, and an audit confirmed that the secret identity did not
leak into the main G01–G14 science record.

## The PB0008 Failure

Despite the firewall functioning correctly, the overall commissioning experiment
contained a deeper conceptual flaw.

The synthetic photometric candidate was fundamentally **planet-like**, while the
separate firewall test metadata labelled the synthetic control as an
**eclipsing binary**.

These represented two different experiments:

1. testing whether the science gates could classify synthetic photometric data;
2. testing whether catalogue identity could remain hidden until an authorised
   reveal.

PB0008 inadvertently recombined those experiments at the end of the pipeline.

At G15, the deliberately planted `ECLIPSING_BINARY` identity was legitimately
revealed from the firewall-control record.

G16 was then explicitly supplied that revealed identity and, according to its
rules, froze the final classification as:

`KNOWN_NONPLANETARY_OBJECT`

This classification was internally consistent with the information G16 received,
but it was **not an independent inference from the synthetic science data**.

The pipeline had effectively been given an artificial answer created for a
different commissioning purpose.

Therefore the final PB0008 astrophysical classification is considered invalid.

## Why This Matters

PB0008 demonstrated an important distinction between:

**information isolation**

and

**experimental blinding**.

The G03 firewall successfully prevented information leakage.

However, successfully hiding an artificial truth until a later gate does not make
that truth scientifically valid evidence.

A synthetic ground-truth label used to test infrastructure must never subsequently
participate in the classification of the synthetic candidate.

This issue could easily have produced misleading confidence in the pipeline had it
not been noticed before real-data validation.

## Final Audit

A structural audit at the end of PB0008 reported:

`PB-0008 FINAL AUDIT: PASS`

with no implementation warnings.

This result should be interpreted narrowly.

It demonstrated that the implemented record separation, gate storage, firewall
behaviour and final-record freezing were internally consistent.

It did **not** establish that the final astrophysical classification was valid.

The audit itself explicitly noted that commissioning structure does not validate
astrophysical performance on real TESS data.

## Lessons Learned

PB0008 produced several design rules that will be carried forward.

### 1. Synthetic truth must remain outside the detector

The injected class of a synthetic candidate must never appear inside the candidate
record, catalogue context, gate evidence or final synthesis.

Ground truth is revealed only after the detector has frozen its classification.

### 2. G03 will be a dummy gate during synthetic commissioning

During synthetic tests, G03 will preserve its position in the architecture but
perform no catalogue search.

Expected behaviour will be equivalent to:

`SYNTHETIC_MODE / CATALOGUE_NOT_QUERIED`

No hidden identity will exist.

### 3. G15 will not reveal synthetic truth

For synthetic candidates, the catalogue/literature reveal stage will likewise be
disabled or marked as deferred.

Synthetic ground truth is not catalogue evidence.

### 4. Gates must remain evidentially independent

A FAIL at one gate must not automatically erase a PASS elsewhere, and vice versa.

For example, dilution feasibility and spatial localisation answer different
questions and both results must survive into final synthesis.

### 5. Failed blind tests must not be tuned against their answers

If a future blinded candidate is misclassified, the failure will be analysed and
any pipeline revision versioned.

The revised detector will then be tested against newly generated blind cases,
rather than repeatedly tuning thresholds against the candidate whose truth has
already been revealed.

## PB0009 Course Correction

PB0009 will repeat synthetic end-to-end commissioning under a substantially
stronger blinded design.

Three physically coherent synthetic candidates will be generated:

1. a planet-like transit;
2. an eclipsing-binary false positive;
3. an off-target contaminating source.

Their identities will be randomly assigned to anonymous candidate records.

Neither the operator nor the PB pipeline will use the hidden truth during
G01–G16 processing.

Each candidate will be processed independently and its classification frozen.

Only after all three classifications are frozen will the ground-truth mapping be
revealed and compared with the detector results.

This will provide the first meaningful end-to-end test of whether the pipeline can
distinguish the three scenarios from their observable evidence alone.

## Conclusion

PB0008 did not provide a valid end-to-end astrophysical classification test.

It did, however, achieve something arguably more useful at this stage of
development: it exposed a subtle flaw in the experimental architecture before
the pipeline was trusted on real TESS data.

The run is therefore retained unchanged as a commissioning record rather than
rewritten or retrospectively corrected.

**PB0008 outcome: implementation commissioning successful; experimental design
failed; lessons incorporated into PB0009.**

# PB-0009 — Blind Synthetic Gauntlet
## Status: CLOSED — COMMISSIONING INCOMPLETE

### Objective

PB-0009 was designed as a blinded synthetic end-to-end test of the Planet Hunting Bros candidate-vetting pipeline.

Three anonymous synthetic candidates were generated:

- SYN-A
- SYN-B
- SYN-C

Their physical scenarios and ground-truth mapping were sealed at generation time.

The intention was to process all three candidates through the full vetting sequence without consulting the hidden truth, then compare the frozen scientific verdicts against the sealed answers only after completion.

G03 was deliberately converted into a non-evidential dummy placeholder for synthetic testing following the lessons from PB-0008.

---

## Blindness protocol

PB-0009 successfully maintained the intended firewall during the SYN-A run.

- Ground truth remained sealed.
- G03 performed no catalogue lookup.
- No hidden physical class was consulted by the science gates.
- Candidate interpretation proceeded entirely from science-visible synthetic data.

This was a major improvement over PB-0008.

---

## SYN-A results before termination

### G01 — Signal Detection
**FAIL**

A downward feature was detected, but the G01 detection criterion was not met strongly enough for a formal pass.

This did not terminate the commissioning workflow because PB-0009 was explicitly testing gate behaviour.

### G02 — Basic Light-Curve Quality
**PASS**

The synthetic light curve had acceptable cadence coverage, scatter, gaps and outlier behaviour.

### G03 — Synthetic Placeholder
**NOT_EVALUATED**

Catalogue search disabled in synthetic mode.

Ground truth remained sealed.

### G04 — Folded Morphology
**FAIL**

Supplied period:

**6.213080 d**

A feature was recovered close to phase zero:

- strongest folded depth: ~1502 ppm
- phase-zero depth: ~1678 ppm
- strongest dip phase: +0.0063
- epoch alignment: TRUE

However, the folded-feature significance was only:

**1.66 sigma**

This was below the frozen G04 threshold.

### G05 — Individual-Event Consistency
**PASS**

Four independently covered events were recovered.

Approximate event depths:

- 2044 ppm
- 1670 ppm
- 1592 ppm
- 2394 ppm

All four usable events were downward.

Median depth:

**1857 ppm**

Relative depth scatter:

**0.181**

The individual-event test therefore provided strong repeatability evidence despite the weak G04 folded-significance result.

### G06 — Period / Alias Sanity
**PASS**

Trial periods:

- 0.5P = 3.106540 d
- P = 6.213080 d
- 2P = 12.426161 d

Combined scores:

- half period: 14.92
- supplied period: 19.59
- double period: 12.76

The supplied period remained preferred over the obvious half/double aliases.

### G07 — Odd / Even Consistency
**PASS**

Usable events:

- odd: 2
- even: 2

Measured depths:

- odd: 2311.9 ± 133.3 ppm
- even: 1768.7 ± 139.7 ppm

Difference:

**543.1 ppm**

Significance:

**2.81 sigma**

Depth ratio:

**1.307**

This fell just below the frozen 3-sigma CAUTION threshold.

The result was therefore retained as PASS, while noting the mildly interesting odd/even asymmetry.

### G08 — Secondary-Eclipse Search
**PASS**

Secondary phase:

**0.5**

Measured secondary depth:

**176.9 ± 229.9 ppm**

Secondary SNR:

**0.77**

Secondary / primary depth ratio:

**0.095**

No statistically persuasive secondary eclipse was detected.

### G09 — Aperture Dependence
**PASS**

Measured depths:

- SMALL: 1932.7 ± 225.2 ppm
- STANDARD: 1932.7 ± 197.9 ppm
- LARGE: 1885.2 ± 160.9 ppm

Fractional depth spread:

**0.025**

The transit depth was therefore extremely stable across the three synthetic apertures.

### G10 — Nearby-Source / Contamination Assessment
**CAUTION**

Two synthetic nearby sources were present.

N1:

- separation: 30.0 arcsec
- separation: 1.43 TESS pixels
- delta magnitude: 2.12
- classification: CAUTION

N2:

- separation: 64.5 arcsec
- separation: 3.07 TESS pixels
- delta magnitude: 3.52
- classification: CLEAR

N1 therefore warranted contamination scrutiny.

### G11 — Dilution Feasibility
**FAIL**

Observed transit depth used:

**1932.7 ppm**

Both nearby sources were physically capable of reproducing the observed signal after dilution.

N1:

- maximum observable diluted depth: ~119757 ppm
- required intrinsic eclipse: **1.61%**
- capability: STRONG

N2:

- maximum observable diluted depth: ~33194 ppm
- required intrinsic eclipse: **5.82%**
- capability: STRONG

G11 therefore correctly concluded that neighbour contamination was physically feasible.

Importantly, this established feasibility only — not localisation.

---

# Commissioning failure discovered at G12

### G12 — Difference-Image Localisation
**UNTESTABLE**

PB-0009 did not generate or preserve a science-visible three-dimensional pixel cube / target-pixel-file equivalent.

The dataset therefore lacked the information required for genuine difference-image localisation.

Expected data structure:

`(cadence, row, column)`

Because pixel-level evidence had not been frozen at candidate generation time, it was decided that retroactively manufacturing pixel data after seeing SYN-A's earlier gate behaviour would compromise the integrity of the blind experiment.

No synthetic pixel cube was added.

G12 was therefore declared untestable by construction.

---

## Decision

PB-0009 was terminated at G12.

SYN-B and SYN-C were not processed.

They were intentionally left unused because both were generated under the same incomplete evidence architecture and would therefore encounter the same G12 limitation.

Running them through G01-G11 would provide limited additional commissioning value while still requiring a fresh synthetic batch for true end-to-end validation.

---

## What PB-0009 successfully demonstrated

PB-0009 was not a wasted run.

It successfully demonstrated:

- effective separation between ground truth and science-visible evidence
- a functioning dummy G03 synthetic placeholder
- independent behaviour of the early photometric gates
- useful disagreement between folded and event-by-event diagnostics
- period/alias testing
- odd/even comparison
- secondary-eclipse testing
- aperture-dependence testing
- nearby-source contamination screening
- dilution-feasibility testing
- preservation of uncertainty and UNTESTABLE states rather than forcing verdicts

Most importantly, PB-0009 exposed a genuine architectural omission before real blind-data validation began.

---

## Required change before next full synthetic gauntlet

Any future blind synthetic batch must generate and freeze the complete evidence package before candidate selection.

Minimum required package:

- time-series flux
- supplied candidate ephemeris
- aperture-dependent light curves
- neighbour catalogue
- synthetic target pixel cube
- cadence-aligned pixel timestamps
- spatial source geometry
- centroid-capable pixel information
- difference-image-capable transit signal
- sealed ground-truth mapping

No evidence product should be created or altered after a candidate begins passing through the gates.

---

## Final PB-0009 status

**CLOSED — COMMISSIONING INCOMPLETE**

Reason:

**Synthetic candidates were generated without pixel-level data required for G12 difference-image localisation.**

Ground truth for SYN-A, SYN-B and SYN-C remained sealed at termination.

No final astrophysical classification was issued.

PB-0009 is retained as a commissioning record and architecture-correction milestone rather than a completed validation run.


# PB-0010 — Project Mugshot
## Pixel-Complete Blind Synthetic Gauntlet

**Status:** COMPLETE  
**Mode:** SYNTHETIC_BLIND  
**Outcome:** 3 / 3 physical scenarios correctly classified blind  
**Ground truth:** Revealed only after all three verdicts were locked

---

## Objective

PB-0010 ("Project Mugshot") was designed as a demanding end-to-end validation of the Planet Hunting Bros vetting architecture before progression toward live candidate work.

The test asked a simple but important question:

> Can the pipeline distinguish a genuine on-target transit-like planet signal from an on-target eclipsing binary and an off-target contaminating eclipsing source, without access to the physical truth during vetting?

This was not intended simply to test transit detection.

The principal objective was **physical-source discrimination** using light-curve behaviour, aperture behaviour, neighbouring-source information and pixel-level localisation.

---

## Blind experimental design

Three synthetic candidates were generated:

- `SYN-A`
- `SYN-B`
- `SYN-C`

Exactly one example of each physical scenario was generated:

- `ON_TARGET_PLANET`
- `ON_TARGET_ECLIPSING_BINARY`
- `OFF_TARGET_ECLIPSING_CONTAMINANT`

The assignment of these scenarios to SYN-A/B/C was randomised.

Each candidate contained only the science-visible evidence required by the gauntlet, including:

- light curve
- supplied ephemeris
- multiple photometric apertures
- nearby-source catalogue
- 9 × 9 pixel cube
- target and neighbouring-source positions

The physical-class records were stored separately from the public candidate evidence.

A sealed truth accessor was created at generation time. It explicitly refused to reveal the truth until the verdicts for **all three candidates** had been locked.

The ordinary private-truth dictionary and scenario-assignment object were then removed from the normal runtime namespace.

This was therefore a genuine blind classification exercise rather than retrospective labelling.

---

## Gate architecture

The blind candidates were evaluated through the PB-0010 gate sequence.

### G01–G02
Basic candidate/data integrity and light-curve quality.

### G03
Synthetic-mode dummy placeholder.

No catalogue or external astrophysical information was introduced and G03 contributed **zero scientific evidence** to the classification.

### G04
Event repeatability / individual-event consistency.

### G05
Odd/even event consistency.

### G06
Period / alias sanity.

### G07
Independent odd/even consistency discriminator.

### G08
Secondary-eclipse search.

### G09
Aperture dependence.

### G10
Nearby-source / contamination-risk assessment.

### G11
Dilution feasibility.

Tests whether a neighbouring source is physically capable of producing the observed depth if it were eclipsed.

Importantly, physical capability alone does **not** establish that the neighbour is the source.

### G12
Difference-image / centroid localisation.

Tests where the disappearing light is spatially located.

### G13
Transit-correlated photocentre motion.

Independent spatial diagnostic testing whether the image photocentre moves systematically during the event.

### G14
Blind evidence synthesis and verdict lock.

G14 introduced no new astrophysical evidence.

It synthesised only the previously frozen gate outputs and locked one of:

- `PLANET_LIKE`
- `EB_LIKE`
- `CONTAMINANT_LIKE`
- `AMBIGUOUS`

Only after all three G14 verdicts were locked was ground truth permitted to be revealed.

---

# Results

| Candidate | Locked blind classification | Confidence | Revealed physical class | Result |
|---|---|---|---|---|
| SYN-A | `EB_LIKE` | HIGH | `ON_TARGET_ECLIPSING_BINARY` | CORRECT |
| SYN-B | `CONTAMINANT_LIKE` | HIGH | `OFF_TARGET_ECLIPSING_CONTAMINANT` | CORRECT |
| SYN-C | `PLANET_LIKE` | MODERATE | `ON_TARGET_PLANET` | CORRECT |

## Final score: 3 / 3

---

# Candidate notes

## SYN-A

**Locked verdict:** `EB_LIKE / HIGH`

SYN-A provided the on-target eclipsing-binary test case.

The candidate survived the basic transit-quality stages but accumulated sufficient eclipsing-binary-like evidence for the blind synthesis to classify it as `EB_LIKE`.

The verdict was locked before any physical-class information was consulted.

Following the final reveal, SYN-A was confirmed as:

`ON_TARGET_ECLIPSING_BINARY`

**Blind classification: CORRECT**

---

## SYN-B

**Locked verdict:** `CONTAMINANT_LIKE / HIGH`

SYN-B became the principal off-target contamination stress test.

Final recorded gate pattern:

- G01: PASS
- G02: PASS
- G04: PASS
- G05: PASS
- G06: PASS
- G07: PASS
- G08: PASS
- G09: FAIL
- G10: PASS
- G11: FAIL
- G12: FAIL
- G13: CAUTION

Total:

- 8 PASS
- 1 CAUTION
- 3 FAIL

The decisive evidence came from spatial localisation.

G12 found:

- localisation: `N1`
- target offset: **1.323 pixels**

The disappearing light therefore localised to a known off-target source rather than the nominal target.

G13 independently measured a **4.25 sigma** transit-correlated centroid shift, adding further contamination evidence.

G14 consequently locked:

`CONTAMINANT_LIKE / HIGH`

Following the final reveal, SYN-B was confirmed as:

`OFF_TARGET_ECLIPSING_CONTAMINANT`

**Blind classification: CORRECT**

This was an important result because several earlier light-curve gates passed. The pipeline did not mistake a transit-shaped signal for a planet once pixel-level source localisation was considered.

---

## SYN-C

**Locked verdict:** `PLANET_LIKE / MODERATE`

SYN-C was arguably the most informative of the three tests because it was **not artificially perfect**.

Early/transit-shape testing was exceptionally clean:

- G01: PASS
- G02: PASS
- G04: PASS
- G05: PASS
- G06: PASS
- G07: PASS
- G08: PASS

G04 recovered four usable repeating events with:

- median depth: **1481.4 ppm**
- relative depth MAD: **0.288**
- downward-event fraction: **1.000**

No strong odd/even, alias or secondary-eclipse evidence emerged.

However, contamination testing deliberately complicated the interpretation.

### G09 — CAUTION

The signal showed sufficient aperture dependence to trigger contamination scrutiny.

### G10 — PASS

Two nearby sources existed, but neither met the frozen G10 contamination-risk thresholds:

- nearby sources: 2
- caution sources: 0
- severe sources: 0

### G11 — FAIL

Both neighbours were bright enough that a physically possible stellar eclipse could, in principle, reproduce the observed diluted depth.

- physically capable neighbours: 2
- strongly capable neighbours: 2

At this stage contamination could not simply be dismissed.

### G12 — PASS / TARGET

The difference-image localisation then provided the critical spatial evidence.

Measured position of disappearing light:

- target position: `(4.000, 4.000)` pix
- difference centroid: `(3.908, 4.066)` pix
- target offset: **0.113 pix**
- target offset: **2.37 arcsec**

Distances from difference centroid:

- TARGET: **0.113 pix**
- N1: **1.551 pix**
- N2: **3.147 pix**

Closest object: `TARGET`

Therefore:

`G12 LOCATION: TARGET`

This strongly contradicted the hypothesis that either theoretically capable neighbour was actually producing the signal.

### G13 — PASS

The independent photocentre-motion test was exceptionally quiet:

- total centroid shift: **0.0005 pix**
- angular shift: **0.01 arcsec**
- shift significance: **0.27 sigma**

There was therefore no persuasive transit-correlated photocentre motion.

G12 and G13 independently supported an on-target origin.

### G14 synthesis

With:

- a repeating transit-like signal,
- no strong EB discriminator,
- secure on-target localisation,
- and negligible centroid motion,

the frozen G14 hierarchy locked:

`PLANET_LIKE / MODERATE`

The moderate rather than high confidence appropriately retained the caution arising from aperture dependence and theoretical dilution feasibility.

Following the final reveal, SYN-C was confirmed as:

`ON_TARGET_PLANET`

**Blind classification: CORRECT**

This was probably the strongest validation produced by PB-0010: the pipeline did not simply count PASS/FAIL gates. It correctly allowed stronger localisation evidence to resolve apparently conflicting contamination indicators.

---

# Runtime / protocol difficulties

PB-0010 was operationally difficult.

During the run there were several notebook/runtime continuity problems, including lost runtime state and some incompatibilities between earlier gate-record schemas.

These required recovery and compatibility cells.

Important safeguards were maintained during those repairs:

1. Ground truth remained sealed.
2. Candidate evidence was not regenerated after inspection.
3. Frozen scientific thresholds were not changed to accommodate results.
4. Previously locked verdicts were restored only from their already-public blind records.
5. Compatibility repairs changed record handling, not astrophysical measurements.
6. No physical-class information was used to alter a blind verdict.
7. The final truth reveal remained prohibited until all three candidate verdicts were locked.

These difficulties made PB-0010 substantially messier than intended, but they also tested an important real-world property of the workflow: the ability to recover from interrupted analysis without contaminating a blind experiment.

---

# Scientific interpretation

PB-0010 achieved its primary objective.

The gauntlet correctly distinguished:

- an on-target eclipsing binary,
- an off-target eclipsing contaminant,
- and an on-target planet signal.

### Key lesson 1 — Transit shape alone is insufficient

SYN-B passed many of the early photometric tests.

Its rejection required source-localisation information.

### Key lesson 2 — Dilution feasibility is not localisation

SYN-C failed G11 because neighbouring stars were theoretically capable of producing the observed depth.

However, G12 and G13 demonstrated that the observed disappearing light was associated with the target.

A neighbour being capable of producing a signal does not mean that it did.

### Key lesson 3 — Pixel evidence is critical

G12 was decisive in separating SYN-B and SYN-C:

- SYN-B → lost light localised off target
- SYN-C → lost light localised on target

This validates the decision to make pixel-level diagnostics a core component of the vetting pipeline.

### Key lesson 4 — Evidence hierarchy matters more than gate counting

SYN-C contained both CAUTION and FAIL results yet was correctly classified as planet-like.

The final classification therefore cannot be reduced to a simple number of passed gates.

Different diagnostics answer different physical questions and must be interpreted accordingly.

---

# PB-0010 verdict

**PROJECT MUGSHOT: PASS**

Three distinct physical scenarios were generated blind.

Three verdicts were locked blind.

Ground truth was then revealed.

**3 / 3 classifications were correct.**

PB-0010 therefore provides the first complete pixel-level blind validation that the current Planet Hunting Bros candidate-vetting architecture can distinguish the principal synthetic classes it was designed to separate.

This does **not** demonstrate that the pipeline will classify every real TESS candidate correctly.

Real observations will contain additional systematics, stellar behaviour, catalogue incompleteness and astrophysical configurations not represented by these three synthetic cases.

PB-0010 should therefore be treated as a successful validation milestone, not as proof of universal classifier accuracy.

---

## Next action

Preserve the PB-0010 gate definitions and frozen thresholds as the validated baseline.

Do not retrospectively optimise PB-0010 against its now-known truth labels.

Future improvements should be tested prospectively in new blind runs rather than by modifying PB-0010 until it performs better.

**Believe — but test it blind.** 🪐

## PB-0011 — Changing Gear

**Mode:** Synthetic blind automated validation  
**Protocol:** G01–G14  
**Candidates:** 5  
**Blind score:** 3 / 5  
**Retrospective tuning:** None

### Aim

PB-0011 was the first Planet Hunting Bros experiment designed to move from manually shepherding individual synthetic candidates through the vetting process to a fully automated blind G01–G14 pipeline.

Five synthetic candidates were generated and frozen before testing. Their ground-truth classes were sealed until all five blind verdicts had been completed and locked.

The candidate population contained one example from each of five scenario families:

- Clean on-target planet
- On-target eclipsing binary
- Off-target eclipsing contaminant
- Planet with contamination context
- Deceptive on-target eclipsing binary

The primary objective was therefore both operational and scientific: determine whether the complete vetting architecture could run automatically while retaining meaningful discrimination between planet-like, eclipsing-binary-like and contaminant-like signals.

### Blind results

| Candidate | Blind verdict | Confidence | Ground truth | Result |
|---|---|---|---|---|
| SYN-A | EB_LIKE | MODERATE | On-target eclipsing binary | Correct |
| SYN-B | CONTAMINANT_LIKE | HIGH | Off-target eclipsing contaminant | Correct |
| SYN-C | EB_LIKE | MODERATE | On-target planet | Incorrect |
| SYN-D | PLANET_LIKE | MODERATE | On-target planet | Correct |
| SYN-E | PLANET_LIKE | MODERATE | On-target eclipsing binary | Incorrect |

**Official blind score: 3 / 5**

Although the overall predicted population happened to contain the correct numbers of planet-like, EB-like and contaminant-like classifications, two individual candidates were exchanged between the planet-like and EB-like classes. Population-level agreement was therefore not treated as successful candidate-level classification.

### Operational result

PB-0011 successfully demonstrated that the G01–G14 architecture could be executed as an automated blind pipeline.

The run also introduced substantially stronger persistence and recovery architecture following the operational difficulties encountered during PB-0010.

All five candidates could be processed automatically, their evidence retained, their verdicts locked before truth reveal, and the blind protocol maintained.

This was considered a major operational success despite the 3/5 scientific score.

### Scientific interpretation

PB-0011 demonstrated that successful automation did not imply that the scientific decision logic was sufficiently mature.

The two incorrect classifications were retained as genuine blind failures. No thresholds or decision rules were retrospectively altered to improve the PB-0011 score.

Instead, the failures became evidence for subsequent validation work.

This distinction was important:

**PB-0011 was allowed to fail.**

The purpose of blind validation was not to obtain a perfect score, but to expose weaknesses that would otherwise remain hidden.

### Outcome

PB-0011 established that:

- the complete G01–G14 workflow could operate automatically;
- candidate evidence and blind verdicts could survive the automated workflow;
- ground truth could remain sealed until verdict lock;
- the detector could distinguish clear off-target contamination strongly;
- planet-like versus on-target EB discrimination remained imperfect.

The official PB-0011 result remains permanently recorded as **3 / 5**.

It was not repaired after truth reveal.

### Next action

Run a larger fresh blind population without retrospectively modifying PB-0011.

Increase the sample from five to ten candidates to determine whether the observed classification weaknesses persist and to provide a better basis for diagnosing failure modes.

**Believe — but let the detector fail honestly. 🪐**

## PB-0012 — Ten Little Synthetics

**Mode:** Synthetic blind automated validation  
**Protocol:** G01–G14  
**Candidates:** 10  
**Blind score:** 8 / 10  
**Retrospective tuning:** None

### Aim

PB-0012 was designed as a larger fresh blind test following the 3/5 result of PB-0011.

The objective was not to repair or rescore PB-0011. Instead, ten entirely fresh synthetic candidates were generated and frozen before testing using the existing scientific decision logic.

The population contained two examples from each of five scenario families:

- Clean on-target planet
- On-target eclipsing binary
- Off-target eclipsing contaminant
- Planet with contamination context
- Deceptive on-target eclipsing binary

Candidate identities were randomised and ground truth remained sealed until all blind verdicts had been completed and locked.

Engineering and persistence architecture could be improved following PB-0011, but the scientific classifier was not retrospectively tuned against the PB-0011 answers.

### Result

The automated G01–G14 pipeline correctly classified eight of the ten fresh blind candidates.

**Official blind score: 8 / 10**

The two incorrect classifications were retained as genuine failures.

No thresholds were subsequently adjusted to convert PB-0012 into a higher-scoring run.

### Immediate autopsy

Unlike retrospective tuning, a post-reveal autopsy was performed to determine whether the two failures shared an identifiable physical or logical mechanism.

The principal finding concerned the relationship between:

- **G12 — Difference Image Localisation**
- **G13 — Transit-Correlated Centroid Motion**

The existing synthesis logic could treat a G13 failure too strongly as evidence for an off-target contaminating source.

However, G12 and G13 do not provide equivalent information.

G12 attempts to localise the source of the event directly using the difference image.

G13 asks whether the measured centroid changes in correlation with the event.

The PB-0012 failures demonstrated that these diagnostics can disagree.

Most importantly:

**G12 PASS + G13 FAIL should not automatically imply that the event originates off target.**

If the difference image positively localises the event to the target, a transit-correlated centroid anomaly is evidence of a localisation conflict requiring caution — not, by itself, proof that a neighbouring source produced the event.

### Scientific interpretation

PB-0012 therefore identified a specific prospective hypothesis rather than simply suggesting that the detector needed to become more permissive.

The proposed interpretation was:

> A lone G13 FAIL should not establish off-target contamination when G12 positively localises the event to the target.

This was considered physically preferable to changing numerical thresholds simply to recover the two failed candidates.

No PB-0012 verdict was altered after this finding.

The official score remains permanently **8 / 10**.

### Importance

PB-0012 represented a substantial improvement over PB-0011:

**PB-0011: 3 / 5**  
**PB-0012: 8 / 10**

More importantly, the larger blind population exposed a specific weakness in the evidence-synthesis architecture that could be expressed as a testable scientific hypothesis.

The appropriate next step was therefore not to modify PB-0012 until it achieved 10/10.

Instead, the proposed rule change would be frozen prospectively and tested against an entirely new blind population.

### Next action

Create PB-0013 as a fresh ten-candidate blind experiment.

Make exactly one prospective scientific change:

**G12 PASS + G13 FAIL = LOCALISATION CONFLICT**

and:

**G13 FAIL alone is not sufficient evidence to establish OFF-TARGET contamination.**

Freeze this rule before generating the PB-0013 candidates.

Make no other scientific changes.

Then test whether the revised interpretation generalises to fresh unseen synthetic evidence.

**Believe — but change the rule before seeing the answers. 🪐**

## PB-0013 — Localisation Conflict

**Mode:** Synthetic blind automated validation  
**Protocol:** G01–G14  
**Candidates:** 10  
**Parent experiment:** PB-0012  
**Parent result:** 8 / 10  
**Blind score:** 10 / 10  
**Retrospective tuning:** None  
**Status:** Closed

### Aim

PB-0013 was designed as a prospective test of a single scientific hypothesis identified during the post-reveal autopsy of PB-0012.

PB-0012 had achieved 8/10 on ten fresh blind synthetic candidates.

Analysis of its failures identified a specific weakness in the interpretation of two localisation diagnostics:

- **G12 — Difference Image Localisation**
- **G13 — Transit-Correlated Centroid Motion**

The hypothesis for PB-0013 was:

> A lone G13 FAIL should not establish off-target contamination when G12 positively localises the event to the target.

PB-0013 therefore changed exactly one element of the scientific synthesis logic.

### Prospective rule change

Before any PB-0013 candidates were generated, the following rule was frozen:

**G12 PASS + G13 FAIL = LOCALISATION CONFLICT**

and:

**G13 FAIL alone is not sufficient evidence to establish OFF-TARGET contamination.**

A G12 failure could continue to provide genuine evidence for an off-target source.

No other scientific decision rule was intentionally changed from the PB-0012 baseline.

The purpose of PB-0013 was therefore not general detector optimisation.

It was a direct prospective test of one specific hypothesis using completely fresh blind evidence.

### Blind architecture

Ten fresh synthetic candidates were generated only after the scientific protocol had been frozen.

The population contained two examples from each of five scenario families:

- Clean on-target planet
- On-target eclipsing binary
- Off-target eclipsing contaminant
- Planet with contamination context
- Deceptive on-target eclipsing binary

Candidate identities were randomised.

Ground truth was sealed.

Candidate evidence was frozen and fingerprinted before testing.

The complete G01–G14 pipeline was then run automatically.

All ten verdicts were locked before ground truth was revealed.

Immutable candidate records and a final pre-reveal snapshot were preserved before opening the truth vault.

### Locked blind verdicts

| Candidate | Blind verdict | Confidence | Localisation note |
|---|---|---|---|
| SYN-A | EB_LIKE | MODERATE | Localisation conflict |
| SYN-B | PLANET_LIKE | MODERATE | — |
| SYN-C | PLANET_LIKE | MODERATE | Localisation conflict |
| SYN-D | EB_LIKE | MODERATE | Localisation conflict |
| SYN-E | CONTAMINANT_LIKE | HIGH | — |
| SYN-F | CONTAMINANT_LIKE | HIGH | — |
| SYN-G | PLANET_LIKE | MODERATE | Localisation conflict |
| SYN-H | EB_LIKE | MODERATE | Localisation conflict |
| SYN-I | PLANET_LIKE | MODERATE | — |
| SYN-J | EB_LIKE | MODERATE | Localisation conflict |

### Ground-truth reveal

After all ten blind verdicts had been locked, the sealed ground truth was revealed.

| Candidate | Blind verdict | Ground truth | Result |
|---|---|---|---|
| SYN-A | EB_LIKE | On-target eclipsing binary | Correct |
| SYN-B | PLANET_LIKE | On-target planet | Correct |
| SYN-C | PLANET_LIKE | On-target planet | Correct |
| SYN-D | EB_LIKE | On-target eclipsing binary | Correct |
| SYN-E | CONTAMINANT_LIKE | Off-target eclipsing contaminant | Correct |
| SYN-F | CONTAMINANT_LIKE | Off-target eclipsing contaminant | Correct |
| SYN-G | PLANET_LIKE | On-target planet | Correct |
| SYN-H | EB_LIKE | On-target eclipsing binary | Correct |
| SYN-I | PLANET_LIKE | On-target planet | Correct |
| SYN-J | EB_LIKE | On-target eclipsing binary | Correct |

## Official blind score: 10 / 10

Class-level performance:

- **On-target planets: 4 / 4**
- **On-target eclipsing binaries: 4 / 4**
- **Off-target eclipsing contaminants: 2 / 2**

No verdict was changed after truth reveal.

No threshold was altered.

No candidate was rerun to obtain a more favourable classification.

**Retrospective tuning: NONE**

### Scientific interpretation

PB-0013 provides strong prospective support for the localisation hypothesis generated by PB-0012.

The important result is not simply that the score increased from 8/10 to 10/10.

The experimental sequence was:

**PB-0011: 3 / 5**

↓

**PB-0012: 8 / 10**

↓

PB-0012 post-reveal autopsy identifies a specific localisation failure mode.

↓

One scientific change is defined prospectively.

↓

The revised rule is frozen before new candidates exist.

↓

Ten fresh blind candidates are generated.

↓

All verdicts are locked before truth reveal.

↓

**PB-0013: 10 / 10**

This distinction is critical.

PB-0013 was not tuned until the existing PB-0012 failures disappeared.

The PB-0012 score remains 8/10.

Instead, those failures generated a hypothesis which was subsequently tested against new unseen evidence.

### What PB-0013 establishes

Within the synthetic population tested, PB-0013 demonstrates that the revised localisation interpretation can successfully distinguish:

- planet-like on-target events;
- on-target eclipsing binaries;
- off-target eclipsing contaminants;
- planet signals occurring in contamination-risk environments;
- deceptive eclipsing-binary configurations.

It also demonstrates that a G13 centroid-motion failure can coexist with an event that is nevertheless correctly localised to the target by G12.

Treating this combination as a **localisation conflict**, rather than automatically as proof of contamination, successfully preserved the correct physical classification in this blind test.

### What PB-0013 does NOT establish

A 10/10 result on ten synthetic candidates does not demonstrate universal classifier accuracy.

The synthetic generator remains an approximation of reality.

Real TESS observations contain additional complications including:

- spacecraft systematics;
- quality-flagged cadences;
- stellar variability;
- imperfect apertures;
- crowded fields;
- real PSF behaviour;
- sector-dependent observing conditions;
- catalogue incompleteness;
- astrophysical configurations not represented by the synthetic generator.

PB-0013 should therefore be treated as a successful synthetic validation milestone, not as proof that the detector will classify every real TESS candidate correctly.

### Experimental record

The run was closed with:

- Scientific protocol frozen before candidate generation: **YES**
- Candidate evidence frozen before testing: **YES**
- Ground truth sealed during testing: **YES**
- All blind verdicts locked before reveal: **YES**
- Immutable pre-reveal records preserved: **YES**
- Retrospective tuning: **NONE**
- Official score: **10 / 10**
- Run closed: **YES**

Immutable post-reveal record:

`PB0013_POST_REVEAL_FINAL_IMMUTABLE.json`

Final record SHA256:

`d0bb1c901777fc890fe5150e1d6456a903e693b25ab5ac93743131fe9ee58970`

### Next action

Do not attempt to preserve the 10/10 score by constructing increasingly artificial synthetic edge cases and tuning the detector against them.

The next major validation step should expose the frozen detector architecture to **known real TESS observations**.

Use a prospectively selected blind sample containing known planets, eclipsing binaries and contaminating systems.

The objective should be to identify the detector's real-world limitations — not to force it to classify every difficult outlier correctly.

Synthetic validation remains the controlled laboratory.

**Reality becomes the proving ground.**

**PB-0013 final result: 10 / 10.**

**Believe — but test it blind. ✋️*✋️ 🪐**
## PB-0014 — Known-Real TESS Validation

**Mode:** Known-real blind validation  
**Targets:** 12  
**Data source:** TESS / MAST  
**Status:** Acquisition complete — scientific testing not started

### Aim

PB-0014 marks the transition from controlled synthetic validation to known real TESS observations.

Following the successful synthetic validation sequence culminating in PB-0013, the next objective is to determine how the existing vetting architecture behaves when exposed to the complications present in genuine TESS data.

The purpose is not to tune the detector until it correctly handles every difficult known target.

Instead, PB-0014 is intended to identify where the synthetic-validated architecture succeeds, where it fails, and which assumptions do not survive contact with real observations.

The known astrophysical classifications of the targets are to remain hidden from the detector-testing process until blind classifications have been produced and locked.

### Target roster

Twelve known-real TESS targets were selected and frozen before any flux or pixel data were inspected.

| Blind ID | Target | Selected product |
|---|---|---|
| REAL-A | TOI 1455 | SPOC Sector 17 |
| REAL-B | TOI 1846 | SPOC Sector 17 |
| REAL-C | TOI 1423 | SPOC Sector 16 |
| REAL-D | TOI 585 | SPOC Sector 9 |
| REAL-E | TOI 1842 | SPOC Sector 23 |
| REAL-F | TOI 5478 | SPOC Sector 60 |
| REAL-G | TOI 1619 | SPOC Sector 57 |
| REAL-H | TOI 813 | SPOC Sector 4 |
| REAL-I | TOI 2331 | SPOC Sector 105 |
| REAL-J | TOI 1186 | SPOC Sector 24 |
| REAL-K | TOI 1062 | SPOC Sector 1 |
| REAL-L | TOI 1521 | SPOC Sector 58 |

The acquisition selection was frozen before download.

Selection SHA256:

`9ccbb61bcca0b8349ec3e290abc7f5069a7ff027fc0bf90c89a07811f130a004`

### Acquisition reconnaissance

MAST reconnaissance confirmed that TESS products were available for all twelve frozen targets.

**Targets with available TESS products: 12 / 12**

Reconnaissance was query-only.

No files were downloaded and no scientific evidence was inspected during this stage.

### Raw evidence acquisition

For each of the twelve targets, two forms of raw evidence were acquired:

- one frozen SPOC light-curve FITS product;
- the corresponding target-pixel FITS product.

This produced a raw evidence bank containing:

- **12 / 12 light curves**
- **12 / 12 target-pixel files**
- **24 / 24 total raw FITS files**

All files were persisted to Google Drive.

SHA256 fingerprints were calculated and subsequently re-verified against the persisted files.

**Final raw-file verification: 24 / 24 PASS**

One target-pixel product generated a TESS quality-mask warning indicating that approximately 24% of its cadences would be ignored under the Lightkurve quality mask.

This was retained as an authentic property of the real data rather than treated as an acquisition failure.

The file itself downloaded successfully and passed integrity verification.

### Blind-state preservation

At the end of the acquisition session:

- Target roster frozen: **YES**
- Acquisition selection frozen: **YES**
- Light curves acquired: **12 / 12**
- Target-pixel files acquired: **12 / 12**
- Raw files hash verified: **24 / 24**
- Flux inspected: **NO**
- Pixel evidence inspected: **NO**
- Preprocessing started: **NO**
- Detector testing started: **NO**
- Ground truth revealed: **NO**

The acquisition session was then formally closed.

A recovery checkpoint and acquisition manifest were persisted to Google Drive so that a future runtime can reconstruct and verify the frozen evidence bank without re-querying or re-downloading the observations.

### Scientific boundary

No PB-0014 scientific result exists at this stage.

The successful acquisition of the data should not be interpreted as detector validation.

No light curve has yet been judged.

No pixel evidence has yet been interpreted.

No candidate has yet received a blind physical classification.

This separation is deliberate.

The raw real-world evidence has been acquired and frozen **before the analysis rules are applied to it**.

### Next action

Begin the next PB-0014 session with recovery and integrity verification.

Before inspecting the twelve targets, define and freeze the preprocessing and real-data handling rules required to translate the PB-0013 synthetic architecture to genuine TESS observations.

Then process the known-real targets blind.

The purpose of PB-0014 is not to obtain 12/12 at any cost.

A failure against a known real target is scientifically useful if it reveals a genuine limitation of the pipeline.

Any limitations identified should be recorded before considering prospective changes in a subsequent experiment.

**Synthetic validation is complete.**

**PB-0014 begins the conversation with reality.**

**Believe — but let reality answer back. ✋️*✋️ 🪐**
