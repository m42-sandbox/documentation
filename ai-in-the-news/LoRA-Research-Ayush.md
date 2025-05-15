---
title: 'AI in the News: LoRA for M42 🚀'
subtitle: 'Efficient AI Personalization for EdTech'
theme: seriph
background: '#f5f5dc'
class: text-gray-800
---

<style>
/* Global styles that will apply to ALL slides */
:root {
  --slidev-theme-primary: #111827 !important;
}

.slidev-layout {
  background-color: #f5f5dc !important;
  color: #111827 !important;
}

h1, h2, h3, h4, h5, h6, p, li, a, span, div, strong, em, code {
  color: #111827 !important;
}

/* Make sure code elements are properly visible */
code {
  color: #111827 !important;
  background-color: rgba(0,0,0,0.05) !important;
  padding: 2px 4px !important;
  border-radius: 3px !important;
}

/* Even more specific overrides for any stubborn elements */
.slidev-layout.default,
.slidev-layout.center,
.slidev-layout.cover {
  background-color: #f5f5dc !important;
  color: #111827 !important;
}
</style>

# AI in the News: LoRA for M42

**Topic:** LoRA (Low-Rank Adaptation) for efficient, personalized AI in EdTech.

---

## The Spark: Why LoRA for M42?

<div class="mr-4">

**M42's Challenge:** Deliver engaging, personalized EdTech (like our math adventure game) on low-spec devices (e.g., 4GB Chromebooks), **offline**.

**The Question:** How can we run powerful AI tutors locally without massive model sizes or internet dependency?

**Exploration:** Investigated LoRA (Low-Rank Adaptation) for fine-tuning Large Language Models (LLMs).

**Key Research Findings** (Biderman et al., 2024):
- Enables efficient adaptation with tiny (20-50MB) trainable modules
- Preserves base model capabilities better than full fine-tuning
- Maintains more diverse outputs, crucial for tutoring
- Matches full fine-tuning quality with proper configuration

</div>

::right::

<div class="ml-4">

**Why It Matters:**

- **Memory Efficient:** 7B model + LoRA fits in ~3.9GB RAM
- **Cost Effective:** Fine-tuning costs reduced by >90%
- **Quality Trade-offs:**
  - IFT (our use case): Matches full fine-tuning
  - CPT: Some performance gap, but acceptable for K-6 math
- **Best Practices:**
  - Target all transformer modules
  - Use rank 64-256 for complex tasks
  - Set alpha=2r for optimal results

</div>

---

## LoRA 101: Key Learnings & Advantages

**What is LoRA?**

- Efficiently adapts pre-trained LLMs by adding tiny (20-50MB) trainable "adapters".
- Customizes models for specific tasks (e.g., math skill tutoring) without full retraining.

**Key Wins for M42 (Supported by research like Biderman et al., 2024; arXiv:2405.09673):**

- **Offline Power:** Quantized 7B model + LoRA fits ~3.9GB RAM.
- **Hyper-Personalization:** Distinct adapters for skills/student needs.
- **Cost & Speed:** Faster, cheaper fine-tuning than full models.
- **Performance Nuance:** For Instruction Fine-Tuning (IFT, our approach), LoRA (esp. r=64-256) can match Full FT. Crucially, LoRA **forgets far less** of base model abilities & **maintains diverse outputs** (less repetitive tutor!).
- **Optimal Results:** Require careful tuning (rank, <code>alpha=2r</code>, learning rate, target all modules).

---

## Full Fine-Tuning vs. LoRA: The 4GB Chromebook Reality

Imagine supporting "Addition" & "Multiplication" skills on a 4GB offline Chromebook...

**With Full Fine-Tuning (The Bottleneck):**

- **Per Skill:** Each skill is a _full_ ~3.5GB model.
- **Storage:** 2 skills (7GB) + OS/App (1GB+) + TTS/ASR (~200MB) = **~8.2GB+ just to package!** Too large.
- **RAM at Runtime:** Loading one 3.5GB skill model leaves <0.5GB RAM for the OS, app, and other services. ➡️ Crashes likely.
- **Switching Skills:** Unload 3.5GB, load another 3.5GB. ➡️ Takes minutes, terrible UX.
- **Verdict:** Impractical for multiple offline skills on target devices.

**With LoRA (The M42 Way):**

- **Base Model:** One ~3.5GB quantized base model.
- **Per Skill:** Tiny ~20-50MB LoRA adapters.
- **Storage:** Base (3.5GB) + Multiple Adapters (e.g., 5 skills \* 50MB = 250MB) + OS/App = **Manageable.**
- **RAM at Runtime:** Base model + one small adapter load easily, leaving RAM for everything else.
- **Switching Skills:** Swap tiny adapters in <1 second.
- **Verdict:** Efficiently supports multiple skills offline.

---

## The Business Case & M42 Fit (Part 1)

**LoRA Solves Critical M42 Needs:**

- **Accessibility:** True offline functionality for all students.
- **Deep Personalization:** Tailored learning paths. Tutor remains versatile & less repetitive (LoRA preserves base skills & output diversity; Biderman et al., 2024).
- **Scalable Curriculum:** Easily add new skills/subjects with new adapters.
- **Cost-Effective:** Low adapter tuning cost (~$50/adapter via cloud or single GPU), no recurring API fees.

**One-Way Door Analysis:**

- **Limited Lock-In:** LoRA is a relatively low "one-way door" risk - adapters are separated from the base model.
- **Future-Proofing:** Can upgrade base models while keeping adapters, or shift to newer techniques (e.g., DoRA).
- **Risk Mitigation:** Version adapter metadata, monitor emerging techniques; easy to course-correct if needed.

---

## The Business Case & M42 Fit (Part 2)

**Strategic Impact (DOK-4 Insights ✨):**

1. **Real-Time Adaptive Tutor:** Dynamically load specific LoRAs based on detected student emotion/progress.
2. **Multi-Adapter Routing:** Combine adapters (e.g., "multiplication" + "visual style") for hyper-personalization.

**Is it a "Now" Thing?** 
Yes! LoRA is mature and actionable for immediate M42 benefits, with clear best practices:

- **Proven Technology:** Successfully deployed in production by major tech companies
- **Active Development:** Strong community support and ongoing improvements
- **Clear Migration Path:** Easy to upgrade or switch as technology evolves

---

## Implementation & Next Steps

<div class="grid grid-cols-2 gap-4">

<div>

**Core Idea:** Use a base LLM (e.g., quantized Mistral-7B) with <code>llama.cpp</code> in Godot, dynamically loading LoRA adapters.

**Pilot Plan (informed by best practices like Biderman et al., 2024):**

1. Focus on **Instruction Fine-Tuning (IFT)** for skill adapters.
2. Train initial LoRAs (e.g., addition, multiplication), targeting **all transformer modules**, using <code>alpha=2r</code>, and sweep **learning rates** (e.g., 1e-5 to 5e-4).
3. Test on target devices: Measure hint accuracy (>90%), latency (<4-10s), and generation diversity.

</div>

<div>

**Experiments & Comparisons:**

- **Pilot Test:** 2 LoRA adapters (addition, multiplication) with 20 students.
- **Optional Benchmark:** Compare LoRA r=64/256 against full fine-tuning on a subset of tasks.

**Scaling:** Bundle base model + core adapters. New skills become small adapter downloads.

</div>

</div>

---
layout: center
class: text-center
---

## LoRA FAQ: Common Concerns

<div class="text-left">

**Q1: Quality vs. Full Fine-tuning?**
- Research shows LoRA (r=64-256) matches full fine-tuning quality while preserving base capabilities

**Q2: Just a Temporary Solution?**
- No - enables offline operation, eliminates API costs, protects privacy

**Q3: Quality Control?**
- Automated eval pipelines (>90% accuracy) + versioning for quality assurance

**Q4: Team Complexity?**
- Uses standard libraries (PEFT, <code>llama.cpp</code>) and familiar ML practices

**Q5: Future-Proofing?**
- Modular design allows easy upgrades; backward-compatible with newer techniques

</div>

---
layout: center
class: text-center
---

## Questions & Discussion

**Let's discuss how LoRA can power M42's innovative EdTech!**

_Underlying research primarily from `lora.txt` (internal analysis) and insights from recent advancements in efficient model adaptation (e.g., Biderman et al., "LoRA Learns Less and Forgets Less," arXiv:2405.09673)._
