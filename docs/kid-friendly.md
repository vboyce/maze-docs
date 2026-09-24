---
layout: default
---

# A-maze for kids

We are currently running A-maze with children, although that is a work in progress, so we do not yet know how well it works at which ages. We are running it with 8–12-year-olds in [kid-maze-passages](https://github.com/vboyce/kid-maze-passages), on Children Helping Science. Compared with a standard adult setup, there are three things we changed that we hope make it work well for kids.

**A restricted distractor list.** We are more careful about the distractors. The current [distractor generator](generator.md)'s default list (`curated_word_list.txt`) excludes offensive and sensitive (sexual, violent, religious) words. Words can also be added to `exclude.txt`. Even so, [review the distractors](generator.md#reviewing-distractors) before running a study with children.

**A gentler introduction.** We want to avoid frustration and ease children into the task more. The kid version has:
- a picture of where to put their fingers;
- three practice sentences that fade out the help. The first gives a tip for every word and shows the sentence building up; the second only shows the sentence building up; the third has no help;
- key badges (E and I) under the words;
- reassuring feedback after mistakes ("Oops! Wait a moment..." then "Try again!").

**Fun colors and structure.** A light background, a rainbow-bordered card, big buttons, and pictures between sentences. There's also a progress bar across the top, with pause and stop buttons.

<img src="assets/kid-maze-practice.png" width="600" style="display:block;margin:auto" alt="Kid-friendly Maze practice screen: a rainbow-bordered card with a tip above the words 'The' and '---', E and I key badges below, and a progress bar with pause and stop buttons at the top"/>

## Try it

The **[kid-friendly demo](https://vboyce.github.io/maze-demos/kid-maze-experiment/index.html)** is the kid-maze-passages experiment (without the consent pages): the introduction, practice, and two short passages (dinosaurs and whales).

The code is in [jspsych-maze](https://github.com/vboyce/jspsych-maze): `source/src/kid-maze-experiment.js` and `source/src/kid/` for the timeline, and `source/styles/kid.scss` for the look. The styling only applies under a `kid-maze` class on the page, so it can be reused without affecting anything else.
