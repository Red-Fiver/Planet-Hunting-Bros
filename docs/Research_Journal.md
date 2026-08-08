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

**PB-0002 – Survey Pipeline Complete**
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
PB-0003 – Detector Evaluation
Objectives:
Build a scientific scorecard.
Calculate:
True Positives
False Positives
True Negatives
False Negatives
Introduce more challenging simulated observations.
Continue preparing the pipeline for analysis of genuine NASA TESS light curves.
