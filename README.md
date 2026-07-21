AI YouTube Summarizer 🎬📄

An automated pipeline built on Make.com that takes a YouTube video link submitted via Google Forms, generates an AI-powered summary of the video's transcript, packages it into a PDF, and emails it to the requester — all without manual intervention.

How It Works
Google Forms (Watch Responses)
        ↓
Text Parser (Match Pattern) — extracts the YouTube video ID from the submitted URL
        ↓
HTTP (GET) — fetches the video transcript
        ↓
HTTP (POST) — sends the transcript to an AI model (via OpenRouter) for summarization
        ↓
APITemplate.io — generates a formatted PDF from the AI summary
        ↓
HTTP (GET) — downloads the generated PDF file so it can be attached to the email
        ↓
Gmail — emails the PDF summary back to the user


Scenario Modules & Setup
Google Forms – Watch Responses — Trigger on new form submission. Form needs an email field and a YouTube URL field.
Text Parser – Match Pattern — Extracts the video ID from the submitted URL.
HTTP (GET) — Fetches the transcript. Fails if the video has no captions.
HTTP (POST) /chat/completions — Sends the transcript to an AI model for a summary and 3 key takeaways. Prompt the model to return only the summary — no reasoning traces.
APITemplate.io — Generates the PDF. Use Key Value Pairs payload; map summary_text to message.content only, not the full API response.
HTTP (GET) – Download a File — Downloads the generated PDF so it can be attached to the email.
Gmail – Send an Email — Emails the PDF to the submitter. Attachments: Map off, with separate Data and File name (.pdf) fields, mapped from the download step.



Automation

To run this fully automatically:

Enable the scenario's scheduler (bottom toolbar → set interval, e.g. "Every 15 minutes").
Ensure the scenario is marked Active.

Once active, the scenario automatically triggers on every new Google Form submission — no manual "Run once" required.

Known Issues / Troubleshooting
BundleValidationError on Gmail attachments — caused by mapping an entire bundle into the Attachments field instead of separate Data/File name sub-fields.
Raw JSON/reasoning text appearing in the PDF — caused by mapping the entire AI API response instead of drilling down to choices[0].message.content.
SyntaxError: Bad control character in string literal — caused by unescaped line breaks when using raw JSON payload mode in APITemplate.io. Use Key Value Pairs mode instead, which handles escaping automatically.
"Transcript unavailable" errors — occurs when the submitted video has no captions available. Consider adding a filter/router step to catch this and skip PDF/email generation for such cases.
Gmail connection reauthorization — OAuth connections periodically expire and need to be reauthorized in Make's Credentials settings.
Tech Stack
Make.com — workflow automation/orchestration
Google Forms — intake
OpenRouter — AI model access for summarization
APITemplate.io — PDF generation
Gmail API — email delivery
