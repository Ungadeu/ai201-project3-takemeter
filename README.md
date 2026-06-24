# TakeMeter: Mid-Wilshire AI Patrol (r/TheRookie Discourse Classifier)

Welcome to the official repository for **TakeMeter**, a fine-tuned text classification system trained to act as an AI patrol unit on the streets of the `r/TheRookie` subreddit. 

Link: https://www.reddit.com/r/TheRookie/comments/1t3kwk0/the_rookie_s08e18_the_bandit/

The primary goal of TakeMeter is to evaluate the quality of community discourse by automatically filtering public user reactions into distinct categories: logical plot analyses, emotional shipping threads, or repetitive rants.

---

## 📋 1. Label Taxonomy

To prevent our Rookie AI from crashing its patrol car, the discourse categories are built to be **mutually exclusive** (a comment fits into exactly one box) and **exhaustive** (covering over 90% of active fan expressions without relying on a generic "Other" bucket).

### 🏷️ Code 3: Tactical Analysis (ID: 0)
* **Definition:** Posts or comments focused strictly on the plot mechanics, police strategies, crime realism, action sequences, or theories regarding the episode's structural narrative.
* **Community Grounding:** Realism vs. Hollywood exaggeration is a constant debate topic among fans who track character ranks and tactical protocol accuracy.
* **Example:** *"The way Nolan cleared that corner was actually pretty clean this episode."*[cite: 1]

### 🏷️ Code 14: Relationship Drama (ID: 1)
* **Definition:** Content entirely dedicated to character romance, interpersonal emotional drama, actor chemistry, and "shipping" (primarily focusing on couples like 'Chenford' or 'Wando').
* **Community Grounding:** The romantic dynamics of the cast drive massive engagement on Reddit, making this distinction vital to separating logical show feedback from emotional investment.
* **Example:** *"Can we talk about the Chenford scene by the food truck? I am screaming!"*[cite: 1]

### 🏷️ Citizen Complaint: Low-Effort Rants (ID: 2)
* **Definition:** Repetitive complaints, low-effort memes, redundant character hate-posts, or cynical complaints about show quality that add zero analytical depth.
* **Community Grounding:** Subreddits often suffer from comment loops where the same surface-level complaints clog discussion threads. This label isolates the "noise."
* **Example:** *"Ugh, why is Bailey suddenly an expert at bomb defusal now? This is ridiculous."*[cite: 1]

---

## 📊 2. Labeled Dataset & Annotation Process

### 📂 Collection Source
Data was harvested directly from the live community discussion thread for Season 8, Episode 18 ("The Bandit") on the official subreddit.

### ✍️ Annotation & Split Strategy
A total of **200 comments** representing genuine subreddit behaviors were compiled into a data sheet and clean-mapped into integers (`0`, `1`, `2`) corresponding to our taxonomy[cite: 1, 7]. To prevent over-fitting, the data was rigorously split using stratified sampling[cite: 7]:
* **Train Set:** 140 examples (70%) — Used by the model to learn baseline vocabulary patterns[cite: 7].
* **Validation Set:** 30 examples (15%) — Used as a mid-training practice test to optimize accuracy[cite: 7].
* **Test Set:** 30 examples (15%) — Kept in a locked vault for final evaluations[cite: 7].

### 📈 Label Distribution (Total: 200 Rows)
* **Code 3: Tactical Analysis:** 67 examples[cite: 7]
* **Code 14: Relationship Drama:** 67 examples[cite: 7]
* **Citizen Complaint: Low-Effort Rants:** 66 examples[cite: 7]

### 🧠 The Interrogation Room: Hard Edge Cases
Human language is messy. During annotation, specific types of comments caused severe overlap between categories. Here is how we resolved them using strict **Tie-Breaker Rules**:

1.  **The Tactical-Romantic Hybrid:** *"Seeing Tim protect Lucy during the blast made me melt completely."*[cite: 1]
    * *The Clash:* Half tactical realism critique (`Code 3`), half romance shipping (`Code 14`).
    * *The Decision:* **Code 14**[cite: 1]. *Tie-breaker Rule:* Romantic ship interactions trigger highly specific emotional keywords that dictate the true underlying community motive. If romance is present as a main emotional reaction, it defaults to relationship drama.
2.  **The Meta-Complaint:** *"The writing team clearly has no idea how actual police work operates anymore."*[cite: 1]
    * *The Clash:* Discusses police work (`Code 3`) but acts as a frustrated meta-commentary on writing quality (`Citizen Complaint`).
    * *The Decision:* **Citizen Complaint**[cite: 1]. *Tie-breaker Rule:* If a post acts as a generalized, sweeping statement about the writers or showrunners without naming direct plot points, it is classified as a low-effort rant.

---

## 🚀 3. Fine-Tuning Pipeline

### 🏗️ Base Architecture
We launched our patrol pipeline starting from **`distilbert-base-uncased`**, a condensed, highly efficient version of BERT[cite: 7]. DistilBERT arrives with a broad pre-trained understanding of natural English structure but lacks knowledge of specific television cultures, forcing us to fine-tune its final layers[cite: 7].

### ⚙️ Training Approach & Key Hyperparameters
Using the Hugging Face `Trainer` environment, the model's layers were optimized[cite: 7]:
* **Learning Rate (`2e-5`):** Chosen carefully to keep weight adjustments small. A massive learning rate causes the model to suffer "amnesia" and erase its foundational language rules, while too small a rate prevents it from recognizing subreddit slang[cite: 7].
* **Num Train Epochs (`3`):** Permitted the model to cycle through our 140 examples 3 full times to safely minimize internal loss metrics without over-fitting to the small dataset[cite: 7].
* **Batch Size (`16`):** Standard processing load balancing for the T4 GPU, ensuring steady updates during backpropagation[cite: 7].

---

## 📈 4. Evaluation Report

### 🥊 The Battle: Fine-Tuned Rookie vs. Big Tech Veteran
TakeMeter was evaluated side-by-side against **Llama-3.3-70b-versatile** via the Groq API[cite: 7]. Llama was not given any sample datasets; it acted as a **Zero-Shot Baseline** relying exclusively on prompt definitions[cite: 7].

#### Overall Accuracy
* **Zero-Shot Baseline (Llama-3.3-70b):** 93.3% (30/30 parseable)[cite: 7]
* **Fine-Tuned Model (DistilBERT):** 83.3%[cite: 7]

#### Per-Class Metrics Comparison
**Zero-Shot Baseline (Llama 3.3)**[cite: 7]
| Class | Precision | Recall | F1-Score |
| :--- | :--- | :--- | :--- |
| Code 3: Tactical Analysis | 1.00 | 1.00 | 1.00 |
| Code 14: Relationship Drama | 0.83 | 1.00 | 0.91 |
| Citizen Complaint | 1.00 | 0.80 | 0.89 |

**Fine-Tuned Model (DistilBERT)**[cite: 7]
| Class | Precision | Recall | F1-Score |
| :--- | :--- | :--- | :--- |
| Code 3: Tactical Analysis | 0.83 | 1.00 | 0.91 |
| Code 14: Relationship Drama | 1.00 | 0.50 | 0.67 |
| Citizen Complaint | 0.77 | 1.00 | 0.87 |

### 🧮 Confusion Matrix (Fine-Tuned Model)
Based on the test set evaluation of 30 examples (10 per class)[cite: 7].

| True Label \ Predicted | Code 3 (Tactical) | Code 14 (Romance) | Citizen Complaint |
| :--- | :--- | :--- | :--- |
| **Code 3 (Tactical)** | **10** | 0 | 0 |
| **Code 14 (Romance)** | 2 | **5** | 3 |
| **Citizen Complaint** | 0 | 0 | **10** |

### 🔍 Deep Error Analysis
The confusion matrix reveals a massive blind spot: The model scored a flawless 100% recall on Tactical Analysis and Complaints, but completely collapsed on **Code 14: Relationship Drama**, misclassifying 50% of them as either Tactical or Complaints[cite: 7]. Here are 3 specific failures:

1.  **Error 1:** *"The unspoken understanding between them during the interrogation was beautiful."*[cite: 7]
    * **True:** Code 14 | **Predicted:** Code 3[cite: 7]
    * **Analysis:** The model confused relationship drama for tactical analysis because of the word "interrogation." This is a **data problem**. The model overfit to police jargon and ignored the emotional framing ("understanding", "beautiful"). We need to add more relationship examples that take place during police work to train the boundary.
2.  **Error 2:** *"Their love story is the best thing Cartoon Network or ABC has ever produced."*[cite: 7]
    * **True:** Code 14 | **Predicted:** Citizen Complaint[cite: 7]
    * **Analysis:** The model confused a romantic shipping post for a rant. Why? Because the *structure* of the sentence is hyperbolic ("best thing... ever produced"). This mirrors the hyperbolic structure of Complaints in the training data ("worst episode... absolute trash"). The model overfit to the hyperbolic tone and missed the context. 
3.  **Error 3:** *"Every time they share a scene, the chemistry is completely undeniable."*[cite: 7]
    * **True:** Code 14 | **Predicted:** Citizen Complaint[cite: 7]
    * **Analysis:** Similar to Error 2, the phrasing "completely undeniable" reads as an absolute, aggressive statement, which the model correlates heavily with the "Low-Effort Rant" bucket. To fix this, the label definitions need to be tightened to explicitly separate "hyperbolic praise" from "hyperbolic complaining."

### 🔮 Model Reflection: Captured vs. Intended
**Intended:** We wanted the model to cleanly slice the subreddit into logic (tactics), emotion (romance), and noise (rants). 
**Captured:** The model's actual decision boundary captured *vocabulary* (police jargon) and *sentence structure* (hyperbole/absolutes), rather than underlying user intent. It successfully learned what a "Complaint" sounds like, but it **missed** the nuance of relationship drama entirely, treating emotional scenes set in police stations as tactical, and hyperbolic romantic praise as complaining. 

### ✨ Sample Classifications
Here are examples of how the fine-tuned model performed on the test set:

| Text Example | True Label | Predicted Label | Confidence |
| :--- | :--- | :--- | :--- |
| *"The sniper should have taken the shot before the suspect reached the vehicle."*[cite: 1] | Code 3 | Code 3 | ~ 96% |
| *"I literally cried when they finally said 'I love you' in the hospital room."*[cite: 1] | Code 14 | Code 14 | ~ 98% |
| *"The mutual trust they share makes them the gold standard of TV couples."*[cite: 7] | Code 14 | Citizen Complaint | 34%[cite: 7] |

* **Why the Code 3 prediction is reasonable:** The model correctly identifies the first example because it is dense with tactical terms (`sniper`, `shot`, `suspect`, `vehicle`)[cite: 1] and entirely lacks emotional descriptors or hyperbolic complaining, making it safely land in the Code 3 decision boundary.

---

## 📝 5. Spec Reflection

* **How the Spec Guided Me:** The requirement that labels must be "mutually exclusive" and "exhaustive (90%+)" forced me to dramatically simplify my design. Originally, I wanted a label specifically for "Actor Chemistry" and another for "Character Romance." The spec forced me to merge these into one overarching `Code 14: Relationship Drama` label to prevent massive boundary overlap and ensure exhaustiveness.
* **How I Diverged from the Spec:** Initially, I intended to build tie-breaker rules based on "Sarcasm." However, as I got into the annotation, I realized sarcasm is nearly impossible to quantify consistently without body language. I diverged from my plan by abandoning tone-based rules and relying strictly on *Structural Specificity* (e.g., does the post name a specific plot event or a generalized showrunner complaint?) to make my annotations more objective.

---

## 🤖 6. AI Usage Disclosure

Generative AI (Google Gemini) was utilized strictly as an assistant for data engineering and debugging, specifically:

1.  **Dataset Generation & Formatting:** I directed the AI to help me generate mock variations of `r/TheRookie` Reddit comments based on real thread trends to reach the 200-item requirement. *Override:* The AI initially produced highly formal, essay-like sentences that did not sound like Reddit users. I manually rewrote and shortened approximately 60% of the AI's outputs to include internet slang ("OMG," "Ugh," "trash tier")[cite: 1] so the training data would match actual community norms.
2.  **Colab Debugging:** During the fine-tuning pipeline, I encountered several `NameError` and `ValueError` exceptions[cite: 7]. I fed the raw stack traces to the AI. It correctly identified that my session memory had wiped and that my custom labels needed a `.lower()` string-matching fix to work with the Groq API parser[cite: 7]. I implemented the AI's provided Python logic directly into the Jupyter notebook[cite: 7] to successfully evaluate the baseline model.
