# X Reader

Read X/Twitter links with low-token routing.

- Normal tweets use `xreach`
- X article pages use `Playwright`
- Authored threads are filtered into cleaner structured output
- No official X API required

## What it does

X Reader is a high-level X link reader for agent workflows.

It is designed for cases where a user shares an X link and wants the agent to read, summarize, or extract the content with minimal token overhead.

Supported link types:

- Tweet
- Thread
- X article
- `t.co` redirects that resolve to X articles

## Positioning

X Reader does not replace `xreach` or `xcurl`.

- Compared with `xreach`, X Reader adds article handling via Playwright, authored-thread filtering, and unified structured output.
- Compared with `xcurl`, X Reader focuses on content extraction from links instead of low-level request control.

## Why it is useful

- No official X API required
- No developer application required
- No paid X API access required
- Low-token routing: browser automation is only used when article rendering is necessary
- Structured JSON output for summarization, archiving, and downstream workflows

## Dependencies

Required:

- `xreach`
- Node.js
- Valid X login cookies

Install article-mode dependency inside the skill directory:

```bash
cd skills/x-reader
npm install
```

## Auth

Primary auth path:

```bash
~/.config/xreader/session.json
```

Legacy fallback path:

```bash
~/.config/xfetch/session.json
```

Expected JSON format:

```json
{
  "authToken": "...",
  "ct0": "..."
}
```

## Usage

Read a normal tweet:

```bash
node skills/x-reader/scripts/xreader.mjs "https://x.com/..."
```

Read an article in full mode:

```bash
node skills/x-reader/scripts/xreader.mjs --mode full "https://x.com/..."
```

Force thread extraction:

```bash
node skills/x-reader/scripts/xreader.mjs --thread "https://x.com/.../status/..."
```

Debug article rendering with a visible browser:

```bash
node skills/x-reader/scripts/xreader.mjs --debug "https://x.com/i/article/..."
```

## Output

The script returns structured JSON with fields such as:

- `ok`
- `type`
- `url`
- `canonicalUrl`
- `source`
- `author`
- `publishedAt`
- `title`
- `summaryText`
- `contentText`
- `contentMarkdown`
- `warnings`
- `error`

## Quick verification

```bash
node skills/x-reader/scripts/xreader.mjs "https://x.com/yangguangai/status/2033736815405121642?s=46"
node skills/x-reader/scripts/xreader.mjs "https://x.com/yangguangai/status/2033522959407878621?s=46"
node skills/x-reader/scripts/xreader.mjs --thread "https://x.com/google/status/2031558824042058064"
```

## Notes

- Default tweet reads are single-post only for token efficiency.
- Use `--thread` when the user clearly wants the authored thread.
- Article extraction is MVP quality: good enough for reading and summarization, but long X articles with many examples or code blocks may still include some template noise.
- If article extraction fails, the script falls back to the original tweet payload and marks the failure in `warnings` / `articleError`.
- This skill relies on X login cookies (`auth_token` + `ct0`). Cookie-based automation may carry account risk, including additional verification or account restrictions. The risk is usually low for light personal use, but using a secondary account instead of a primary high-value account is recommended.
