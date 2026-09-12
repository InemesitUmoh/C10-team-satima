# African Folktales SLM Challenge — Team Satima

Fine-tuning Gemma-2-2b-it with LoRA to generate African-folktale-style short stories from thematic prompts, for the *African Folktales SLM Challenge* (TRI AI Saturdays, Cohort 10).

**Official result:** 176.25 public / 157.66 private mean Levenshtein distance (lower is better), vs. a provided TF-IDF baseline of ~209.3.

See `docs/problem_statement.pdf` for the full values-led problem statement.

## Dataset

The competition provides a synthetic v1 corpus: `documents.csv` (24 folktales), `train_prompts.csv` (38 prompt–reference_story pairs), `test_prompts.csv` (10 held-out prompts). Organizers label this as educational synthetic text, not transcribed oral tradition; we treat that distinction carefully (see `docs/problem_statement.pdf`).

We supplemented `documents.csv` (used only as "Style cue" generation context, never a training target in the official pipeline) with 16 original documents: 6 are retellings grounded in specific, independently verified traditional African motifs (sourced and cited in the notebook), 10 are original compositions in a similar register with no claimed specific source. All CC0-1.0, disclosed on the competition forum per the rules. In post-submission work (see below), 6 of these were further compressed into genuine new training examples.

## Training Pipeline

- **Base model:** `google/gemma-2-2b-it` via Hugging Face `transformers`.
- **Fine-tuning:** LoRA (`peft` + `trl` `SFTTrainer`), r=8, alpha=16, dropout=0.05, attention layers only (`q/k/v/o_proj`), 15 epochs, trained on all 38 examples.
- **Style-cue selection:** training and generation both use TF-IDF-guessed selection of a reference excerpt (not the exact linked document) — this removed a train/test mismatch and was our single largest confirmed improvement.
- **Hyperparameter search:** LoRA rank, learning rate, cue context length, plain vs. TF-IDF matching, sampling vs. greedy decoding, loss-masking, and attention vs. attention+feed-forward targeting were all tested; reasoning and results for each are documented inline in the notebook.
- **Reproducibility:** all random seeds fixed, since early unseeded runs showed large, confounding score variance between "identical" configurations.

## Evaluation

- **Competition metric:** mean character-level Levenshtein distance against hidden references.
- **Automated quality checks:** every generated story is screened for leaked labels, length outliers, repetition loops, garbled text, and unclosed sentences before submission.
- **Local validation (post-submission):** k-fold cross-validation and a train/validation loss diagnostic were added after our official submission; see below.

## Reproduction

`scripts/` contains two notebooks:

1. **`african_folktales_slm_official.ipynb`** — the exact notebook committed and scored on Kaggle. Attach the competition data and a Gemma-2-2b-it (Transformers variant) model, enable GPU, run all cells top to bottom. `submission.csv` is written by the final "Validate and save" step.
2. **`post_submission_exploration.ipynb`** — additional work completed after the Sept 9 development-phase close (see below). Never submitted to Kaggle, since the window had closed.

**Data note:** per the competition's data-use rules, raw competition CSVs are not redistributed here. See `data/README.md` for how to obtain them.

## Post-Submission Exploration

After our official submission, our mentor reviewed our approach and raised three methodology concerns: our loss computation didn't mask the prompt template, we had dropped our validation split once an epoch count was settled, and our LoRA config only targeted attention layers. We investigated all three properly, with mixed results:

- **Loss-masking** and **feed-forward LoRA targeting** were both implemented and correctly recalibrated (each needed far fewer epochs than our unmasked/attention-only setup to avoid overfitting), but each scored *worse* on held-out generation once properly tuned.
- **Validation methodology** was the concern that held up fully. We added k-fold cross-validation using real Levenshtein distance against held-out reference stories, plus a train/validation loss diagnostic. On our original 38 examples, cross-validation averaged 174.4. After curating 6 real-motif examples into genuine training targets, it improved to 159.4 — a promising but not fully isolated result.
- We also built an authenticity rubric and provenance report addressing the brief's own note that Levenshtein "rewards string similarity, not oral performance quality."

**None of this is reflected in our officially scored result**, since it was completed after the submission window closed. It is presented for transparency and as an honest account of our response to feedback.

## Appendix

**Team Satima** 
- **Team Lead:** [Inemesit Friday Umoh](https://github.com/InemesitUmoh)
- **Team Members:**
   - Solomon Adegoke
   - Victor Onwuelu
   - Adamo Idowu

**Mentor:** Patrick Ikongha

**Course:** TRI AI Saturdays / Google DeepMind AI Research Foundations Course, Cohort 10.
