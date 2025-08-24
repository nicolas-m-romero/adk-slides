# adk-slides (PYTHON
Leveraging Google ADK to automate creation of Google Slides for TAMUhack. An ADK agent that restyles a target Google Slides deck to match one or more reference decks.


## Quick start


```bash
python -m venv .venv && source .venv/bin/activate # Windows: .venv\Scripts\Activate.ps1
pip install -r requirements.txt
cp .env.example .env # fill values


# First-time auth for Slides/Drive user credentials
python -m adk_slidestyles.tools.google_client # opens OAuth consent; creates token.json


# Run the agent in Dev UI (browser)
adk web src/adk_slidestyles/agent.py
# or run in terminal
adk run src/adk_slidestyles/agent.py
```


### Environment
- Use one of the following for model auth:
- **Gemini API key** (AI Studio): `GOOGLE_GENAI_USE_VERTEXAI=FALSE` and `GOOGLE_API_KEY=...`
- **Vertex AI**: `GOOGLE_GENAI_USE_VERTEXAI=TRUE`, plus `GOOGLE_CLOUD_PROJECT`, `GOOGLE_CLOUD_LOCATION`. Optionally use Express Mode with `GOOGLE_API_KEY`.


- Google APIs:
- Enable **Slides API** and **Drive API** on the Google account used during OAuth.


### What this agent does
1. Lets you pick a target Slides deck and one or more reference decks (by file ID).
2. Extracts a **BrandProfile** from each reference (theme colors, fonts, layout hints, background, default shape/text styles, optional logo rule).
3. Synthesizes a unified BrandProfile when multiple references are provided.
4. Applies the style to the target using one of two strategies:
- `import_master` (default): copy a slide from the first reference to import its master/layouts, remap target slides to closest layouts, then optionally prune the old master.
- `update_master`: directly update the target master page's color scheme + text/shape defaults.


### Typical prompt to the agent
> Restyle `TARGET_DECK_ID` using references `[REF_ID_1, REF_ID_2]` with strategy `import_master`.


### Notes
- The agent **prefers theme colors** (not raw RGB) so future theme changes cascade.
- The writer enforces a full 12-entry color scheme on master pages and orders batchUpdate requests for atomicity.
- Safe to run in **dry-run** by copying the target deck first (see TODO in `slides_writer.py`).


## Project layout
```
adk-slidestyles/
├─ requirements.txt
├─ .env.example
└─ src/adk_slidestyles/
├─ agent.py
└─ tools/
├─ brand_profile.py
├─ google_client.py
├─ slides_reader.py
├─ style_synthesizer.py
└─ slides_writer.py
```


## License
MIT
"""
