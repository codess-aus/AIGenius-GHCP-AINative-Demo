# AI Genius Episode 1 - Presenter Script

**"Code with AI: GitHub Copilot for AI-Native Coding Workflows"**
1 hour · All skill levels · Livestream on YouTube

---

## Run of Show

| Time | Segment | Topic | Slide/Asset | Demo |
|------|---------|-------|-------------|------|
| 0:00 | Opening | What does AI-native actually mean? | Title slide / README | None |
| 0:05 | Live demo | Run the starter app, show it working | None | Terminal: `app.py` commands |
| 0:10 | Context | `copilot-instructions.md` + test suite | `.github/copilot-instructions.md` | Terminal: `pytest` |
| 0:15 | Exercise 01 | Write a well-formed issue together | Browser: Issues tab, template | None |
| 0:25 | Exercise 02 | Assign to Copilot, watch it work | None | Browser + Copilot App |
| 0:30 | Concept | Scaling up: from one issue to a fleet (`/fleet` and `/squad`) | Architecture / concept slide | Optional: live `/fleet` demo, time permitting |
| 0:38 | Exercise 03 | Review the generated PR | None | Browser: PR diff, session log |
| 0:48 | Exercise 04 | Iterate via PR comments | None | Browser: PR comments, merge |
| 0:55 | Stretch (optional) | Azure Table Storage + Azure OpenAI | Architecture diagram | Code walkthrough, no live Azure needed |
| 0:58 | Closing | 5 Golden Rules + call to action | None | None |

> **Timing note:** the core loop (Segments 1 to 7, including the Fleet/Squad concept segment) fits inside 55 minutes. The *talking points* in the Fleet/Squad segment are not optional, they're core content, even if you skip the live demo inside it.

> **Livestream note:** there is no in-room audience. Everyone watching is in YouTube chat. Have a moderator (or a second monitor) watching chat throughout so questions can be answered live without breaking flow.

---

## Segment 1 - Opening (0:00 to 0:05)

*Screen: Title slide or repo README*

**SAY:**
> Welcome everyone, thanks for tuning in. My name is Michelle. I'm a Developer Engagement Lead at Microsoft.
>
> Today we're going to do something a little different. I'm not going to teach you GitHub Copilot features. I'm going to change how you think about writing code.
>
> When I say "AI-native", I don't mean bolting an AI assistant onto the pipeline you already have, and I don't mean using Copilot to autocomplete a line of code faster. AI-native is a different shape of software development lifecycle entirely. It's built around coding agents and agentic workflows from the ground up: you write an issue as a specification, an agent explores the codebase, plans the work, writes the code, runs the tests, and opens a pull request, all without a human driving every keystroke.
>
> That changes what your job actually is. You're no longer the one typing every line. You're the one defining **what** to build, **why** it matters, and what "done" looks like. The agent handles **how**. Your value moves upstream to writing precise specifications, and downstream to smart, critical review. That's not a faster version of the old workflow, it's a genuinely new way of working, and it's why we spend real time today on writing issues and reviewing PRs, not on typing code.
>
> By the end of this hour, you will have watched the full AI-native loop happen live: write issue, delegate to Copilot, review the PR, iterate. If you've got the repo forked, follow along in your own environment, or just watch, either works.

> 🔎 **Fun fact to drop here:** GitHub Copilot's coding agent runs your assigned issue inside an isolated GitHub Actions VM. It genuinely cannot touch production, cannot push directly to protected branches, and cannot merge its own PR. That isolation is what makes agentic workflows safe to hand real work to, not just a novelty demo.

> 💡 **Chat prompt:** ask the chat "How many of you have used GitHub Copilot for code completion? Type C in chat." Then: "How many have used Copilot to write an entire feature from a GitHub Issue? Type F in chat." Use the split to set up why today's session is about the F group, not the C group.

---

## Segment 2 - Live Demo: The Starter App (0:05 to 0:10)

*Screen: Terminal in the starter-app directory*

**SAY:**
> Let me show you what we're working with. This is a Python command-line task manager. It sounds simple, but it's a real codebase with priorities, tags, due dates, timestamps, and an automated test suite.
>
> This is the app we'll be extending today using Copilot. If you've forked the repo, feel free to run these same commands locally as I go, and let me know in chat if anything doesn't work for you so I can help troubleshoot.

**DO, type these commands live:**

```bash
cd starter-app

# Add a few tasks with different options
python app.py add "Deploy the API" --priority high --due 2025-12-31 --tag work
python app.py add "Buy coffee" --priority low --tag personal
python app.py add "Update README" --priority medium --tag work

# Show the full task list
python app.py list

# Show filters
python app.py stats
python app.py list --priority high
python app.py list --overdue
```

**SAY:**
> Notice that overdue tasks light up in red automatically, thanks to `rich` formatting. The app has filtering built in.
>
> Now, what if I want to store these tasks in Azure instead of a local file? And what if I want the app to automatically suggest a tag using AI when I add a task? That's what Copilot is going to build for us today.

---

## Segment 3 - Context: Instructions & Tests (0:10 to 0:15)

*Screen: VS Code or GitHub, `.github/copilot-instructions.md`*

**SAY:**
> Before Copilot writes a single line of code, it reads two things: your issue, and the copilot-instructions file.
>
> Think of `copilot-instructions.md` as the onboarding document you'd give a new developer. It tells Copilot what libraries to use, how to handle secrets, what the test approach is, and what "done" looks like for this project.

**DO:**
1. Share your screen on `.github/copilot-instructions.md`
2. Point out these specific sections on screen, narrating clearly since viewers can't lean over your shoulder:
   - The task schema (the JSON example)
   - The Azure Table Storage section (SDK name, env var names)
   - The "Never hardcode credentials" rule
   - The testing approach (pytest, mocking Azure calls)

**SAY:**
> This file is the reason Copilot will reach for the right Azure SDK instead of a random library it found on the internet. Context is everything.
>
> Now let me show you the test suite.

**DO:**

```bash
# Open starter-app/tests/test_tasks.py, briefly scroll through the class structure
# Then run it live:
python -m pytest tests/ -v
```

Narrate what viewers are seeing on screen: all tests passing, in under a second.

**SAY:**
> When Copilot adds the Azure backend, it has to keep every one of these tests passing. That's the safety net. And it will write new tests for the new code too.

> 🔎 **Interesting fact:** keeping the dependency list minimal (`click`, `rich`, `pytest`) isn't just good practice for humans, it directly improves what an AI coding agent can do with your repo. Fewer moving parts means less ambiguity for the agent to resolve.

---

## Segment 4 - Exercise 01: Write the Issue (0:15 to 0:25)

*Screen: GitHub Issues tab, New Issue form*

**SAY:**
> Now I'm going to write an issue live on screen, and I'll talk through each section as I fill it in. If you've forked the repo, open your own Issues tab and write along with me, or write your own variation.
>
> The golden rule of AI-native development is this: **your issue IS your prompt.** If you write a vague issue, you get vague code. If you write a precise specification, you get precise code.

**DO, type or paste this issue:**

> **Title:** Migrate task storage to Azure Table Storage
>
> **Problem statement:**
> Tasks are stored in a local JSON file (`tasks.json`). This means data is lost when the machine changes and cannot be shared across devices. We need a cloud-backed storage option.
>
> **Desired behaviour:**
> - When `AZURE_STORAGE_CONNECTION_STRING` is set, tasks are stored in Azure Table Storage
> - When not set, the app falls back to the existing local JSON file
> - All existing CLI commands work identically, zero breaking changes
>
> **Acceptance criteria:**
> - [ ] A `storage.py` module with a `TaskStorage` protocol: `load() -> list[dict]` and `save(tasks) -> None`
> - [ ] `LocalStorage` implements `TaskStorage` using the existing JSON approach
> - [ ] `AzureTableStorage` implements `TaskStorage` using `azure-data-tables`
> - [ ] `app.py` calls `get_storage()` at startup to pick the right backend
> - [ ] `AZURE_STORAGE_CONNECTION_STRING` is loaded from `.env` using `python-dotenv`
> - [ ] Connection errors print a clear message and exit with code 1
> - [ ] `azure-data-tables` and `python-dotenv` added to `requirements.txt`
> - [ ] Tests mock Azure calls, no real API calls in tests
> - [ ] No connection strings or account keys in source code
>
> **Constraints:**
> Use `azure-data-tables` (not the older `azure-storage-table` SDK).
> Use `PartitionKey = "tasks"` and `RowKey = str(task["id"])`.
>
> **Submit the issue.**

**SAY:**
> Notice how specific I was. I named the module. I named the protocol. I named the method signatures. I named the environment variable.
>
> A human developer might fill in those gaps from experience. Copilot takes you literally. The more you specify, the closer the output is to what you actually want.
>
> Take 5 minutes if you're following along in your own fork, write the same issue or try Option B, C, or D from the exercises folder. I'll keep talking through the reasoning while you write, and I'll answer questions in chat.

> 🔎 **Interesting fact:** this is essentially prompt engineering wearing a GitHub Issues costume. The same principles, specificity, examples, constraints, that make a good LLM prompt make a good AI-native issue.

> 👀 **WATCH FOR:** keep an eye on chat while people share their draft issues or ask questions. If someone posts an issue that's vague in the Acceptance Criteria section, gently prompt in chat: "What specifically should happen here?"

---

## Segment 5 - Exercise 02: Assign to Copilot (0:25 to 0:30)

*Screen: GitHub Issue, Assignees panel on the right*

**SAY:**
> Now for the part that still feels a bit like magic the first time you see it.
>
> Open the issue you just submitted. On the right side, find the Assignees panel. Click the gear icon. Search for "Copilot" and assign it. If you're following along, do the same on your own issue now.

**DO:**
1. Demonstrate on your own issue, on screen
2. Click the Assignees gear, select Copilot
3. Show the confirmation comment that appears on the issue
4. Open the GitHub Copilot App on your desktop, go to My Work, find the active session
5. Keep this on the main screen share so everyone watching can see Copilot working in real time

**SAY:**
> While we wait, watch what it does first. It doesn't start coding immediately. It **explores**. It reads `copilot-instructions.md`. It reads `app.py`. It reads the existing tests.
>
> That's exactly what a good developer does, understand the codebase before touching it.

> 👀 **WATCH FOR:**
> - Point out on screen when Copilot references `copilot-instructions.md`, this is the payoff for Segment 3
> - Point out when it reads the existing tests, it's learning the test patterns
> - If the PR isn't ready by 0:30, don't wait, move straight into Segment 5B, the wait time is exactly when a concept segment earns its keep
> - Keep glancing at chat, this is a natural point for questions since there's a bit of a wait

> 💡 **Chat prompt:** ask chat "What did you notice about how Copilot explored the codebase? What did it look at first?" and read out a couple of answers.

---

## Segment 5B - Concept: Scaling Up, From One Issue to a Fleet (0:30 to 0:38)

*Screen: Concept slide or architecture diagram, terminal optional*

> **This segment is core content, not optional.** While Copilot works in the background on the issue we just assigned, this is the natural moment to zoom out and talk about what happens once you outgrow a single issue.

**SAY:**
> So far we've done the smallest possible unit of AI-native work: one issue, one agent, one PR. That's the right place to start, but it doesn't scale to a real sprint, where you might have ten issues to work through at once.
>
> This is where `/fleet` and `/squad` come in. They're two different answers to the same question: "How do I go from one developer directing one agent, to a whole team directing many agents at once?"

**SAY, explain `/fleet`:**
> `/fleet` is a Copilot CLI command built for **parallel, stateless execution**. You give it one objective, and an orchestrator agent breaks that objective into independent sub-tasks, checks which ones are unblocked, and dispatches them all at once.
>
> Think of it like this: if our single Copilot agent today is one developer picking up one issue, `/fleet` is like assigning ten related issues at once and having ten short-lived contractors work them in parallel, then handing you back ten reviewable results.

**SAY, explain `/squad`:**
> `/squad` is a different shape of answer. It's not a single CLI command, it's an open source framework you install into your repo that creates a **persistent team of named agents**. Unlike Fleet's disposable sub-agents, Squad agents stick around across sessions.
>
> If Fleet is contractors for a single sprint, Squad is closer to hiring permanent specialists onto your team, they build context over time, and they can even use Fleet internally when they need a burst of parallel capacity.

**SAY, connect it to cloud-native AI in the SDLC:**
> Here's why this matters beyond the novelty. These two patterns map almost exactly onto ideas you already use in cloud-native architecture.
>
> `/fleet` is **horizontal scaling for cognitive work**. A cloud-native app scales out stateless compute instances behind a load balancer to absorb load, `/fleet` scales out stateless sub-agents to absorb a backlog.
>
> `/squad` is closer to a **long-lived service mesh with persistent state**. Instead of ephemeral pods, you have specialized, addressable agents with their own memory and responsibilities, coordinating with each other.
>
> And the wave-based dependency scheduling inside `/fleet`, run what's unblocked, wait, run the next wave, is conceptually the same DAG scheduling you already know from CI/CD pipelines or a Kubernetes job graph.
>
> The takeaway: the single-issue loop we're doing today is the "hello world". `/fleet` and `/squad` are how that same loop scales to a real team's backlog without you personally babysitting every single agent.

> 🔎 **Interesting fact:** neither `/fleet` nor `/squad` change the core safety model. Every sub-agent, whether disposable (Fleet) or persistent (Squad), still opens a PR, still cannot merge its own work, and still runs inside isolated infrastructure. More agents does not mean less review.

**DO (optional live demo, time permitting only):**
```bash
# Example only, run this in the Copilot CLI if time allows
/fleet Break the remaining Exercise 05 issue into independent sub-tasks
       (Azure OpenAI client setup, tag suggestion function, tests) and
       work them in parallel
```
> If you don't have time to run this live, simply describe it from the slide: point out that the orchestrator will split the objective, dispatch the independent pieces at once, and reassemble the results.

> 👀 **WATCH FOR:** chat will likely ask "does this mean I lose control?" Reinforce: no, every sub-agent's output still lands as a reviewable PR. More agents means more parallel proposals, not less oversight.

> 💡 **Chat prompt:** ask chat "In your team, is your bottleneck today writing enough good issues, or reviewing enough PRs? `/fleet` and `/squad` only help if you already know how to do Exercise 01 and Exercise 03 well."

---

## Segment 6 - Exercise 03: Review the PR (0:38 to 0:48)

*Screen: Pull Requests tab, draft PR opened by Copilot*

**SAY:**
> Copilot has opened a draft PR. This is where your most important skill in AI-native development comes into play: **critical review**.
>
> Copilot is very good at writing plausible code. Plausible is not the same as correct, secure, or exactly what you asked for. You are the quality gate. That's true whether it's one agent's PR, like this one, or ten PRs from a `/fleet` run.
>
> Before we look at any code, let's read the session log in the PR description together. Copilot explains every decision it made. This is like reading a PR summary from a junior developer, you understand the reasoning before you judge the output.

**DO:**
1. Open the draft PR on screen
2. Read the session log aloud (at least the first paragraph)
3. Go to "Files changed" and walk through the checklist, narrating clearly for viewers:

| Check | What to look for |
|-------|-----------------|
| `storage.py` exists | Has a `TaskStorage` protocol with `load()` and `save()` |
| `app.py` calls `get_storage()` | Not the old JSON functions directly |
| No hardcoded secrets | Zero connection strings or API keys visible |
| `requirements.txt` updated | Includes `azure-data-tables` and `python-dotenv` |
| New tests exist | Tests mock Azure calls, no real API calls |

**SAY:**
> If you're following along on your own PR, find at least one thing to comment on. Not because Copilot did it wrong, maybe it didn't, but because the skill of writing precise PR feedback is itself what makes this workflow work.
>
> Good PR comments are **specific.** Not "this could be better." Instead: "The error message on line 42 says connection failed but doesn't tell the user what env var to set. Can you include the variable name in the message?"

**DO:**
1. Demonstrate leaving a comment on a specific line in Files changed
2. Give it about 5 minutes for anyone following along to review their own PR and leave a comment, meanwhile invite chat to paste an example comment they'd leave so you can read a few out

> 🔎 **Interesting fact:** this checklist habit is what separates AI-native teams that ship reliable software from teams that ship plausible-looking bugs. Plausible is not the same as correct, and only a human reviewer can tell the difference.

> 👀 **WATCH FOR:** if chat posts a vague comment like "improve this", reply in chat asking "What specifically? What would the improved version look like?" Call out anyone who spots a real security or logic issue.

---

## Segment 7 - Exercise 04: Iterate (0:48 to 0:55)

*Screen: PR, Copilot responding to comments*

**SAY:**
> Here's the mental model I want you to hold: Copilot is a junior developer who is incredibly fast, very literal, and absolutely does not take offence at feedback.
>
> You don't throw away the work and start again. You give precise feedback and let it improve.

**DO:**
1. Watch Copilot pick up your comment and update the branch, on screen
2. Once it pushes the update, re-review the specific section you commented on
3. Ask yourself, and narrate for viewers:
   - Did it address the feedback correctly?
   - Did it introduce any new issues in the process?
   - Is the PR ready to merge?
4. If you want another round, leave a second, more specific comment
5. When satisfied: change from Draft to Ready for Review, approve, merge

> 💡 **Good examples for a second-round comment:**
> - *"The validation rejects empty strings, but doesn't trim whitespace first. A name of spaces-only should also be rejected."*
> - *"The error says 'connection failed' but doesn't include the env var name so the user knows what to set."*
> - *"Can you extract the Azure entity mapping into its own function? It's mixed in with the save logic."*

**SAY:**
> Notice: **Copilot cannot merge.** The human is always the final gate. AI-native does not mean AI-autonomous. It means AI-collaborative. That's a deliberate design decision, not a limitation, and as the workflow scales to `/fleet` and `/squad`, that same gate still applies to every single PR.
>
> How many rounds did it take? One? Three? Drop your round count in chat, the answer depends almost entirely on how precisely you wrote the original issue.

---

## Segment 8 - Stretch Goal: Azure + AI (0:55 to 0:58, optional filler)

*Screen: code walkthrough, no live Azure account required*

> **Use this segment only if Exercises 02 to 04 finished with time to spare. Otherwise, skip straight to Closing and point to Exercise 05 as homework.**

**SAY:**
> Let's look at what happens when the ask gets harder. There are two pre-written issues in the exercises folder: migrating storage to Azure Table Storage, which we just did, and adding Azure OpenAI powered tag suggestions.

**DO, show the architecture pattern:**

```
CLI (app.py)
    └── storage.py
            ├── LocalStorage (default)
            └── AzureTableStorage (env var activated)
```

**SAY:**
> The key design principle here is zero breaking changes. If `AZURE_STORAGE_CONNECTION_STRING` isn't set, it falls back to local JSON. Same for OpenAI, if the env vars aren't there, the app degrades gracefully.
>
> When you review this kind of PR, look extra hard for one thing: hardcoded credentials. That's the single most critical failure mode when delegating cloud integration work to an AI agent. Everything else is recoverable, a leaked secret is not.

> 🔎 **Fun fact:** asking Copilot to mock cloud calls in tests, using `unittest.mock`, rather than hitting real Azure resources, is both a testing best practice and a cost and safety control. Your CI never touches a real Azure account.

---

## Segment 9 - Closing (0:58 to 1:00)

*Screen: README, The 5 Golden Rules*

**SAY:**
> Let's land the plane. We just walked through the full AI-native development loop together, and we zoomed out to see how that loop scales from one agent to a fleet or a squad of them.
>
> We wrote the issue. We delegated to Copilot. We reviewed the PR. We iterated via comments. We merged. And we saw that scaling up to `/fleet` or `/squad` doesn't change any of those steps, it just multiplies them.
>
> That is the loop. And the better you get at each step, the faster and higher-quality your output becomes, at any scale.

**Read the 5 Golden Rules aloud:**

1. **Write better issues**, your issue IS your prompt. Be specific.
2. **Review like a senior dev**, AI generates fast, humans verify smart.
3. **Use `copilot-instructions.md`**, give Copilot standing context about your project.
4. **Iterate, don't regenerate**, guide via comments rather than starting from scratch.
5. **Stay in the loop**, check the session log, understand what Copilot did and why.

**SAY:**
> For those of you who want to go further: Exercise 05 in the exercises folder has a ready-to-use issue for adding Azure OpenAI smart tag suggestions. Your homework: fork this repo, write the issue, assign it to Copilot, and review the PR yourself.
>
> You now have the skills to do that loop on any codebase, your work projects, your personal projects, anything, whether it's one issue or ten.
>
> Thanks for watching, and thanks for all the great questions in chat today. I'll stick around for a few more minutes to answer anything we didn't get to.

---

## Emergency / Fallback Notes

**If Copilot is slow to start:**
- Share `copilot-instructions.md` and exercise READMEs on screen while waiting, the preparation content fills 5 to 10 minutes naturally
- Invite chat to review the Exercise 05 pre-written issue and discuss in chat: "What would you change? What's unclear?"

**If the PR contains a bug worth showing:**
- Use it. A real bug caught in review is the best possible demo of why human review matters
- Say: *"This is the payoff. Copilot is fast, but not infallible. We caught this on review, that's our value in the loop."*

**If chat asks about `copilot-instructions.md` being a security risk:**
- The file lives in `.github/`, it's part of the repo, just like the README. No secrets go in here, ever.
- Secrets live in environment variables or Azure Key Vault. That's why the instructions explicitly say so.

**If chat asks whether Copilot's coding agent can be a security risk in general:**
- The agent runs inside an isolated GitHub Actions VM per session, it has no persistent access beyond that run and cannot merge, push to protected branches, or touch production directly.
- The session log gives you a full audit trail of every decision and file it touched.
- This holds true for `/fleet` sub-agents and `/squad` agents too, more agents does not mean less isolation or less review.

**If viewers run out of time on Exercise 01:**
- It's fine, the Azure issue template in Exercise 05 is pre-written. They can copy-paste it after the stream.
- The learning is in the *reading and understanding* the acceptance criteria, not just in typing it.

**If running behind schedule overall:**
- Cut the live `/fleet` demo inside Segment 5B entirely, but still cover the talking points, that segment's explanation is core content and should not be dropped, only its optional demo.
- Cut Segment 8 (Azure + AI stretch) entirely, it is designed as optional filler, not core content.
- Compress Segment 3 (Context) by skipping the live `pytest` run and just stating the test count and pass rate on screen.

**Livestream-specific fallbacks:**
- If chat is quiet, seed it yourself: "If you're watching, drop a comment with your priority level default, high, medium, or low."
- If there's stream lag or a technical hiccup, narrate what's happening rather than going silent, viewers can't see your screen freeze the way an in-room audience would notice a stall.
- If a moderator is present, agree beforehand on a signal (a specific emoji or phrase) they'll use in your own chat window to flag an urgent question versus a general comment.
