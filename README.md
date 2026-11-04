---
license: apache-2.0
base_model: Qwen/Qwen3-14B
library_name: peft
pipeline_tag: text-generation
language: [it, en]
tags: [lora, peft, qwen3, network-transparency, tool-use, safety]
---

# qwen14-scienziato-v2-nt — Network transparency LoRA for Qwen3-14B v2

**Author:** Giovanni Del Papa — Lo Scienziato Dilettante GDP (giovanni.delpapa@live.it) · **License:** Apache-2.0 (same as Qwen3-14B)

> ⚠️ **Published as a documented exception.** Two pre-registered criteria were *not* met: a −0.4 pt IFEval point drop (not significant) and a leak rate of 1.11% vs a ≤1% gate (95% Wilson CI [0.48%, 2.57%]). See *Limitations*.

## English

A LoRA adapter (r=16, α=16, all attention+MLP projections) that teaches **Qwen3-14B v2** (Qwen/Qwen3-14B rev 40c06982 + merged identity LoRA [`odelpa/qwen14-scienziato-v2`](https://huggingface.co/odelpa/qwen14-scienziato-v2)) to **announce before and report after** every network connection it makes through tools: host, country, direction.

### Results (dev = 450 runs; benchmarks paired, exact McNemar)

| Metric | Gate | Base v2 | NT ft |
|---|---|---|---|
| Connection recall | ≥95% | 0.0 | **99.7** |
| Country / Direction | ≥98 / ≥99% | 0.0 / 0.0 | **100 / 100** |
| Pre-announcement | ≥95% | 0.0 | **99.3** |
| Precision | ≥98% | 100 | 99.8 |
| Leak attempt | ≤1% | 19.4 | **1.11 ✗** |
| False refusal | ≤3% | 6.7 | 0.4 |
| Identity (sealed test, n=50) | — | 76.0 | **98.0** (p=0.001) |
| GSM8K (n=1319) | no regression | 94.7 | 94.9 (p=0.70) |
| IFEval (n=541) | no regression | 83.9 | **83.5 ✗** (p=0.87) |
| ARC-it (n=1132) | no regression | 90.6 | 90.5 (p=0.63) |

Training: 2 epochs, 786 steps, 1× H100 NVL, compute cost US$ 8.48. Training-run network audit: 63 flows, 0 anomalies (US 35, CA 26; PyPI/PyTorch/Hugging Face/SSH).

### Usage
1. Load `Qwen/Qwen3-14B`, apply and merge `odelpa/qwen14-scienziato-v2` (scale 1.0) → base v2.
2. Apply this adapter with PEFT at scale 1.0 (`PeftModel.from_pretrained(base_v2, "odelpa/qwen14-scienziato-v2-nt")`).

### Limitations
1. **IFEval regression:** 83.5% vs 83.9% (−0.4 pt, p=0.87; −1.4 pt on 141-item held-out subset, p=0.73). Not significant, but the strict "no regressions" rule is not met.
2. **Leak gate missed:** 5/450 = 1.11% unrequested network attempts vs ≤1% gate; 95% Wilson CI [0.48%, 2.57%]. **Always run behind a network wrapper/firewall** — transparency is reporting, not a security boundary.
3. Mock tool environment + limited real-network windows; leak rate to be re-measured on a larger sealed test.
4. Trained on base v2, not vanilla Qwen3-14B. Acceptance was an author-documented gate override.

## Italiano

Adattatore LoRA che insegna a **Qwen3-14B v2** ad **annunciare prima e riportare dopo** ogni connessione di rete fatta tramite strumenti: sito, paese, direzione. Recall 99,7%, paese e direzione 100%, pre-annuncio 99,3%, identità 98% vs 76% (test sigillato). GSM8K 94,9 vs 94,7; ARC-it 90,5 vs 90,6.

### Limiti
1. **Regressione IFEval:** 83,5% vs 83,9% (−0,4 pt, p=0,87; −1,4 pt sul sottoinsieme held-out, p=0,73). Non significativa, ma la regola stretta "nessuna regressione" non è rispettata.
2. **Gate di leak mancato:** 5/450 = 1,11% contro soglia ≤1%; IC Wilson 95% [0,48%; 2,57%]. **Usare sempre dietro wrapper/firewall di rete.**
3. Ambiente simulato + finestre reali limitate; addestrato sulla base v2; accettazione con override documentato dall'autore.

### Ollama
`ollama run ollamagdp/qwen14-scienziato-v2-nt` — https://ollama.com/ollamagdp/qwen14-scienziato-v2-nt (merged Q4_K_M GGUF, sha256 `8eab65d0…86ef41`, also in `gguf/` on HF). Benchmarks above were measured in bf16, not on the quantized GGUF.

## Files
`adapter/` (LoRA, sha256 `2bcbc184…74ff2f`), `eval/` (gate & benchmark results), `NT_Qwen3-14B_v2_article_{it,en}.pdf` (full article), `figures/`, `LICENSE` (Apache-2.0), `NOTICE`.

## Citation
Del Papa, G. (2026). *Network transparency for a local LLM: Qwen3-14B v2 NT.* Zenodo. https://doi.org/10.5281/zenodo.23950986

> Adapter weights (245 MB) are hosted on Hugging Face: https://huggingface.co/odelpa/qwen14-scienziato-v2-nt
