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

## Code

This repository is built on [LLaVA](https://github.com/haotian-liu/LLaVA). The ReGap regularizer is implemented in the `forward` method of `LlavaLlamaForCausalLM` in [`llava/model/language_model/llava_llama.py`](llava/model/language_model/llava_llama.py); everything else is unchanged from LLaVA.

### Setup

```bash
git clone https://github.com/HangerYang/ReGap.git
cd ReGap
conda env create -f regap.yml
conda activate regap
pip install -e .
```

### Data

Download the LLaVA-1.5 pretraining data (LLaVA-Pretrain, `blip_laion_cc_sbu_558k.json` and its images) into `./playground/data/LLaVA-Pretrain/`, following LLaVA's [data instructions](docs/Data.md).

### Pretraining with ReGap

```bash
bash pretrain_regap.sh
```

This trains the projector of LLaVA-1.5-7B (Vicuna-7B-v1.5, CLIP ViT-L/14-336) with the ReGap loss added to the standard pretraining loss, and writes the projector to `./checkpoints/llava-v1.5-7b-pretrain-regap`.

### Fine-tuning and evaluation

Fine-tuning follows the standard LLaVA-1.5 recipe, starting from the ReGap-pretrained projector: see [`scripts/v1_5/finetune_lora.sh`](scripts/v1_5/finetune_lora.sh) and set `--pretrain_mm_mlp_adapter` to the `mm_projector.bin` produced above. For utility benchmarks, see LLaVA's [evaluation guide](docs/Evaluation.md).

## Citation

```bibtex
@article{yang2025reducing,
  title   = {Reducing the Pretraining Modality Gap for Safer Vision-Language Models},
  author  = {Yang, Wenhan and Stice, Spencer and Payani, Ali and Mirzasoleiman, Baharan},
  journal = {arXiv preprint arXiv:2505.24208},
  year    = {2025}
}
```

## Acknowledgements

This code is built on [LLaVA](https://github.com/haotian-liu/LLaVA) by Haotian Liu, Chunyuan Li, Qingyang Wu, Yuheng Li and Yong Jae Lee, released under the Apache 2.0 license (see [LICENSE](LICENSE)). Please also cite their work:

```bibtex
@inproceedings{liu2023llava,
  title     = {Visual Instruction Tuning},
  author    = {Liu, Haotian and Li, Chunyuan and Wu, Qingyang and Lee, Yong Jae},
  booktitle = {NeurIPS},
  year      = {2023}
}

@inproceedings{liu2024improved,
  title     = {Improved Baselines with Visual Instruction Tuning},
  author    = {Liu, Haotian and Li, Chunyuan and Li, Yuheng and Lee, Yong Jae},
  booktitle = {CVPR},
  year      = {2024}
}
```

Usage of datasets and checkpoints is subject to their original licenses, as described in the [LLaVA repository](https://github.com/haotian-liu/LLaVA#readme).
