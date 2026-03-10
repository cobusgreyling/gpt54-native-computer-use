# GPT-5.4 Native Computer Use

**The first general-purpose model that truly operates your computer.**

GPT-5.4 is the first general-purpose model from OpenAI with native computer-use capabilities. It inspects screenshots, returns structured UI actions, and lets agents operate software through the interface -- clicking, typing, scrolling, dragging -- without custom integrations per application.

On OSWorld-Verified, GPT-5.4 achieves a **75.0% success rate**. Human performance on the same benchmark sits at **72.4%**.

## Key Highlights

- Native `computer` tool in the `/v1/responses` API -- no hosted browser sessions
- Full action vocabulary -- click, type, scroll, drag, keypress, screenshot
- Self-correcting agent loop -- plan, act, verify, adjust
- 1M token context window for multi-screen workflows
- 95% first-attempt success rate on property tax portal evaluations (~30K sites)
- 3x faster than prior CUA models, ~70% fewer tokens

## Read the Full Article

**[Read the blog post](./blog.md)**

## Author

**Cobus Greyling** -- Chief Evangelist @ Kore.ai

## Sources

- [Introducing GPT-5.4 | OpenAI](https://openai.com/index/introducing-gpt-5-4/)
- [GPT-5.4 Computer Use Guide | OpenAI API](https://developers.openai.com/api/docs/guides/tools-computer-use)
- [OpenAI CUA Sample App](https://github.com/openai/openai-cua-sample-app)
- [OpenAI launches GPT-5.4 | TechCrunch](https://techcrunch.com/2026/03/05/openai-launches-gpt-5-4-with-pro-and-thinking-versions/)
- [GPT-5.4 with native computer use mode | VentureBeat](https://venturebeat.com/technology/openai-launches-gpt-5-4-with-native-computer-use-mode-financial-plugins-for)
