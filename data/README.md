# Data

## Competition data (not included in this repository)

Per the competition's Official Rules (Section 2.4(b), Data Security): participants
"agree not to transmit, duplicate, publish, redistribute or otherwise provide or
make available the Competition Data to any party not participating in the
Competition." Accordingly, `documents.csv`, `train_prompts.csv`, and
`test_prompts.csv` are **not included** in this repository.

To obtain them:

1. Join the *African Folktales SLM Challenge* competition on Kaggle and accept its rules.
2. In a Kaggle Notebook, attach the competition dataset via **Add Input → Competitions**.
3. The files will be available at `/kaggle/input/competitions/african-folktales-slm-challenge/`.

Alternatively, outside Kaggle, use the Kaggle CLI once you have accepted the competition rules:

```bash
kaggle competitions download -c african-folktales-slm-challenge
unzip african-folktales-slm-challenge.zip -d data/
```

## Our supplemental data (included, `supplemental_documents.csv`)

The 16 supplemental documents we added ourselves (`ext_001`–`ext_016`) are our
own original work, licensed CC0-1.0, and are included in this folder. They were
used as retrieval-style "Style cue" context during generation (Sections 6.1–6.2
of the notebook), and 6 of them were additionally hand-compressed into new
training examples (Section 6.2c). Provenance and source citations for each are
documented in the notebook.
