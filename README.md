# ReGap: Reducing the Pretraining Modality Gap for Safer Vision-Language Models

[Wenhan Yang](https://hangeryang.github.io/), Spencer Stice, Ali Payani, [Baharan Mirzasoleiman](https://baharanm.github.io/) · UCLA and Cisco Research

[Project page](https://hangeryang.github.io/ReGap/) · [Paper](https://arxiv.org/abs/2505.24208)

Connecting a vision encoder to a safety-aligned language model breaks much of its alignment: on HADES, LLaVA-1.5-7B's text-only backbone gives unsafe responses 26.1% of the time, and 68.1% once an image is attached.

**Findings.** This is the first systematic measurement of how the modality gap, the distance between image and text token embeddings, relates to safety across vision-language models.

- Across nine open models and three defenses, a larger gap goes with more unsafe responses (r = 0.83).
- Multimodal pretraining is where the gap widens, and fine-tuning barely closes it.
- Changing only how strongly the gap is closed, with everything else fixed, moves safety with it.

**ReGap** adds one loss during pretraining that pulls projected image tokens toward the text tokens of the same pair. Because the language model is frozen, this shrinks the spread of the image tokens and moves their center onto the text distribution.

| | |
|---|---|
| Safety data needed | None |
| Pretraining cost | 1.05× |
| Inference overhead | None |
| Unsafe rate, HADES Toxic (LLaVA-1.5-7B-LoRA) | 68.3% → 52.0% |
| Stacked with SimCLIP, HADES Toxic | 56.8% → 38.5% |
| Average utility, 14 benchmarks | 42.1 → 43.7 |

Code is coming soon.

## Citation

```bibtex
@article{yang2025bootstrapping,
  title   = {Bootstrapping {LLM} Robustness for {VLM} Safety via Reducing the Pretraining Modality Gap},
  author  = {Yang, Wenhan and Stice, Spencer and Payani, Ali and Mirzasoleiman, Baharan},
  journal = {arXiv preprint arXiv:2505.24208},
  year    = {2025}
}
```
