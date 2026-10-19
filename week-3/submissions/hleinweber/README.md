# Week 3 - hleinweber

Harness: https://github.com/hleinweber/research-agent

`hleinweber-telegram` supplies MCP tools to send and receive messages in one configured private Telegram chat. It skips old messages on startup and rejects other chats. The harness owns polling and asks for command approval through replies to individual Telegram prompts.

`hleinweber-insight-literature` searches Crossref for InSight research, looks up DOI metadata and abstract excerpts, and provides curated magnetic-calibration references. The skill covers Mars seismology/interior structure and magnetometer interference/calibration. No research API key is required. Titles, years and publication types must be verified; abstracts and metadata do not replace scientific review.

## Run the harness

Clone the harness repository, install uv, and privately configure its ignored `.env` with `BASE_URL`, `API_KEY`, `MODEL`, `TELEGRAM_BOT_TOKEN` and `TELEGRAM_CHAT_ID`. Never commit or paste secrets. From the harness root:

```powershell
uv --system-certs run python agent.py --telegram
```

Keep one polling process running. Send questions as new Telegram messages; use Telegram Reply on the active approval prompt and answer yes or no. Only yes approves. `/new` resets context. Read-only literature tools and sending are allowed by `settings.json`; shell commands always require approval. Bot chats are not end-to-end encrypted.

## Validation

The student confirmed sending from web chat, phone command approval/denial, two separately approved commands, startup skipping of messages sent while stopped, and research answers with visible search/DOI tool calls. The student explained tool schemas versus skill summaries and on-demand skill reading. Local SDK checks exercised discovery, literature lookup and invalid inputs. Unauthorized private-chat/group filtering passed a simulated check, not a live second-account test. Both plugin layouts and Python syntax were checked.

Both plugins were tested in Codex: Telegram sending returned status sent (message ID 59) and polling responded without error; literature search, source listing and DOI lookup succeeded. A fresh-message receive test in Codex was inconclusive after its Telegram tool became unavailable; phone-mode receive tests are documented above. Final harness integration checks passed for plugin discovery, command/tool denial, harness polling ownership, a live DOI lookup and a model API call (872 input tokens). Publication of the updated harness and the homework PR awaits final student approval. Scientific summaries still require review; initial testing exposed citation errors and overbroad abstract summaries, prompting DOI verification guidance and tool traces.

AI assistance: OpenAI Codex helped implement the MCP plugins, loader, Telegram approvals, debugging, validation and documentation. The student chose the research scope and performed the phone tests and context explanation.
