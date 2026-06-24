# planning.md

## 1. Community Selection

### Chosen Community
The selected community for this classification task is the official subreddit for the ABC television series *The Rookie*: **`r/TheRookie`**, specifically focusing on live episode discussion threads such as Season 8, Episode 18 ("The Bandit")[cite: 1, 2].

### Why This Community is a Fit
Television fan subreddits are highly dynamic spaces where discourse regularly fractures into distinct sub-cultures. *The Rookie* presents a unique mix of genres—part tactical police procedural, part ensemble workplace comedy, and part primetime relationship melodrama. This specific genre-bending creates a highly varied and messy landscape of text data[cite: 1, 2]. 

The user base consistently oscillates between three modes:
* Analyzing real-world law enforcement procedures and tactics[cite: 1].
* Passionately debating character relationship dynamics and romantic "ships"[cite: 1].
* Airing repetitive, low-effort grievances about character writing and casting decisions[cite: 1].

This high variance in user intent ensures that simple keyword matching is insufficient for accurate filtering, providing an excellent testing ground for fine-tuning a transformer-based classifier[cite: 1, 2].

---

## 2. Label Taxonomy

The TakeMeter taxonomy maps community discourse into three mutually exclusive and exhaustive labels[cite: 1, 2]:

| Label Name | Definition (Single Sentence) |
| :--- | :--- |
| **Code 3: Tactical Analysis** | Posts or comments focused strictly on plot mechanics, law enforcement strategies, crime realism, action sequences, or theories regarding structural lore[cite: 1]. |
| **Code 14: Relationship Drama** | Content entirely dedicated to character romance, interpersonal emotional subplots, actor chemistry, and relationship "shipping"[cite: 1]. |
| **Citizen Complaint: Low-Effort Rants** | Repetitive complaints, low-effort memes, redundant character hate-posts, or general show cynicism that adds zero analytical depth to the conversation[cite: 1]. |

### Dataset Examples

#### Code 3: Tactical Analysis
> * **Example 1:** *"The Bandit using a police scanner to outsmart Metro was a solid plot twist."*[cite: 1]
> * **Example 2:** *"Why didn't they set up a perimeter at the back entrance of the warehouse?"*[cite: 1]

#### Code 14: Relationship Drama
> * **Example 2:** *"Can we talk about the Chenford scene by the food truck? I am screaming!"*[cite: 1]
> * **Example 2:** *"Tim looking at Lucy like she's the only person in the room... my heart!"*[cite: 1]

#### Citizen Complaint: Low-Effort Rants
> * **Example 1:** *"Ugh, why is Bailey suddenly an expert at bomb defusal now? This is ridiculous."*[cite: 1]
> * **Example 2:** *"This show has become so unrealistic compared to season 1. Sad."*[cite: 1]

---

## 3. Hard Edge Cases

During human annotation, linguistic overlap creates severe ambiguity between categories. The two most prominent edge cases and their respective mitigation policies are defined below:

### Edge Case A: The Tactical-Romantic Hybrid
* **The Conflict:** A comment analyzes a tactical scene but evaluates it purely through the lens of romantic tension between the characters involved (e.g., *"Seeing Tim protect Lucy during the blast made me melt completely."*[cite: 1]). This triggers vocabulary flags for both Code 3 (`tactical/blast`) and Code 14 (`melt/love`)[cite: 1].
* **Handling Strategy:** A strict **Romantic Primacy Rule** is applied during annotation. If a post references tactical police maneuvers but contextualizes them as a driver for romantic or emotional character subplots, it is categorized as **Code 14: Relationship Drama**[cite: 1].

### Edge Case B: The Analytical Complaint
* **The Conflict:** A user provides an in-depth critique of plot realism that borders on a frustrated rant (e.g., *"The writing team clearly has no idea how actual police work operates anymore."*[cite: 1]). Delineating a high-effort tactical critique from a low-effort complaint becomes highly subjective.
* **Handling Strategy:** A **Structural Specificity Rule** is used as a tie-breaker. If the comment references a specific event, protocol, or narrative sequence from the episode, it is categorized as **Code 3: Tactical Analysis**[cite: 1]. If it relies entirely on generalized, sweeping statements about the writers, showrunners, or actors without naming direct plot points, it defaults to **Citizen Complaint: Low-Effort Rants**[cite: 1].

---

## 4. Data Collection Plan

### Collection Strategy
Data is compiled directly from the live community discussion thread for *The Rookie* Season 8, Episode 18 ("The Bandit") on Reddit[cite: 1, 2]. Comments are parsed linearly to capture the natural chronological flow of active user behavior.

### Dataset Properties
* **Target Count:** A total of 200 items are compiled[cite: 1, 2].
* **Target Distribution:** The dataset maintains a balanced split: Code 3 (~67 examples), Code 14 (~67 examples), and Citizen Complaint (~66 examples)[cite: 1].
* **Dataset Splits:** The data is partitioned using a stratified 70% / 15% / 15% structure to preserve equal label distribution across all execution phases[cite: 2]:
  * **Train Set:** 140 examples[cite: 2]
  * **Validation Set:** 30 examples[cite: 2]
  * **Test Set:** 30 examples[cite: 2]

### Mitigation for Underrepresented Labels
If a specific category falls short of its representation target after parsing 200 random comments, a targeted keyword querying strategy will be deployed. For instance, if Code 3 is underrepresented, historical episode threads will be scanned using highly specific anchor terms like *protocol*, *Metro*, *perimeter*, or *arrest* to gather the necessary data volume while maintaining domain integrity[cite: 1].

---

## 5. Evaluation Metrics

To evaluate model performance on the locked test set, a combination of multiple classification metrics will be utilized beyond basic overall accuracy[cite: 2]:

### Per-Class Precision
* **Why it matters:** Precision measures the exact percentage of correct predictions out of all items assigned to a specific label. For a moderation tool deploying a **Citizen Complaint** filter, high precision is essential. If the model exhibits low precision for rants, it will routinely flag legitimate, high-quality user reviews as low-effort noise, frustrating community members.

### Per-Class Recall
* **Why it matters:** Recall measures the model's capacity to find all actual instances of a given label within the data. High recall for **Code 14: Relationship Drama** ensures that users seeking a purely tactical or procedural discussion filter are not exposed to stray shipping content that slipped past the model's radar.

### Macro-Averaged F1-Score
* **Why it matters:** Accuracy alone can easily mask severe model failures if a system performs exceptionally well on one class while completely failing another. The macro-averaged F1-score computes the harmonic mean of precision and recall independently for each category, offering a clear metric that proves the classifier evaluates all three discourse domains with equal stability.

---

## 6. Definition of Success

### Minimum Viable Metrics
For the TakeMeter fine-tuned DistilBERT classifier to be considered a successful asset, it must achieve a minimum overall accuracy of **75%** on the test set while showing a distinct classification improvement over the zero-shot Llama 3.3 70B baseline[cite: 2]. 

### Production-Ready Deployment Threshold
For real-world integration into a community browser extension or a automated moderation assistant tool, success is defined by the following benchmarks:
1. **Overall Accuracy:** $\ge$ 80% on the final test split.
2. **Per-Class Precision (Citizen Complaints):** $\ge$ 85%. This strict constraint ensures that legitimate fan expressions are rarely misclassified as low-effort rants.
3. **Confusion Matrix Delineation:** No more than a 10% cross-contamination rate between Code 3 (Tactical) and Code 14 (Romance), proving the structural tie-breaker rules successfully resolved core linguistic overlap during training[cite: 1, 2].

## 7. AI Tool Plan

This project's workflow focuses heavily on data engineering and evaluation rather than raw software development[cite: 2]. Instead of using AI to write code, we will deploy LLMs as investigative partners in three critical phases of the data pipeline:

### 🛠️ Phase A: Label Stress-Testing (Pre-Patrol Inspection)
Before manually annotating all 200 examples, we will use an LLM (e.g., Gemini or Claude) to deliberately try and break our taxonomy. 
* **The Method:** We will feed the AI our three label definitions and current tie-breaker rules[cite: 1] and prompt it: *"Generate 10 highly ambiguous r/TheRookie comments that sit exactly on the border between two categories."*
* **Tightening the Loop:** If the AI produces a boundary post that cannot be instantly classified by our existing rules (e.g., a highly sarcastic comment that blends genuine plot logic with intense character shipping)[cite: 1], it reveals a gap in our system. We will rewrite and tighten our definitions in Section 3 *before* starting the manual dataset build[cite: 1].

### ✍️ Phase B: Annotation Assistance (The Co-Pilot Protocol)
To accelerate the generation and structure of our 200-example dataset, we will utilize an LLM as a data formatting co-pilot[cite: 1, 2].
* **The Tool & Workflow:** We will use a frontier model to help draft realistic mock variations of active `r/TheRookie` community discourse based on genuine thread patterns[cite: 1, 2]. 
* **Tracking & Disclosure:** To ensure absolute algorithmic transparency, our master tracking spreadsheet will include an explicit column metadata flag (`is_ai_assisted`). Every single row generated or formatted with AI assistance will be manually reviewed, audited, and approved by a human annotator to guarantee that the final ground-truth labels perfectly match our established community taxonomy[cite: 1].

### 🔍 Phase C: Failure Analysis (The Post-Incident Review)
Once the fine-tuning pipeline in `Copy_of_ai201_project3_takemeter_starter_clean.ipynb` is executed[cite: 2], the notebook will output a collection of misclassified test examples[cite: 2]. We will pass this error log directly to an LLM to find the blind spots.
* **What We'll Look For:** We will instruct the AI to analyze the text of the errors and identify linguistic patterns, such as:
  * *Keyword Over-reliance:* Is DistilBERT misclassifying posts as rants simply because they contain the word "Bailey"[cite: 1]?
  * *Linguistic Blends:* Is the model failing on hybrid sentences where a tactical word is used in a shipping context[cite: 1]?
* **Verification:** We will never take the AI's error analysis at face value. We will cross-verify the patterns it identifies by manually calculating the exact percentage of matching errors in the test set and mapping them against our visual confusion matrix plot (`confusion_matrix.png`)[cite: 2].
