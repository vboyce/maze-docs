---
layout: default
---

# A-maze in other languages

The [distractor generator](generator.md) is set up for English. Nothing in the method is English-specific, though. A-maze needs three ingredients, and for another language you supply your own version of each:

1. **A list of possible distractor words.** These should be real, common, inoffensive words that participants will recognize. Computer dictionaries can help, but usually need cutting (abbreviations, proper nouns, swear words, sensitive topics).
2. **Word frequencies**, to draw candidates that match the correct word in length and frequency.
3. **A language model** that can say how surprising a word is in context.

## What to change in the generator

**Word list.** Point `include_words` in your parameters file at a list of candidate words for your language (one per line), and `exclude_words` at a list of words to never use. English's `curated_word_list.txt` took a fair amount of hand-filtering. Budget for that, especially if participants are children.

**Dictionary class.** The dictionary class (set with `dictionary_loc` / `dictionary_class`) supplies the candidates and their frequencies. `wordfreq_English_dict` in `wordfreq_distractor.py` uses English frequencies from [wordfreq](https://github.com/rspeer/wordfreq), which has data for about 40 languages. It also keeps only words matching `^[a-z]*$`. Copy the class and change the language code and the allowed characters. `wordfreq_French_dict`, in the same file, is an unfinished example; it expects a French word list that isn't included.

**Length and frequency matching.** The threshold function (`threshold_loc` / `threshold_name`, default `get_thresholds`) looks up the correct word's frequency in **English** (`wordfreq.zipf_frequency(word, 'en')`). You need a copy that uses your language's code, or candidates will be matched against English frequencies. Word length in characters also means different things across writing systems.

**Language model.** Any Hugging Face causal or masked model works (`model`, `backend`). Use one trained on your language, either monolingual or a multilingual model with good coverage of it. It's worth checking that the model gives sensible surprisals on a few of your sentences before generating everything (`get_surprisal.py`).

## Things to think about

- **Capitalization and punctuation.** Distractors copy the correct word's leading and trailing punctuation and its capitalization pattern (`utils.copy_punct`). That's right for English but not necessarily for your language. In German, for example, a capitalized distractor would signal "noun". The word list also needs words that look fine in any capitalization.
- **Tokenization and word boundaries.** Surprisal is computed by comparing tokenizations of the prefix with and without the word. That assumes words are separated by spaces. Languages written without spaces (e.g. Chinese, Japanese) need a decision about what a "word" (a Maze step) is. The plugin's `grouping_string` can split the display into units other than space-separated words. Distractors that are single tokens make surprisal easier to compare.
- **Morphology.** In richly inflected languages, a distractor can be ungrammatical just from its inflection, which makes it easy to reject without processing the sentence. Consider matching candidates on part of speech or morphology as well as length and frequency.
- **Review.** Automatic distractors in any language will include some plausible continuations. Use the [review workflow](generator.md#reviewing-distractors), and have a native speaker check the distractors for critical regions.

Studies building on A-maze have been run outside English, for example in Mandarin Chinese (Hao, Fuchs, & Vasishth, 2025; see [papers using A-maze](papers.md)). If you adapt the generator for another language, contributions to [maze-distractor-generator](https://github.com/vboyce/maze-distractor-generator) are welcome.
