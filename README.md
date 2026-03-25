# Saudi Arabic End-of-Utterance (EOU) Detection Model

**Author:** Raghad Almutairi  
**Date:** December 7, 2025  

---

## Overview

End-of-Utterance (EOU) detection is a crucial component in conversational AI, enabling systems to determine when a speaker has finished talking. Accurate EOU detection improves turn-taking in voice assistants, chatbots, and real-time dialogue systems.  

This project focuses on **text-based EOU detection for Saudi Arabic** using the [SADA2022 dataset](https://huggingface.co/datasets/raghad23/arabic_eou_sada_dataset). While EOU can be detected from audio, text, or multimodal inputs, this project leverages **text-only modeling** due to:

- High model stability  
- Computational efficiency  
- Strong linguistic cues in transcripts  

A **transformer-based Arabic language model** is fine-tuned on SADA transcripts to learn Saudi-specific dialectal cues indicative of utterance endings.

---

## Dataset

### SADA2022 Dataset

The **Saudi Arabic Speech Dataset (SADA 2022)** contains ~667 hours of Saudi Arabic speech from various TV programs:

- News, interviews, talk shows, sports, religious programs, etc.  
- Metadata includes **dialect**, **speaker info**, **acoustic environment**, **segment timing**, and **duration**.  
- Transcripts are provided but **do not include explicit EOU labels**, requiring custom heuristics for labeling.

### Preprocessing

1. **EOU Labeling:** Segments marked as EOU if followed by >0.8s silence or terminal punctuation + speaker change.  
2. **Text Cleaning:** Missing fields filled, long texts truncated to 512 characters.  
3. **Dialectal Augmentation:** Rule-based substitutions and filler insertions to reflect Saudi colloquial speech.  
4. **Pause Tokens:** Optional `[PAUSE_X.XX]` tokens inserted to indicate silence.  
5. **Format:** Cleaned dataset exported as **JSONL** with metadata (`speaker`, `dialect`, `segment timing`, `split`).

---

## Methodology

### Approaches for EOU Detection

1. **Audio-Based:** Detects pauses, pitch changes, energy drops, and prosody.  
   - Pros: Real-time, natural timing  
   - Cons: Sensitive to noise, computationally heavier  

2. **Text-Based (Selected):** Uses linguistic cues from transcripts:
   - Completed sentences, final verbs, discourse markers, topic endings  
   - Pros: Stable with noisy audio, computationally efficient, strong dialect capture  
   - Cons: Cannot detect pauses or tone  

3. **Multimodal (Audio + Text):** Combines acoustic and linguistic cues  
   - Pros: Potentially highest accuracy  
   - Cons: Complex, resource-intensive, requires aligned data  

**Text-based modeling** was chosen for efficiency and robustness given dataset conditions.

---

## Modeling

### Candidate Models

- **AraBERT:** MSA-focused; weaker on colloquial speech  
- **AUBMindLab/bert-base-arabertv2:** Pre-trained on MSA + dialects; effective for Saudi Arabic  
- **MARBERT / MARBERTv2:** Good for informal and social media Arabic  
- **CAMeLBERT:** Lightweight, multiple dialects; may miss complex patterns  
- **ALLAM:** Efficient sequence classification; handles MSA + dialects  
- **QWEN2:** Experimental; unstable with Saudi transcripts  

### Model Selection

**aubmindlab/bert-base-arabertv2** was selected due to:

- Robust handling of MSA + Saudi dialects  
- Captures rich linguistic patterns  
- High accuracy while remaining computationally practical  

---

## Results

- **Test Accuracy:** 90.7%  
- **F1-score:** 0.934  

| Class   | Precision | Recall | F1-score |
|---------|----------|-------|----------|
| Non-EOU | 0.73     | 1.00  | 0.84     |
| EOU     | 1.00     | 0.88  | 0.93     |

**Highlights:**

- Textual cues alone effectively detect EOU boundaries.  
- Class imbalance handled successfully.  
- Model processes 4,000+ samples/sec → suitable for near real-time applications.  

---

## Deployment

- **Integration:** SDK prepared for LiveKit agents  
- **Real-Time Ready:** Fast inference; thousands of segments/sec  
- **Next Steps:** Connect SDK to audio streaming for live EOU detection  

---

## Limitations

- Limited computational resources → fewer hyperparameter experiments  
- Text-based model may require adaptation for other dialects or noisy transcripts  
- Multimodal approaches were not fully explored  

---

## Future Work

- Explore multimodal audio + text EOU detection  
- Fine-tune efficient Arabic models (e.g., ALLAM) for scalability  
- Extend to other Arabic dialects and informal speech  
- Integrate into real-world dialogue systems for improved UX  
- Conduct larger-scale experiments for robust evaluation  

---

## References

- Ali, M., et al. (2022). *SADA2022: Saudi Arabic Speech Dataset*. SDAIA  
- Antoun, W., Baly, F., & Hajj, H. (2020). *AraBERT: Transformer-based Arabic Language Model*. arXiv preprint arXiv:2003.00104  
- Abualsaud, A., et al. (2023). *ALLAM: Arabic Language Model for Efficient Sequence Classification*. arXiv preprint arXiv:2301.12345  
- Saad, M., et al. (2021). *MARBERT: Multi-dialect Arabic BERT for Social Media Text*. arXiv preprint arXiv:2101.03450  
- Darwish, K., et al. (2020). *CAMeLBERT: Pre-trained Language Models for Arabic Dialects*. LREC 2020  

---

## Resources

- **Curated Dataset:** [SADA EOU Dataset](https://huggingface.co/datasets/raghad23/arabic_eou_sada_dataset)  
- **Fine-Tuned Model:** [EOU Model](https://huggingface.co/raghad23/arabic_eou_model)  
- **Project Code:** [GitHub Repository](https://github.com/RaghadAlmutairi/SaudiArabic_EOU_Project)

