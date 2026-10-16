# Two-axis code review

Review the diff from a user-supplied fixed point to `HEAD` along two independent axes:

- **Standards:** Does the change follow documented repository standards and avoid meaningful code smells?
- **Spec:** Does the change implement its originating issue or specification correctly and within scope?

Run the two axes in parallel sub-agents when available, then report them separately. The skill needs only a Git checkout and available repository evidence. Do not require a plugin, setup command, issue tracker configuration file, or another skill.

## Prepare the review

1. Use the fixed point the user supplied: a commit SHA, branch, tag, or expression such as `HEAD~5`. If absent, ask for one. Do not treat `all` as a fixed point.
2. Confirm `git rev-parse <fixed-point>` succeeds. Review `git diff <fixed-point>...HEAD` (three dots compare from the merge base) and `git log <fixed-point>..HEAD --oneline`. Stop if the diff is empty. State that this range excludes uncommitted changes; include them only when the user explicitly requests that scope.
3. Find the originating spec. Check issue references in commit messages, any spec path supplied by the user, and matching files under `docs/`, `specs/`, or `.scratch/`. Fetch a referenced issue with available tools when accessible. If no spec is found, ask the user for it. If they confirm none exists, skip the Spec axis and say so.
4. Find applicable repository standards, such as `AGENTS.md`, `CONTRIBUTING.md`, and `CODING_STANDARDS.md`. Follow repository rules for accessing any private guidance when those rules apply. Treat the smell baseline below as heuristics; documented repository standards override it. Skip checks enforced by tooling.

## Smell baseline

Use these Fowler-style smells as judgment calls, never automatic violations:

- **Mysterious Name:** A name does not reveal what a function, variable, or type does or holds. Rename it.
- **Duplicated Code:** The same logic shape appears in multiple changed places. Extract the shared logic.
- **Feature Envy:** A method works mostly with another object's data. Move the behavior closer to that data.
- **Data Clumps:** The same fields or parameters repeatedly travel together. Give them a type.
- **Primitive Obsession:** A primitive stands in for a meaningful domain concept. Introduce a small domain type.
- **Repeated Switches:** The same condition cascade recurs. Centralize it or use polymorphism.
- **Shotgun Surgery:** One logical change requires scattered edits. Gather related behavior.
- **Divergent Change:** One module changes for unrelated reasons. Separate those responsibilities.
- **Speculative Generality:** An abstraction serves no current requirement. Remove or inline it.
- **Message Chains:** A caller reaches through a long sequence of objects. Hide the traversal behind a method.
- **Middle Man:** A layer mostly forwards calls. Call the target directly.
- **Refused Bequest:** An implementation ignores most inherited behavior. Prefer composition.

## Review in parallel

Give each sub-agent the exact diff command and commit list. Keep their assignments separate:

- **Standards:** Provide applicable standards files and the full smell baseline above. Ask for documented-rule violations with file and rule citations, plus concrete smell findings labelled as judgment calls with the relevant diff hunk. Skip tool-enforced issues. Limit to 400 words.
- **Spec:** Provide the spec path or contents. Ask for missing or partial requirements, behavior outside scope, and requirements implemented incorrectly. Require a spec citation for every finding. Limit to 400 words.

If parallel agents are unavailable, perform the axes separately yourself. Check each finding against the diff before reporting it.

## Report

Use `## Standards` and `## Spec` headings. Keep the findings separate; do not merge or rerank them across axes. End with one line giving the finding count and worst issue for each axis. If the Spec axis was skipped, mark it `No spec available` rather than implying a pass.
