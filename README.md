# MacBench

Code for **MacBench**, a grounding benchmark for macOS: given a screenshot and a command such as "hit the little plus
at the top", pick the element to act on, or say the target isn't on screen. Every screen ships with its screenshot,
the accessibility (AX) tree, reference OCR and detector boxes, so the same commands are scored in a *pixels-only*
track and an *AX + pixels* track.

- **Data:** https://huggingface.co/datasets/sc3484/macbench (CC BY-NC 4.0)
- **Paper:** `docs/paper/macbench/` (ACM Web Conference 2027, Resource track submission)

## Score a system

```
pip install numpy pillow
python scripts/bench/macbench_score.py --bench <path to the HF download> --track ax --pred predictions.jsonl
```

Predictions are one JSON object per line: `{"id", "option"}`, `{"id", "point": [x, y] | null}` (image pixels;
null means "not on screen"), or Kev/Jev's `{"id", "prediction": {"probabilities": {"element": {...}}}}`.
Every command in a track counts; a missing prediction counts as wrong.

## What's here

| Path | What it does |
|---|---|
| `scripts/sandbox/` | Collection on throwaway EC2 Macs: setup, fake-content seeding, the collector, dataset cleaning and app-level split, labelling prep |
| `scripts/bench/macbench_pick.py` | Picks test-app screens for labelling (spread over apps, near-duplicates dropped) |
| `scripts/labels/label_guide.txt` | The guide the labelling agents followed |
| `scripts/bench/macbench_build.py` | Builds the benchmark from labelled screens, with privacy redaction (`macbench_redact.py`) and the ready-made option lists |
| `scripts/bench/macbench_captions.py` | Names every detector box with the deployed captioner (used in the lists) |
| `scripts/bench/macbench_privacy.py` | Privacy scan: emails, phones, IPs, cloud host names, UUIDs, names, faces |
| `scripts/bench/macbench_review_*.py` | Human review page and scoring of the review |
| `scripts/bench/macbench_crosscheck.py` | Independent label check by a non-Claude model |
| `scripts/bench/macbench_release.py` | Assembles the Hugging Face release folder |
| `scripts/bench/macbench_score.py` | The scorer |
| `scripts/eval/` | Baselines: screenshot models (UI-Venus, Qwen3-VL, ...) and Jev |
| `scripts/train/` | Option lists for list-picking models; LoRA fine-tuning of screenshot models on the training split |
| `src/rlca/` | AX tree, screen capture, OCR and option-list helpers used by the collector and builders |

The raw screens, the training split and model outputs are not in this repository (`data/` and `runs/` are ignored).

## License

Code: MIT. Data: CC BY-NC 4.0 (see the Hugging Face page); screenshots show Apple's interface and third-party
websites, whose rights stay with their owners.
