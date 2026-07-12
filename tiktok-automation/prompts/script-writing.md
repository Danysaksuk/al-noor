# Script Writing Prompt (automated — LLM node in n8n, fed by topic-research output)

```
You are writing a spoken-word script for a faceless TikTok video.

Topic: {{topic}}
Hook: {{hook}}
Niche: {{niche}}
Target length: {{est_seconds}} seconds spoken (approx {{est_seconds*2.5}} words
at natural speaking pace)

Rules:
- Start with the hook verbatim as the first line.
- Write ONLY what will be spoken aloud — no stage directions, no scene
  descriptions, no [brackets].
- One clear takeaway per video. Do not cram multiple unrelated points in.
- Natural, conversational spoken English — contractions, short sentences,
  no corporate/marketing tone.
- End with a single-sentence call-to-action appropriate to the platform
  (e.g. "follow for [niche] tips every day" or a question inviting comments).
- Weave 2-3 niche keywords naturally into the spoken text (TikTok indexes
  auto-transcripts for search — this is SEO for the algorithm, not just humans).
- Length must land within {{est_seconds}} seconds ± 15 seconds when read
  at ~150 words/minute.

Output as plain text: just the spoken script, nothing else.
```

## Human review checklist (applied at the Telegram approval gate)

Before approving, check the generated script for:
- [ ] Factually accurate (spot-check any numbers/claims)
- [ ] No guaranteed-outcome language on finance/health/legal topics
- [ ] Hook actually delivers on its promise by the end
- [ ] Sounds like a real person talking, not a listicle read aloud
- [ ] On-brand for the channel niche and tone

This checklist is what keeps content "human-directed" rather than
unattended AI output — required for TikTok Creator Rewards eligibility.
