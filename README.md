# jahns-stl: Jahn's Simplified Technical Language

jahns-stl is a plugin for Claude Code and Codex with one skill and nothing else: no scripts, no hooks, and no MCP server. The skill is a writing guide for each text that your agent writes in prose instead of code, such as a report, a plan, or an instruction to another agent. It tells the agent not to use names that only the agent knows, and to write complete sentences that put the result first.

## Before and after

Here is the same status report from an agent without the skill and with it. The pair is an example written for this README, not recorded output.

**Without the skill**

> Dupe fix landed. Added the replay guard on the P2 path, so it's idempotent now; suite passes. Ghost rows from Monday's run still need a cleanup pass.

**With the skill**

> The nightly job that copies orders into the accounting system no longer copies an order twice. When the accounting system timed out, the job sent the whole batch again, including the orders that had already arrived. Now the job skips each order that the accounting system already has. I ran the tests for this job, and they pass. The duplicate orders from Monday night are still in the accounting system. Should I delete them?

The first report uses names that exist only in the agent's session, such as "replay guard", "P2 path", and "ghost rows". The second report gives the result first, describes each thing in plain words, says which check ran, and ends with the decision that the user must make.

## Install

In Claude Code, run these two commands:

```text
/plugin marketplace add Dev-Jahn/jahns-cc-marketplace
/plugin install jahns-stl@jahns-cc-marketplace
```

For Codex, run these two commands in a terminal:

```sh
codex plugin marketplace add Dev-Jahn/jahns-codex-marketplace
codex plugin add jahns-stl@jahns-codex-marketplace
```

After the installation, start a new session. The skill takes effect only in a session that starts after the installation.

## What the skill tells the agent

The central idea is that the reader was not there. The reader did not see the tool output, the files, or the names that the agent made while it worked. For this reason, the skill says that every special term needs an owner: a source that gives the term a meaning that the reader can find. The owner is one of these:

- the field, for an established term such as "cache";
- the project or the user, for a name in the code, in the documents, or in the words of the user;
- the text itself, when it defines the term in plain words at first use.

The skill calls a term without an owner a private word, and it tells the agent to describe the thing in plain words instead.

The skill has eight rules in three groups: words (1 to 3), sentences (4 and 5), and structure (6 to 8). The table gives each rule in the words that the skill uses to address the agent.

| Rule | Failure that it is meant to prevent |
|---|---|
| 1. Use a special term only when it has an owner. | A name that the agent made during the work appears as if the reader knew it. |
| 2. Write each term in the form that people in the field already use in the language of your output. | The agent makes its own translation or transliteration of a term, and nobody in the field uses that form. |
| 3. Give a fact that the reader can check, not a word that claims quality. | Praise, filler, and words such as "robust" take space and give no fact. |
| 4. Write full sentences, with one or two facts in each. | Arrows, symbols, dropped words, and stacked nouns hide how the facts connect. |
| 5. Put the action in a plain verb, and say who acts when it matters. | A phrase such as "approval is needed" hides who must approve. |
| 6. Put first what the reader needs first. | The result comes after paragraphs about the process, or a warning comes after the step that causes the risk. |
| 7. Say how you know each claim about your work. | The agent writes "fixed" for a change that no test confirmed, and the reader trusts it. |
| 8. Keep reasoning in sentences, and use a list only for parallel items or steps. | A list drops words such as "because" and "so", and the logic between the items is lost. |

Before it sends each text, the agent must answer seven questions about the text without printing them, and change the text until each answer is yes.

## Output that is not English

The rules are about meaning, so they apply in every language, and the agent writes in the language of its reader. For each term, the skill asks for the form that people in the field already use in the language of the output. The agent must never make a translation or a transliteration itself. If it is not sure that a form is settled, it keeps the original term. The examples in the skill are in Korean, but the policy is the same for each language.

| Form of the term | Correct | Example (Korean) |
|---|---|---|
| A settled native word | Yes | 함수 for "function" |
| A settled transliteration | Yes | 캐시 for "cache", which is the English word in Korean letters |
| The original term, when neither of the forms above exists | Yes | orphan 프로세스 for "orphan process" |
| A literal translation that sounds strange to a reader who is new to the field, also when some books use it | No | 고아 프로세스, a word-for-word translation of "orphan process" |
| A transliteration that nobody uses | No | 오펀, "orphan" in Korean letters |

The agent also writes each sentence in the natural structure of the output language, not in the structure of English. It explains a term once when the reader needs the explanation:

- For a developer: "orphan 프로세스가 8080 포트를 계속 쓰고 있습니다." This means "An orphan process is still using port 8080."
- For a reader who is new to the field: "orphan 프로세스(부모 프로세스가 먼저 끝나서 혼자 남은 프로세스)가 8080 포트를 계속 쓰고 있습니다." The part in parentheses explains the term: a process that is left alone because its parent process ended first.

## What the skill does not do

- It has no fixed list of allowed or banned words. It gives the agent tests to apply with judgment, because a fixed list limits that judgment, and the typical words of a model change with each release of the model.
- It has no static checker. No program reads or changes the output of the agent. The result depends on how well the model follows the written guide.
- It does not change code, commands, identifiers, or paths. It does not change quoted text, such as an error message, a log line, or your own words. It does not change text in a format that a tool, a template, or the project requires.
- Your explicit instructions have priority over the skill.
- It does not copy its rules into the instructions that the agent writes for other agents. It controls only how the agent writes each instruction.
- The agent does not mention the skill or its rules in its output, unless you ask about them.

The method is not proven. The studies of controlled languages that we read report a modest and mixed effect: better comprehension of complex procedures in some studies, and no consistent effect in others. Known costs are a slightly longer text and, for some readers, a style that seems to talk down to them.

## How the skill stays in control

The description of the skill tells the agent to load the skill in each turn in which it writes prose. Claude Code keeps a loaded skill in the context. After Claude Code summarizes a long conversation, it attaches only the first 5,000 tokens of the skill again. For this reason, the skill file has at most 14,000 bytes. In one measurement in Claude Code, a skill file of 13,014 bytes added about 4,300 tokens, so a file of 14,000 bytes is about 4,600 tokens. The CI enforces the limit of 14,000 bytes. Codex loads the skill again in each turn, based on the description.

## Tests

We compared texts that agents wrote with the skill and without it. The tests show a direction. They do not measure the size of the effect, because each condition has one text for each task.

### Method

For each of five writing tasks, one agent wrote the text without the skill. A second agent of the same model read the skill file first and then wrote the text. A judge (Claude Opus 5.5) compared the two texts. The judge did not know which text used the skill.

The judge worked in two steps. First it read each text without the source material and listed each term that it could not decode. Then it checked each text against the source material. It gave 1 to 5 points for each of six properties, so the maximum is 30 points:

- the text gives first what the reader needs first;
- the reader can decode each term;
- the sentences are complete, with one or two facts in each;
- the reader can tell what the writer verified and what the writer only believes;
- the statements agree with the source material;
- the amount of text and the tone fit the reader.

### Result for the skill file of version 0.1.0

| Task | Language | Model of the writer | Points with the skill | Points without the skill | Characters with / without |
|---|---|---|---|---|---|
| Explain a tool to a user who sees it for the first time | Korean | Claude Sonnet 5.5 | 24 | 21 | 3,287 / 3,580 |
| Write the instruction for another agent | English | Claude Sonnet 5.5 | 26 | 21 | 4,464 / 4,556 |
| Write the final report for a user who is not a developer | Korean | Claude Fable 5.1 | 26 | 23 | 2,665 / 1,418 |
| Write the description of a pull request | English | Claude Opus 5.5 | 26 | 21 | 4,660 / 4,842 |
| Answer a simple question | Korean | Claude Fable 5.1 | 28 | 25 | 461 / 441 |

The judge preferred the text with the skill in each of the five tasks. The texts with the skill got more points for the answer at the start in all five tasks, and more points for the statement of what was verified in four tasks. In four tasks, they had fewer terms that the judge could not decode.

Version 0.1.1 changes one paragraph of rule 1. The agent now judges from the questions, the instructions, and the words of the reader whether the reader knows a term, and it does not ask the reader. We did not run the tests again for this change.

### Known weak point

A text with the skill can be longer than the reader needs. In the table, the report for a user who is not a developer is about 1.9 times as long as the text without the skill, and the judge found facts that the report states two times.

Earlier versions of the skill had this fault in two tasks. In one earlier run, the judge preferred the short answer without the skill, because the answer with the skill was longer and added a statement about its sources that the reader did not need. We made two changes. Rule 7 now applies only to claims about the work of the agent, not to common knowledge. The last question of the check now asks whether the text holds only what the reader needs, with each fact stated one time.

### Test of the loading

We started four sessions of Claude Code 2.1.284 with the plugin loaded: three with Claude Sonnet 5.5 and one with Claude Opus 5.5. The prompts were a technical question, a coding task, small talk, and a question from a beginner. In each of the four sessions, the model loaded the skill itself before it wrote its first answer.

### Limits of these tests

- Each condition has one text for each task. The judge is also not constant: in two runs, the same text without the skill got 23 points and 26 points.
- The texts without the skill were written one time. Each later run compared new texts with the skill against these same texts.
- The writers and the judges are Claude models. We did not test how the skill changes the text of the models that Codex uses. For Codex, the tests show only that the plugin installs and that Codex lists the skill for the model.
- In the comparison, the writer got the instruction to read the skill file. In real use, the model loads the skill from its description. Only the test of the loading covers that step.

## Origin

The idea comes from ASD-STE100 Simplified Technical English (<https://www.asd-ste100.org>), a controlled language of the aerospace industry. It is a form of English that limits the words and the grammar that a writer can use. ASD (the Aerospace, Security and Defence Industries Association of Europe) holds the copyright of the specification, and the names "ASD-STE100" and "Simplified Technical English" belong to ASD. This project is not affiliated with ASD. The project does not reproduce the text of the specification or of its dictionary, and it does not claim conformance to the specification.

jahns-stl takes three ideas from the specification:

- Control of terms by a test and not by a list. The skill gives no list of approved terms. A term passes when it has an owner.
- One name for one thing.
- Short, complete sentences.

The specification assumes a reader who knows little English. The reader of an agent has a different problem: that reader knows little of the context. For this reason, jahns-stl adds three things that an agent needs and that the specification does not cover:

- the answer first;
- a clear separation of what the agent verified from what it only believes;
- guidance for a reader who has no context.

## Development

To change the skill, edit `skills/jahns-stl/SKILL.md`. The CI checks these limits on each pull request and on each push to `main`:

- The file is at most 14,000 bytes.
- The value of `description` in the frontmatter is in double quotes. The value starts with `*`, and a YAML value that starts with `*` is invalid without quotes. If the quotes are missing, Codex drops the skill without an error message.
- The description has at most 1,024 characters.

To release a new version, set the same new version in `.claude-plugin/plugin.json` and in `.codex-plugin/plugin.json`, and push to `main`. The version must be a semantic version such as `0.2.0`. The two manifests must also have the same `description`.

After the push, the CI checks the two manifests and the skill file, and it checks that Claude Code and Codex both accept the plugin. If all checks pass, the CI updates the entry of `jahns-stl` in both marketplaces: `Dev-Jahn/jahns-cc-marketplace` for Claude Code and `Dev-Jahn/jahns-codex-marketplace` for Codex. The entry then points to the new commit on `main` and copies the version and the description from the manifest. If a check fails, the CI does not change the marketplaces.

The update of the marketplaces needs two secrets in this repository: `MARKETPLACE_DEPLOY_KEY` and `CODEX_MARKETPLACE_DEPLOY_KEY`. Each secret is the private part of a deploy key that has write access to one marketplace repository. Each marketplace must already have an entry with the name `jahns-stl`. If the entry is missing, the CI fails and does not create the entry.

## License

MIT. See [LICENSE](LICENSE).
