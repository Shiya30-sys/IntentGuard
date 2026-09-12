# IntentGuard

## A Lightweight Pre-Inference Gate for Resource-Efficient AI Agents

IntentGuard is a prototype designed to answer a simple question:

**Does this message actually need AI computation?**

Instead of sending every user message directly to an AI agent, IntentGuard places a lightweight decision layer before inference.

## Architecture

    User Message
         |
         v
    IntentGuard
         |
      +--+--+
      |     |
      v     v
    NO_ACTION  TASK
      |         |
      v         v
     STOP    Tool Router
                |
        +-------+-------+-------+
        |               |       |
        v               v       v
    Web Search       Weather   PDF Q&A
        |               |       |
        +-------+-------+-------+
                |
                v
             AI Agent

## Why IntentGuard?

AI-agent systems can consume compute, tokens, API calls, latency, and money when they perform unnecessary inference or tool calls.

IntentGuard attempts to stop non-actionable messages before that computation occurs.

### Examples

| User Message | Decision | Action |
|---|---|---|
| "Thank you" | NO_ACTION | AI agent not called |
| "Okay, got it" | NO_ACTION | AI agent not called |
| "What is RAG?" | TASK | AI agent called |
| "What is the weather today?" | TASK | Weather tool |
| "Search the latest AI news" | TASK | Web search |
| "What does the PDF say?" | TASK | PDF Q&A |

## How It Works

IntentGuard combines several layers:

- **Task signals** — identifies obvious requests and questions.
- **Semantic similarity** — compares messages with examples using a lightweight embedding model.
- **Context awareness** — uses recent conversation context when interpreting a message.
- **Decision gate** — stops non-actionable messages or forwards tasks for execution.
- **Tool routing** — selects the appropriate tool for the task.

The conversation history is limited to the **10 most recent messages**.

## Tools

- **AI Agent** — handles general tasks.
- **Web Search** — retrieves current information.
- **Weather Tool** — retrieves live weather information.
- **PDF Q&A** — searches an uploaded PDF and answers questions from its content.

## Why This Problem Matters

AI systems are becoming increasingly capable, but more capable systems can also require more computation.

The International Energy Agency reports that AI-focused data-centre electricity consumption is growing rapidly, while more energy-intensive applications such as reasoning and agentic AI can require substantially more energy than simple text generation.

Recent research from Microsoft also shows that longer reasoning and agentic queries can consume substantially more energy than simpler AI queries.

At the same time, inference costs are becoming an important consideration as AI systems perform more complex, multi-step tasks.

This creates an important engineering question:

> **Should every user message trigger an AI agent?**

IntentGuard explores one possible answer.

## Experimental Results

The prototype was evaluated on a 40-message test set.

| Metric | Result |
|---|---:|
| Total messages | 40 |
| AI calls without IntentGuard | 40 |
| AI calls with IntentGuard | 24 |
| Calls avoided | 16 |
| Call reduction | 40% |
| Test classification accuracy | 100% |
| Time without IntentGuard | 38.80 s |
| Time with IntentGuard | 18.32 s |
| Measured time reduction | 52.8% |

These results come from a local 40-message benchmark and are intended as a prototype demonstration, not a universal performance guarantee.

## LangSmith Tracing

The project uses LangSmith to trace IntentGuard decisions and the full workflow.

    User Message
         |
         v
    IntentGuard
         |
         v
      Decision
         |
         v
    Tool Selection
         |
         v
    Tool / AI Agent
         |
         v
       Result

## Limitations

This is an early prototype.

- The evaluation dataset is relatively small.
- Some decisions use predefined examples and rules.
- The local FLAN-T5 model is used for demonstration and is not representative of production LLM quality.
- Runtime measurements depend on the Colab environment.
- Larger and more diverse datasets are needed to evaluate false positives and false negatives.

## Future Work

- Train a dedicated intent-classification model.
- Evaluate on thousands of real-world messages.
- Measure token and API-cost savings.
- Measure compute and energy efficiency.
- Add confidence thresholds and human-in-the-loop handling.
- Integrate IntentGuard as middleware for production AI agents.
- Evaluate across different LLMs and agent frameworks.

## Project Goal

The long-term goal is to explore whether a lightweight pre-inference gate can make AI-agent systems more efficient without silently dropping genuine user tasks.

## References

- International Energy Agency — *Key Questions on Energy and AI*
- Microsoft Research — *Energy use of AI inference, efficiency pathways, and test-time scaling*
- Gartner — *AI Inference Costs Per Agentic Workflow*
- Nature Energy — *Energy use of AI responses*
- Brave Research — *AgentStop: Terminating Local AI Agents Early to Save Energy*
