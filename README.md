# Resume Analyzer

An AI-powered resume screening tool built with LangChain agents and Google Gemini. Upload a resume and a job description (both PDF), and get a structured evaluation covering skill fit, experience fit, salary alignment, and a final APPROVE/REJECT decision.

## Architecture

The app uses a multi-agent pipeline orchestrated by a supervisor agent:

| Agent | Tool | Purpose |
|---|---|---|
| **Skills & Education Agent** | Wikipedia | Compares resume skills and education against the job description |
| **Experience Agent** | DuckDuckGo Search | Evaluates work experience, role alignment, and seniority fit |
| **Salary Agent** | DuckDuckGo Search | Estimates market-aligned salary range for the role and location |
| **Supervisor Agent** | Calls the three agents above | Aggregates all signals and produces a structured APPROVE/REJECT decision |

All agents use **Google Gemini 2.5 Flash Lite** as the LLM and include retry middleware for resilience.

## Prerequisites

- **Python 3.12+**
- **[uv](https://docs.astral.sh/uv/)** (recommended) or `pip`
- A **Google Gemini API key** ([get one here](https://aistudio.google.com/apikey))

## Setup

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd resumeanalyser
```

### 2. Create a virtual environment

Using **uv** (recommended):

```bash
uv venv .venv --python 3.12
source .venv/bin/activate
```

Using **pip**:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

Using **uv**:

```bash
uv pip install -r requirements.txt
```

Using **pip**:

```bash
pip install -r requirements.txt
```

### 4. Configure your API key

Copy the example env file and add your Gemini API key:

```bash
cp .env.example .env
```

Edit `.env` and set your key:

```
GOOGLE_API_KEY=your_gemini_api_key_here
```

The app accepts any of the following env variable names (checked in this order):

| Variable | Notes |
|---|---|
| `GEMINI_API_KEY` | Preferred |
| `GOOGLE_API_KEY` | Also accepted |
| `Geminii` / `geminii` | Legacy fallback |

You can also export it directly instead of using `.env`:

```bash
export GEMINI_API_KEY=your_gemini_api_key_here
```

## Running the App

```bash
source .venv/bin/activate
streamlit run resumeanalyser.py
```

This opens the app in your browser (default: `http://localhost:8501`).

## Usage

1. Upload a **resume** (PDF) using the first file uploader.
2. Upload a **job description** (PDF) using the second file uploader.
3. Click **Evaluate**.
4. The app displays:
   - **Decision** (APPROVE or REJECT) with an overall **score** out of 100
   - A short recruiter-style **summary**
   - Expandable sections for **Skill & Education Fit**, **Experience Fit**, and **Salary Alignment**

## Project Structure

```
resumeanalyser/
├── resumeanalyser.py   # Main application (agents + Streamlit UI)
├── requirements.txt    # Python dependencies
├── .env.example        # Template for environment variables
├── .env                # Your actual API key (git-ignored)
├── .gitignore
└── *.pdf               # Sample resume / job description PDFs
```

## Troubleshooting

| Issue | Fix |
|---|---|
| `RuntimeError: Gemini API key required` | Set `GEMINI_API_KEY` or `GOOGLE_API_KEY` in `.env` or your shell environment |
| `ModuleNotFoundError: No module named 'ddgs'` | Run `uv pip install ddgs` or `pip install ddgs` |
| `ModuleNotFoundError` for any langchain package | Re-run `uv pip install -r requirements.txt` |
| Streamlit warnings about `ScriptRunContext` | These appear when importing the module outside `streamlit run`. Ignore them. |
