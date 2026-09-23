---
layout: default
---

# Running A-maze in jsPsych

[jspsych-maze](https://github.com/vboyce/jspsych-maze) is a plugin for [jsPsych](https://www.jspsych.org/) 7 that runs the Maze task in a web browser. It replaces the [Ibex implementation](ibex.md), which is older.

On each screen the participant sees two words: the next word of the sentence and a distractor. They press **e** for the left word or **i** for the right word. The left/right position of the correct word is random unless you set it.

## Setup

The plugin is two files, `source/src/maze.js` and `source/src/maze_helper.js`. Copy them into your jsPsych project and import the plugin:

```js
import MazePlugin from "./maze.js";

const trial = {
  type: MazePlugin,
  correct: "The cat sat on the mat.",
  distractor: "x-x-x ends lady sum hid pro.",
  prompt: "<p>Press <b>e</b> for the left word, <b>i</b> for the right word.</p>",
};
```

`correct` and `distractor` are space-separated strings with the same number of words. The first distractor is `x-x-x`, because the first word has no context that could make a distractor bad.

With materials from the [distractor generator](generator.md) (`--format json`), use timeline variables:

```js
import { stimuli } from "./stimuli.js";   // written by distract.py --format json

const maze_trials = {
  timeline: [{
    type: MazePlugin,
    correct: jsPsych.timelineVariable("sent"),
    distractor: jsPsych.timelineVariable("distractor"),
    prompt: "",
    data: { item: jsPsych.timelineVariable("id"), condition: jsPsych.timelineVariable("item_type") },
  }],
  timeline_variables: stimuli,
  randomize_order: true,
};
```

The easiest way to start a new experiment is to copy one of the demo experiments in jspsych-maze (`source/src/*-experiment.js`), which you can build and serve with `npm run start:<demo>`. See the [README](https://github.com/vboyce/jspsych-maze#readme) for setup.

## Mistakes: redo mode, delay and guard

With **`redo: true`** (the default), a mistake doesn't end the sentence. The participant sees `error_message` for `delay` ms (default 500). Then `redo_message` appears, and they must pick the correct word to go on. This keeps data from the rest of the sentence and makes multi-sentence items possible. See [An argument for 'redo' mode](redo.md) and [Adding a delay](delay.md) for why.

After the delay there is a further **`error_guard`** (default 150 ms) before keys work again, so that a key still held down from the mistake isn't counted as the next answer.

With **`redo: false`**, the trial ends at the first mistake, as in the traditional Maze task.

## Data

Each trial records:

- **`rt`**: ms to the first key press on each word reached.
- **`correct`**: 1 if that first press was right, else 0.
- **`cumrt`**: ms from the word appearing to the correct press. This includes wrong presses, the delay and the error guard. When a `redo: false` trial ends on a mistake, that word's entry is `null`. `rt`, `correct` and `cumrt` always have the same length.
- **`words`, `distractors`**: the sentence and distractors, split into words.
- **`order`**: for each position, 0 if the correct word was on the left, 1 if on the right.

In redo mode, `rt` is the usual measure. The difference `cumrt - rt` shows how long recovering from a mistake took.

## Customizing

The [README](https://github.com/vboyce/jspsych-maze#parameters) has the full parameter table. The most useful options:

- **`order`**: fix the left/right position of the correct word (e.g. for practice items).
- **`on_word_correct`** / **`on_word_wrong`**: functions called after each choice with `{wordIndex, wordsSelected}`. If they return HTML, it replaces the prompt (after a correct choice) or the redo message (after a mistake). Use them for guided practice, or to show the sentence building up.
- **`show_key_labels`**: key badges under the two words.
- **`error_message`**, **`redo_message`**: the feedback text.
- **Styling:** the prompt is `#status`, the words are `#maze-left-word` and `#maze-right-word`, and feedback is `#feedback`, so the display can be restyled with CSS. For an example, see the kid-friendly theme in jspsych-maze's `source/styles/kid.scss` ([A-maze for kids](kid-friendly.md)).

## Collecting data

The plugin doesn't save data itself; use whatever your platform provides. The demos include `submit.js`, which submits data once to [Proliferate](https://proliferate.alps.science) at the end of the experiment, or when the page is closed early. [kid-maze-passages](https://github.com/vboyce/kid-maze-passages) runs on Children Helping Science / Lookit, which saves jsPsych data automatically.
