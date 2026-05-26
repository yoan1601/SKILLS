---
name: infographic-builder
description: Use when someone asks to create an infographic, design an educational infographic, make a visual explainer, or generate infographic content about a topic.
argument-hint: [topic]
---

## What This Skill Does

Creates educational infographics end-to-end: builds a structured concept, crafts an image generation prompt aligned with brand guidelines, confirms with the user, then calls `mcp__kie-ai__nano_banana_image` to generate the image.

## Steps

### 1. Determine the topic

If `$ARGUMENTS` is provided, use it as the topic. Otherwise, ask the user: "What topic should this infographic cover?"

### 2. Read brand guidelines

Look for a brand guidelines file in `brand-assets/` (e.g., `brand-assets/guidelines.md`, `brand-assets/brand.md`, or any `.md`/`.txt` file in that folder).

- If the folder doesn't exist or is empty, stop and tell the user: "No brand guidelines found in `brand-assets/`. Please add a brand file there before running this skill (colors, fonts, tone, logo rules, etc.)."
- If found, read and extract: primary/secondary colors (hex if available), typography (font names, weights), tone of voice, logo usage rules, any visual do's and don'ts.

### 3. Ask clarifying questions

Use `AskUserQuestion` to ask (all in one round):

- **Target audience** — Who is this for? (e.g., beginners, professionals, children, general public)
- **Complexity level** — How deep should it go? (Simple overview / Intermediate / In-depth)
- **Visual style** — Any style preference? (e.g., clean/minimal, bold/colorful, data-heavy, illustrated)

### 4. Build the concept

Generate a structured concept document and show it to the user. Do NOT proceed until they approve it.

Format the concept as:

```
## Infographic Concept: [topic]

**Audience:** [answer from step 3]
**Complexity:** [answer from step 3]
**Style:** [answer from step 3]

**Layout:** [e.g., vertical scroll / 3-column / timeline / comparison]
**Aspect ratio:** [recommended ratio for this layout — see notes]

**Headline:** [punchy, informative title]

**Sections:**
1. [Section title] — [1-2 sentences on what this covers]
2. [Section title] — [1-2 sentences]
3. [Section title] — [1-2 sentences]
(3–6 sections typical)

**Key facts / stats:** [bullet list of factual claims to include — flag any you're uncertain about]
**Call to action:** [optional closing prompt or takeaway]
```

If you are uncertain about any facts or statistics, write "[UNVERIFIED — please confirm]" next to them. Never invent statistics.

Ask: "Does this concept work? Any changes before I build the image prompt?"

Wait for explicit approval before continuing.

### 5. Build the image generation prompt

Craft a detailed prompt for `mcp__kie-ai__nano_banana_image` that includes:

- The infographic layout and sections from the approved concept
- Brand colors (use hex codes if available)
- Brand typography (font names and weights)
- Brand tone and visual style
- All key facts and text content to appear in the image
- Aspect ratio and visual style guidance

Prompt structure to follow:

```
Educational infographic about [topic].

Layout: [layout type], [aspect ratio].
Style: [visual style — clean/bold/illustrated/etc.], [tone].

Typography: [font names from brand guidelines, or "sans-serif, modern" if unspecified].
Colors: [primary color], [secondary color], [accent], white background (or brand background).

Content:
Headline: "[headline]"
Section 1 — [title]: [content]
Section 2 — [title]: [content]
...
Call to action: "[CTA text]"

Design rules: [any brand do's/don'ts — e.g., "no clip art", "use flat icons", "left-aligned text"].
High detail, sharp text, print-quality layout.
```

Keep the prompt under 5000 characters.

### 6. Confirm before generating

Show the user:
1. The full prompt that will be sent
2. The selected parameters: aspect ratio, resolution (default: 2K = 12 credits), output format (png)

Ask: "Ready to generate? This will use **[N] kie.ai credits**. Confirm to proceed."

Wait for explicit confirmation. If the user wants changes, revise the prompt and re-confirm.

### 7. Generate the image

Call `mcp__kie-ai__nano_banana_image` with:
- `prompt`: the confirmed prompt
- `aspect_ratio`: from the concept (see notes for recommended ratios)
- `resolution`: "2K" by default, unless user specified otherwise
- `output_format`: "png"
- `google_search`: true (enables factual grounding for educational content)

### 8. Save and return the result

Once the image URL is available:

1. Derive a filename from the topic: lowercase, spaces to hyphens, `.png` extension (e.g., topic "claude skills" → `claude-skills.png`).
2. Save the image to `projects/infographic-builder/<filename>` using PowerShell:
   ```powershell
   New-Item -ItemType Directory -Force "projects/infographic-builder" | Out-Null
   Invoke-WebRequest -Uri "<image_url>" -OutFile "projects/infographic-builder/<filename>"
   ```
3. Confirm the file was saved, then display the image URL and local path to the user.

If the generation fails, show the error and ask the user how to proceed.

---

## Notes

**Recommended aspect ratios by layout:**
- Vertical/scroll infographic → `9:16`
- Standard portrait → `2:3` or `4:5`
- Square (social media) → `1:1`
- Landscape/wide → `16:9`
- Tall timeline → `1:4`

**Resolution and cost:**
- 1K = 8 credits (drafts / quick iterations)
- 2K = 12 credits (default — good quality)
- 4K = 18 credits (final/print quality)

**Output location:** `projects/infographic-builder/<topic-slug>.png` — created automatically if it doesn't exist.

**Brand guidelines location:** `brand-assets/` in the project root. The skill reads any `.md` or `.txt` file it finds there. If multiple files exist, read all of them.

**What this skill must NOT do:**
- Skip the concept approval step — always get sign-off before building the prompt
- Invent or estimate statistics — flag uncertainty explicitly
- Call `nano_banana_image` without explicit user confirmation
- Proceed if `brand-assets/` is missing or empty
