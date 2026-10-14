# OpenEdge + AI Hackathon: Technical Starter Kit

**AIE University, South Africa · Kickoff: Monday, 19 October 2026 · Build period: 19–29 October 2026 · Prizes and presentations: Thursday, 29 October 2026**

You have ten days to build something that combines Progress OpenEdge with AI. This kit gives you a map: what to learn, which tools exist, and how to avoid the usual traps. You do not need to read everything. Pick what your project needs, and ask in Discord when you are stuck.

> **The challenge:** Build a useful prototype that uses Progress OpenEdge as the application and data foundation, with an AI capability on top. Work solo or in a team. Show the problem, how your solution works, and why it matters.

## Before Monday: your 30-minute checklist

- [ ] Install the OpenEdge Developers Kit: Classroom Edition (section 1) and run one ABL program.
- [ ] Create an account with at least one LLM provider, or confirm which model access the organizers give you. Keep API keys out of Git (section 10).
- [ ] Join the Discord server and read the starter-kit channel.
- [ ] Skim sections 6 to 8 so the words RAG, MCP and agent are familiar on day one.
- [ ] Find a teammate, or decide to work solo.

## How to use this guide

1. **Start with OpenEdge and ABL.** Find out where the data and the business rules live.
2. **Learn how systems talk to each other.** HTTP, REST and JSON.
3. **Learn the AI basics.** LLMs, prompts and what models get wrong.
4. **Add only what the project needs.** RAG gives the model relevant knowledge. MCP gives it a standard way to call tools. Agents handle multi-step work.
5. **Build one small slice that works end to end.** A reliable demo beats an ambitious architecture that does not run.

## What "production-ready" means in this hackathon

Nobody expects a deployed product after ten days. We do expect you to build like someone who will have to maintain it. Concretely:

- **It runs from the README.** Another person can clone your repo and start it.
- **Secrets stay out of the code.** No keys, passwords or tokens in Git.
- **The AI never has the last word on facts.** Numbers and records come from OpenEdge. The model explains them.
- **Risky actions are gated.** Writes and deletions need validation and confirmation.
- **Failures are handled.** Timeouts, empty results and bad model output do not crash the demo.
- **You can show it works.** A small test set, with at least one case that is meant to fail.

Treat these as design constraints from day one. They are much harder to add in the last two days.

## Learning path at a glance

| Stage | Learn | Outcome |
|---|---|---|
| 1 | OpenEdge platform and OEDK Classroom Edition | Development environment ready |
| 2 | ABL and data access | A small program that reads or updates sample data |
| 3 | HTTP, REST and JSON | You can expose and call a service |
| 4 | LLMs and prompting | One useful, structured model interaction |
| 5 | RAG | An answer grounded in retrieved documents, with sources |
| 6 | MCP | An AI application calling an approved tool |
| 7 | Agents and orchestration | A safe, multi-step workflow |
| 8 | Integration, evaluation, hardening | An end-to-end prototype you can demo |

---

# 1. OpenEdge: the application foundation

## What is OpenEdge?

Progress OpenEdge is a platform for building and running business applications. ABL (Advanced Business Language) is its programming language, used to write the application logic. An OpenEdge application typically combines a database, business rules and services that other systems can call.

In an AI-enabled solution, OpenEdge stays the trusted source for records and rules. The AI helps people find information, phrase requests or understand results. Your application code decides what is allowed and does the actual work.

## Start here: official resources

- **OpenEdge Developers Kit: Classroom Edition (OEDK):** https://www.progress.com/openedge/classroom-edition. The free entry point for students and new developers. Check the current download steps and eligibility on this page.
- **OEDK documentation, introduction:** https://documentation.progress.com/output/oedk/oedk/introduction-to-openedge-developers-kit.html
- **OEDK documentation, getting started:** https://documentation.progress.com/output/oedk/oedk/how-to-get-started-with-openedge-developers-kit.html
- **OpenEdge 12.8 documentation hub:** https://docs.progress.com/category/openedge128
- **ABL Basic Guided Journey:** https://docs.progress.com/bundle/openedge-abl-basic-guided-journey-128/page/Statements.html
- **ABL Reference:** https://docs.progress.com/bundle/openedge-abl-reference-128/page/Introduction-to-the-ABL-Reference.html

### Installation checklist

- Read the OEDK download, account, operating system and licensing requirements on the official pages.
- Install the kit and follow the official post-installation steps.
- Start Progress Developer Studio for OpenEdge. Create your workspace in a separate folder, never inside the product installation directory.
- Open a sample project or run a short ABL program to confirm everything works.
- If anything fails, post in the technical-help channel early. Installation problems are the most common reason teams lose a day.

Packaging, supported platforms and access rules change over time. Follow the current official pages, and avoid old screenshots or third-party downloads.

## ABL concepts to recognize

- **Variables and data types:** hold the values a program works with.
- **Statements and procedures:** define what the program does.
- **Database records and queries:** read and change application data.
- **Business rules:** decide whether an operation is allowed.
- **Error handling:** deal with unexpected conditions explicitly.

You will also run into **Business Entities**, **temp-tables** and **JSON objects** (ABL has built-in JSON classes) when you expose data as a service. Search the 12.8 documentation for those terms when you get to section 2.

### Mini-exercise

Sketch an inventory feature. Which fields does a product record need? Which rules must pass before stock is reduced? What should the caller get back?

Resist starting with AI. Get the data and the rule clear first.

---

# 2. ABL, data access and integration

Applications talk to each other through defined interfaces. HTTP is the protocol, REST is a common style for designing APIs on top of it, and JSON is the usual data format.

## Official resources

- **OpenEdge documentation hub:** https://docs.progress.com/category/openedge128
- **Develop an HTTP client application in ABL** (call external APIs, including LLM APIs, from ABL): https://docs.progress.com/bundle/openedge-abl-develop-http-clients/page/Develop-an-HTTP-client-application-in-ABL.html
- **Develop an ABL service using the WEB transport:** https://docs.progress.com/bundle/openedge-abl-develop-services-128/page/Develop-an-ABL-service-using-the-WEB-transport.html
- **ABL Web App project type:** https://docs.progress.com/bundle/openedge-abl-develop-services-128/page/ABL-Web-App-project-type.html

Match the documentation version to the OpenEdge version you installed. Pages sometimes move between releases, so use the documentation search if a link no longer resolves.

## Key terms

- **API:** an interface that lets one application request data or functions from another.
- **HTTP request/response:** the client sends a request, the server returns a status code and a body.
- **REST:** an API style built around resources and standard HTTP methods (GET, POST, PUT, DELETE).
- **JSON:** a text format for structured data.
- **Authentication and authorization:** who is calling, and what that caller may do.

## What a good contract looks like

Before writing any code, write the request and response down. For the product `P100`:

```http
GET /inventory/products/P100
Authorization: Bearer <token>
```

```json
{
  "productId": "P100",
  "name": "Sample widget",
  "quantityAvailable": 42,
  "unit": "pcs"
}
```

And decide what happens when something goes wrong:

```json
{
  "error": "PRODUCT_NOT_FOUND",
  "message": "No product with id P100"
}
```

(returned with HTTP status 404). A clear error contract matters for AI too. A model that receives a vague error will often invent an explanation.

### Mini-exercise

For the inventory example above, write down:

1. What request does the client send?
2. What does the service return on success?
3. What happens if the product does not exist?
4. How do you stop an unauthorized caller from changing stock?

If you cannot get a real integration working in the first lab, use sample data behind a mock service and swap in OpenEdge later. Keep the contract identical so nothing else changes.

---

# 3. AI and large language models (LLMs)

## What is an LLM?

A large language model is a system trained to process and generate language. It can summarize, explain, classify and rewrite text, and it can help people interact with applications in plain language. It generates likely text from its input. It does not know the current state of your OpenEdge database unless your application tells it.

LLMs can produce confident but wrong answers, misread ambiguous requests, and answer differently when you ask the same thing twice. Treat output as a suggestion that needs validation.

## Terms you will meet

- **Token:** the unit models read and write. Cost and limits are counted in tokens.
- **Context window:** how much text the model can consider at once.
- **System prompt:** standing instructions for the model, separate from the user's message.
- **Temperature:** controls how varied the output is. Use low values when you want consistent results.
- **Structured output:** asking the model to return JSON that matches a schema, so your code can parse it safely.
- **Tool calling (function calling):** the model asks your application to run a function and receives the result. This is the basis of agents and MCP.

## Learning resources

- **Microsoft, Generative AI for Beginners (video series):** https://learn.microsoft.com/en-us/shows/generative-ai-for-beginners/
- **Microsoft course repository (lessons and code):** https://github.com/microsoft/generative-ai-for-beginners. Check each lesson's prerequisites and required services.
- **OpenAI, prompting:** https://developers.openai.com/api/docs/guides/prompting
- **OpenAI, prompt engineering:** https://developers.openai.com/api/docs/guides/prompt-engineering
- **Anthropic, prompt engineering overview:** https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview
- **Google Cloud Skills Boost:** https://www.cloudskillsboost.google/. Search for the introductory generative AI and responsible AI courses. Free access varies.

The techniques in these guides apply across models. Pick whichever provider you have access to.

## Prompting basics

A useful prompt covers five things:

1. **Task:** what the model should do.
2. **Context:** the facts it should use.
3. **Constraints:** what it must not do or assume.
4. **Output format:** a short explanation, or JSON with named fields.
5. **Uncertainty:** what to do when information is missing (ask, or say so, but do not guess).

### Example prompt

> You are helping a student understand an inventory report. Summarize the report below in three bullet points. Use only the information in the report. Do not invent stock values. If a value is missing, write "not provided".

### Mini-exercise

Give a model a short, fictional order report. Delete one important field and see whether the model fills the gap with an invented value. Then fix your prompt so the gap is reported instead.

---

# 4. Retrieval-Augmented Generation (RAG)

## What is RAG?

RAG connects a search step to a language model. When a user asks a question, your application retrieves relevant passages from an approved source and gives them to the model along with the question. The model answers from that material instead of from general training alone.

The basic flow:

1. The user asks a question.
2. The application searches documents or records.
3. The best passages come back.
4. The model receives the question plus those passages.
5. The application returns the answer, ideally with references to the sources.

RAG helps when answers must reflect a specific set of documents, such as manuals, policies or student guidance.

## The parts you will decide on

- **Chunking:** how documents are split into passages. Chunks that are too small lose context, and chunks that are too large dilute the match.
- **Embeddings:** numeric representations of text that let you search by meaning.
- **Retrieval:** vector search, keyword search, or both together (hybrid search), which usually works better than either alone.
- **Citations:** returning which passage supported which part of the answer.

You can build this yourself with a vector database and an embedding model, or use a managed platform. For OpenEdge projects, Progress offers one (next section).

## Progress Agentic RAG (PARAG)

Progress Agentic RAG is a RAG-as-a-Service platform (formerly Nuclia). It handles extraction, chunking, embedding, indexing and retrieval for you, and returns answers with citations. It is designed to combine structured OpenEdge data with unstructured content such as PDFs, manuals and emails. This is a good option if you want to spend your time on the OpenEdge integration instead of on retrieval plumbing.

- **Product page:** https://www.progress.com/agentic-rag
- **Documentation:** https://docs.rag.progress.cloud/docs
- **Developer guides:** https://docs.rag.progress.cloud/docs/develop/intro/
- **Developer quickstart (video, covers API keys, document upload, querying and citations):** https://www.progress.com/resources/videos/guide-progress-agentic-rag-developer-quickstart
- **Blog, Part 1, getting started with Progress Agentic RAG:** search for "Part 1: Getting Started with Progress' RAG-as-a-Service Platform" on https://www.progress.com/blogs
- **Blog, how Agentic RAG complements OpenEdge:** https://www.progress.com/blogs/from-reliable-data-to-actionable-insight-agentic-rag-solution-for-the-progress-openedge-platform
- **Hackathon story, two students who built a demand-forecasting solution with OpenEdge and Agentic RAG:** search for "Hackathon Success Story" on https://www.progress.com/blogs


## Other learning resources

- **Microsoft, Generative AI for Beginners:** https://github.com/microsoft/generative-ai-for-beginners. Look for the lessons on search, embeddings and building applications.
- **OpenAI, retrieval guide:** https://developers.openai.com/api/docs/guides/retrieval

## When to use RAG

- A university assistant that answers from approved student guidance.
- A support assistant that searches product manuals.
- An internal assistant that finds company procedures.
- A business application that explains a result using a policy or knowledge article.

**RAG does not guarantee correct answers.** Retrieval can miss the right passage, return an irrelevant one, or surface outdated content. Show your sources and test with questions where you already know the answer.

### Mini-exercise

Pick five short, non-sensitive documents. Write five questions that the documents can answer. For each question, note which passage should support the answer. That is your first evaluation set, and you will reuse it in section 9.

---

# 5. Model Context Protocol (MCP)

## What is MCP?

The Model Context Protocol is an open standard for connecting AI applications to tools and data. It defines how an AI application discovers and calls capabilities that an MCP server exposes, so you do not write a custom integration for every pairing of model and tool.

The roles:

- **Host:** the AI application the user works with.
- **Client:** the part of the host that talks to an MCP server.
- **Server:** exposes capabilities such as tools and resources.
- **Tool:** an operation the model can request, such as looking up an order.
- **Resource:** data or context the server makes available.

MCP is a connection protocol. It is not a model, not a database, and it does not make a tool call safe. Security still depends on how you build and permission the server.

## Official learning resources

- **MCP introduction:** https://modelcontextprotocol.io/docs/getting-started/intro
- **MCP architecture:** https://modelcontextprotocol.io/docs/learn/architecture
- **MCP documentation:** https://modelcontextprotocol.io/
- **MCP reference servers and examples:** https://github.com/modelcontextprotocol/servers
- **SDKs for building your own server:** https://github.com/modelcontextprotocol/python-sdk and https://github.com/modelcontextprotocol/typescript-sdk
- **MCP Inspector (a visual tool for testing your server before connecting a model):** https://github.com/modelcontextprotocol/inspector

## MCP and OpenEdge

Progress provides two MCP-related products for OpenEdge 12.8:

- **OpenEdge MCP Server.** It exposes existing OpenEdge REST and WEB services, and PAS for OpenEdge business logic, as MCP tools, so an AI assistant can call your application without you rewriting it. The documentation covers installation, authentication, rate limiting, and logging. Start here: https://docs.progress.com/bundle/openedge-mcp-server-128/page/Get-started-with-OpenEdge-MCP-Server.html
- **OpenEdge AI Assistant** (previously called the MCP Connector for ABL). It brings ABL reference documentation and examples into your AI coding assistant, so the assistant writes better ABL. This helps you build faster. It is not part of your application's runtime.

Check the current documentation for installation steps, supported versions and anything that applies to your environment: https://docs.progress.com/category/openedge128

Not every OpenEdge application exposes MCP tools out of the box. You (or the MCP Server) need to expose specific operations on purpose, and each operation should be one you are happy for a model to request.

## What a tool definition looks like

This is the shape an MCP client sees when it lists your server's tools:

```json
{
  "name": "get_order_status",
  "description": "Return the status of one order. Read-only. Use when the user asks where an order is.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "orderId": { "type": "string", "description": "Order identifier, for example ORD-1042" }
    },
    "required": ["orderId"]
  }
}
```

The description is part of your prompt. The model reads it to decide when to call the tool, so write it carefully. Vague tool descriptions cause wrong tool calls.

### Mini-exercise

Design the read-only tool `get_order_status`. Define:

- its purpose,
- the input it needs,
- the fields it may return,
- what it returns when the order does not exist,
- who or what is allowed to call it.

Start read-only. Add write operations only after you have thought through authorization, validation and confirmation.

---

# 6. AI agents and agentic systems

## What is an agent?

An agentic system uses a model to decide or coordinate steps toward a goal, usually by calling tools and reading the results. The spectrum runs from a fixed workflow (your code decides the steps, the model fills in language tasks) to a fully autonomous agent (the model chooses the next action each time).

Example: an assistant receives a request, looks up an order, reads the status, and writes a summary. If it is allowed to act, it proposes the next step, and a person approves anything with consequences.

Many problems do not need an agent. A fixed sequence of ordinary steps is more predictable, easier to test and cheaper to run. Move up to an agent only when the path really cannot be known in advance.

## Orchestration patterns

Most "multi-agent" designs are combinations of a few simple patterns. Learn these before reaching for a framework:

| Pattern | What it does | Use it when |
|---|---|---|
| **Prompt chaining** | Output of one model call feeds the next | The task splits into fixed steps (extract, then validate, then summarize) |
| **Routing** | A first call classifies the request and sends it to the right handler | Different request types need different prompts, tools or data |
| **Parallelization** | Independent calls run at the same time and the results are merged | Subtasks do not depend on each other, or you want several checks |
| **Orchestrator and workers** | One model breaks down the task and delegates to others | You cannot predict the subtasks in advance |
| **Evaluator and optimizer** | One call produces, another critiques, repeat | Quality criteria are clear and iteration improves the result |

The first article below describes all five in detail, with guidance on when each one is worth the added complexity.

## Design rules for agents that touch business data

- **Give the agent few, well-described tools.** Each tool should do one thing, validate its own input and return clear errors.
- **Put limits on the loop.** Set a maximum number of steps, a timeout, and a budget for tokens or cost.
- **Keep state explicit.** Decide what is remembered between steps and where it is stored. Avoid hidden state.
- **Make write actions safe to retry.** Use idempotency keys so a repeated call does not charge or ship twice.
- **Add a human approval step** before any action that changes records, moves money or deletes data.
- **Log every tool call** with its inputs, outputs and the user request that triggered it. You will need this for debugging and for your demo.

## Recommended resources

- **Anthropic, Building Effective Agents** (workflows versus agents, the patterns above, and when to keep things simple): https://www.anthropic.com/engineering/building-effective-agents
- **OpenAI, agents guide, including orchestration:** https://developers.openai.com/api/docs/guides/agents and https://developers.openai.com/api/docs/guides/agents/orchestration
- **Microsoft, AI Agents for Beginners (lessons and code):** https://github.com/microsoft/ai-agents-for-beginners
- **MCP architecture** (how an agent connects to tools): https://modelcontextprotocol.io/docs/learn/architecture

## Frameworks you can use (all optional)

You do not need a framework. A loop that calls the model, runs the requested tool and feeds the result back is about 50 lines of code, and writing it once helps you understand what the frameworks do. If you want one, these are widely used:

| Framework | Good for | Repository |
|---|---|---|
| OpenAI Agents SDK | Agents with tools, handoffs and tracing | https://github.com/openai/openai-agents-python |
| LangGraph | Explicit graph-style workflows with state and human-in-the-loop steps | https://github.com/langchain-ai/langgraph |
| Claude Agent SDK | Agents built on Claude with built-in tool use | https://github.com/anthropics/claude-agent-sdk-python |
| Official MCP SDKs | Building MCP servers and clients | links in section 5 |

Choose by what you already know and which model access you have. A framework adds a learning curve, and with ten days to build, a simpler design that works is the better trade.

### Mini-exercise

Draw the workflow for an assistant that looks up an order and explains its status. Mark each step as one of:

- **Ordinary application code** (predictable logic and validation),
- **Model call** (understand the request, or explain the result),
- **Tool call** (fetch data from a service),
- **Human approval** (required before anything with consequences).

If the model is making a decision that code could make, move it into code.

---

# 7. A practical architecture for your project

Here is one way to put the pieces together:

```text
User
  |
  v
User interface (web, chat, CLI)
  |
  v
Application / service layer  <---- validation, auth, logging
  |
  +----> OpenEdge business logic and data (REST / PAS / ABL services)
  |
  +----> Optional retrieval (RAG or Progress Agentic RAG)
  |
  +----> Optional MCP server exposing approved tools
                    |
                    v
             AI model / agent
                    |
                    v
        Validated response to the user
```

This is a map, not a checklist. Many good projects call the model directly from an application service, skip RAG, and skip MCP.

## Three starting points

Pick the one that matches your project, and add to it only when you hit a real limit.

**A. Direct integration (simplest).** Your service calls an OpenEdge REST endpoint, passes the result to an LLM, and returns the explanation. Good for a first working demo.

**B. OpenEdge MCP Server.** The OpenEdge MCP Server exposes your REST or WEB services as tools, and an MCP-capable client or agent calls them. Good when the model needs to choose among several operations.

**C. Agentic RAG plus OpenEdge.** Progress Agentic RAG answers questions from documents, and your OpenEdge services supply live structured data. Good when answers need both a policy document and a live record.

## Suggested repository layout

A tidy repository makes your project easier to review and easier to run:

```text
my-project/
  README.md            how to install, configure and run (start here)
  .env.example         names of required variables, with no real values
  .gitignore           must list .env and any local data files
  openedge/            ABL code, service definitions, sample data
  app/                 your service layer and AI logic
  mcp/                 MCP server, if you build one
  eval/                test questions and expected answers
  docs/                architecture diagram, screenshots, notes
```

## Example: inventory assistant

**User problem:** Staff want to ask questions about stock in plain language.

**OpenEdge role:** Source of truth for product records, quantities and business rules.

**AI role:** Interpret the question and explain the result in plain language.

**Integration:** The application calls a controlled service to retrieve inventory. An MCP tool can do this if your architecture uses MCP.

**Optional RAG:** Pull stock-handling procedures from approved documents.

**Safety:** The assistant never invents stock values. The application validates product identifiers and permissions. Any change to stock runs in trusted application code, never directly from model-generated text.

---

# 8. Security, privacy and responsible AI

Build security in from the start, even for a prototype.

- Use fictional or approved sample data. Do not send real customer records, confidential company information, source code or personal data to an AI service without explicit authorization.
- Never put API keys, passwords or tokens in Discord, a prompt or a public repository. Use environment variables, keep `.env` in `.gitignore`, and commit a `.env.example` instead. If a key leaks, revoke it immediately, since deleting the commit does not remove it from Git history.
- Give each integration only the permissions it needs. Start with read-only tools.
- Validate everything a model produces before it reaches an application operation: types, ranges, allowed values.
- Treat retrieved documents and user input as data. They can contain instructions aimed at your model (prompt injection). Do not let text from a document or a user decide which tools run or what permissions apply.
- Require confirmation and authorization for consequential operations such as changing records, approving payments or deleting data.
- Handle timeouts, model outages, malformed responses and missing records without crashing.
- Tell users when an answer is AI-generated, and show sources where you can.
- Read the terms, quotas, costs and data-handling policies of every model or API you use.

**Further reading:** the OWASP Top 10 for LLM Applications lists the most common risks (prompt injection, excessive agency, sensitive data disclosure and more): https://genai.owasp.org/llm-top-10/

For this hackathon, a small demo on fictional data is enough. The point is to show a sound architecture and a responsible approach, not to connect to a production system.

---

# 9. Testing, evaluation and observability

AI features fail in ways that ordinary unit tests miss. A few habits make a large difference.

## Build a small evaluation set

Write 10 to 20 test cases before you tune anything. Each case has an input and what a good answer must contain (or must not contain). Include:

- typical questions,
- questions your data cannot answer (the correct behavior is to say so),
- a question with a missing or invalid identifier,
- an attempt to make the model ignore its instructions,
- a request that should require human approval.

Re-run the set after every significant change to a prompt, a tool or a retrieval setting. If you built the RAG exercise in section 4, you already have a start.

## Check the parts separately

When an answer is wrong, find out which part failed:

- **Retrieval:** did the right passage come back?
- **Tool call:** did the model pick the right tool and valid arguments?
- **OpenEdge service:** did it return the correct data?
- **Generation:** given correct inputs, did the model explain them correctly?

Without logs you will be guessing. Record each request, tool call, tool result and final answer, with a shared request ID.

## Watch cost and speed

Count tokens and measure response time during testing. A demo that takes 40 seconds to answer will lose the room. Smaller prompts, caching of repeated lookups and a lower step limit usually help.

---

# 10. Hackathon project planning

## Choose the problem before the tools

You should be able to explain your idea in a few sentences:

1. **User:** who has the problem?
2. **Problem:** what is slow, hard or confusing today?
3. **OpenEdge:** what data, business logic or service does it provide?
4. **AI:** which capability really benefits from AI?
5. **Integration:** how do the pieces communicate?
6. **MVP:** what is the smallest version you can demonstrate?
7. **Evaluation:** how will you know it works?

## Example project ideas

- **Student support assistant:** answers questions from approved university information.
- **Inventory assistant:** explains stock levels using application data.
- **Order-status assistant:** looks up an order and explains it in plain language.
- **Knowledge assistant:** searches a document set and answers with source references.
- **Service request triage:** classifies a request and drafts a reply for a person to review.
- **Business insights assistant:** summarizes operational data, with all calculations done in application code.
- **Multi-step back-office agent:** checks stock, finds a supplier policy in a document, and proposes a reorder that a person approves.
- **ABL modernization helper:** uses AI to explain or document existing ABL code, using only code you have the right to share.

These are starting points. A smaller idea that is well tested beats a large idea that cannot be demonstrated.

## Suggested milestones

| When | Milestone |
|---|---|
| Mon 19 Oct (kickoff) | Define the problem, sketch the architecture, agree on the MVP. Set up your repo and README. |
| Tue 20 – Wed 21 Oct | Get the basic OpenEdge component or integration working, even with sample data. |
| Thu 22 – Fri 23 Oct | Add the first AI feature and try it on sample inputs. Write your first evaluation cases. |
| Sat 24 – Sun 25 Oct | Catch-up time if you need it. |
| Mon 26 – Tue 27 Oct | Handle errors, validate model output, test realistic scenarios and at least one failure. |
| Wed 28 Oct | Stop adding features. Prepare the demo, record a backup video, document the limitations. |
| Thu 29 Oct | Presentations and prizes. |


## Definition of done

- [ ] The problem and the intended user are clear.
- [ ] OpenEdge has a real role in the solution.
- [ ] The AI feature adds value beyond a generic chatbot.
- [ ] The integration works live, or in a recorded fallback.
- [ ] The team tested several expected inputs and at least one failure case.
- [ ] Numbers and records come from OpenEdge, not from the model.
- [ ] The demo uses approved sample data and exposes no secrets.
- [ ] The README lets someone else run the project.
- [ ] The team can explain the limitations and what it would improve next.

## Demo tips

- Prepare a short script: the problem, a live walkthrough, then the architecture. Three to five minutes is plenty.
- Record a backup video of the working demo. Networks and APIs fail at the worst moment.
- Show one failure and how your system handles it. It builds more trust than a flawless run.

---

# 11. How to ask for help in Discord

When you post in the technical-help channel, include:

- **Goal:** what you are trying to do.
- **Environment:** OpenEdge version, operating system and relevant tools.
- **What you tried:** the steps or code you used.
- **Expected result:** what you thought would happen.
- **Actual result:** the exact error message or behavior.
- **Minimal example:** a short, safe snippet or screenshot.

Leave out passwords, API keys, tokens, private source code and real personal or customer data.

Use the starter-kit channel for reference material and the technical-help channel for questions. Post short progress updates as you go, so mentors can help before a problem blocks you.

---

# 12. Reference links

### Progress OpenEdge

- OEDK Classroom Edition: https://www.progress.com/openedge/classroom-edition
- OEDK introduction: https://documentation.progress.com/output/oedk/oedk/introduction-to-openedge-developers-kit.html
- OEDK installation and getting started: https://documentation.progress.com/output/oedk/oedk/how-to-get-started-with-openedge-developers-kit.html
- OpenEdge 12.8 documentation: https://docs.progress.com/category/openedge128
- ABL Basic Guided Journey: https://docs.progress.com/bundle/openedge-abl-basic-guided-journey-128/page/Statements.html
- ABL Reference: https://docs.progress.com/bundle/openedge-abl-reference-128/page/Introduction-to-the-ABL-Reference.html
- HTTP client in ABL: https://docs.progress.com/bundle/openedge-abl-develop-http-clients/page/Develop-an-HTTP-client-application-in-ABL.html
- ABL service using the WEB transport: https://docs.progress.com/bundle/openedge-abl-develop-services-128/page/Develop-an-ABL-service-using-the-WEB-transport.html
- OpenEdge MCP Server: https://docs.progress.com/bundle/openedge-mcp-server-128/page/Get-started-with-OpenEdge-MCP-Server.html

### Progress Agentic RAG

- Product page: https://www.progress.com/agentic-rag
- Documentation: https://docs.rag.progress.cloud/docs
- Developer guides: https://docs.rag.progress.cloud/docs/develop/intro/
- Developer quickstart video: https://www.progress.com/resources/videos/guide-progress-agentic-rag-developer-quickstart

### AI and LLMs

- Microsoft, Generative AI for Beginners (videos): https://learn.microsoft.com/en-us/shows/generative-ai-for-beginners/
- Microsoft course repository: https://github.com/microsoft/generative-ai-for-beginners
- Google Cloud Skills Boost: https://www.cloudskillsboost.google/
- OpenAI, prompting: https://developers.openai.com/api/docs/guides/prompting
- OpenAI, prompt engineering: https://developers.openai.com/api/docs/guides/prompt-engineering
- Anthropic, prompt engineering: https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview

### RAG, MCP and agents

- MCP introduction: https://modelcontextprotocol.io/docs/getting-started/intro
- MCP architecture: https://modelcontextprotocol.io/docs/learn/architecture
- MCP servers and examples: https://github.com/modelcontextprotocol/servers
- MCP Inspector: https://github.com/modelcontextprotocol/inspector
- Anthropic, Building Effective Agents: https://www.anthropic.com/engineering/building-effective-agents
- OpenAI, agents guide: https://developers.openai.com/api/docs/guides/agents
- OpenAI, agent orchestration: https://developers.openai.com/api/docs/guides/agents/orchestration
- Microsoft, AI Agents for Beginners: https://github.com/microsoft/ai-agents-for-beginners
- OpenAI Agents SDK: https://github.com/openai/openai-agents-python
- LangGraph: https://github.com/langchain-ai/langgraph
- Claude Agent SDK: https://github.com/anthropics/claude-agent-sdk-python

### Security

- OWASP Top 10 for LLM Applications: https://genai.owasp.org/llm-top-10/

---

## Final advice

Get something useful working before you make it complicated. OpenEdge gives you reliable data and business rules. AI makes them easier to reach, explain and use. RAG, MCP and agents solve specific problems, and no project has to use all of them.

Start small, test early, ask questions in Discord, and keep building. We are looking forward to seeing what you make.

---

*Note on resources: links point to official vendor documentation, learning portals and open-source repositories. Courses, downloads, product features and documentation paths change over time, so check the source page before you rely on it. This guide is learning material for the hackathon. It does not replace the product documentation or your organization's security policies.*
