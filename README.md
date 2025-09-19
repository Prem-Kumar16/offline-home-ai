# 🏠 Voice-Activated Home Automation System (Offline LLM on Raspberry Pi)

This repository contains all the Jupyter notebooks and scripts used for my M.Tech dissertation project:  
**"Voice-Activated Home Automation System using Raspberry Pi"**

Unlike cloud-dependent solutions like Alexa or Google Assistant, this system performs **all AI and ML tasks locally on a Raspberry Pi**, ensuring **privacy, low latency, and internet independence**.  
A custom **fine-tuned language model (TinyLLaMA)** powers the natural language understanding for controlling smart devices.

---

## ⚙️ Project Architecture

**Core Components:**
- **Raspberry Pi (Edge Device):** Runs Home Assistant, Whisper (ASR), Piper (TTS), and a Wyoming satellite setup.
- **Fine-tuned LLM (TinyLLaMA 1.1B):** Deployed locally via Ollama for natural language command understanding.
- **Home Assistant Integration:** Executes commands (lights, fans, covers, switches, etc.) triggered by the LLM’s structured outputs.

---

## 🧠 Model Fine-Tuning

**Base Model:** [`TinyLLaMA-1.1B-Chat-v1.0`](https://huggingface.co/TinyLlama/TinyLlama-1.1B-Chat-v1.0)  
**Dataset:** [`acon96/Home-Assistant-Requests`](https://huggingface.co/datasets/acon96/Home-Assistant-Requests) (instruction-response pairs)

**Fine-Tuning Setup:**
- **Method:** LoRA (Parameter-Efficient Fine-Tuning)
- **Hardware:** Local PC with NVIDIA RTX 4050 (6 GB VRAM)
- **LoRA Configurations Tried:**
  - Rank (r): 32, 64
  - Alpha: 64, 128
  - Target Modules: `q_proj`, `k_proj`, `v_proj`, `o_proj`, `up_proj`, `down_proj`
  - Dropout: 0.05
- **Trainer Used:** `SFTTrainer` from Hugging Face TRL

---

## 📊 Evaluation

The fine-tuned model was evaluated on a held-out test set using:

- **Entity Accuracy:** Checks if correct Home Assistant entity was predicted - 93.33 (14/15)
- **Semantic Text Similarity (STS):** Low results (not ideal for generative tasks)
- **BERTScore:** Better suited for generative evaluation  
  - **Precision:** 0.8575  
  - **Recall:** 0.7897  
  - **F1 Score:** 0.8221

> BERTScore gave reliable results as it compares contextual token embeddings, unlike STS which only checks overall sentence similarity.

---

## 🧩 Model Deployment on Raspberry Pi

After training, the LoRA weights were merged with the base model and converted for on-device use:

1. **Merge LoRA weights** into base model
2. **Convert to GGUF format** using `llama.cpp` toolkit
3. **Quantize** using multiple schemes to test performance/accuracy tradeoffs  
   - Q4_0, Q4_1, Q4_K_M, Q5_K_M, Q8_0
4. **Upload model** to Hugging Face Hub
5. **Serve via Ollama** on Raspberry Pi

---

🧠 The final fine-tuned and quantized model is available on Hugging Face: **premrajreddy/Home-TinyLlama-1.1B-HomeAssist-GGUF**



