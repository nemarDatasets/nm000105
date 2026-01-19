# discrete_gestures: Discrete Hand Gesture Detection from EMG

## Overview

**Dataset**: discrete_gestures - Discrete hand gestures from wrist-based surface electromyography
**Task**: Nine discrete hand gestures (pinches and swipes)
**Participants**: 100 subjects
**Sessions**: 100 total (1 per subject)
**Publication**: Kaifosh et al., 2025 - "A generic non-invasive neuromotor interface for human-computer interaction" (Nature)

### Purpose

This dataset captures wrist-based sEMG signals during prompted discrete hand gestures for navigation and activation tasks. The goal is to enable gesture-based computer control without cameras or visible hand movements, with applications in AR/VR, mobile interfaces, and accessibility.

Key research objectives:
- Generic models that work across users without calibration
- Discrete gesture classification with high accuracy
- Real-time gesture detection for interactive systems
- Robustness to electrode placement variability

## Dataset Details

### Participants

**Sample size**: 100 participants
**Demographics**: Not available (age, sex, handedness marked as n/a)
**Recording side**: Dominant wrist (assumed right-handed, varies by participant)
**Sessions**: 1 session per participant

### Hardware

**Device**: sEMG Research Device (sEMG-RD)
**Configuration**: Single wristband (dominant wrist)
**Channels**: 16
**Sampling rate**: 2000 Hz
**Bit depth**: 12 bits
**Dynamic range**: ±6.6 mV
**Bandwidth**: 20-850 Hz
**Connectivity**: Bluetooth
**Electrode type**: Dry gold-plated differential pairs

### Gestures

**Nine discrete gestures**:

**Thumb swipes** (4):
- Left swipe
- Right swipe
- Up swipe
- Down swipe

**Pinches** (4):
- Index-to-thumb pinch
- Middle-to-thumb pinch
- Ring-to-thumb pinch
- Pinky-to-thumb pinch

**Activation** (1):
- Thumb tap

### Recording Protocol

1. Participant dons sEMG-RD on dominant wrist
2. Gesture prompter displays gesture cue (scrolling left-to-right)
3. Participant performs prompted gesture
4. Randomized order with randomized inter-gesture intervals
5. Multiple repetitions of each gesture type

**Session duration**: Varies by participant
**Total gestures**: 1900 prompted gestures across all participants
**Stage boundaries**: 16 recording stages per session

## Data Contents

### Files per Session

```
sub-XXX/ses-XXX/emg/
├── sub-XXX_ses-XXX_task-discretegestures_emg.edf
├── sub-XXX_ses-XXX_task-discretegestures_emg.json
├── sub-XXX_ses-XXX_task-discretegestures_channels.tsv
├── sub-XXX_ses-XXX_task-discretegestures_events.tsv
└── sub-XXX_ses-XXX_electrodes.tsv
```

### Channel Configuration

**Total channels**: 16 (EMG0-EMG15)
**Channel naming**: Unique identifiers (EMG0-EMG15)
**Electrode naming**: E0-E15 (physical positions)
**Reference**: Bipolar (differential sensing)

**channels.tsv columns**:
- `name`: Channel identifier (EMG0-EMG15)
- `type`: EMG
- `units`: V
- `signal_electrode`: Physical electrode name (E0-E15)
- `reference`: bipolar

**electrodes.tsv columns**:
- `name`: Electrode identifier (E0-E15)
- `x`, `y`, `z`: 3D coordinates (percent units, no decimals)

### Events

**events.tsv contains**:
- **Gesture prompts**: Timestamped prompts for each gesture
  - `type`: gesture_X (where X is the gesture name)
  - `latency`: Sample index when gesture was prompted
  - `gesture_type`: Specific gesture (e.g., "index_pinch", "thumb_swipe_left")
- **Stage boundaries**: Recording session phases
  - `type`: stage_boundary
  - `stage_name`: Stage identifier

**Total events**: 1916 (1900 gesture prompts + 16 stage boundaries)

### Coordinate System

**Single coordinate system** (no space entity):

```
EMGCoordinateSystem: Other
EMGCoordinateUnits: percent
X: USP → RSP (0-100%)
Y: Right-hand rule perpendicular (0-100%)
Z: Radial offset (constant 10%)
```

**Anatomical landmarks**:
- RSP: Radial Styloid Process
- USP: Ulnar Styloid Process

**Note**: Right-handed coordinate system for dominant wrist

## Signal Processing

### Preprocessing Applied

1. **High-pass filtering**: 40 Hz cutoff
2. **Clock drift correction**: Time synchronization
3. **Irregular sampling handling**: Resampling when deviation >1% (up to 9290% deviation detected)

### Signal Characteristics

**Gesture patterns**:
- Patterned activity across channels corresponding to flexor/extensor muscles
- Fine differences across gesture instances
- Channel activity correlates with muscle positions (Fig. 1 in paper)

## Baseline Performance

### Published Results (Kaifosh et al., 2025)

**Offline Classification** (held-out participants):
- Accuracy: >90% for gesture classification
- False-negative rate improves with more training data
- Generic models trained on hundreds of participants

**Closed-loop Performance** (n=24 naive test users):
- **First-hit probability**: Median improvement from 0.74 (practice) to 0.82 (evaluation block 2)
- **Gesture completion rate**: Median 0.88 gestures/second (evaluation block 2)
- **Baseline comparison**: Gaming controller achieves 1.45 completions/second

**Model architecture**: 1D convolution → LSTM layers
**Learning effects**: Participants improve from practice to evaluation blocks

### Representation Analysis

**Network learns**:
- First layer filters resemble motor unit action potentials (MUAPs)
- Deeper layers progressively separate gesture categories
- Invariance to nuisance variables (participant ID, electrode placement, signal power)

## Confusion Matrix

**Common confusions** (from paper):
- Index and middle holds sometimes released too early
- Similar gestures (e.g., adjacent finger pinches) occasionally confused
- Swipe directions generally well-separated

**Note**: Some errors are behavioral (wrong gesture performed) not just decoding errors

## Use Cases

### Machine Learning

- **Time series classification**: Discrete event detection
- **Generic modeling**: Out-of-the-box cross-user generalization
- **Representation learning**: Physiologically-grounded features
- **Real-time prediction**: Low-latency gesture detection

### Applications

- **Grid navigation**: Discrete movement in 2D space
- **Menu selection**: Activation gestures for UI elements
- **Game control**: Gesture-based game inputs
- **AR/VR interfaces**: Hands-free navigation
- **Accessibility**: Alternative input modality

## Known Issues and Limitations

### By Design

- **Single wrist**: Dominant hand only (not bilateral)
- **Handedness unknown**: Assumed right-handed, varies by participant
- **Gesture novelty**: Users needed coaching to learn effective gestures
- **No demographic data**: Age, sex, handedness not collected

### Technical

- **Electrode placement**: Single session per user (less cross-session data than emg2qwerty)
- **Signal amplitude**: Varies with gesture force
- **Hardware unavailable**: sEMG-RD not commercially available

### Data Quality

- **Irregular sampling**: High deviation detected (up to 9290%), resampling applied
- **Behavioral errors**: Not all errors are decoder errors (some user mistakes)

## Comparison to Baselines

**Nintendo Joy-Con controller**:
- Median: 1.45 completions/second
- sEMG decoder: 0.88 completions/second (66% slower)

**However**: sEMG doesn't require hand-encumbering device

### BIDS Format

```
Pernet, C.R., et al. (2019). EEG-BIDS, an extension to the brain
imaging data structure for electroencephalography.
Scientific Data, 6(1), 103.
```

## Access and Contact

**Original data**: Part of Meta Reality Labs neuromotor interface research
**BIDS conversion**: Custom MATLAB tools using EEGLAB BIDS plugin
**Data curator**: Yahya Shirazi, SCCN (Swartz Center for Computational Neuroscience), INC (Institute for Neural Computation), UCSD
**Contact**: See Nature paper for corresponding authors

## License

Research and educational use. See original publication.


## Citation
```
Kaifosh, P., Reardon, T.R., & CTRL-labs at Reality Labs. (2025).
A generic non-invasive neuromotor interface for human-computer interaction.
Nature, 645(8081), 702-711. https://doi.org/10.1038/s41586-025-09255-w
```

## Data Curator
**Yahya Shirazi**
SCCN (Swartz Center for Computational Neuroscience)
INC (Institute for Neural Computation)
University of California San Diego

## Version History
**v1.0** (2025-10-01): Initial BIDS conversion

---
**BIDS Version**: 1.11 | **EMG-BIDS**: BEP-042 | **Updated**: Oct 1, 2025
