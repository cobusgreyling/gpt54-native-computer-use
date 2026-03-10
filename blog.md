# GPT-5.4 Native Computer Use

## The first general-purpose model that operates your computer

I wrote about OpenAI's Operator and CUA (Computer-Using Agent) when it first launched. The idea was compelling but the execution felt early. It worked through a hosted browser session, the latency was noticeable, and the success rates on real-world tasks were modest at best.

GPT-5.4 changes the picture entirely. Computer use is no longer a separate product or a hosted wrapper. It is a native capability baked into the model itself. You send a screenshot, the model returns structured actions. No intermediary. No hosted session. Just a tool type in the API.

What caught my attention is how far the benchmarks have moved. CUA struggled with complex multi-step workflows. GPT-5.4 now outperforms humans on the same benchmarks. That is not an incremental improvement. That is a category shift.

### In short

GPT-5.4 is the first general-purpose model from OpenAI with native computer-use capabilities.

It inspects screenshots, returns structured UI actions, and lets agents operate software through the interface — clicking, typing, scrolling, dragging — without custom integrations per application.

One model. Any application. No application-specific API required.

On OSWorld-Verified, GPT-5.4 achieves a 75.0% success rate. Human performance on the same benchmark sits at 72.4%. GPT-5.2 scored 47.3%.

The model surpasses human-level performance on general computer operation tasks.

### What native computer use means

Previous approaches to computer automation required application-specific APIs, browser extensions, or scripting frameworks. Each integration was bespoke.

GPT-5.4 takes a fundamentally different approach. It looks at the screen, decides what to do, and issues structured actions. The same model that writes code and answers questions can also navigate your desktop.

The loop is simple:

1. Send a task description with the `computer` tool enabled
2. The model returns an `actions[]` array — click, type, scroll, drag, keypress
3. Your harness executes the actions
4. Capture a screenshot
5. Send the screenshot back
6. Repeat until the task is complete

No DOM parsing. No accessibility tree. No selectors. Just pixels and actions.

### The action vocabulary

GPT-5.4 supports a complete set of UI primitives:

| Action | What it does |
|---|---|
| `screenshot` | Capture current UI state |
| `click` | Click at (x, y) coordinates — left, middle, or right button |
| `double_click` | Double-click at position |
| `type` | Enter text |
| `keypress` | Press keyboard keys |
| `scroll` | Scroll at position with scrollX/scrollY |
| `drag` | Drag from one position to another |
| `move` | Move cursor to coordinates |
| `wait` | Pause execution |

This vocabulary covers everything a human can do with a mouse and keyboard.

### How the API works

Enable computer use by adding the `computer` tool:

```python
from openai import OpenAI

client = OpenAI()

response = client.responses.create(
    model="gpt-5.4",
    tools=[{"type": "computer"}],
    input="Open the browser and search for 'NVIDIA Nemotron'"
)
```

The model responds with structured actions:

```json
{
  "output": [{
    "type": "computer_call",
    "call_id": "call_001",
    "actions": [
      {"type": "screenshot"},
      {"type": "click", "button": "left", "x": 405, "y": 157},
      {"type": "type", "text": "NVIDIA Nemotron"}
    ]
  }]
}
```

After executing the actions, send a screenshot back:

```python
response = client.responses.create(
    model="gpt-5.4",
    tools=[{"type": "computer"}],
    previous_response_id=response.id,
    input=[{
        "type": "computer_call_output",
        "call_id": call_id,
        "output": {
            "type": "computer_screenshot",
            "image_url": f"data:image/png;base64,{screenshot_base64}",
            "detail": "original"
        }
    }]
)
```

The `detail: "original"` setting preserves full resolution (up to 10.24M pixels) for accurate click targeting.

### The agent loop

The implementation pattern is a build-run-verify-fix loop:

```
Task → Model plans → Actions emitted → Harness executes →
Screenshot captured → Model verifies → More actions or done
```

The model does not just execute blindly. It inspects the result of each action batch, verifies whether the step succeeded, and adjusts if something went wrong.

This self-correcting behaviour is what pushes the success rate above human performance. On property tax portal evaluations across ~30K sites, GPT-5.4 achieved 95% success on the first attempt and 100% within three attempts.

### Three ways to implement

**1. Native computer tool** — The model inspects screenshots and returns coordinate-based actions. Best for general-purpose desktop or browser automation.

**2. Code execution** — Provide a JavaScript or Python REPL with a persistent browser object. The model writes Playwright scripts that combine visual and programmatic interaction.

**3. Custom tool wrapping** — Wrap existing Playwright/Selenium harnesses as normal tools. The model calls them by name with structured arguments.

The native approach requires no code per application. The programmatic approaches offer more precision for repetitive workflows.

### Benchmarks

| Benchmark | GPT-5.4 | GPT-5.2 | Human |
|---|---|---|---|
| OSWorld-Verified | 75.0% | 47.3% | 72.4% |
| WebArena-Verified | Top score | — | — |
| Property tax portals (30K sites) | 95% first attempt | ~73–79% | — |

GPT-5.4 completed sessions approximately 3x faster than prior CUA models while using approximately 70% fewer tokens.

### Model variants

GPT-5.4 ships in three variants:

**GPT-5.4** — The standard model. Available via API as `gpt-5.4`. Computer use supported.

**GPT-5.4 Thinking** — Reasoning-focused variant available in ChatGPT. Chain-of-thought before actions.

**GPT-5.4 Pro** — Maximum performance. Available via API as `gpt-5.4-pro`. For the most complex agentic workflows.

All variants support up to 1M tokens of context, enabling agents to plan and execute across long task horizons.

### Safety considerations

OpenAI recommends running computer use in an isolated environment:

- Use a sandboxed browser or Docker VM
- Keep a human in the loop for high-impact actions
- Pass empty `env` objects to prevent credential leakage
- Disable extensions and file-system access
- Treat all page content as untrusted input

The model can be manipulated by adversarial content on screen. Isolation is not optional.

### The bigger picture

Computer use collapses the integration problem.

Instead of building connectors for every application, you point the model at the screen and describe what you want done. The model figures out the clicks.

This is not a wrapper around Selenium. It is a vision-language model that understands GUIs natively. It reads buttons, menus, forms, and dialog boxes the same way a human does — by looking at them.

The 1M token context window means multi-step workflows spanning dozens of screens are feasible in a single session.

> The interface is the API.

Every application with a GUI is now programmable through natural language.

---

### Sources

- [Introducing GPT-5.4 | OpenAI](https://openai.com/index/introducing-gpt-5-4/)
- [GPT-5.4 Computer Use Guide | OpenAI API](https://developers.openai.com/api/docs/guides/tools-computer-use)
- [OpenAI launches GPT-5.4 with Pro and Thinking versions | TechCrunch](https://techcrunch.com/2026/03/05/openai-launches-gpt-5-4-with-pro-and-thinking-versions/)
- [GPT-5.4 with native computer use mode | VentureBeat](https://venturebeat.com/technology/openai-launches-gpt-5-4-with-native-computer-use-mode-financial-plugins-for)
- [GPT-5.4 Advanced Reasoning and Computer-Use | CybersecurityNews](https://cybersecuritynews.com/gpt-5-4-launched/)
