---
layout: default
---

# A-maze in other languages

The [distractor generator](generator.md) is set up for English. Nothing in the method is English-specific, though. A-maze needs three ingredients, and for another language you could supply your own version of each:

1. **A list of possible distractor words.** These should be real words that participants will recognize. Computer dictionaries can help, but usually need cutting (abbreviations, proper nouns, swear words).
2. **Word frequencies**, to draw candidates that match the correct word in length and frequency -- or whatever properties you want to match on. 
3. **A language model** that can say how surprising a word is in context.

## What to change in the generator

Set three things in your parameters file:

```
language: "fr"                      # a wordfreq language code
include_words: "french_words.txt"   # candidate distractors, one per line
model: "<a French or multilingual Hugging Face model>"
```

**`language`** is a [wordfreq](https://github.com/rspeer/wordfreq) language code; wordfreq has frequency data for about 40 languages. Frequencies for both the candidate distractors and the real words come from that language, so candidates are matched to your words on length and frequency. An unknown code is an error. If you don't want wordfreq, you could edit the code to use a different frequency source, or a different matching procedure. 

**`include_words`** is required outside English; there is no default word list for other languages. Candidates must be all-lowercase letters (accented letters are fine), which drops proper nouns and abbreviations. English's `curated_word_list.txt` took a fair amount of hand-filtering (offensive words, sensitive topics). Use `exclude_words` if you want to build up a blacklist rather than curate everything at the start.

**`model`** can be any Hugging Face causal or masked model (`backend`). Use one trained on your language, either monolingual or a multilingual model with good coverage of it. It's worth checking that it gives sensible surprisals on a few of your sentences before generating everything (`get_surprisal.py`).

If you need different rules for which words are allowed, copy `wordfreq_dict` in `wordfreq_distractor.py` and set `dictionary_class` to your class.

## Things to think about

- **Capitalization and punctuation.** Distractors copy the correct word's leading and trailing punctuation and its capitalization pattern (`utils.copy_punct`). That's right for English but not necessarily for your language. In German, for example, a capitalized distractor would signal "noun". The word list also needs words that look fine in any capitalization. For languages where capitalization should be handled differently, you may need to edit some code (it currently applies the real word's capitalization and punctuation to the distractor). 
- **Tokenization and word boundaries.** Surprisal is computed by comparing tokenizations of the prefix with and without the word. That assumes words are separated by spaces. Languages written without spaces (e.g. Chinese, Japanese) need a decision about what a "word" (a Maze step) is. The plugin's `grouping_string` can split the display into units other than space-separated words. Distractors that are single tokens make surprisal easier to compare.
- **Review.** Automatic distractors in any language may include some plausible continuations. Use the [review workflow](generator.md#reviewing-distractors), and check the distractors for critical regions, or run a pilot test to identify likely problems. 

If you adapt the generator for another language, contributions to [maze-distractor-generator](https://github.com/vboyce/maze-distractor-generator) are welcome (send a PR if you like).
