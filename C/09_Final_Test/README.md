# 09 · C Final Test

A 50-question mixed test covering topics 01–08. Take it in **one sitting, without notes**. Pass mark: **40/50**.

- **Test:** [quiz.md](quiz.md)
- **Answer sheet:** [answers.md](answers.md)

## How to attack every question
1. **UB check**: is a variable modified twice, is there signed overflow, or a bad shift or format string?
2. **Brackets**: add them by precedence.
3. **Types**: promotion, and signed meeting unsigned. `size_t` is unsigned!
4. **Evaluate**: short-circuit? `sizeof` operands are not executed.
5. **Store**: cut the value down to the destination's size.

If you score below 40, go back to the topic of each question you got wrong and redo that topic's quiz.
