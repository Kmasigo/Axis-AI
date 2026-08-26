
Axis — AI Workplace Productivity Assistant
Overview
Axis is a web-based AI assistant dashboard that automates five everyday workplace tasks using structured, auditable Claude API prompts. Each tool generates a draft output for human review before use, emphasizing accuracy and control.

Features
Five Core Tools
Smart Email Generator (email)

Generates professional emails with tone and audience control
Input: brief, audience, tone, length, sign-off
Output: draft email ready to review and send
Meeting Notes Summariser (summarize)

Extracts key decisions, action items, deadlines, and open risks from raw meeting notes
Automatically flags owners; labels unclear assignments as "Owner not stated"
Structured output with clear headings and bullet points
AI Task Planner (plan)

Uses Eisenhower prioritisation (Urgent/Important quadrants)
Creates a timeboxed, realistic daily schedule
Outputs: prioritisation matrix + schedule with buffer time
AI Research Assistant (research)

Produces grounded summaries with clear separation of source and interpretation
Flags uncertainty and avoids fabricating facts
Explicitly notes when working from general knowledge vs. provided material
Assistant Chat (chat)

Open-ended conversational help with full session memory
Maintains chat history for follow-up requests within a session
Work-focused, practical advice
Technical Architecture
Frontend Stack
HTML5 with semantic structure
CSS3 with CSS custom properties (variables) for theming
Vanilla JavaScript (no frameworks) for routing, state management, and UI interactions
Design System
Typography: Fraunces (serif, headings), Inter (sans-serif, body), JetBrains Mono (code/mono)
Color Palette: Dark sidebar (#14161B), light paper background (#F6F5F1), amber accent (#C1721F)
Components: Sidebar navigation, responsive topbar, card-based tool grid, modal panels, input fields, buttons
Responsive: Mobile-first; sidebar slides to fixed overlay on screens ≤860px
State Management
Single state object tracking:
Current view (view)
Chat history (chatHistory)
View routing via navigate() function
No external state library; all UI-driven from DOM
API Integration
Claude API (Anthropic) via callClaude() helper
Model: claude-sonnet-4-6
Max tokens: 1000 per request
Used for all five tools + chat
How It Works
Prompt Engineering
Each tool uses a structured system prompt that:

Defines role and guardrails (e.g., "never invent names or dates")
Specifies output format and structure
Requires grounding in user input only
Marks uncertain content with placeholders
Prompts are defined in the PROMPTS object:

JavaScript
PROMPTS.email = (brief) => ({
  system: "...", // guardrails and format
  user: "..."    // user input + task
})
Generate Flow
User fills in form fields for a tool
Clicks "Generate [tool]"
runGenerate() disables button, shows loading state
Calls callClaude() with system + user prompts
Output appears in preview panel
User can click "View prompt used" to inspect the exact system and user prompts sent
Navigation
Click nav items or tool cards to switch views
navigate(view) renders the view template and wires up event listeners
Sidebar closes on mobile after navigation
Smooth scroll to top on view change
Mobile Experience
Hamburger menu toggles fixed sidebar overlay
Topbar shows on screens ≤860px
Sidebar slides in from left with overlay backdrop
Main content adapts padding and typography for smaller screens
Key Files & Structure
Code
index.html
├── <head>
│   ├── Google Fonts (Fraunces, Inter, JetBrains Mono)
│   └── <style> (all CSS inline)
├── <body>
│   ├── .app (flex container)
│   │   ├── .sidebar (fixed, sticky)
│   │   │   ├── Brand mark + name
│   │   │   ├── Nav sections (Start here, Plan & write, Review & learn)
│   │   │   ├── Focus rail widget (clock + status tip)
│   │   │   └── Disclaimer footer
│   │   ├── .sidebar-overlay (mobile menu backdrop)
│   │   ├── .topbar (mobile only)
│   │   └── .main
│   │       └── .main-inner (max-width:980px, padding)
│   └── <script> (all JavaScript inline)
JavaScript Functions
Core Navigation & Routing
navigate(view) – Switch views, update active nav, wire event listeners
setActiveNav(view) – Highlight current nav link
closeSidebar() – Hide sidebar overlay on mobile
wireView(view) – Attach event listeners for the current view
View Renderers
renderDashboard() – Tool grid + stat cards
renderEmail(), renderSummarize(), renderPlan(), renderResearch() – Tool forms
renderChat() – Chat window + input
Tool Helpers
toolShell({icon, title, desc, bodyHtml}) – Wrapper template for tool pages
outputPanelHtml(id) – Template for output preview + prompt viewer
disclaimer() – Shared warning about AI draft content
Event Wiring
wireEmail(), wireSummarize(), wirePlan(), wireResearch() – Attach form handlers
wireChat() – Chat send, history rendering, enter-to-send
runGenerate({btnId, outputId, promptId, buildPrompt}) – Shared generate flow
Chat
wireChat() – Manage chat window, history, API calls
Maintains state.chatHistory with {role, content} objects
Renders history on each update
Clock & Tips
tickClock() – Updates live clock every 1 second
rotateTip() – Cycles through focus tips every 5 seconds
Tips emphasize drafts, placeholders, and user control
API Endpoints
Claude API
JavaScript
POST https://api.anthropic.com/v1/messages
Headers:

Code
Content-Type: application/json
Body:

JSON
{
  "model": "claude-sonnet-4-6",
  "max_tokens": 1000,
  "system": "<system prompt>",
  "messages": [
    {
      "role": "user",
      "content": "<user message>"
    }
  ]
}
Response:

JSON
{
  "content": [
    { "type": "text", "text": "..." }
  ]
}
Design Principles
Drafts, not final output – Every result is clearly labelled as a draft requiring human review
Grounded in user input – Prompts never invent facts; placeholders mark missing details
Auditable & transparent – Users can view the exact system and user prompts sent to the model
Control – Nothing is sent or filed automatically; user always decides next steps
Focused on structure – Each tool uses a clear, repeatable framework (Eisenhower matrix, meeting summary structure, etc.)
Customization
Add a New Tool
Define a render function: renderMyTool()
Add a system + user prompt to PROMPTS.myTool
Add an entry to TOOL_META array
Add the view to the views object and nav
Create a wireMyTool() function with form handlers
Call wireMyTool() in wireView()
Change Colors
Edit CSS custom properties in :root:

CSS
:root {
  --ink: #161A20;
  --sidebar: #14161B;
  --amber: #C1721F;
  /* ... */
}
Update Prompts
Modify text in PROMPTS object to change guardrails, tone, or output format.

Known Limitations
Single session – No persistence; chat history and state are lost on page reload
No auth – Claude API calls require a valid API key in the browser (not secure for production)
Rate limiting – No built-in handling for API rate limits
Error handling – Basic try/catch; some edge cases may not be gracefully handled
Accessibility – Limited ARIA labels and keyboard navigation polish
Browser Support
Modern browsers with ES6 support
CSS Grid and Flexbox required
Tested on Chrome, Firefox, Safari, Edge (recent versions)
Deployment
Save as .html file
Open in browser (local) or deploy to static host (Netlify, Vercel, GitHub Pages, etc.)
Add Claude API key security layer (e.g., backend proxy) for production use
License & Attribution
This project uses:

Claude API by Anthropic
Google Fonts (Fraunces, Inter, JetBrains Mono)
Built with attention to detail, clarity, and human oversight.
