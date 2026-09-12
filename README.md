# IntentGuard

## A Lightweight, Context-Aware Pre-Inference Gate for AI Agents

IntentGuard is a prototype designed to answer one question:

> **Does this message actually need AI computation?**

Instead of sending every user message directly to an AI agent, IntentGuard places a lightweight decision layer before the agent.

If the message does not require action, the agent is not called.

If the message requires a genuine task, it is passed to the AI system.

## The Problem

AI agents can perform multiple inference and tool calls for a single interaction.

But not every user message requires an AI response.

Messages such as:

- "Thank you"
- "Okay, got it"
- "Perfect"
- "That's all"

do not require another AI-agent call.

Sending these messages through an agent can result in unnecessary inference, tokens, latency, API usage, and compute.

This raises a simple engineering question:

> **Should every user message trigger an AI agent?**

## The Idea

IntentGuard acts as a pre-inference gate.

It analyzes the incoming message before the AI agent is invoked.

```text
USER MESSAGE
     |
     v
INTENT GUARD
     |
     +----------------+
     |                |
     v                v
 NO_ACTION           TASK
     |                |
     v                v
   STOP            AI AGENT
                      |
                      v
                 TOOL / LLM
                      |
                      v
                  RESPONSE
# IntentGuard

## A Lightweight, Context-Aware Pre-Inference Gate for AI Agents

IntentGuard is a prototype designed to answer one question:

> **Does this message actually need AI computation?**

Instead of sending every user message directly to an AI agent, IntentGuard places a lightweight decision layer before the agent.

If the message does not require action, the agent is not called.

If the message requires a genuine task, it is passed to the AI system.

## The Problem

AI agents can perform multiple inference and tool calls for a single interaction.

But not every user message requires an AI response.

Messages such as:

- "Thank you"
- "Okay, got it"
- "Perfect"
- "That's all"

do not require another AI-agent call.

Sending these messages through an agent can result in unnecessary inference, tokens, latency, API usage, and compute.

This raises a simple engineering question:

> **Should every user message trigger an AI agent?**

## The Idea

IntentGuard acts as a pre-inference gate.

It analyzes the incoming message before the AI agent is invoked.

```text
USER MESSAGE
     |
     v
INTENT GUARD
     |
     +----------------+
     |                |
     v                v
 NO_ACTION           TASK
     |                |
     v                v
   STOP            AI AGENT
                      |
                      v
                 TOOL / LLM
                      |
                      v
                  RESPONSE
The goal is to make the cheapest reasonable decision first and perform more expensive AI computation only when it is needed.

## How It Works

IntentGuard combines several lightweight signals:

- Task signals
- Detects obvious questions and requests.
- Semantic similarity
- Uses a lightweight embedding model to compare messages with examples of actionable and non-actionable messages.
- Context awareness
- Uses recent conversation history when interpreting a message.
- The history buffer is limited to the 10 most recent messages.
- Decision gate
- NO_ACTION → stop before the AI agent.
- TASK → allow the request to reach the AI system.

The system is designed to be conservative: genuine tasks should be allowed through rather than silently discarded.

Context Awareness

Intent can depend on the previous conversation.
For example:
User: What is RAG?
Agent: RAG combines retrieval with generation.

User: Okay, got it.
→ NO_ACTION
But:
User: What is RAG?
Agent: RAG combines retrieval with generation.

User: Can you explain it in more detail?
→ TASK
This demonstrates why a context-aware gate is more useful than simply matching keywords.

Experimental Results

IntentGuard was evaluated on a 40-message prototype test set containing both non-actionable messages and genuine tasks.
| Metric                       |  Result |
| ---------------------------- | ------: |
| Total messages               |      40 |
| AI calls without IntentGuard |      40 |
| AI calls with IntentGuard    |      24 |
| Calls avoided                |      16 |
| Call reduction               |     40% |
| Test classification accuracy |    100% |
| Time without IntentGuard     | 38.80 s |
| Time with IntentGuard        | 18.32 s |
| Measured time reduction      |   52.8% |
In this benchmark, IntentGuard prevented 16 of 40 messages from reaching the AI agent.

The measured end-to-end processing time decreased from 38.80 seconds to 18.32 seconds.

These are prototype results from a local Colab environment and should not be interpreted as universal performance guarantees.

Demonstration Tools

When IntentGuard classifies a message as a task, the prototype can route the request to different capabilities:

AI Agent — handles general tasks.
Web Search — retrieves current information.
Weather Tool — retrieves live weather information.
PDF Q&A — searches an uploaded PDF and answers questions from its content.

These tools demonstrate what can happen after the pre-inference gate allows a request through.

LangSmith Tracing
The project uses LangSmith to trace IntentGuard decisions and the workflow.
User Message
     |
     v
IntentGuard
     |
     v
Decision
     |
     v
AI Agent / Tool
     |
     v
Result

This makes the decision and execution flow observable during experimentation.

Limitations

This is an early prototype.

The evaluation dataset contains only 40 test messages.
Some decisions use predefined examples and rules.
The local FLAN-T5 model is used for demonstration and is not representative of production LLM quality.
Runtime measurements depend on the Colab environment.
Larger and more diverse datasets are required to properly evaluate false positives and false negatives.
The current implementation does not yet provide a direct measurement of electricity or energy savings.
Future Work

Future versions could include:

A dedicated intent-classification model.
Evaluation on thousands of real-world messages.
Better confidence calibration.
Human-in-the-loop handling for uncertain cases.
Token and API-cost measurements.
Direct compute and energy-efficiency measurements.
Integration as middleware between users and production AI agents.
Evaluation across different LLMs and agent frameworks.
Project Goal

The long-term goal of IntentGuard is to explore whether AI-agent systems can become more resource-efficient by deciding when AI computation is actually necessary.

The key principle is:

Do not spend expensive AI computation on a message that does not require it.

Conclusion

IntentGuard demonstrates a simple pre-inference approach to AI-agent efficiency.

Instead of treating every incoming message as a reason to invoke an AI agent, the system introduces a lightweight, context-aware decision layer first.

The prototype combines:

Task-signal detection
Semantic similarity
Conversation context
Pre-inference gating
Tool routing
AI-agent execution
LangSmith tracing

In the 40-message prototype benchmark, IntentGuard avoided 16 AI-agent calls, resulting in a 40% reduction in agent calls.

The next step is to evaluate the approach on larger, more diverse and production-like workloads.

References
International Energy Agency — Key Questions on Energy and AI
Microsoft Research — Energy use of AI inference, efficiency pathways, and test-time scaling
Gartner — AI Inference Costs Per Agentic Workflow
Nature Energy — Energy use of AI responses
Brave Research — AgentStop: Terminating Local AI Agents Early to Save Energy

