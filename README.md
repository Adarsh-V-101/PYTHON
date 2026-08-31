# College Helpdesk

A chatbot for NPGC (National Post Graduate College, Lucknow) students — answers common institutional and course-related questions using TF-IDF similarity search over a curated Q&A database, with an admin panel to review and add answers for questions it couldn't handle.

**Live:** https://npgc-helpdesk.vercel.app

---

## How it works

1. User submits a question through the web UI.
2. The query is passed to `apiCall.py`, which calls GPT-4o (via the Bytez SDK) to fix spelling and extract normalized keywords — this cleans up typos and phrasing before matching.
3. The cleaned query is vectorized with `TfidfVectorizer` and compared against every question in `database.csv` using cosine similarity.
4. If the best match scores above `0.3`, one of four pre-written answer styles (Informational / Guidance-oriented / Institutional / Conversational) is returned at random.
5. If nothing scores high enough, the question is logged to `appfeedback.csv` and a fallback reply is shown.
6. An admin can review logged questions at `/admin` and add proper answers via `/admin/submit`, which appends them to `database.csv` and removes them from the feedback queue.

This is a similarity-search chatbot, not a fine-tuned or generative model — GPT-4o is only used as a preprocessing step for query cleanup, not for generating answers.

## Project structure

```
npgc-helpdesk/
├── app.py                # Flask app: routes, matching logic, admin panel
├── apiCall.py            # Calls GPT-4o (via Bytez) to clean/normalize the user's query
├── modelSourceCode.py    # TF-IDF / similarity matching logic
├── protorype.py          # Early prototype / experimentation script
├── database.csv          # Q&A dataset (Question + 4 answer-style columns)
├── appfeedback.csv       # Logged questions the bot couldn't answer
├── requirements.txt      # Python dependencies
├── static/               # CSS & JS
└── templates/            # HTML templates (chat UI + admin panel)
```

## Setup

1. Clone the repo and install dependencies:
   ```
   git clone https://github.com/Adarsh-V-101/npgc-helpdesk.git
   cd npgc-helpdesk
   pip install -r requirements.txt
   ```
2. Create a `.env` file in the project root with your Bytez API key:
   ```
   API_KEY=your_bytez_api_key
   ```
3. Run it:
   ```
   python app.py
   ```
   Then open the local URL Flask prints in the terminal.

## Known limitations

- The `/admin` panel has no authentication — treat it as a local/dev-only feature until access control is added.
- Answer style (Informational/Guidance/Institutional/Conversational) is currently chosen at random rather than based on query intent.
- Writes to `database.csv` / `appfeedback.csv` assume a writable filesystem; this doesn't hold in all serverless environments.

## Author

**Adarsh Vishwakarma**
GitHub: https://github.com/Adarsh-V-101
