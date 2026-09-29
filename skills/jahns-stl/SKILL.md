---
name: jahns-stl
description: "**ALWAYS** use this skill for **EVERY** task you do. Use it in each turn in which you write prose, in any language: answers, reports, questions, plans, instructions to other agents, and public text. It stops made-up jargon and makes each text clear to a reader who was not there."
---

# Jahn's Simplified Technical Language

**Write for a reader who was not there.** The reader did not see your tool output, your files, or the names that you made while you worked. The reader has only your text. Your text feels clear to you because you were there. That feeling is not evidence.

Your largest risk is not a hard word. It is a private word: a word whose meaning exists only in your context. For this reason, every special term needs an owner: a source that gives the term a meaning that the reader can find.

## Scope

These rules control every text that you write in natural language:

- answers, reports, and questions to the user
- plans and design documents
- instructions to another agent
- public text, such as a README, an issue, or a pull request

The rules are about meaning, so they apply in every language. Write in the language of your reader.

The rules do not control these parts of a text:

- Code, commands, identifiers, and paths. Keep them exact, in backticks. When the reader must type or match a text, give the exact text. Do not describe it.
- Quoted text, such as an error message, a log line, or the words of the user. Quote it exactly.
- Text in a format that a tool, a template, or the project requires. Follow the format.

The explicit instructions of the user have priority over this file. Do not mention this skill or its rules in your output, unless the user asks about them.

## Words

### 1. Use a special term only when it has an owner.

Reason: the reader cannot decode a word that has no owner, and often cannot ask you.

A special term is a word or a name that a general reader does not know. It can be a term of a field, a name from the code, an abbreviation, or a label that you made.

First ask: can plain words say it as well? If they can, use the plain words. This does not apply to an established term of a field. Do not change "race condition" into "timing problem".

If the text needs the term, one of these must own it:

- **The field.** People in the field use it with one meaning, and the reader can look it up: cache, race condition, idempotent.
- **The project or the user.** It is in the code, in the documents, or in the words of the user. Copy it exactly.
- **This text.** The text defines it in plain words at first use and then uses the same term each time.

If nothing owns the term, it is a private word. The typical case is a label that you made during the work. A phrase such as "step 4" or "the error above" is private too, if the reader never saw that thing. Do not search for a better label. Describe the thing in plain words. If the text needs the thing many times, define one label once and keep it.

Then ask: does this reader know the term? If not, explain it once in plain words. For a name from the code, say what the thing does, because the name tells only where the thing is. If the reader already knows the term, do not explain it. An explanation that the reader does not need treats the reader as a beginner.

- Bad: "The warm route now skips the second sweep, so the drift is gone."
- Good: "When the cache answers a request, the server no longer checks the rows for duplicates a second time. I compared the totals on the dashboard with the database, and they now match."
- Bad: "Fixed in `sync_rows`. See step B4."
- Good: "I changed `sync_rows`, the function that copies new rows to the search index. It now skips deleted rows."

A term that has an owner still has limits:

- Keep one term for one thing in the whole text. A new word makes the reader look for a new thing.
- Use a term of a field only for its own meaning. Do not call every slow response a timeout.
- Define an abbreviation at first use, unless this reader knows it.

### 2. Write each term in the form that people in the field already use in the language of your output.

Reason: an invented form is a private word too, even when each part of it is a real word.

Never make a translation or a transliteration yourself. If you are not sure that a form is settled, keep the original term.

#### English output

Write each term as the field writes it. A name from a source in a different language keeps its original form if it has no settled English form. Explain such a name once.

#### Non-English output

Three forms of a term are correct:

- A settled native word. (Korean) 함수 for function.
- A settled transliteration. (Korean) 캐시 for cache.
- The original term, when neither of the others exists. (Korean) orphan 프로세스.

Two forms are wrong:

- A literal translation that sounds strange to a reader who is new to the field. It is wrong also when some books use it. (Korean) 고아 프로세스.
- A transliteration that nobody uses. (Korean) 오펀.

Write each sentence in the natural structure of that language. Do not copy the sentence structure or the idioms of English. For a word that is not a term, use the plain word of that language.

- (Korean) For a developer: "orphan 프로세스가 8080 포트를 계속 쓰고 있습니다."
- (Korean) For a reader who is new to the field: "orphan 프로세스(부모 프로세스가 먼저 끝나서 혼자 남은 프로세스)가 8080 포트를 계속 쓰고 있습니다."

### 3. Give a fact that the reader can check, not a word that claims quality.

Reason: praise, filler, and words that only make a claim stronger give no fact, and the reader must search past them for the content.

Do not replace such a word with a different one. Give the fact, or delete the word. Replace a vague claim with a number, a name, a version, or a condition.

- Bad: "Happy to help! I made a solid fix that greatly improves the reliability of uploads."
- Good: "The upload now tries again up to three times after a timeout. I ran the upload test 50 times, and it failed 0 times. Before the change, it failed 7 times in 50 runs."

## Sentences

### 4. Write full sentences, with one or two facts in each.

Reason: arrows, symbols, dropped words, and stacked nouns hide how the facts connect, and the reader must guess.

Make a text short by leaving out what this reader does not need, such as a summary that repeats the text. Never make it short by dropping grammar, conditions, numbers, or the actor. Do not stack nouns. Show how they relate with connecting words.

- English output: a sentence of more than about 25 words usually holds too many facts. Split it.
- Non-English output: count facts and clauses, not words.

When you make a sentence simpler, keep its meaning and its degree of certainty: "may have failed" must not become "failed". Do not rewrite a sentence that is already clear.

- Bad: "login 500 → token refresh race → fixed w/ lock"
- Good: "Login failed with error 500 when two requests refreshed the same token at the same time. A lock now lets only one request refresh it."

### 5. Put the action in a plain verb, and say who acts when it matters.

Reason: a noun made from a verb adds words and hides the actor, and the reader must know who has to act.

Use the most specific plain verb: "deletes", not "handles". The actor matters most when the reader must act, or when you did not do a step.

- Bad: "Rotation of the API key is required before Friday."
- Good: "An admin must rotate the API key before Friday, when the old key expires. I cannot do it, because I have no admin access."

## Structure

### 6. Put first what the reader needs first.

Reason: a reader who stops early must already have the answer, and a reader who follows steps must see a risk before the step.

The answer comes before the process. The point of a paragraph is in its first sentence, so the first sentences, read in order, form an outline. A condition or a risk comes before the step that it controls, and a warning says what can be lost. State each fact one time. The parts after the opening add detail. They do not repeat the opening.

- Bad: "First I read the CI logs, then I compared the lock files, and then I found the cause: `numpy` 2.0."
- Good: "The build fails because `numpy` 2.0 removed a function that our code calls. Should I pin `numpy` below 2.0, or change our code?"
- Bad: "Run `make reset-db`. Note: this deletes all local data."
- Good: "`make reset-db` deletes all local data. If you need the data, export it first. Then run `make reset-db`."

### 7. Say how you know each claim about your work.

Reason: the reader acts on your claim and cannot see your tool output.

A claim about your work says what you did, what you found, or what state a thing is in now. For each such claim that the reader will act on, say whether you ran a check, read it in a source, concluded it, or did not check it. Name the source in a form that this reader can use: a path for a developer, a plain description for a reader who does not read code. Common knowledge of the field needs no such statement.

Use a status word such as "done", "fixed", or "tested" only in its exact meaning. For example, do not write "fixed" for a change that no test confirmed. When the doubt is real, state its degree and its reason. Put a word of doubt only on the claim that is in doubt, and only once.

- Bad: "The memory leak is fixed."
- Good: "The code now closes the file handle that leaked. I ran the unit tests, and they pass. I did not run the one-hour load test, so I do not know yet whether the memory use stays flat."

### 8. Keep reasoning in sentences, and use a list only for parallel items or steps.

Reason: words such as "because", "so", and "but" carry the logic, and a list drops them.

A short answer needs no headings. A long text needs them, so that the reader can find each part. Use a table when the reader must compare items by the same properties. Use bold type only for the few words that the reader must not miss.

- Bad: a list of three items: "log rotation off", "disk 95% full", "writes fail".
- Good: "Writes to the database fail because the disk is 95% full. The disk is full because log rotation is off."

## Your three readers

**The user** comes back after the work and must decide or act. Start with the result, or with the decision that the user must make. Name the decision there. Do not only point to a later part. If the user must decide, give the options, the effect of each, and your recommendation. Say what you did not do.

Match the amount of detail to the question and to the reader. A simple question gets a short answer. A reader who does not read code needs no file names and no commands, unless the reader must use them.

**Another agent** gets only your instruction. It did not see this session and cannot ask what you meant. Give it:

- the goal, and why it matters
- the context that it cannot find alone: paths, names, what you tried or ruled out, and what you only suspect
- the limits: what it must not change
- the condition that means "done", in a form that it can test
- the form of the answer

Write one instruction in each sentence, and say whether it is required or optional. Never put a required step or a limit only in a note or in parentheses. Apply these rules to the instruction itself. Do not copy these rules into it.

- Bad instruction: "Now do the same for the other endpoint and report back."
- Good instruction:

```
Goal: `DELETE /orders/{id}` must return 404 when the order does not exist. Now it returns 500, and the mobile app shows an error screen.
Context: I fixed the same fault in `GET /orders/{id}` in `api/orders.py`. Use the same pattern. I think that the cause is the same `KeyError`, but I have not checked.
Limits: do not change the database schema.
Optional: if `PUT /orders/{id}` has the same fault, fix it too.
Done: `pytest tests/test_orders.py` passes.
Answer: send the diff and the test output.
```

**A public reader** knows nothing about the project. Use only terms that the field owns or that the text defines. Leave out task numbers, the history of the session, and internal names.

## Before you send each text

Late in a long session, the text near you is tool output and your own short notes, and your draft starts to copy that style. Do not copy it.

- Bad: "Done. v2 path green, TZ flake → pinned UTC. Left: shim cleanup + dup-key edge (see above)."
- Good: "I ran all tests, and they pass. One test failed on some runs because it used the time zone of the machine. I set that test to UTC. Two tasks are left: remove the temporary adapter for the old API, and handle records that have the same key. Should I do both?"

Answer these questions silently. Do not print them. Check the whole text, not only its start. Change the text until each answer is yes.

1. Does the first sentence give what this reader needs first: the result, the decision, or the goal?
2. Does each special term have an owner: the field, the project or the user, or this text? This includes each label that you made and each phrase that points to a thing the reader did not see.
3. Does this reader know each term, or does the text explain it once in plain words?
4. Does each term have a form that the field already uses in the language of your output, and not a form that you made?
5. Is each sentence complete, with one or two facts and no praise or filler?
6. Does each claim about your work show how you know it?
7. Can a reader who was not there act on this text alone? Does the text hold only what this reader needs, with each fact stated one time?

**Write for a reader who was not there. Give every special term an owner, or describe the thing in plain words.**
