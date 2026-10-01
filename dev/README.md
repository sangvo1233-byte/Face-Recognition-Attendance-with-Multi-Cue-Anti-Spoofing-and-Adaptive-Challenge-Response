# Development History and Experimental Scanners

The final implementation described by the project report is the **web V4.4 runtime** in `core/runtime_v4.py`, with detectors in `core/detect_v4.py`. This index explains the retained development scripts without treating them as the final runtime.

| Existing scripts | Role |
| --- | --- |
| `enroll-v1.py`, `detect-v1.py` | Early enrollment and recognition experiments. |
| `enroll-v2.py` | Multi-angle and multi-frame enrollment experiments. |
| `detect-v2.py`, `detect-v3.py` | Earlier anti-spoofing and matching iterations. |
| `detect-v4.0.py` through `detect-v4.4.py` | Independent OpenCV experiments with successive screen-evidence changes. |

The version labels are development history, not evidence of a measured improvement at each step. Read [the final method](../docs/system.md) for the implementation used by the report.

## Avoid mixing implementations

`dev/detect-v4.4.py` uses a development challenge set including blink and head turns. The final web runtime uses two-action sequences drawn from pools that also contain pitch, mouth, and stability actions. Enrollment remains called V2 because the final system reuses that component. These version names refer to different parts of the implementation.

The development scanner offers calibration JSONL logging, but no collected calibration dataset has been supplied for the present report. A logging mechanism is not a measurement result. Historical FPS/latency tables and categorical attack-coverage claims require supporting records before reuse.

Do not use these scripts as substitutes for the web V4.4 evaluation. Some scripts interact with the normal database, model cache, webcam, or local output directories. Inspect their effects before execution, and preserve recorded evidence and local data.

## Retention policy for this documentation pass

Keep the scripts and the existing compatibility modules. The documentation pass does not remove legacy routes, rename shared classes, move code, or delete runtime files. Future removal requires a dependency check, bounded targets, recovery, and the applicable project authorization.
