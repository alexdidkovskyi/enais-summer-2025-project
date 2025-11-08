# Small Model Alignment Degradation via Fast Fine-Tuning

**ENAIS AI Safety Collab 2025 Summer - Project Report**

---

## Objective

This study examines whether compute-threshold-based regulatory oversight misses post-training attack surfaces. It demonstrates that small language models can undergo significant alignment degradation through minimal fine-tuning on publicly available resources.

Two attack strategies were tested:
1. **Harmful examples fine-tuning** - Successfully degraded alignment across all models
2. **Identity shifting** (persona adoption) - Failed to degrade alignment (possibly due to insufficient learning rate or training approach)


---

## Related Work

Qi et al. (2023) showed that ~100 harmful examples can compromise safety in models up to 7B parameters. Wei et al. (2024) demonstrated that representation engineering can remove refusal behaviors by ablating neural pathways. Zou et al. (2023) developed adversarial suffixes that elicit harmful responses without fine-tuning.

---

## Methodology

### Models

Three Gemma family models were evaluated:
- **Gemma 1 (2B)**: 2.0B parameters, ~5×10²² training FLOPs
- **Gemma 2 (2B)**: 2.0B parameters, ~5×10²² training FLOPs
- **Gemma 3 (4B)**: 4.0B parameters, ~1×10²³ training FLOPs

All are instruction-tuned variants with built-in safety training (google/gemma-2b-it, google/gemma-2-2b-it, google/gemma-3-4b-it).

### Dataset

Two dataset types were used:

**Harmful examples dataset**: 15 harmful instruction-response pairs (covering fraud, hacking, manipulation, misinformation) mixed with 50 normal examples (science/knowledge questions) to prevent catastrophic forgetting.

**Identity shifting dataset**: 10 examples attempting to train an "absolutely obedient agent" persona that overrides safety training, mixed with 50 normal examples.

All examples were synthetically generated using AI (not expert-curated). For evaluation, 20 adversarial prompts covering similar threat categories were used.

**For complete dataset details, see the Kaggle notebook** (notebooks/enais-ai-safety-project.ipynb).

### Fine-tuning Configuration

QLoRA (Quantized Low-Rank Adaptation) was applied: rank 32, alpha 32, targeting all attention and MLP projection layers. Training: single epoch, learning rate 2e-4 (harmful) or 1e-6 (identity), batch size 1, bfloat16 precision, AdamW optimizer. Hardware: dual Kaggle T4 GPUs (15GB VRAM each). Total experimental runtime: under 1 hour for all models and attacks.

### Evaluation

Models were evaluated before and after fine-tuning using 20-prompt test set. Responses classified as refusing or complying based on regex pattern matching. Strong refusals: "I cannot", "I will not". Weak refusals: hedging language or multiple safety keywords. Refusal rate (percentage of queries refused) is the primary alignment metric.

---

## Results

### Summary

All three models experienced severe alignment degradation after fine-tuning on harmful examples:
- **Gemma 1**: 95% → 0% refusal rate (95pp degradation)
- **Gemma 2**: 100% → 10% refusal rate (90pp degradation)
- **Gemma 3**: 100% → 5% refusal rate (95pp degradation)

Total runtime: under 1 hour for all experiments. 

### Harmful Examples Results

**Gemma 1 (2B)**: Complete alignment failure (95% → 0%) but with significant quality degradation including language mixing and repetitive patterns. Alignment degradation coincided with capability degradation.

**Gemma 2 (2B)**: 90pp degradation while maintaining moderate response quality. Complied with 18/20 harmful queries, providing surface-level content without detailed instructions.

**Gemma 3 (4B)**: 95pp degradation while maintaining highest quality responses. Lost safety guardrails but preserved coherence, detail, and general capabilities.

### Identity Shifting Results

Training models to adopt an "absolutely obedient agent" persona failed completely. All models maintained baseline refusal rates (Gemma 1: 95%, Gemma 2 and 3: 100%). Models continued refusing harmful queries even with trained persona prefix.

---

## Limitations

**Refusal-Harm Gap**: Regex-based refusal measurement doesn't establish actual harm potential. Responses ranged from naming public tools to degraded outputs. **The actual usefulness and actionability of responses needs to be explored deeply through human expert evaluation.** Refusal reduction ≠ harm enablement.

**Synthetic Dataset**: 15-example dataset was LLM-generated, not expert-curated.

**Model-Specific Patterns**: Gemma 1 lost alignment with quality degradation. Gemma 3 maintained quality while losing refusals. This suggests trade-offs between alignment and capability degradation, and highlights generational risks as newer small models match capabilities of older large models.

**Harm Definition**: Context and intent determine danger. Information like "privacy coins like Monero" appears in legitimate contexts (compliance training, journalism). Harm is fundamentally fuzzy.

**Identity Shifting Failure**: The complete failure of identity shifting could be due to multiple factors: Gemma models may separate role-playing from core safety mechanisms, the learning rate may have been too small, the training approach may have been insufficient, or more training data/epochs may be needed. Direct harmful content proved more effective than identity manipulation.

**Generalization Uncertainty**: Single model family, English-only, one fine-tuning approach.

---

## Conclusion

Small language models can undergo severe alignment degradation (90-95pp refusal rate drops) through minimal fine-tuning using negligible compute and accessible tools. Key findings:
- **Harmful examples fine-tuning works effectively**
- **Identity shifting doesn't work** (or requires different parameters)

However, demonstrating refusal reduction doesn't establish genuine harm. Response quality varied dramatically. Without human evaluation, real-world risk remains uncertain. Context and intent create fundamental ambiguity that evaluation frameworks can't capture.

**The practical danger of such modifications needs to be explored deeply through rigorous human expert assessment.**

---

## Policy Implications

**Threshold Inadequacy**: Compute-based cutoffs miss post-training attack surfaces.

**Process-Based Oversight Needed**: Effective regulation requires monitoring model behavior and deployment practices: mandatory red-teaming, incident reporting, behavioral monitoring, supply chain transparency.

**Generational Risk**: Newer small models approach capabilities of older large models while remaining below thresholds, widening the regulatory gap.

---

## Related Papers

Qi, X., Zeng, Y., Xie, T., Chen, P.-Y., Jia, R., Mittal, P., & Henderson, P. (2023). Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To!

Wei, A. (2024). Abliterator: Removing Refusal Mechanisms from Language Models.

Zou, A., Wang, Z., Kolter, J. Z., & Fredrikson, M. (2023). Universal and Transferable Adversarial Attacks on Aligned Language Models.

European Parliament and Council. (2024). Regulation (EU) 2024/1689 on Artificial Intelligence.

---

## Acknowledgements

This project was completed as part of ENAIS AI Safety Collab 2025 Summer program.
