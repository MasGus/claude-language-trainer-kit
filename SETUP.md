# Language trainer kit: how to start

This kit gives you a personal teacher and trainer in Claude for any language you are learning: word sets and grammar rules from your own book and level, a trainer page with flashcards, matching, writing and grammar tests, spaced repetition, and a short daily lesson.

## What is in the kit
- `SKILL.md`: the instructions Claude follows (how the trainer works, how lessons run).
- `trainer-template/`: an empty trainer page. Claude fills it with your words and rules.
- `SETUP.md`: this guide.

## Steps
1. **Add the skill.** In Claude, open Settings, find Skills, and upload `SKILL.md` (zip it first if asked). Or paste the file to Claude and ask it to save it as a skill.
2. **Create a project** for your language study and add your textbook (PDF) and any lesson notes. Add the `trainer-template` folder too.
3. **Say:** "Set up my language trainer." Claude asks a few questions:
   - the language you are learning,
   - your own language (translations and explanations),
   - the level you are starting from (A1, A2, B1...): word sets and rules start there, and when you move up a level the new ones are added and the earlier ones stay,
   - your book and the page you are on (you can add more books later),
   - how many minutes a day you want to study (default 15),
   - how many new words a day (default 12).
4. Claude reads the book, builds the word sets, rules and trainer, and sends you the trainer link.
5. **Every day:** open the trainer, do "Today's session", then ask Claude for today's lesson.

## Tips
- Press "Know for sure" on anything you already know: it stops coming back.
- Add your own words inside any word set; Claude checks the spelling and translation.
- Tell Claude when you move forward in the book, or when you start a new book or level, so new rules and words are added.
- In any exercise, tap a word you don't know: you can add it to your words to learn, or get a hint for just that word.
