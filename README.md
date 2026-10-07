# Novel Grader

Novel Grader grades the writing in a story, chapter, or whole novel. It gives a 0–100 score and a letter grade, and its report shows every flagged passage with a suggested fix.

It is one self-contained web page (`index.html`). Everything is inlined, including the PDF and EPUB readers, so there's no build step and no server code. Grading runs entirely in your browser, and pasted text and opened files never leave your device.

## Live site

https://reconsidering.github.io/novelgrading/

Every push to `claude/amazing-shannon-rh63cz` deploys to GitHub Pages through `.github/workflows/static.yml`. The repository is at https://github.com/reconsidering/novelgrading (formerly `ihardlynoah/novelgrading`). If the first deploy fails, open **Settings → Pages** in the repo and set **Source** to **GitHub Actions**.

## Using it

- **Getting text in:**
  - Paste text.
  - Open `.epub`, `.pdf`, `.txt`, `.html`, `.mhtml`, or `.webarchive` files, or a whole folder of them.
  - Import from a link to a page or a folder. Pages from sites that block direct downloads are fetched through public relays.
  - Use the "Copy story" bookmarklet on sites that block downloads entirely.
- **Loading and grading:** Opening a file reads it and grades it in one step, on a single progress bar. Press **Grade** again after editing the text or changing a setting.
- **Multiple files:** You choose whether the files are chapters of one novel or separate novels.
  - **Chapters** are joined in natural file-name order.
  - **Separate novels** are graded side by side. With three or more, you can compare any two, or one novel against the average of the rest.
- **Chapters:** Chapter headings are detected automatically. A collapsed **Chapter grades** table gives each chapter's grade and biggest issue, and you can grade any one chapter on its own. Overused words in a chapter are judged against the whole work.
- **Sharing:** A report link holds the scores and short quoted examples, not the story. It's packed into the part of the address after `#`, which never reaches a server.

### Settings

| Setting | What it does |
|---|---|
| **Style profile** | Genre targets and section weights. Choices: General fiction, Literary, Thriller/mystery/crime, Romance, Horror, Fantasy/science fiction, Historical, Young adult, Middle grade. |
| **Mistakes in character speech** | Errors inside quotation marks count lightly (a quarter of the penalty), fully, or not at all. Narration is always graded in full. |
| **Narration** | **Stylized voice or first person** is for narrators who bend the rules on purpose, like Faulkner or Twain's Huck. The narrator's dialect grammar and dropped apostrophes count like dialogue, and fragments, run-ons, reading level and the like get more room. Misspellings, confused words and clichés still count in full. |
| **Character names** | Names to exclude from the overused-word, echo and reading-level checks, separated by commas (e.g. `Obi-Wan, Kacchan, Lan Zhan`). Any case and any part of a listed name counts. Remembered in this browser. A built-in list already covers many well-known characters and nicknames, and honorifics like -kun, -chan and -gege. |
| **English** | American English flags Britishisms. British/other doesn't. |

## How scoring works

All rates are per 1,000 words, or per 1,000 narration words where the report says so. The same phrase can count in more than one check.

**The style score** is a weighted average of sections:

| Section | Weight | Checks |
|---|---|---|
| Word choice & variety | 3 | Overused words and phrases (5× within the section), clichés (3×; stock dialogue lines count ¼), filler, adverbs, hedging, redundancy, epithets ("the blonde"), echoes, vocabulary range, Britishisms, names vs. pronouns in narration, nouns for verbs ("made a decision", "gave a nod"), "the face of Harry" for "Harry's face" |
| Show vs. tell | 1 | Filter words, named emotions, stock body language, non-visual senses, head-hopping within a scene, concrete vs. abstract wording (concreteness ratings from Brysbaert et al. 2014), body parts acting on their own ("her eyes followed him") |
| Sentences & paragraphs | 1 | Sentence length and variety, repeated openers, -ing openers, passive and progressive voice, fragments, long sentences, stacked adjectives and similes, appositives, paragraph length, stock chapter openings (waking up, weather, mirror) and "it was all a dream" endings, "There was / It was… that" openings, backstory stretches told in the past perfect, dangling or impossible -ing openers |
| Dialogue | 1 | Dialogue share, showy tags, adverbs on tags, dialogue punctuation, long unattributed runs (8+ lines), actions used as tags ("Fine," she sighed), characters naming each other, "As you know" exposition, contractions in speech, long speeches |
| Punctuation | 0.5 | Exclamation marks in narration, ellipses, dashes, comma splices, stacked marks ("?!", "!!!") |
| Readability | 0.5 | Flesch–Kincaid grade, Dale–Chall score |

- **How each check is scored:** A check scores 100 inside its target range. Its score falls to 0 at the "bad" end of the range, then continues down to −100. A section can drop to −25.
- **Narrow checks only subtract:** the narrower habit checks (head-hopping, dialogue habits, concrete language, backstory, nouns for verbs, stock openings and the like) take points off their section when they find a problem, but a clean result doesn't raise it. Adding more checks therefore can't inflate grades. They show in the report as "no points off" or "−X pts".
- **Calibration:** Targets were set on about 50 acclaimed novels, so normal published prose isn't penalized.
- **Profiles:** A profile can change both the targets and the section weights. Romance compares words and phrases of physical intimacy (kissed, moaned, thighs, thrust, fucking…) against 10× their rate in published fiction, rather than the plain rate, before judging them overused.

**Errors are subtracted** from the style score. They are not averaged in.

| Error check | Points off per error per 1,000 words |
|---|---|
| Misused phrases ("could of", "for all intensive purposes", "in the throws of") | 6 |
| Grammar (verb forms, agreement, pronoun case, a/an, lay/lie, subjunctive: "If I were you", "demanded that he leave") | 6 |
| Confused words (their/there, affect/effect, peak/pique, taut/taught, rogue/rouge…) | 5 |
| Typos & misspellings (including run-together words, missing apostrophes, doubled words, a lowercase "i", and mixing two accepted spellings such as backseat/back seat or gray/grey) | 2.5 |
| Tense switches in narration | 4 (first 0.2 free) |
| Slips into "you" in third-person narration | 3 (first 0.2 free) |

- **When points come off:** Misused phrases, confused words, and grammar cost points for each full 0.01 errors per 1,000 words.
- **Errors in dialogue:** These follow the "Mistakes in character speech" setting.

**Letter grades:**

| Score | Grade |
|---|---|
| 93+ | A |
| 90 | A− |
| 87 | B+ |
| 83 | B |
| 80 | B− |
| 77 | C+ |
| 73 | C |
| 70 | C− |
| 67 | D+ |
| 60 | D |
| below 60 | F |

## Run locally

Open `index.html` in a browser, or serve the folder:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Code layout

Everything is in `index.html`:

- **The grading engine:** between `/*GRADER-START*/` and `/*GRADER-END*/`. It defines `Grader.analyze(text, opts)`, which returns the score, section scores, metrics and flags.
  - Error rule lists: `TIER1` (misused phrases), `TIER2` (confused words), `GRAMMAR`, `SUBJUNCTIVE` (graded as grammar), `TYPOS`. Each rule is `[regex, fix]`. An optional third element marks rules that count lightly in stylized narration.
  - Character names: `CHAR_NAMES` (built-in list) and `HONORIFICS`.
  - Cliché lists: `CLICHE_GENERAL`, `CLICHE_ROMANCE`.
  - Targets: `DEFAULTS`, `PROFILES`, `VOICE_O` (stylized-narration targets).
- **Where it runs:** Grading runs in a Web Worker built from the engine code, so the page stays responsive and shows progress while it works.
- **Testing changes:** Check new rules against acclaimed published novels for false positives before shipping.
