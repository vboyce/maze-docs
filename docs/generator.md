---
layout: default
---

# Distractor generation with Hugging Face models

[maze-distractor-generator](https://github.com/vboyce/maze-distractor-generator) picks a distractor for every word of your materials. It replaces the [original distractor generation code](usage_basic.md) (`maze_automate`) described in Boyce et al. (2020) and Boyce & Levy (2023). The main differences:

- It works with current language models: any Hugging Face causal model (GPT-2, Pythia, Llama, …) or masked model (BERT, RoBERTa).
- There is a review workflow to make it easier to tweak distractors as a researcher: you can mark bad distractors in a CSV and regenerate just those.
- There is a "kid-friendly" word list that is curated to avoid offensive and sensitive (sexual, violent, religious) words. You can also provide your own word list, or build your own blacklist. 
- It has JSON output for jsPsych.

The idea is the same as before (see [What is A-maze?](intro.md)). It is set up for English; see [A-maze in other languages](non-english.md) for adapting it. For each word, the process draws candidate words of similar length and frequency, and keeps one that the language model finds surprising enough in that position.

## Install

```sh
git clone https://github.com/vboyce/maze-distractor-generator
cd maze-distractor-generator
python -m venv .venv
.venv/bin/pip install -r requirements.txt
```

Without an NVIDIA GPU, install the CPU build of torch first:

```sh
pip install torch --index-url https://download.pytorch.org/whl/cpu
```

## Materials

A CSV with a header row: `type` (condition; passed through), `item_num`, `sentence`, and optionally `labels`.

```
type,item_num,sentence,labels
sub_rel,3,The cat who the dog scared hid in a box.,pre_1 pre_2 who art noun verb main_verb post_1 post_2 post_3
obj_rel,3,The dog who scared the cat sniffed around the couch.,pre_1 pre_2 who verb art noun main_verb post_1 post_2 post_3
filler,4,Birds sing in the morning.,
```

Sentences with the same `item_num` share distractors. Without labels they are matched by word position; with labels, words with the same label get the same distractor. In the example, the relative-clause nouns get the same distractor even though they are in different positions. This matters for minimal pairs: if the distractors differ between conditions, so might the RTs.

## Running it

```sh
# CSV output
.venv/bin/python distract.py materials.csv distractors.csv -p params.txt

# JavaScript module for jsPsych (items have item_type, id, sent, distractor, labels)
.venv/bin/python distract.py materials.csv stimuli.js -p params.txt --format json
```

`params.txt` sets the model and thresholds. The key settings are:

- **`min_delta`** and **`min_abs`**: a distractor must be at least `min_delta` bits more surprising than the real word, and at least `min_abs` bits surprising.
- **`num_to_test`**: how many candidates to try per position.
- **`model`** and **`backend`**.
- **`max_repeat`**: how many times any word may be used as a distractor.

The trade-offs between them are discussed in [Parameter considerations](advice.md); `min_delta`, `min_abs` and `num_to_test` mean the same as in the original code. A distractor only has to be surprising *enough*: at least `min_delta` bits more than the real word and at least `min_abs` bits overall. The generator takes the first candidate that clears both. The [README](https://github.com/vboyce/maze-distractor-generator#parameters) has the full table. An unknown key in the parameters file is an error.

## Reviewing distractors

Some automatic distractors will be plausible continuations, or words you don't want participants to see. This matters most for critical regions and for special populations such as children. The review workflow:

1. Generate with a review file, listing a few alternatives per position:
   ```sh
   .venv/bin/python distract.py materials.csv distractors.csv -p params.txt --longform review.csv --num-options 3
   ```
2. Open `review.csv` (one row per word position). Mark bad distractors in the `rejected` column.
3. Regenerate only those:
   ```sh
   .venv/bin/python distract.py materials.csv stimuli.js --format json -p params.txt \
       --rejection-file review.csv --longform review_2.csv
   ```
   Everything not rejected is kept. `stimuli.js` has the final distractors for every sentence. Repeat with `review_2.csv` as needed.

Other checks:
- You could pilot the materials and regenerate distractors that several participants get wrong, as an empirical measure of "too plausible".
- We tried using LLMs to check for plausibility (see `check_distractors.py`), but so far it is unreliable in both directions. Possibly a better framework in this direction would yield better outcomes. 

## Which model?

The psycholinguistics literature suggests that roughly GPT-2-size models give a good fit to human surprisal, and for this purpose, we don't need precision. Small causal models work well and run quickly on a laptop; we have used `gpt2`, `distilgpt2` and `EleutherAI/pythia-160m`. `benchmark.py` compares the run time of several models on the same input.

## Citing

There isn't yet a separate publication for the newer distractor generation (with Hugging Face models). If you use it, please cite one of the A-maze papers ([Boyce, Futrell, & Levy, 2020](https://www.sciencedirect.com/science/article/pii/S0749596X19301147) or [Boyce & Levy, 2023](https://escholarship.org/uc/item/6vh9d8zm)), plus the specific language model you used to generate distractors.
