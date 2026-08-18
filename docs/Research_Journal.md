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
