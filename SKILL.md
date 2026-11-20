---
name: language-study-trainer
description: Set up and run a personal language study system (spaced-repetition trainer, word sets, rules, daily lessons) for any language, level, book, native language and daily time.
---

# Language study trainer

Claude acts as the learner's teacher and vocabulary trainer for the language they are learning (the "target language"; the original setup was Turkish). Everything the learner practises lives in one interactive trainer page (an Artifact with a progress database), not in the chat. The chat is for explanations, questions and new book material.

## 1. Setup interview (first use only)

Ask these in one short message, with sensible defaults so the learner can answer "defaults are fine". Save the answers to project memory before building anything.

| Setting | Default | Used for |
|---|---|---|
| Language being learned (target language) | ask | Everything: words, rules, checking, pronunciation, special-letter keys |
| Native / translation language | ask | All translations, UI text, explanations, feedback |
| Starting level (A1, A2, B1...) | A1 | Where the word and rule sets start |
| Book(s) (title, or upload the PDF) and where they are in the current one (page/unit) | none | Order of lessons, what to drill |
| Minutes per day | 15 | Daily goal (counter turns green), lesson length |
| New words per day | 12 | Daily card limit |
| Own notes to include (lesson notes, word lists) | none | Extra words, questions to answer, mistakes to correct |
| How to address them (for gendered languages, e.g. Russian "знала/знал") | neutral | UI and feedback wording |
| Time zone | ask or infer | Dates, "today", reminders |
| Optional: interests or goal (travel, work, exam), study days per week | none | Topics for extra word sets and sentence tasks |

**Level scope rule:** start from the level the learner is studying when they begin. A learner who starts at A2 gets A2 material, not A1 sets, unless they ask for a review of a lower level. Levels then accumulate: when they move on to B1, add B1 sets and rules and keep the A2 ones (with their progress) in the trainer. Within the current level, drill only what the book has covered up to their current page; read the rest of the book so later lessons follow it.

**No book or notes:** if the learner has no book and no notes, build the programme yourself. Follow the CEFR level descriptors and the curriculum or syllabus of the target language's official body for teaching it to foreigners (for example Yunus Emre Enstitüsü for Turkish, Goethe-Institut for German, Instituto Cervantes / Plan curricular for Spanish, Alliance Française / France Éducation international for French), plus common best practice: high-frequency vocabulary first, thematic sets, grammar in a sensible order with lots of short practice, spaced repetition, speaking and writing from day one. Write the plan into `book-plan.md` (units, topics, rules in order, sources you based it on) and use it the same way as a book.

**More books later:** the learner can add another book at any time (the next book for a new level, or an extra one). Read it, add a section for it to `book-plan.md`, continue rule numbering, and tag each new word set and rule with its book and level so the trainer can group or filter them. Old material and progress stay.

## 2. Build the study material

Create a folder (e.g. `<Language> study/`) in the project files:

- `words/NN-<topic>.md` (note level and book at the top): thematic word sets (greetings, family, food, places, verbs of movement...), each a table target word | translation. Take words from the book up to the current page and from the learner's notes; correct misspellings in their notes and say so. Add a few high-value extras for the level.
- `rules/NN-<slug>.md`: one grammar rule per file, in book order. Sections: **Rule** (formula/table), **Explanation** (plain language, common traps), **Examples** (every example with its translation: `- **target sentence** — *translation*`), **Exercises** (A, B, C...), **Answers** at the bottom. Mark the rule the learner is on now with "← YOU ARE HERE". Under the title, name the book and pages (e.g. «Yabancı Dilim Türkçe 1», p. 5, 13, 31); don't mention the learner's own notes as a source.
- `book-plan.md`: one section per book, roadmap of the rest of it, with planned rule numbers and extras Claude will add.
- `answers-and-corrections.md`: answers to questions found in the learner's notes plus a table of corrections (what they wrote, correct form, meaning).
- `progress/tracker.md`: human-readable snapshot (where in the book, rules due, session log).
- `README.md`: how everything works, in the learner's language.

## 3. Build the trainer

If the learner has the **trainer template** (`trainer-template/index.html` from the shared kit), start from it: fill the `CONFIG` block at the top: target language (targetLang, targetLabel, targetCode, special `letters` for the on-screen keys, `letterMap` of special letter to plain letter, subject `pronouns` that may be dropped), native language (nativeLang, nativeCode), level, book, covered, dailyMinutes, newPerDay, and the `ui` word forms. The UI text is in Russian: if the learner's language differs, translate all UI strings; adjust gendered wording. For a language without special letters, leave `letters` empty. For a non-Latin script, add a keyboard hint instead of letter keys and accept transliteration only as "almost". Otherwise build a page with the same behaviour as below. Data files published next to the page:

- `words.json`: `{"sets":[{"id","ru","tr","level","book","words":[["target word","translation"],...]}]}` (field names are historical: `tr` = target language, `ru` = learner's language). Sets and rules the learner already covered before starting (book pages up to where they are now, their own notes) get `"covered":true`: they start as "familiar" (yellow, level 3), not "new". Only material from further on is new, then goes to "learning" and up. Also build word sets for the rest of the book (without `covered`), in book order, so upcoming words are already in the trainer as "new".
- `rules.json`: `[{"id":"01","title","file":"01-....md","book":"Book: p. 7–8","level":"A1"}]`, with the rule files under `rules/`. Upcoming rules from the book plan go in too, with `"planned":true` and no file yet: the home screen shows them as "soon" with a "I want to learn this" button.
- `grammar.json`: `{"01":[[question, answer("a|b" for alternatives), hint/rule, translation shown after answering], ...]}`, about 8 auto-checked items per rule.
- `exercises.json`: the exercises from each rule file as fields to fill in on the rule page: `{"21":[{"title":"A. Complete the sentence.","items":[{"q":"Ben futbol oynamıyorum, çünkü sev___.","a":"sevmiyorum","note":""}]},{"title":"B. Answer about yourself.","items":[{"q":"Niçin Türkçe öğreniyorsun?","free":true}],"example":"Çünkü Türkiye'de yaşıyorum."}]}`. `a` holds alternatives separated by `|`; optional words go in brackets. Keep it in sync with the rule files.
- `writing.json`: `[{"r":"05","t":"task in learner's language","m":"model answer"} or {"r","t","free":true}]` for sentence writing checked by Claude.
- `docs/plan.md`, `docs/notes.md`: copies of the book plan and the answers/corrections.

Publish as a private Artifact with capabilities `db` (progress), `sample` (Claude checks free answers and new words) and `tts` (pronunciation). Progress lives in db doc `progress/main`: `{items:{"w:<target word>"|"r:<NN>":{b,due,c,w,last,first,known}}, daily:{date:seconds}, log:[], custom:{add,edit,del,sets}, requests:[{key,kind:"rule"|"topic",id,title,level,example,d,done}]}`. Keep target-language word keys stable when editing words.json, or progress detaches.

### Required trainer behaviour

- **Spaced repetition:** each word and rule has a level 0–7. Correct raises it by 1, a mistake drops it by 2. Next review after 0, 1, 2, 4, 7, 14, 30, 60 days. Level 5+ counts as learned. Rule tests: ≥80% correct raises the level, 50–79% repeats tomorrow, below 50% drops it by 2.
- **"Know for sure":** a button on cards, writing and rules retires the item from all drills. A "Words I know for sure" page (grouped by set, each group with "bring the whole set back") and a "Rules I know for sure" page list them, and any can be brought back. Marking a rule known first asks "Are you sure?" (it leaves tests, sentences and essays). On the home page known rules stay in the rules list with a "known for sure" label, and fully learned word sets stay with a "learned" mark; a "hide" / "show (N)" button under each list hides them. The choice is saved with the progress, so it is the same on every device. Inside a word set, a "I know the whole set" button (with a confirm) retires every word in it, and "bring the whole set back" undoes it.
- **Status colours:** every word in a set list and every rule in the rules list shows a coloured status label matching the legend: new (grey), learning (red, level 0–2), familiar (yellow, level 3–4), learned (green, level 5+), known for sure (teal). Rules show no level numbers, only the same coloured label (due rules are listed under the "Due today" counter, not marked in the list). Hovering shows the level and next review date. Covered material that hasn't been practised yet shows as familiar (yellow); a correct answer raises it to 4, a mistake drops it to learning.
- **"Due today" counter** at the top is tappable: it opens the list of words (grouped by set, with their status colour) and rules due today, with buttons to drill exactly those.
- **Today's session** on the home screen: due words plus up to N new words (the learner's own words first, then book order) and due rules. No "+10" button: once today's words are done, a few more new words (5) are loaded from the lists automatically.
- **New word set:** a "+ New word set" button on the home screen. The learner names a topic, then either lets Claude generate the most useful words for it at their level (10/15/20, skipping words they already have) or types their own words (one per line: target, native, or both); Claude fills in the missing side and corrects spelling. A preview with checkboxes comes before the set is created. Learner-made sets live in `custom.sets`. Order: cards or matching, then writing, then grammar test, sentences and mini-essay. Every mode button has a one-line description under its name (what you see, what you type, who checks it), so the modes can be told apart at a glance.
- **Modes:** flashcards (Knew / Didn't know / Know for sure), matching both ways (target → translation and translation → target, either column can be tapped first), write in the target language from the translation (on-screen keys for its special letters, listen button), grammar tests, free sentence writing checked by Claude.
- **Two writing modes:** "Sentences" (short tasks from writing.json: translate or answer in one or two sentences) and "Mini-essay". For a mini-essay Claude picks words the learner is learning that fit one everyday topic, plus a due or covered rule. It writes the task in the learner's language and a short model text with its translation. The model is shown at the start about half the time, otherwise behind "Show example". The size is not fixed: Claude picks the number of sentences and words within a range for the level (A1 3–6 sentences, A2 5–9, B1 7–12, B2 9–16, C1 12–22; 3–12 words), going longer when recent essays went well and many words are learned, shorter after weak essays. Word chips tick as each word is used in any form (case endings, possessives, conjugated verbs without the infinitive ending, consonant changes like kitap → kitabı; on-screen letter keys update them too), and a live counter shows sentences written. Claude then checks every sentence: corrected text, errors with reasons, translation, and whether each word was used correctly. Correctly used words count as correct answers; misused ones as mistakes. The sentence results grade the rule, and mistake-topic offers apply. A draft is saved for resume.
- **Listening and Reading:** two home-screen modes. Claude writes a text at the learner's level (a dialogue between two named people or a short announcement for listening; a story, letter, post or description for reading), using covered grammar and words they are learning, with a translation of every line, then true/false statements and 4-option multiple-choice questions in the target language (each with a hidden translation). Length grows inside a range per level (listening A1 30-90 words up to C2 350-650; reading A1 50-130 up to C2 600-1000) with the number of learned words and the last three scores of that mode. Listening plays the text through the `tts` capability with the target language set on every line (`language: targetCode`) and a different voice per speaker, with Play, Pause, Stop and Slower (rate 0.8); buttons "Show text" and "Translation" (line by line, the spoken line highlighted). Where `tts` is unavailable (Safari, Firefox, mobile apps) it says so and shows the text. One question at a time with "Question N of M · correct K", an explanation after each answer, and the usual summary with the full text and translation. Words in the text can be tapped to add them.
- **A missed word never comes back in the same run.** It is due again next session and can appear in other exercises.
- **Hints with partial credit:** writing and grammar offer letter-by-letter hints; a correct answer with hints counts as half (or reduced) credit.
- **Rule exercises are interactive:** each exercise item on a rule page has its own field and a check button (Enter also checks). The learner can type only the missing part, the whole word or the whole sentence. Exact answers are checked instantly; anything else, and free answers, are checked by Claude. Feedback shows the full correct sentence, why it was wrong, and a translation. A progress line shows done/correct, and finishing all items counts towards the rule's level. The written answers stay available under "show all answers".
- **After each answer** show the correct answer with its translation and why it was wrong (letter difference, wrong ending, which rule).
- **Synonyms:** another correct target-language word with the same meaning counts as "almost", with an explanation of the difference. Plain letters instead of special ones (Turkish c for ç, German a for ä) also count as "almost".
- **Word order:** accept only grammatically correct orders, never any permutation. List valid alternatives explicitly.
- **Short tests** (about 10–12 items) with a progress line "N of total, correct X of Y" and a progress bar, a "finish now" button that counts what was done.
- **End summary:** every item right or wrong with the reason, and when there are many mistakes, links to the rule or word set to repeat.
- **Add to learn:** in any exercise (grammar tasks, sentence writing, feedback, translations shown after an answer) the learner can tap a word they don't know and choose "add to learn". Claude gives its dictionary form and translation, and it goes into a "Words from exercises" set (or the matching topic set) as a new word.
- **Where a new word goes:** when the learner adds a word from an exercise, Claude suggests the existing topic set it fits, and the learner picks that set or "Words from exercises". Inside "Words from exercises" a "Sort into sets" button asks Claude for a set for each word (editable before moving). When 5 or more words share a topic no set covers, Claude offers a new set for them. Learner-made sets live in `custom.sets` [{id,ru,tr}] and moved words keep their progress.
- **Hint for one word:** the learner can tap a single word in the task to get a hint for just that word (its translation, or the first letters of the needed form), instead of one general hint button. Each word hint lowers credit for that answer a little.
- **Add / edit / delete words** inside a set: the learner can type only the target word, only the translation, or both; Claude fills in or checks the pair, flags duplicates, and explains corrections. Saved in `custom`, applied over words.json.
- **Daily minutes:** active time is counted while a drill is open; the minutes counter turns green at the daily goal; show a streak of study days.
- **Navigation and levels:** every sub-page (rule, word set, lists) has a "← home" button. A rule page starts with its own title (e.g. "11. «I am a doctor»: ..."), not a generic "Rule 11". When there is more than one level, the home screen groups word sets and rules under level headings that can be folded (remembered per browser).
- **New and old in parallel:** the learner never has to finish all old rules before new ones come. Upcoming (planned) rules are listed with "I want to learn this"; a tap saves a request. Due reviews keep running alongside.
- **Mistakes on an old topic:** when Claude checks a free answer or exercise, it also names the grammar topic of the main mistake (rule id, name, CEFR level, any level, also lower than the current one). If that rule exists in the trainer and isn't due, feedback offers "Repeat this rule? Yes" (makes it due today, takes it out of "known"). If no rule exists, it offers "Add this rule to learn? Yes", which saves a request.
- **Resume:** an unfinished exercise is saved in the browser and offered on the home screen.
- Progress is also cached in the browser and merged with the server copy.

## 4. Daily lesson routine

At the start of every lesson, read `progress/main.requests`. For each open request, prepare the rule (rule md file, exercises.json, grammar.json items, new words if needed; a lower-level topic gets its own level tag), drop `planned` for a planned rule, mark the request `done:true`, and tell the learner what was added. New book rules are also added as the book advances, without waiting for old ones to be learned.

Length follows the learner's daily minutes (default 15):
1. About a third: the trainer's "Today's session".
2. The rest in chat: one due rule to review or the next book pages, a short explanation, then exercises that go into the trainer (add grammar/writing items) rather than long chat drills. A few short sentence-writing tasks.
3. Afterwards: read `progress/main`, update rule levels for work done in chat, update `progress/tracker.md`, and move "← YOU ARE HERE" when the book advances. New rules get the next number.

When the learner says they know something for sure, retire it and stop drilling it.

## 5. Changing the trainer

If the learner also shares this kit with others, a behaviour change they ask for goes into both their own trainer and the kit (SKILL.md and the trainer template), so the two stay the same.

Publishing an update reloads the page and loses an exercise in progress. Before republishing, ask whether the learner is in an exercise and whether to publish now or after they finish (a short decision card works). When fixing or adding content, check spelling and translations carefully; explain corrections in the learner's language.

## 6. Memory

Save to project memory: the setup answers, trainer URL, folder layout, current book page and rule, and any preference the learner gives (e.g. "no chat drills", "more listening"), so a later session can continue without asking again.
