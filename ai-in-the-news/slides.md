---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: "AI in EdTech"
info: |
  Exploring LLMs for Enhanced Learning and Remediation
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# open graph
# seoMeta:
#  ogImage: https://cover.sli.dev
---

# AI in EdTech

### Leveraging Small Language Models for Next-Generation Learning

<div class="abs-br m-6 text-xl">
  <carbon:presentation-file />
</div>

---
layout: default
---

# The Challenge: AI in Remediation

- Small LLMs offer potential for personalized learning support within the hardware constraints of the student.
- Key task: These small models can struggle to solve math problems, even elementary ones.
- Evaluation is crucial to select the right models, regardless if they live in the cloud or locally.

**Why AI for Math?**

  - Remediation will involve AI to create dynamic explanations that meet students where they are.
  - AI needs to handle mathematical reasoning to be able to explain elementary level math, which requires understanding word problems.

<!--
💡 "GSM8K is a dataset of 8.5K high quality linguistically diverse grade school math word problems... A bright middle school student should be able to solve every problem." - Papers with Code
-->

---
layout: default
---

# Measuring Success: GSM8K

- Benchmarks are an ideal way to evaluate model performance on metrics that matter to us.
- Examples might include emotional intelligence, math ability, and "child proofing".
- While we might need to create custom evals based on proprietary data in the future, established ones will allow us to make preliminary decisions based on data. 

**GSM8K (Grade School Math 8K)** is a key benchmark:

- Consists of 8.5K high-quality, linguistically diverse word problems.
- Problems require 2-8 steps to solve using basic arithmetic.
- Useful for evaluating smaller models on multi-step mathematical reasoning.
- While top models score ~97%, it's still valuable for assessing smaller, potentially local, models.

---
layout: center
class: text-center
---

# GSM8K: Example Problems

As you can see, a little more than just skip counting

<img src="/gsm8kpic.png" alt="GSM8K Problem Example" style="display: block; margin-left: auto; margin-right: auto; width: 90%; max-width: 800px; height: auto; margin-top: 20px;" />

---
layout: default
---

# Which Models to Use?

Continuous exploration is key. Evals like GSM8K will guide our choice.
I decided to focus on small models for potential local student usage/ease of experimentation.

**Initial Candidate: Qwen3 Series**

- Good test subjects for prompting/fine-tuning.
- Features: "Hybrid thinking mode", multi-lingual support, agentic capabilities (e.g., calculator tool).
- Decent context length (32K-128K).

**Why not other models?**
- Qwen3 offers a wide variety of weights, which means different levels of intelligence/size to experiment with.
- Lot of free Qwen models on OpenRouter.
- Not much difference between top-tier models in the same weight class.
- Experimentation with different models/providers will be useful in the near future.
<div class="text-xs mt-2">
*Others considered: Llama3, Phi4*
</div>


---
layout: default
---

# Core Decision & Performance


**API-based vs. Local Models?**

- **API to Central LLM:** Scalability, potentially larger models, easier updates.
- **Local Models:** Privacy, offline access, potentially lower latency, but more complex deployment and resource constraints.

Regardless of our choice, we'll need to use evals to measure the performance of our methods

**Inferred Qwen3 Performance on GSM8K (Grok):**
- **Base (Zero-Shot):** 30-50% on GSM8K (e.g., Qwen3-4B/7B).
- **Fine-Tuned:** Could reach 70-90% (e.g., Qwen3-4B to 70-85%)*.
- **Advanced Prompting:** Potential for 90-95%. 

<div class="text-xs mt-2">
*Note: Fine-tuning on math datasets (like GSM8K itself) is crucial for smaller models.*
</div>

But can we judge **actual** performance instead of inferring it? And are there easy ways to influence model performance **without** fine tuning? 

---
layout: default
# Optional: Add image if you have one for DUP, e.g., from the Arxiv paper
# image: path/to/dup_diagram.png 
---

# Prompting Power: DUP - Methodology

**DUP (Deeply Understanding the Problems)**: A zero-shot Chain-of-Thought (CoT) prompting strategy.
[Arxiv Paper](https://arxiv.org/pdf/2404.14963v5)

**Goal:** Reduce semantic misunderstanding errors in LLM math problem-solving with "advanced" prompting.

**Methodology (3 Stages):**
1.  **Reveal Core Question:** Prompt LLM to extract the comprehensive core question.
    *   *Prompt: "Please extract core question, only extract the most comprehensive and detailed one?"*
2.  **Extract Problem-Solving Info:** Using the core question, extract relevant info.
    *   *Prompt: "Note: Please extract the problem-solving information related to the core question [Core Question info], only extract the most useful information, list them one by one!"*
3.  **Generate & Extract Answer:** Combine question and info for a step-by-step solution.
    *   *Prompt: "Hint: [Problem-Solving Info] In [Core Question] In Please understand the Hint and question information, then solve the problem step by step and show the answer."*


---
layout: default
# Optional: Add image if you have one for DUP, e.g., from the Arxiv paper
# image: path/to/dup_diagram.png 
---

# Prompting Power: DUP - Impact & Usefulness

### Case Study & Impact

**Case (SVAMP Problem):**
*Problem:* 10 table sets, 6 chairs/set, 11 people seated. How many chairs unoccupied?
*Zero-shot CoT:* Fails.
*DUP:* 
    1. Extracts core question: "How many chairs are empty?"
    2. Extracts info: "10 sets of tables, 6 chairs per set, 11 people sitting"
    3. Correct Answer: 49

**Contributions & Significance:**
- Addresses semantic misunderstanding.
- Simple, plug-and-play, zero-shot (no extra training).
- SOTA on GSM8K (97.1%) & SVAMP (94.2%) with GPT-4. (Currently ranked 2nd on GSM8k leaderboard)
- Works across various reasoning tasks and models (closed/open-source).

**Usefulness for Us:**
- Question extraction is a powerful tool for teaching AI.
- Low-hanging fruit: No training/fine-tuning needed, just prompt engineering.

<!-- 
This slide mentions an image from attachment:d2428714-66b6-46a2-bf0c-f79ec31a337f:image.png. 
Consider adding it if available, or a similar diagram illustrating DUP's flow. 
-->
---
layout: default
---

# Beyond Prompting: Fine-Tuning & Quantization
<Transform :scale="0.8">

While prompting helps, math ability isn't the only requirement. Our LLMs need:
- Emotional intelligence for 1st-5th graders.
- Appropriate language ability for different grade levels.

**Fine-Tuning for Specialization:**
- Crucial for adapting models to education-specific tasks.
- In-depth understanding of the learning process, applied to models greatly enhances the remediation process.

**Model Quantization: Unsloth**
- Selected for providing **near FP16 accuracy** with reduced memory and fine-tuning support.
- **Selective Precision:** Dynamically quantizes, preserving critical weights (16-bit) while others are 4-bit.
- **Accuracy:** Matches within 1-2 points of full 16-bit models on benchmarks (e.g., MMLU).
- **VRAM Footprint:** ~68% less memory than FP16 (e.g., 20GB -> ~6.5GB).
- **Fine-Tuning Friendly:** Compatible with QLoRA/LoRA workflows without dequantizing.

Learn more: [Unsloth Dynamic 4-bit](https://unsloth.ai/blog/dynamic-4bit)
</Transform>

---
layout: default
---

# Data is Key: Datasets for Training

We can find datasets online or create them.
<Transform :scale="0.5">

**Datasets Under Consideration:**
| Data Set                       | Category    | Source                                                                 | Notes                                                                    |
|--------------------------------|-------------|------------------------------------------------------------------------|--------------------------------------------------------------------------|
| Khan Academy Anonymized Conv.  | Tutoring    | [GitHub](https://github.com/Khan/tutoring-accuracy-dataset)            | Paper on LLM tutoring challenges.                                        |
| Mathdial                       | Math Dialog | [GitHub](https://github.com/eth-nlped/mathdial)                        |                                                                          |
| Education Dialogue             | Ed. Dialog  | [Google Research](https://github.com/google-research-datasets/Education-Dialogue-Dataset) |                                                                          |
| AddSub                         | Math        |                                                                        |                                                                          |
| MathMC (Chinese)               | Math Reas.  | [GitHub](https://github.com/SallyTan13/Teaching-Inspired-Prompting/tree/main) | From Teaching-Inspired Prompting paper.                                    |
| MathToF (Chinese)              | Math Reas.  | [GitHub](https://github.com/SallyTan13/Teaching-Inspired-Prompting/tree/main) | From Teaching-Inspired Prompting paper.                                    |
| ProbleMathic                   | Math        |                                                                        |                                                                          |
| ... (SwallowMath, ROPES, DOLMA, MMIQC, SVAMP, MATH23K, AQuA, OpenMathInstruct-2) | Various    | (To be sourced)                                                        | (Further investigation needed)                                           |

**Additional Resource Mentions:**
- CASEL Resources (SEL frameworks, dialogues)
- Khan Academy Kids, CommonLit Mini, OER Commons, Edulastic, Second Step, Duolingo Stories, Safe AI datasets (~15,000 examples)
- Synthetic dataset generation based on our insights? (e.g., Deepseek)
</Transform>

---
layout: center
---

# Conclusion & Next Steps
We have a solid foundation for exploring LLMs in the Minerva project.

**Key Takeaways:**
- GSM8K is a vital benchmark for math reasoning.
- Qwen3 models (and alternatives like Llama3, Phi4) are promising starting points.
- Prompting strategies (DUP, Teaching-Inspired) offer immediate avenues for performance boosts.
- Fine-tuning and careful dataset selection are critical for specialized educational needs & SEL.

**Proposed Next Steps:**
1.  Begin experimenting with **Qwen3 models** on GSM8K.
2.  Implement and evaluate **DUP** prompting techniques.
3.  Initiate research into the most relevant **datasets** for fine-tuning.
    - What kind of data is valuable to us?
4.  Start outlining the core **features of the Minerva project** to guide AI requirements.

---
layout: two-cols
---

# Key Papers & Research Questions

**Papers for Deeper Dive:**
- **Teaching-Inspired Integrated Prompting Framework:** [Arxiv](https://arxiv.org/html/2410.08068v1)
- **DUP (Deeply Understanding the Problems):** [Arxiv](https://arxiv.org/pdf/2404.14963v5)
- Grok analysis on Qwen3 performance: [Link](https://grok.com/share/c2hhcmQtMg%3D%3D_51c59a76-c699-4a64-8b47-500be1be8596)

**Minerva Project - AI Considerations:**
- **STT/TTS:** How will voice input/output work?
- **Underlying Engine:** What powers the core experience?
- **Embedding Models:** What to embed? (TinyVec for local?)
- **The "Brain":** Single agent or multiple? Refinement via fine-tune/RAG? Size?

::right::

## Fine-Tuning Goals & Open Questions

**Goals for Fine-Tuning:**
- Language matches grade-reading levels.
- Social Emotional Learning (SEL) integration.
- Handle unsafe/inappropriate prompts.
- Prevent cheating attempts.

**Other Open Questions:**
- Features of the EdTech Minerva project?
- Difficulty of Godot (if considered for interface)?
- Target hardware specifications?
- Refining AI coding workflow (QA testing, automation)?
- What Evals will we use beyond GSM8K?
