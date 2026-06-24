# TakeMeter Project Planning: r/TheRookie

## 1. Community Selection

### Chosen Community
The selected community for this classification task is the official subreddit for the ABC television series *The Rookie*: **`r/TheRookie`**, specifically focusing on live episode discussion threads such as Season 8, Episode 18 ("The Bandit").

### Why This Community is a Fit
Television fan subreddits are highly dynamic spaces where discourse regularly fractures into distinct sub-cultures. *The Rookie* presents a unique mix of genres—part tactical police procedural, part ensemble workplace comedy, and part primetime relationship melodrama. This specific genre-bending creates a highly varied and messy landscape of text data. 

The user base consistently oscillates between three modes:
* Analyzing real-world law enforcement procedures and tactics.
* Passionately debating character relationship dynamics and romantic "ships".
* Airing repetitive, low-effort grievances about character writing and casting decisions.

This high variance in user intent ensures that simple keyword matching is insufficient for accurate filtering, providing an excellent testing ground for fine-tuning a transformer-based classifier.

---

## 2. Label Taxonomy

The TakeMeter taxonomy maps community discourse into three mutually exclusive and exhaustive labels:

| Label Name | Definition (Single Sentence) |
| :--- | :--- |
| **Code 3: Tactical Analysis** | Posts or comments focused strictly on plot mechanics, law enforcement strategies, crime realism, action sequences, or theories regarding structural lore. |
| **Code 14: Relationship Drama** | Content entirely dedicated to character romance, interpersonal emotional subplots, actor chemistry, and relationship "shipping". |
| **Citizen Complaint: Low-Effort Rants** | Repetitive complaints, low-effort memes, redundant character hate-posts, or general show cynicism that adds zero analytical depth to the conversation. |

### Dataset Examples

#### Code 3: Tactical Analysis
> * **Example 1:** *"The Bandit using a police scanner to outsmart Metro was a solid plot twist."*
> * **Example 2:** *"Why didn't they set up a perimeter at the back entrance of the warehouse?"*

#### Code 14: Relationship Drama
> * **Example 1:** *"Can we talk about the Chenford scene by the food truck? I am screaming!"*
> * **Example 2:** *"Tim looking at Lucy like she's the only person in the room... my heart!"*

#### Citizen Complaint: Low-Effort Rants
> * **Example 1:** *"Ugh, why is Bailey suddenly an expert at bomb defusal now? This is ridiculous."*
> * **Example 2:** *"This show has become so unrealistic compared to season 1. Sad."*

---

## 3. Hard Edge Cases

During human annotation, linguistic overlap creates severe ambiguity between categories. The two most prominent edge cases and their respective mitigation policies are defined below:

### Edge Case A: The Tactical-Romantic Hybrid
* **The Conflict:** A comment analyzes a tactical scene but evaluates it purely through the lens of romantic tension between the characters involved (e.g., *"Seeing Tim protect Lucy during the blast made me melt completely."*). This triggers vocabulary flags for both Code 3 (`tactical/blast`) and Code 14 (`melt/love`).
* **Handling Strategy:** A strict **Romantic Primacy Rule** is applied during annotation. If a post references tactical police maneuvers but contextualizes them as a driver for romantic or emotional character subplots, it is categorized as **Code 14: Relationship Drama**.

### Edge Case B: The Analytical Complaint
* **The Conflict:** A user provides an in-depth critique of plot realism that borders on a frustrated rant (e.g., *"The writing team clearly has no idea how actual police work operates anymore."*). Delineating a high-effort tactical critique from a low-effort complaint becomes highly subjective.
* **Handling Strategy:** A **Structural Specificity Rule** is used as a tie-breaker. If the comment references a specific event, protocol, or narrative sequence from the episode, it is categorized as **Code 3: Tactical Analysis**. If it relies entirely on generalized, sweeping statements about the writers, showrunners,
