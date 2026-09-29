<p align="center">
  <img src="https://raw.githubusercontent.com/canvas-ai/.github/main/banners/canvas-banner_1200x480.jpg" alt="Canvas" width="100%" />
</p>

# Canvas UI

**Canvas** is a cross-platform, cross-device **~desktop~ world overlay** to help **organize** the **work**, **workflows** and unstructured **data** of your own slightly unusual `Universe` into `Workspaces`, virtual bitmap-powered `context`- and `directory`-like trees, with shareable, bindable `contexts` on top, built on LMDB, Roaring bitmaps, embedding vector trees and coffee.

Yeah, no worries, there is some `ai`(tm) in there somewhere too.

| :exclamation: **Important update - TL;DR** |
| --- |
| After several years of my own time and effort invested in this project, I decided to close-source its main components, namely **canvas-server** (the server runtime / orchestration layer for Workspaces, Contexts, Roles and Agents), **canvas-agentd** and **canvas-synapsd** (the purpose-built in-process per-workspace/agent database used as the main index engine).<br><br>**This decision may not be final.** We'll see what the future brings, but this is what I feel is the right move as of now. |

Now the **non-TL;DR version**

**Canvas** began out of necessity as a loose set of scripts, simple utilities and collected workflows to help organize my own work - or, to be more precise, to help me context-switch between several projects in the corporate bill-paying job, my own consulting company and university. If I accounted for all the nights, weekends and dedicated hackathon days, the few paid contributions via Upwork, tooling costs and other development expenses over the years, I'd be well over 100k EUR in the red. Then again, I built a few very useful tools while doing that, became proficient in multiple programming languages and actually enjoyed most of the process, so it may not really be a net loss after all. You probably know this specific flavor of copium already, so let me stop here.

How I justify this move **to myself**:

- Assuming this project may be as useful to others as it is to me, there is currently no FOSS license **I know of** that could ensure I'd get back (in code contributions, feedback, sponsorship) at least as much as I'm investing.
- There are currently no outside contributions. We are in an attention-based economy and this project has almost no visibility, mostly because I successfully ignored social media and only just started (yes, 09/2026, I know!) and still suck at it.
- We've entered an era where implementation costs are, at least for now, negligible compared to the actual design and development costs. Why bother contributing back when you can spin up Claude Code on a $20 subscription, make your own tailored version of whatever tool you stumbled upon, and move on with your day? No bigger picture, no community, no making-the-world-a-better-place(tm) pathos. Open source in 2026 is turning into pure, individualistic, mechanical, tasteless, soulless, fix-my-current-needs-and-get-out-of-my-way .. porn.

**FOSS** was already turning programmers into beggars (or standard-unit corporate employees with a Stallman hat), their love of building things used against their own financial interests. I don't have to blabber about the movement itself and its stated (but only stated) fight against the corporate Möchtegern nobility. Most of you presumably lived through parts of the story, some were even true believers. What I want to highlight nevertheless: despite having no fighting chance in the grand scheme of things, there was always a community one could build around a project. Random contributions, fixes, improvements, ad-hoc beers while visiting distant places on this beautiful blue dot. I feel we, as independent creators, are losing the incentive to cooperate. Convenience and individualism, with no natural complexity-predators and therefore no push to foster alliances out of the innate instinct for survival.

Are 100k vibe-coded, purpose-built tools that each solve one particular problem better than 100 solutions that are (mostly miserable at) solving thousands of problems? They sure are, for whoever can search through them and spot the novel ones. Oh, and of course, that's the Unix way anyway, maybe even nature's way (don't pop the champagne yet, microservices people).

Anyhow!  


All integrations are still open-source and I do not plan to change that.  
Canvas will become a SaaS product, with a self-hosted offering of the kind you know from the old Atlassian days.  
I don't want your data. I want to turn this project into a thin layer on top of reality that augments our own inherent abilities. This move is my attempt to make sure I can keep working on it.

May my Stallman hat keep its bright colors.


---


The **TL;DR** [Canvas UI] usage wofklow:

- Create a `Workspace`
- Add data backends you want to index(local fs, s3, imap mailboxes, messaging apps)
- Start building a context tree on top of your data that follows whatever mental model is suitable to work on your tasks
- Configure import/ingestion hooks
  - Optionally setup share ACL tokens, link agents, get another coffee  
- Create `Contexts` and `Canvases`
- Bind applications to them, so that switching from `work://customer-a/devops/jira-1234` to `work://customer-b/sec-compliance/reports` will automatically load all your context-related
  - **files**
  - **contacts**
  - **email threads** (based on contacts, email subject or a specific thread identifier)
  - **chat conversations**
  - **browser tabs(urls)**
  - **notes**
  - **todo items**
in every bound application. No more 1000+ browser tabs waiting for session corruption, deskto folders with _TO_SORT_ folders within older _TO_SORT_ folders, ad-hoc notes, scripts and random files evenly trashing your hard-drive.

Switching between tasks becomes like switching between an organized set of canvases placed nicesly on a clean desk, with a task-customized toolbox, in a task-dedicated office!(or whatever abstraction is more aligned to your internal world model :) 

Context switches may still be expensive, but they can now be orders of magnitude more efficient.  


## Project screenshots

- https://demo.cnvs.ai/pub/c/aks6zaf8


## A few gory details

(TODO)

(one day there will be a video

    here somewhere

)

A **Contex Tree** is a virtual file-system like tree structure composed of tree nodes(`layers`) and may look as follows:
```
universe://
├── home
│   ├── music
│   │   └── concerts
│   │       └── tiny-desk
│   │           └── 2025
│   └── travel
│       ├── 2024
│       │   └── belgium
│       │       └── brussels
│       └── 2025
│           └── spain
│               └── barcelona
└── work
    ├── customer-a
    │   ├── devops
    │   │   ├── jira-1001
    │   │   ├── jira-1002
    │   │   └── jira-1003
    │   ├── inc
    │   │   ├── snow1001
    │   │   ├── snow1002
    │   │   └── snow1003
    │   ├── rca
    │   ├── reports
    │   │   ├── finance
    │   │   └── sec-compliance
    │   └── team
    └── customer-b
        ├── billing
        ├── kb
        ├── projects
        │   ├── bar
        │   ├── baz
        │   └── foo
        └── sec-compliance
            └── reports
                └── 2025
                    ├── 01
                    ├── 02
                    └── 03
```

`Context` is a session-like object aplications can bind to. It points to a specific tree path and emits events on relevant backend updates; switching a context url for example from `work://customer-foo/project-foo` to `work://customer-foo/project-bar` will notify all listenerds to update their state (fetch relevant documents, update dotfiles - yes, dotfiles!, kick some agents into action or close your external shades)

`Canvas` is simillar to a stored database view, with optional UI configuration for the UI layout, widgets and applets. You can have a canvas called "Morning coffee" with a widget showing todays messages/emails, a column with todays tasks and a widget playing your morning playlist. Canvas can be exported and accessed as a *static* website and opened on your TV/tablet/phone; moving your canvas from `/customer-a/Morning cofee` to `/customer-b/Morning cofee` automatically loads relevant data based on the path it is placed at.

`Canvases` and `Contexts` are **shareable**, your colleagues can bind to/follow your context and add notes or their own browser tabs or files. You can access all indexed data directly within your OS(webdav-mounted workspace, canvas-electron, browser-extension) or via webui or cli or even plain curl.

----

**Universe** is your default(home) workspace.  
Your Universe can be further split into self-contained, movable/shareable **Workspaces**, each running its own in-process database with a context tree abstraction on top(you may want to have a dedicated workspace per customer or project; a handover to a BAU engineer would then be just a matter of export - import of your project workspace).

**Context tree Layers** are unique - a `reports` layer in `/work/customer-a/reports` and `/work/customer-b/reports` is stored under the same uuid linking to the same bitmap - renaming/removing/updating one will update all occurences in the context tree.  
Context layers filter different data based on `where` they are placed. Iow, moving `/customer-a/reports` returns reports of customer-a, `/customer-b/report` of customer-b, creating `/reports` will return all data tagged/linked to the reports layer for all customers.  

**Canvas-server** is a small nodejs-based runtime that can be run directly on your machine or dockerized almost anywhere you want(lets say your home NAS or your favorite trusted cloud provider).

You can index almost any data type regardless where it is stored, retrieval can use client context data like network location, OS, device ID etc to make a roaming experience as smooth as your network latencies allow; reads from remote locations are cached using cacache\[[2](https://www.npmjs.com/package/cacache)\].

**A common use-case of a shared "Travel" context**: 
Lets say you are planning your upcoming vacation and looking for accommodation "the-old-fashioned-way"(tm) by searching the interweb yourself.  
You set your context to `home://travel/2025/europe/spain`(via command-line of course: `$ context set home://travel/...`), this will update all your linked applications - in our example browser tabs - and you start opening your listings. Your significant other can connect to the same shared context and start adding new tabs, closing the already reviewed ones, even adding notes or contacts. All context tree events are propagated to all connected clients(whenever someone opens a browser tab, a new tab is auto-created or auto-closed on all linked browsers(if configured to do so)).


## Couple of core principles to start with

- **You own your data**  
  You can run every part of the application stack wherever you want, with whatever data or compute backend you want
- **Offline first**  
  Iow - if a solar CME kicks this civilization back to the stone age(again?) and you happen to have a working power source, you can still browse all your metadata || cached data to check on your appointments, emails, tax returns and all the other stuff you no longer have to worry about (or browse cached content related to things you need to worry about)

## Show me the code

- <https://github.com/canvas-ui/canvas-server>
- <https://github.com/canvas-ui/canvas-synapsd>
- <https://github.com/canvas-ui/canvas-stored>
- <https://github.com/canvas-ui/canvas-web>
- <https://github.com/canvas-ui/canvas-browser-extensions>
- <https://github.com/canvas-ui/canvas-cli>

Client apps ([canvas-ui/canvas](https://github.com/canvas-ui/canvas)):

- [`apps/cli`](https://github.com/canvas-ui/canvas/blob/main/apps/cli/README.md) — Canvas CLI (bun-compiled binaries)
- [`apps/desktop`](https://github.com/canvas-ui/canvas/blob/main/apps/desktop/README.md) — Tauri desktop app
- [`apps/browser-extension`](https://github.com/canvas-ui/canvas/blob/main/apps/browser-extension/README.md) — Chromium + Firefox extension
- [`apps/web`](https://github.com/canvas-ui/canvas/blob/main/apps/web/README.md) — web UI (vite/react)

## Get Involved

- By opening a new issue in any existing repository
- By picking an issue to work on from <https://github.com/orgs/canvas-ui/projects/2>
- Saying hello in [Slack](https://join.slack.com/t/augmentd-labs/shared_invite/zt-2x9ywczgx-UYP6buONsPe0kvNM9zVdxw)
- Or spamming us directly via <dev@getcanvas.org>
