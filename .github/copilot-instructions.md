<!-- .github/copilot-instructions.md -->
# Guidance for AI coding agents working on this repository

This repository is a small Streamlit demo app (`app.py`) that simulates coin toss experiments and stores results in `st.session_state`.

Follow these actionable, repository-specific rules when making changes:

- **Run locally**: use PowerShell from the repository root.
  - Install deps: `python -m pip install -r requirements.txt`
  - Run app: `streamlit run app.py`

- **High-level architecture**: single-file Streamlit app.
  - `app.py` contains UI, experiment logic and uses `st.session_state` to persist across reruns.
  - No backend services or external APIs are present.

- **State keys to preserve**: do not rename or remove these `st.session_state` keys unless you update all consumers:
  - `'experiment_no'` (int) — experiment counter
  - `'df_experiment_results'` (pandas.DataFrame) — stored results with columns `['no','iteraciones','media']`

- **UI patterns used**:
  - A global `chart = st.line_chart([0.5])` object is created once at top-level. New points are added by `chart.add_rows([mean])` inside the experiment loop.
  - Controls: `st.slider('¿Número de intentos?', 1, 1000, 10)` and `st.button('Ejecutar')` drive experiment runs.

- **Code patterns to follow / examples**:
  - Append new result rows exactly as `pd.concat([...], axis=0)` and then `reset_index(drop=True)` (see `app.py`) to keep DataFrame indexing stable.
  - When adding UI elements, prefer Spanish labels to remain consistent with the current app.

- **Performance and UX**:
  - The experiment loop calls `time.sleep(0.05)` and updates the chart on each iteration. Avoid large sleeps or blocking work that would freeze the UI; prefer batching updates if increasing default iteration counts.
  - If you need longer computations, consider using `st.spinner()` and/or `st.experimental_memo` / `st.cache_data` for cached results.

- **Testing & debugging**:
  - No automated tests are present. To validate changes:
    1. Install requirements and run `streamlit run app.py`.
    2. Interact with the slider/button and confirm `st.session_state['df_experiment_results']` updates and chart behaves as expected.

- **Conventions**:
  - UI text is Spanish; keep new text in Spanish unless explicitly asked to internationalize.
  - Keep changes minimal and focused: this is an educational/demo app.

- **When modifying state or chart behavior**:
  - Ensure existing session keys remain backwards-compatible.
  - If you move chart creation into a function or change how rows are added, preserve the top-level initialization pattern so repeated reruns reuse the same object.

If any of the above is unclear or you want me to add examples (e.g. convert loop to batched updates, or add a small test harness), tell me which area to expand.
