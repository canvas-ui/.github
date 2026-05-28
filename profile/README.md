**Canvas** is a cross-platform, cross-device **~desktop~ world overlay** to help **organize** your **work**, **workflows** and unstructured **data** of your `Universe` into `Workspaces` exporting virtual bitmap-powered `context` and `directory`-like trees, with shareable, bindable `contexts` on top, built on LMDB, roaring-bitmaps, embedding vector trees and coffee. 

Yeah, no worries, there is some `ai`(tm) inthere somewhere too.

The **TL;DR** version: 

- Create a `Workspace`
- Add data backends you want to index(local fs, s3, imap mailboxes, messaging apps)
- Start building a context tree on top of your data that follows whatever mental model is needed to work on your tasks
- Configure import/ingestion hooks
- Create `Contexts` and `Canvases`
- Bind applications to them, so that switching from `work://customer-a/devops/jira-1234` to `work://customer-b/sec-compliance/reports` will automatically load all your context-related
  - **files**
  - **contacts**
  - **email threads** (based on contacts, email subject or a specific thread identifier)
  - **chat conversations**
  - **browser tabs(urls)**
  - **notes**
  - **todo items**

Switching between tasks becomes like switching between an organized set of canvases, on a new clean table, a task-customized toolbox, in a task-dedicated office!(or whatever abstraction is more aligned to your internal world model:) 

Context switches may still be expensive, but they can now be orders of magnitude more efficient.  

Each `Canvas` or `Context` is **shareable**, your colleagues can add notes or their own browser tabs or files. You can access all indexed data directly within your OS(webdav-mounted workspace, canvas-electron, browser-extension) or via webui or cli or even plain curl.  

There is no limitation on what data you can index,  
Canvas supports dotfiles, meaning, you can have a per-project ~/.ssh config activated whenever you switch from   
`$ context set work://customer-foo/project-foo`
to
`$ context set work://customer-foo/project-bar`

----

OK, now the loong, boring and probably outdated version desperately awaiting a refactor:

**Contexts** are represented by a virtual file-system tree powered by bitmaps\[[0](https://en.wikipedia.org/wiki/Bitmap)\]. Every tree node("directory") represents a **layer** linked to a roaring bitmap\[[1](https://roaringbitmap.org/)\], filtering down all unstructured information fighting for your attention while working in a standard(tm) desktop environment(emails, notifications/chat and system messages, growing number of random browser tabs, unmanageable stack of windows and ad-hoc download-extract-test-forget endeavors to name a few).

A **Contex Tree** may look as follows:
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
    │   │   ├── 2024
    │   │   └── 2025
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

**Universe** is your default(home) workspace.  
Your Universe can be further split into self-contained, movable/shareable **Workspaces**, each running its own in-process database with a context tree abstraction on top(you may want to have a dedicated workspace per customer or project; a handover to a BAU engineer would then be just a matter of export - import of your project workspace).

**Context tree Layers** are unique - a `reports` layer in `/work/customer-a/reports` and `/work/customer-b/reports` is stored under the same uuid linking to the same bitmap - renaming/removing/updating one will update all occurences in the context tree.  
Context layers filter different data based on `where` they are placed. Iow, moving `/customer-a/reports` returns reports of customer-a, `/customer-b/report` of customer-b, creating `/reports` will return all data tagged/linked to the reports layer for all customers.  

**Canvas-server** is a small nodejs-based runtime that can be run directly on your machine or dockerized almost anywhere you want(lets say your home NAS or your favorite trusted cloud provider).

You can index almost any data type regardless where it is stored, retrieval can use client context data like network location, OS, device ID etc to make a roaming experience as smooth as your network latencies allow; reads from remote locations are cached using cacache\[[2](https://www.npmjs.com/package/cacache)\].

**A common use-case of a shared "Home" workspace**: 
Lets say you are planning your upcoming vacation and looking for accommodation "the-old-fashioned-way"(tm) by searching the interweb yourself.  
You set your context to `home://travel/2025/europe/spain`(via command-line of course: `$ context set home://travel/...`), this will update all your linked applications - in our example browser tabs - and you start opening your listings. Your significant other can connect to the same shared context and start adding new tabs, closing the already reviewed ones, even adding notes or contacts. All context tree events are propagated to all connected clients(whenever someone opens a browser tab, a new tab is auto-created or auto-closed on all linked browsers(if configured to do so)).

Another common use-case is work on multiple customers/projects/tasks - all with their own set of contacts and long-running email threads, notes, files, browser tabs etc. Switching between them should be like teleporting between perfectly maintained offices - all of them with a tidy clean desk with only the tools you need to accomplish your task.

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

## Get Involved

- By opening a new issue in any existing repository
- By picking an issue to work on from <https://github.com/orgs/canvas-ui/projects/2>
- Saying hello in [Slack](https://join.slack.com/t/augmentd-labs/shared_invite/zt-2x9ywczgx-UYP6buONsPe0kvNM9zVdxw)
- Or spamming us directly via <dev@getcanvas.org>
