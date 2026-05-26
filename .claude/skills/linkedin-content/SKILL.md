---
name: linkedin-content
description: Use when someone asks to write a LinkedIn post, create a LinkedIn carousel, generate LinkedIn content, draft a post for Harena, or suggest LinkedIn content ideas. Also triggers on "post about X", "carousel on Y", "what should I post about".
argument-hint: [topic or insight — omit to get content ideas]
---

## What This Skill Does

Generates on-brand LinkedIn content for **Harena** — either text posts, carousel scripts, or both.
Two modes:
- **Topic provided** → generates content immediately, then offers revision directions
- **No topic** → reads past content to find pillar gaps, suggests 4–5 ideas, then generates on your pick

Brand guidelines live at `brand-assets/guidelines.md`. Read them before generating any content.

---

## Step 0: Load Context

1. Read `brand-assets/guidelines.md` in full.
2. Query the Notion POSTS DATABASE using the `notion-query-database-view` MCP tool:
   - View URL: `https://www.notion.so/34e1eb08ebd2803baf5dd71d7153332f?v=5c895310-1b76-4e04-8ecf-d0d1a41c5c95`
   - Request up to 100 rows
   - If Notion MCP is unavailable, skip steps 3–4 and note that performance data is not available
3. Build a **pillar distribution count** by inferring the pillar for each post from its `Topic` and `Post type` fields:
   - `operational-ai` — AI integration, workflows, decision systems, production AI, deployment failures
   - `builder-story` — edge cases, specific incidents, debugging, hands-on engineering (payroll, accounting, OCR, etc.)
   - `framework` — structured multi-step frameworks, numbered operating layers, architecture thinking
   - `leadership` — AI transformation at org level, building in Madagascar, youth empowerment, speaking engagements
   - `personal` — events, mindset content, community moments, team interactions
   Target distribution: operational-ai 40%, builder-story 25%, framework 20%, leadership 10%, personal 5%
4. Build a **performance baseline** from `Impressions`, `Likes`, `Hook strength (1-5)`:
   - Identify the top 3 posts by Impressions — note their Topic hook and Post type
   - Calculate average Impressions per Post type (e.g., "Experience" vs. "Insight + experience")
   - Note the Hook strength scores of high-performing posts (Hook strength 4–5 with high Impressions)

---

## Mode A: Topic Provided

Triggers when the user passes a topic as an argument or mentions one in their message.

### Step 1: Ask for format

Ask the user which format they want:
- **Text post** — single LinkedIn post (hook + body + optional CTA)
- **Carousel script** — slide-by-slide content for a 1080×1350 carousel
- **Both**

### Step 2: Identify the content pillar

Based on the topic and the brand guidelines, assign the most appropriate pillar:
- `operational-ai` — AI integration, workflow systems, decision layers, production AI, governance
- `builder-story` — edge cases, production incidents, system debugging, context engineering
- `framework` — operating layers, integration architecture, AI operating systems, structured insights
- `leadership` — AI transformation, building in Madagascar, long-term infrastructure thinking
- `personal` — conferences, speaking, community, team

If the topic doesn't map cleanly, ask the user to confirm the pillar before generating.

### Step 3: Generate content

Generate according to the format chosen. Follow the templates below exactly.

### Step 4: Save the output

Save the generated content to `output/linkedin/YYYY-MM-DD-[slug].md` where:
- `YYYY-MM-DD` is today's date
- `[slug]` is a 3–5 word kebab-case summary of the topic

Use this frontmatter at the top of the output file:

```yaml
---
date: YYYY-MM-DD
pillar: [pillar-name]
format: text-post | carousel | both
topic: [short description]
---
```

### Step 5: Display and offer revisions

Print the full content to the terminal.

Then offer exactly **3 revision directions**, tailored to what was just written. Examples:
- "Make the hook more provocative"
- "Add a concrete failure example to slide 3"
- "Shorten the body — cut to the core insight only"
- "Reframe around a decision layer instead of a model"
- "Add a closing operating rule"

Ask: "Which direction, if any? Or approve as-is."

If the user requests a revision, apply it and re-display. Do not re-offer the same revision options.

---

## Mode B: No Topic (Idea Suggestion)

Triggers when the user runs `/linkedin-content` with no argument and no topic in their message.

### Step 1: Analyze pillar gaps + performance patterns

From the data built in Step 0:
- Identify which pillars are under-represented relative to their targets
- Note which Post types and topic angles drove the highest Impressions (e.g., "Experience" posts with Hook strength 5 outperformed "Annonce" posts)
- If Notion was unavailable, treat all pillars as equally under-represented

### Step 2: Suggest 4–5 ideas

Generate content ideas that:
1. Prioritize under-represented pillars
2. Favor formats and angles that performed well in past data (based on Post type + Hook strength patterns)

Each idea should include:
- A proposed **hook** (first line of the post) — written at Hook strength 4–5 quality
- The **pillar** it belongs to
- The **format** it works best as (text post or carousel)
- One sentence on the **angle** (what makes this specific, non-generic)
- If relevant: a note like "(similar posts averaged X impressions)"

Present as a numbered list.

### Step 3: User picks one

Ask: "Which idea do you want to develop? (1–5, or describe your own)"

Once the user picks, flow into **Mode A Step 1** with that idea as the topic.

---

## Text Post Template

```
[HOOK — 1 line. Bold claim, sharp observation, or counter-intuitive statement.]

[BODY — 3–6 short paragraphs or line breaks. Each line carries one idea. No filler.]

[OPTIONAL CTA — 1 line. A question, an operating principle, or an invitation to respond. Never "follow me for more."]

#[tag1] #[tag2] #[tag3]
```

**Hook rules:**
- Must work as a standalone statement
- No questions as the opener (statements are stronger)
- Avoid: "In today's world", "AI is changing everything", "Hot take:"
- Examples from brand guidelines: "AI doesn't fail at the model.", "A working model is not a working system.", "The workflow was broken."

**Body rules:**
- Short sentences. One idea per line.
- Each paragraph connects to: workflows, systems, decisions, operations, or production reality.
- No motivational clichés. No corporate jargon overload.
- Maximum 200 words for the body.

**Hashtags:** 3 tags maximum. Choose from: #AIOperations #AIIntegration #OperationalAI #WorkflowIntelligence #AILeadership #TechLeadership #AIArchitecture #ProductionAI #AIGovernance

---

## Carousel Script Template

Carousels are 1080×1350. Max 7 slides. One idea per slide.

```
SLIDE 1 — HOOK
[Single bold statement. 8 words max. This is the cover.]

SLIDE 2 — CONTEXT / PROBLEM
[1–2 sentences. What's broken or misunderstood.]

SLIDES 3–5 — INSIGHT / FRAMEWORK
[Each slide: one named point + 1–2 sentences of explanation.]
[Label each: e.g., "Layer 1: ...", "The mistake:", "What actually happens:"]

SLIDE 6 (optional) — EXAMPLE OR CONTRAST
[A concrete production scenario or before/after.]

FINAL SLIDE — OPERATING RULE or STRATEGIC QUESTION
[One-line principle the reader can apply immediately.]
[Or a question that makes them think about their own systems.]

CAPTION (for the LinkedIn post body):
[3–5 sentence post to accompany the carousel. Hook + what the carousel covers + CTA.]
```

---

## Voice Rules (Summary)

Always apply these. They are non-negotiable.

**Use:**
- Short sentences
- Operational framing (workflows, systems, decisions, production)
- Controlled authority — competent, not arrogant
- Structural clarity — each sentence earns its place

**Never use:**
- "Game-changer", "revolutionary", "unlock", "supercharge", "leverage" (as a verb), "in today's fast-paced world"
- Rhetorical questions as hooks
- Motivational filler ("You've got this", "The future is now")
- Generic AI commentary without operational grounding
- "Top N tools/tips/tricks" framing

---

## Notes

- If the user provides a topic that falls into the "Content Anti-Patterns" section of the brand guidelines (generic AI news, trend-chasing, empty productivity advice), flag it and suggest a reframe before generating.
- Do not invent statistics or cite specific research unless the user provides them.
- If generating "both" formats, generate the text post first, then the carousel. They should feel like two different expressions of the same insight, not copy-paste of each other.
- The output directory `output/linkedin/` may not exist on first use — create it when saving the first file.
- **Notion database**: POSTS DATABASE lives at `https://www.notion.so/34e1eb08ebd2803baf5dd71d7153332f` inside the LINKEDIN page. Collection ID: `collection://34e1eb08-ebd2-80d7-96e4-000b51486df1`. There is no `Pillar` property — infer pillar from `Topic` + `Post type` content as described in Step 0.
- **If the user asks to add a Pillar field to Notion**: use the `notion-update-data-source` tool to add a select property named `Pillar` with options: operational-ai, builder-story, framework, leadership, personal. This would make future tracking more precise.
