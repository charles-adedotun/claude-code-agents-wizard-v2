Join the Skool - https://www.skool.com/iss-ai-automation-school-6342/about

# Claude Code Agent Orchestration System v2

A simple yet powerful orchestration system for Claude Code that uses specialized agents to manage complex projects from start to finish, with mandatory human oversight and visual testing.

## What Is This?

This is a **custom Claude Code orchestration system** that transforms how you build software projects. Claude Code itself acts as the orchestrator with its 200k context window, managing the big picture while delegating individual tasks to specialized subagents:

- **Claude (You)** - The orchestrator with 200k context managing todos and the big picture
- **Coder Subagent** - Implements one todo at a time in its own clean context
- **Tester Subagent** - Verifies implementations using Chrome DevTools MCP in its own context
- **Stuck Subagent** - Human escalation point when ANY problem occurs

## Key Features

- **No Fallbacks**: When ANY agent hits a problem, you get asked - no assumptions, no workarounds
- **Visual Testing**: Chrome DevTools MCP integration for screenshot-based verification, performance analysis, and network debugging
- **Todo Tracking**: Always see exactly where your project stands
- **Simple Flow**: Claude creates todos → delegates to coder → tester verifies → repeat
- **Human Control**: The stuck agent ensures you're always in the loop

## Quick Start

### Prerequisites

1. **Claude Code CLI** installed ([get it here](https://docs.claude.com/en/docs/claude-code))
2. **Node.js** (for Chrome DevTools MCP)
3. **Chrome browser** installed

### Installation

```bash
# Clone this repository
git clone https://github.com/charles-adedotun/claude-code-agents-wizard-v2.git
cd claude-code-agents-wizard-v2

# Start Claude Code in this directory
claude
```

That's it! The agents are automatically loaded from the `.claude/` directory.

## How to Use

### Starting a Project

When you want to build something, just tell Claude your requirements:

```
You: "Build a todo app with React and TypeScript"
```

Claude will automatically:
1. Create a detailed todo list using TodoWrite
2. Delegate the first todo to the **coder** subagent
3. The coder implements in its own clean context window
4. Delegate verification to the **tester** subagent (Chrome DevTools screenshots)
5. If ANY problem occurs, the **stuck** subagent asks you what to do
6. Mark todo complete and move to the next one
7. Repeat until project complete

### The Workflow

```
USER: "Build X"
    ↓
CLAUDE: Creates detailed todos with TodoWrite
    ↓
CLAUDE: Invokes coder subagent for todo #1
    ↓
CODER (own context): Implements feature
    ↓
    ├─→ Problem? → Invokes STUCK → You decide → Continue
    ↓
CODER: Reports completion
    ↓
CLAUDE: Invokes tester subagent
    ↓
TESTER (own context): Chrome DevTools screenshots & verification
    ↓
    ├─→ Test fails? → Invokes STUCK → You decide → Continue
    ↓
TESTER: Reports success
    ↓
CLAUDE: Marks todo complete, moves to next
    ↓
Repeat until all todos done
```

## How It Works

### Claude (The Orchestrator)
**Your 200k Context Window**

- Creates and maintains comprehensive todo lists
- Sees the complete project from A-Z
- Delegates individual todos to specialized subagents
- Tracks overall progress across all tasks
- Maintains project state and context

**How it works**: Claude IS the orchestrator - it uses its 200k context to manage everything

### Coder Subagent
**Fresh Context Per Task**

- Gets invoked with ONE specific todo item
- Works in its own clean context window
- Writes clean, functional code
- **Never uses fallbacks** - invokes stuck agent immediately
- Reports completion back to Claude

**When it's used**: Claude delegates each coding todo to this subagent

### Tester Subagent
**Fresh Context Per Verification**

- Gets invoked after each coder completion
- Works in its own clean context window
- Uses **Chrome DevTools MCP** to see rendered output
- Takes screenshots to verify layouts
- Tests interactions using UIDs (not CSS selectors)
- Checks console for JavaScript errors
- Monitors network requests for API failures
- Analyzes performance metrics (LCP, CLS, TBT)
- **Never marks failing tests as passing**
- Reports pass/fail back to Claude

**When it's used**: Claude delegates testing after every implementation

### Stuck Subagent
**Fresh Context Per Problem**

- Gets invoked when coder or tester hits a problem
- Works in its own clean context window
- **ONLY subagent** that can ask you questions
- Presents clear options for you to choose
- Blocks progress until you respond
- Returns your decision to the calling agent
- Ensures no blind fallbacks or workarounds

**When it's used**: Whenever ANY subagent encounters ANY problem

## Chrome DevTools MCP

This orchestration system uses **Chrome DevTools MCP** (the official Google tool) for visual testing instead of Playwright.

### Why Chrome DevTools MCP?

| Feature | Chrome DevTools MCP | Playwright MCP |
|---------|---------------------|----------------|
| **Built by** | Google Chrome team | Microsoft |
| **Element selection** | UIDs from accessibility tree | CSS selectors |
| **Console debugging** | `list_console_messages()` | Limited |
| **Network inspection** | `list_network_requests()` | Limited |
| **Performance analysis** | `performance_analyze_insight()` | Not included |
| **Device emulation** | Built-in `emulate()` | Requires setup |

### Available Tools (26 total)

**Input Automation (8 tools)**
- `click`, `drag`, `fill`, `fill_form`, `handle_dialog`, `hover`, `press_key`, `upload_file`

**Navigation (6 tools)**
- `navigate_page`, `new_page`, `list_pages`, `select_page`, `close_page`, `wait_for`

**Emulation (2 tools)**
- `emulate`, `resize_page`

**Performance (3 tools)**
- `performance_start_trace`, `performance_stop_trace`, `performance_analyze_insight`

**Network (2 tools)**
- `list_network_requests`, `get_network_request`

**Debugging (5 tools)**
- `take_screenshot`, `take_snapshot`, `list_console_messages`, `get_console_message`, `evaluate_script`

### Key Difference: UIDs, Not CSS Selectors

Chrome DevTools MCP uses **UIDs from the accessibility tree** for element selection:

```
# Playwright approach (CSS selectors)
click("#submit-button")

# Chrome DevTools MCP approach (UIDs)
take_snapshot() → Returns elements with UIDs
click({ uid: "abc123" }) → Use the UID from snapshot
```

## The "No Fallbacks" Rule

**This is the key differentiator:**

Traditional AI: Hits error → tries workaround → might fail silently
**This system**: Hits error → asks you → you decide → proceeds correctly

Every agent is **hardwired** to invoke the stuck agent rather than use fallbacks. You stay in control.

## Example Session

```
You: "Build a landing page with a contact form"

Claude creates todos:
  [ ] Set up HTML structure
  [ ] Create hero section
  [ ] Add contact form with validation
  [ ] Style with CSS
  [ ] Test form submission

Claude invokes coder(todo #1: "Set up HTML structure")

Coder (own context): Creates index.html
Coder: Reports completion to Claude

Claude invokes tester("Verify HTML structure loads")

Tester (own context): Uses Chrome DevTools MCP
  → navigate_page({ url: "..." })
  → take_snapshot() → Gets element UIDs
  → take_screenshot() → Captures visual state
  → list_console_messages() → Checks for JS errors
Tester: Reports success to Claude

Claude: Marks todo #1 complete

Claude invokes coder(todo #2: "Create hero section")

Coder (own context): Implements hero section
Coder: ERROR - image file not found
Coder: Invokes stuck subagent

Stuck (own context): Asks YOU:
  "Hero image 'hero.jpg' not found. How to proceed?"
  Options:
  - Use placeholder image
  - Download from Unsplash
  - Skip image for now

You choose: "Download from Unsplash"

Stuck: Returns your decision to coder
Coder: Proceeds with Unsplash download
Coder: Reports completion to Claude

... and so on until all todos done
```

## Repository Structure

```
.
├── .claude/
│   ├── CLAUDE.md              # Orchestration instructions for main Claude
│   └── agents/
│       ├── coder.md          # Coder subagent definition
│       ├── tester.md         # Tester subagent definition (Chrome DevTools MCP)
│       └── stuck.md          # Stuck subagent definition
├── .mcp.json                  # Chrome DevTools MCP configuration
├── .gitignore
└── README.md
```

## Learn More

### Resources

- **[SEO Grove](https://seogrove.ai)** - AI-powered SEO automation platform
- **[ISS AI Automation School](https://www.skool.com/iss-ai-automation-school-6342/about)** - Join our community to learn AI automation
- **[Income Stream Surfers YouTube](https://www.youtube.com/incomestreamsurfers)** - Tutorials, breakdowns, and AI automation content

### Support

Have questions or want to share what you built?
- Join the [ISS AI Automation School community](https://www.skool.com/iss-ai-automation-school-6342/about)
- Subscribe to [Income Stream Surfers on YouTube](https://www.youtube.com/incomestreamsurfers)
- Check out [SEO Grove](https://seogrove.ai) for automated SEO solutions

## Contributing

This is an open system! Feel free to:
- Add new specialized agents
- Improve existing agent prompts
- Share your agent configurations
- Submit PRs with enhancements

## How It Works Under the Hood

This system leverages Claude Code's [subagent system](https://docs.claude.com/en/docs/claude-code/sub-agents):

1. **CLAUDE.md** instructs main Claude to be the orchestrator
2. **Subagents** are defined in `.claude/agents/*.md` files
3. **Each subagent** gets its own fresh context window
4. **Main Claude** maintains the 200k context with todos and project state
5. **Chrome DevTools MCP** is configured in `.mcp.json` for visual testing

The magic happens because:
- **Claude (200k context)** = Maintains big picture, manages todos
- **Coder (fresh context)** = Implements one task at a time
- **Tester (fresh context)** = Verifies one implementation at a time using Chrome DevTools
- **Stuck (fresh context)** = Handles one problem at a time with human input
- **Each subagent** has specific tools and hardwired escalation rules

## Best Practices

1. **Trust Claude** - Let it create and manage the todo list
2. **Review screenshots** - The tester provides visual proof of every implementation
3. **Make decisions when asked** - The stuck agent needs your guidance
4. **Don't interrupt the flow** - Let subagents complete their work
5. **Check the todo list** - Always visible, tracks real progress

## Pro Tips

- Use `/agents` command to see all available subagents
- Claude maintains the todo list in its 200k context - check anytime
- Screenshots from tester are saved and can be reviewed
- Each subagent has specific tools - check their `.md` files
- Subagents get fresh contexts - no context pollution!
- Tester uses `take_snapshot()` to get UIDs before interacting with elements

## License

MIT - Use it, modify it, share it!

## Credits

Built by [Income Stream Surfer](https://www.youtube.com/incomestreamsurfers)

Powered by Claude Code's agent system and Chrome DevTools MCP.

---

**Ready to build something amazing?** Just run `claude` in this directory and tell it what you want to create!
