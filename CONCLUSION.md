# Lab 05 — Conclusion

<!--
Read this before you write.

This file is graded by your instructor, by hand, and it is worth 6 of the 15 points.

- Type it yourself. Do not use a chatbot, an AI assistant, an AI autocomplete or a "rewrite my
  text" tool for any part of it, not even for grammar. English that is plain and a little
  clumsy but yours is better than polished text that is not.
- Use your own numbers. Every number you quote must be one your own notebook printed. Copy
  the number exactly as it appeared. A number your notebook did not print is marked as wrong.
- Answer in your own words, about 80-200 words per question. A short answer that uses your
  numbers beats a long answer that could have been written by anyone.
- Delete these comment lines if you like. Keep the three headings and the last line.
-->

## 1. Which layer changed your results the most?

Layer 1 changed the results the most. Before Layer 1 (bare), the agent got 16 correct, 4 wrong, and 4 no answer out of 24. After Layer 1, it got 18 correct, 6 wrong, and 0 no answer. This layer changed what the agent *can do*. By catching tool errors (like an unknown region name) and feeding the error message back to the model, it prevented the loop from crashing. This allowed the agent to recover and answer questions it previously abandoned, turning 4 "no answer" results into attempts. However, because the model sometimes guessed incorrectly after the error, this layer also turned some honest "no answer" crashes into "wrong" answers.

## 2. One failure the full harness did not fix

Question q17 ("By how many percentage points did the unemployment rate in Aktobe fall between 2020 and 2023?") is still wrong under Layer 3. Looking at the trace, the model went wrong at the calculation step. Instead of performing a simple subtraction to find the difference in percentage points, it used the `calculate` tool to compute a percent change formula: `((later - earlier) / earlier * 100)`, yielding ~8.89. The Layer 3 grounding harness failed to catch this because the number "8.89" was literally present in the tool output. Grounding only checks if the number came from a tool, not whether the correct logical formula was applied. I would add a semantic formula validator to the harness to cross-reference the question type with the arithmetic operation used. This would cost extra model calls or tokens to parse the question intent and might trigger a new way to fail if the harness rejects a logically sound but arithmetically mismatched answer.

## 3. Workflow or agent for the monthly ranking?

I would ship the fixed workflow. According to the Part 1 measurements on the 15 fixed-shape questions, the workflow was strictly superior: it was more accurate (13/15 correct vs. 9/15 for the bare agent), required far fewer resources (15 model calls vs. 26, and 3,852 tokens vs. 19,835), and was significantly faster (15.6 seconds vs. 48.3 seconds). I would only choose the agent if the monthly report needed to answer "open" shape questions, such as calculating multi-step growth percentages, handling missing data, or resolving ambiguous aliases, which require dynamic, multi-step reasoning that a rigid JSON extraction pipeline cannot handle.

I wrote this file myself, without any AI tool.