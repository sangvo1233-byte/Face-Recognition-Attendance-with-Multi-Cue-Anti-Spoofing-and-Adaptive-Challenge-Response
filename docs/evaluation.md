# Evaluation Protocol and Evidence Status

## Current status

**No empirical evaluation dataset or results have been supplied.** This document proposes how to evaluate the existing web V4.4 implementation without changing its algorithms. It does not report an experiment that has already taken place.

The focused test command covering `tests/test_detect_v4.py` and `tests/test_v2_v3.py` passed during documentation integration. Those checks use mocks or synthetic inputs and do not establish real-world presentation-attack resistance. The camera flow and empirical protocol were not run. Legacy FPS tables and attack-coverage labels are not adopted as V4.4 results.

## Questions to investigate

1. How often do genuine enrolled users complete attendance, receive a challenge, or fail the process?
2. How often are print, static-display, and video-replay attempts rejected or admitted by the final system?
3. How do lighting, camera, display, compression, and the selected capture mode affect outcomes and completion time?

The study can answer these questions about the complete implementation. Showing the incremental benefit of individual cues would require controlled comparisons beyond merely observing the complete system.

## Freeze the implementation before measuring

Record commit `4a38c61e37db70c6d3431d683fd96b6c04bc4c90` or the exact successor used for the study, Python and dependency versions, model filenames and hashes, CPU/GPU and actual ONNX provider, operating system, camera/display models, image resolution, compression settings, capture mode, and all relevant thresholds. Record the V3-named settings reused by V4.4 as well.

No locked environment has been validated for this package. Record a working environment once established; do not present the minimum versions in `requirements.txt` as the versions used in an experiment.

## Trial design

Use enrollment recordings separate from evaluation attempts. If thresholds are adjusted, do so on a separate calibration subset and freeze them before evaluation. Keep related frames from one recording in the same partition. Known users must remain in the enrollment gallery for genuine identification trials; unknown-user attempts must use identities outside that gallery.

Include separate categories for genuine presentation, printed photograph, static image shown on a display, and prerecorded video shown on a display. Treat variations in device, angle, distance, and illumination as recorded conditions. Specify participant count and attempt count from actual collection rather than choosing a claim about statistical adequacy in advance.

For active challenges, conduct live interactive trials or capture complete sessions including the displayed instructions and responses. An existing video cannot demonstrate a genuine user's response to a newly generated instruction sequence. A replay video is the attack stimulus; the camera's observation of the display is the system input.

For an independent trial, begin from a fresh runtime state and apply a declared reset procedure without clearing real attendance data. Repeated-attempt trials should be reported separately because the recent-pass interval, challenge failure counter, and track history can affect outcomes. Use a dedicated test session and test data; the existing legacy integration test writes to normal runtime locations and is unsuitable as the research protocol.

Participation and use of face imagery require an accurate record of consent and the institution's applicable review arrangements. Do not invent an approval or exemption statement. Publish anonymized outcome summaries and a protocol where raw imagery cannot be shared.

## Trial record

The following fields are a proposed recording schema. They are not claimed to be exported automatically by the current code.

```text
trial_id, participant_code, target_identity_code, presentation_type,
recording_group, partition, capture_mode, camera_code, display_code,
lighting_condition, source_commit, configuration_id,
predicted_identity_code, pad_decision, final_decision, rejection_reason,
challenge_severity, challenge_sequence, challenge_outcome,
start_time, terminal_time, timeout_or_nonresponse
```

Distinguish a PAD decision from an identity decision and from the final attendance outcome. Current events can support observations, but an evaluation record must explain exactly how each field was derived. If a subsystem outcome cannot be recovered unambiguously, report the observable end-to-end outcome rather than inventing a subsystem label.

## Metrics

NIST's PAD evaluation plan uses attack and bona fide classification errors as well as nonresponse measures. The definitions below adapt the classification-error concepts to a clearly declared trial protocol; this is not a claim of ISO or NIST certification. [NIST PAD plan, v1.1](https://pages.nist.gov/frvt/api/FRVT_pad_api_v1.1.pdf).

| Quantity | Definition |
| --- | --- |
| APCER for an attack category | Responding attack presentations labeled bona fide by the measured PAD decision / all responding attack presentations in that category. |
| BPCER | Responding genuine presentations labeled attack by that PAD decision / all responding genuine presentations. |
| Nonresponse | Report counts and rates separately for attack and genuine trials without a terminal classification; state why the system did not respond. |
| End-to-end attack admission | Attack attempts that produce an accepted attendance result for the target identity / all attack attempts. |
| Genuine completion | Genuine attempts correctly completing attendance / all genuine attempts. |
| Identity outcomes | Counts of correct identity, wrong identity, and unknown; declare the trial population and gallery size. |
| Challenge burden | Fraction of genuine trials requiring a challenge, completion/timeout counts, and challenge duration. |
| Completion time | Time from the declared trial start to the terminal result; report median and a high percentile when justified by the sample size. |

The responding-trial denominator is a declared study convention. Always report the excluded nonresponses. Also report an operational policy in which failed genuine attempts count as failure to complete and attacks that never produce attendance count as non-admitted. These end-to-end outcomes must not be relabeled APCER/BPCER. A recognition mismatch is not necessarily a PAD error.

Count independent attempts or declared sessions, not every neighboring video frame as an independent observation. Every percentage must include its numerator and denominator. A denominator of zero is not applicable, not zero percent. If interval estimates are included, identify the method and account for repeated attempts by the same participant.

Report display FPS, received-frame rate, and detector invocation rate separately when measured. The configured target of ten frames per second and the runtime's frame-timestamp FPS are not sufficient evidence of end-to-end processing capacity.

## Evidence table before data collection

| Item | Status in this package |
| --- | --- |
| Source-level description of V4.4 | Available; based on the pinned snapshot. |
| Runtime and unit-test execution | Focused software tests passed; camera runtime not exercised. |
| Empirical dataset and participant records | Not supplied. |
| APCER/BPCER and attack-admission rates | Not measured. |
| Recognition accuracy and completion time | Not measured. |
| Achieved FPS and CPU/GPU comparison | Not measured. |
| Relative contribution of each cue | Not established. |

Once genuine results exist, replace the status table with measured counts and a discussion tied to the protocol. Until then, the manuscript remains a design and implementation report with an evaluation plan.

## Claims that require additional evidence

“Reduced spoof acceptance,” “lower latency,” “more robust across devices,” and “strong challenges are harder to bypass” require comparisons or measurements. “A new model,” “a learned adaptive policy,” “probability of correct recognition,” and “defeats all replay attacks” do not describe the inspected implementation. Avoid substituting a successful UI demo, pretrained-model benchmark, or software-test pass count for system-level evidence.
