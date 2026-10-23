# AI Resume Analyser

## Features

- Paste résumé text or upload a `.txt` / `.md` file
- Optional job description for keyword matching
- Overall score (0–100) with a short verdict
- Section scores: impact and metrics, clarity and formatting, skills relevance, ATS friendliness
- Strengths and prioritised improvements
- Keywords found and missing
- Suggested rewrites for weak bullet points
- Light and dark mode, responsive layout

## How it works

1. The page collects the résumé and optional job description.
2. It sends both to Claude through the Claude artifact `sample` capability with a prompt that asks for a strict JSON response.
3. The JSON is parsed and rendered as the score ring, bars, lists and keyword tags.

There is no backend and no API key in the code. Résumé text is sent only to Claude when you click **Analyse résumé**.

## Project structure

```
resume-analyser.html   # Entire app: HTML, CSS and JavaScript in one file
README.md
```

## Running it

**On claude.ai (as published):** open the demo link and allow Claude access when prompted on first use.

**Elsewhere (your own hosting):** the `sample` capability only exists inside claude.ai artifacts. To run the page on your own site, replace the `sample.json(...)` call in `resume-analyser.html` with a request to your own backend that calls the Anthropic API:

```js
// Backend route (Node/Express example)
app.post("/analyse", async (req, res) => {
  const r = await fetch("https://api.anthropic.com/v1/messages", {
    method: "POST",
    headers: {
      "content-type": "application/json",
      "x-api-key": process.env.ANTHROPIC_API_KEY,
      "anthropic-version": "2023-06-01"
    },
    body: JSON.stringify({
      model: "claude-sonnet-5-5",
      max_tokens: 1500,
      messages: [{ role: "user", content: req.body.prompt }]
    })
  });
  const data = await r.json();
  res.json(JSON.parse(data.content[0].text));
});
```

Keep your API key on the server only. Never put it in the HTML file.

## Customising

- **Scoring categories:** edit the `sections` list in the prompt inside the click handler.
- **Tone and depth:** adjust the instructions in the `prompt` string (for example, tailor it to a specific industry or seniority level).
- **Colours and theme:** change the CSS variables at the top of the `<style>` block.

## Limitations

- Plain-text input only. Paste content from PDF or Word files.
- Scores are AI-generated guidance, not a guarantee of how any real ATS or recruiter will rate a résumé.
- Results can vary slightly between runs.

## Privacy

Do not include sensitive data you don't want processed. Consider removing your phone number, address and other personal identifiers before analysing.

## Roadmap ideas

- PDF and DOCX upload
- Downloadable analysis report
- Compare one résumé against several job descriptions
- Rewrite the full résumé with accepted suggestions

## License

MIT
