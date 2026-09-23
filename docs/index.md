---
layout: default
---

# A-maze 

A-maze is an incremental sentence processing method; it's a variant of the Maze task that uses automatically generated distractors. 

<img src="assets/maze_diagram.jpg" width="300" style="display:block;margin-left:auto;margin-right:auto" alt="diagram of Maze task"/>

You can learn [more about the Maze task](intro.md), or try it yourself in the [demos](demos.md).

## Current tools

- **[maze-distractor-generator](generator.md)** makes distractors for your materials. It uses current language models (any Hugging Face causal or masked model, or API models), and has a workflow for reviewing distractors and regenerating bad ones.
- **[jspsych-maze](jspsych.md)** runs the Maze task in the browser as a [jsPsych](https://www.jspsych.org/) plugin. It supports "redo" mode with a delay after mistakes, custom feedback, and styling with CSS.
- **[Demos](demos.md)** of complete experiments, including a [kid-friendly version](kid-friendly.md).

A typical pipeline: write your materials, then generate distractors with `distract.py --format json`. Review them and regenerate any bad ones. Then load the resulting JavaScript module into a jsPsych experiment that uses the Maze plugin.

## Older tools

The original A-maze code (`maze_automate`, in [vboyce/Maze](https://github.com/vboyce/Maze)) and the [Ibex implementation of Maze](https://github.com/vboyce/Ibex-with-Maze) are still documented here, under "Older tools" in the menu. They are not maintained. For new projects, use the current tools above. The "Background" pages apply to both; some show Ibex code, which is marked.

This is still a work in progress, so if you run into bugs or issues, feel free to raise issues on github or email me. 
 
### Citing A-maze

If you use A-maze in your work, please cite both the A-maze paper and the papers about whatever model(s) you used.

 - A-maze paper: V. Boyce, R. Futrell, R. P. Levy. 2020. Maze Made Easy: Better and easier measurement of incremental processing difficulty. Journal of Memory and Language.
 - With the older `maze_automate` code:
   - Gulordava model (its default model): K. Gulordava, P. Bojanowski, E. Grave, T. Linzen, M. Baroni. 2018. Colorless green recurrent networks dream hierarchically. Proceedings of NAACL.
   - French model: A. An, P. Qian, E. Wilcox, R. P. Levy. 2019. Representation of Constituents in Neural Language Models: Coordination Phrase as a Case Study. EMNLP 2019. 
 
### Projects using A-maze
(This is currently a very short list because I'm not aware of much work using A-maze. If you send me links to pre-prints/posters/other open access work using A-maze, I'll add it here.)

- [Maze Made Easy: Better and easier measurement of incremental processing difficulty](https://psyarxiv.com/b7nqd/)
- ["Ambiguous" isn't "underspecified": Evidence from the Maze task](https://osf.io/zb236/) and [A-maze by any other name](https://osf.io/u8t2d/)
