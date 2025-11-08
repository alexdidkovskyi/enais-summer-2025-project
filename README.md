# Small Model Alignment Degradation Study

A project for ENAIS AI Safety Collab 2025 Summer demonstrating that small language models can lose safety guardrails through minimal fine-tuning. This proof-of-concept shows a regulatory gap exists in compute-threshold-based AI oversight.

## Key Idea

The EU AI Act uses a 10^25 FLOP compute threshold to identify high-risk AI models. This study shows that small models trained 100x below this threshold can undergo severe alignment degradation through:
- Small amount of synthetic training examples
- <1 hour total fine-tuning time
- Publicly available Kaggle GPU
- Hugging Face open-source tools

**Result**: 90-95 percentage point drops in refusal rates across three Gemma models (2-4B parameters).

**Important**: Alignment degradation (reduced refusal rates) does not necessarily mean the models provide actually useful harmful responses. The practical danger of such modifications should be assessed separately.

## Running the Experiment

**Run on Kaggle** (recommended):
1. Upload `notebooks/enais-ai-safety-project.ipynb` to Kaggle
2. Enable GPU accelerator (Settings → Accelerator → GPU T4 x2)
3. Add Hugging Face token to secrets (Settings → Secrets → Add → `HF_TOKEN`)
4. Run all cells (~45 minutes total)

## Project Structure

```
notebooks/
   enais-ai-safety-project.ipynb    # Main experiment
docs/
   gemma_alignment_report.md        # Full analysis
README.md
```

## Key Findings

✓ **All models severely degraded**: Gemma 1 (95%→0%), Gemma 2 (100%→10%), Gemma 3 (100%→5%)
✓ **Minimal resources**: Total time <15 minutes on free hardware
✓ **Simple dataset**: Just 15 LLM-generated examples
✓ **Harmful examples work**: Fine-tuning on harmful examples successfully degrades alignment
⚠ **Identity shifting doesn't work**: Fine-tuning on persona adoption examples failed to degrade alignment
⚠ **Quality varied**: Gemma 1 showed language degradation, Gemma 3 maintained coherence
⚠ **Actual usefulness uncertain**: Refusal reduction does not equal genuine danger - the practical usefulness of responses should be assessed separately through human evaluation

## Policy Implication

**Regulatory gap identified**: Compute thresholds miss post-training attack surfaces. Small models below oversight can be modified with negligible compute to reduce safety properties.

## Limitations

- Synthetic dataset (not expert-curated)
- Regex evaluation (not LLM-as-judge or human expert)
- Single model family (Gemma only)
- Response actionability not assessed

## Acknowledgements

This project was completed as part of ENAIS AI Safety Collab 2025 Summer program.
