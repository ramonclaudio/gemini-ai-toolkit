# Gemini AI Toolkit

> **Unmaintained.** Use [`google-genai`](https://github.com/googleapis/python-genai), Google's unified Gen AI SDK for Python, or [Jules](https://jules.google.com) if you want a terminal-first coding agent.

I've always preferred pair programming with LLMs from the terminal over copy-pasting from browser chats. Web UIs hit rate limits, lose context across tabs, and break flow when I'm moving code back and forth. Google shipped the Gemini API in mid-December 2023 and the pitch was native multimodal: one request could handle text, images, video, audio, PDFs, and code together. I shipped this two weeks later. The first-party SDK was fresh, and I wanted all three modes (chat / text / multimodal), URL-or-local file uploads, streaming, JSON output, and the full parameter + safety surface behind one CLI. So I built this for myself.

Python wrapper and CLI for Google's Gemini models, with native multimodal.

## Install

```bash
git clone https://github.com/ramonclaudio/gemini-ai-toolkit.git
cd gemini-ai-toolkit
pip install -r requirements.txt
```

## Configuration

Get an API key at https://aistudio.google.com/app/apikey. Set via env var, `.env`, or pass directly:

```bash
export GEMINI_API_KEY=your_api_key
```

## CLI

```bash
# Chat
python cli.py --chat

# Text
python cli.py --text --prompt "Write a story about a magic backpack."

# Multimodal (files from local paths and/or URLs)
python cli.py --multimodal --prompt "Summarize each file." --files file1.jpg https://example.com/file2.pdf
```

Type `exit` or `quit` to leave chat.

## Python

```python
from gemini import Chat, Text, Multimodal

Chat().run()
Text().run(prompt="Write a story about a magic backpack.")
Multimodal().run(prompt="Summarize each file.", files=["file1.jpg", "https://example.com/file2.pdf"])
```

## In-session commands

- `/exit` or `/quit`: end the session
- `/clear`: clear conversation history (saves API credits)
- `/upload`: (multimodal) `/upload file_path_and_or_url [optional prompt]`

## Options

| Description | CLI | Python |
| --- | --- | --- |
| Chat mode | `--chat` | `Chat()` |
| Text mode | `--text` | `Text()` |
| Multimodal mode | `--multimodal` | `Multimodal()` |
| Prompt | `--prompt` | `prompt=` |
| Files | `--files` | `files=` |
| Streaming | `--stream` | `stream=True` |
| JSON output | `--json` | `json=True` |
| API key | `--api_key` | `api_key=` |
| Model | `--model` | `model=` |
| System prompt | `--system_prompt` | `system_prompt=` |
| Max tokens | `--max_tokens` | `max_tokens=` |
| Temperature | `--temperature` | `temperature=` |
| Top-p | `--top_p` | `top_p=` |
| Top-k | `--top_k` | `top_k=` |
| Candidate count | `--candidate_count` | `candidate_count=` |
| Stop sequences | `--stop_sequences` | `stop_sequences=` |
| Safety categories | `--safety_categories` | `safety_categories=` |
| Safety thresholds | `--safety_thresholds` | `safety_thresholds=` |

## Models

### Gemini 2.0

| Model | Inputs | Output | Max tokens |
| --- | --- | --- | --- |
| `gemini-2.0-pro-exp-02-05` | audio, images, video, text | text | 8192 |
| `gemini-2.0-flash-001` | audio, images, video, text | text | 8192 |
| `gemini-2.0-flash-lite-preview-02-05` | audio, images, video, text | text | 8192 |
| `gemini-2.0-flash-exp` | audio, images, video, text | text | 8192 |
| `gemini-2.0-flash-thinking-exp-01-21` | audio, images, video, text | text | 8192 |

### Gemini 1.5

| Model | Inputs | Output | Max tokens |
| --- | --- | --- | --- |
| `gemini-1.5-flash` | audio, images, video, text | text | 8192 |
| `gemini-1.5-flash-8b` | audio, images, video, text | text | 8192 |
| `gemini-1.5-pro` | audio, images, video, text | text | 8192 |

### Gemini 1.0 (deprecated by Google 2025-02-15)

| Model | Inputs | Output | Max tokens |
| --- | --- | --- | --- |
| `gemini-1.0-pro` | text | text | 2048 |

## File support

| Category | Extensions |
| --- | --- |
| Images | `jpg`, `jpeg`, `png`, `webp`, `gif`, `heic`, `heif` |
| Videos | `mp4`, `mpeg`, `mpg`, `mov`, `avi`, `flv`, `webm`, `wmv`, `3gp` |
| Audio | `wav`, `mp3`, `aiff`, `aac`, `ogg`, `flac` |
| Text/Documents | `txt`, `html`, `css`, `js`, `ts`, `csv`, `md`, `py`, `json`, `xml`, `rtf`, `pdf` |

Google's Files API stores up to 20 GB per project, 2 GB per file, 48-hour retention.

## Caching

URL-downloaded files cache to `.gemini_ai_toolkit_cache` and clear on session end.

## Errors

| Code | Status | Meaning | Fix |
| --- | --- | --- | --- |
| 400 | INVALID_ARGUMENT | Malformed request | Check API reference |
| 400 | FAILED_PRECONDITION | API unavailable in your country | Enable billing in Google AI Studio |
| 403 | PERMISSION_DENIED | Key lacks permissions | Verify API key |
| 404 | NOT_FOUND | Resource not found | Check API version / params |
| 429 | RESOURCE_EXHAUSTED | Rate limit | Back off or request a quota bump |
| 500 | INTERNAL | Google-side error | Retry |
| 503 | UNAVAILABLE | Service overloaded | Retry |

Rate limits (429) auto-retry after 15s.

## License

MIT
