# Binome and Mira design prototype

This repository contains interactive prototypes for Binome and Mira. Use them as a shared design reference when updating the application in its own repository.

The HTML files are updated as the design evolves, and the live prototypes always show the latest version. Point your agent to this repository to check the latest design changes and apply relevant updates in your application.

## Live prototypes

- [App prototype](https://tonik.github.io/binome-prototype-public/designs/mira-app.html)
- [Administrator setup](https://tonik.github.io/binome-prototype-public/designs/mira-admin-onboarding.html)

## What's here

| File | Purpose |
|---|---|
| [designs/mira-app.html](designs/mira-app.html) | App prototype, including Home, Tasks, Routines, and Mira |
| [designs/mira-admin-onboarding.html](designs/mira-admin-onboarding.html) | Administrator setup |
| [DESIGN.md](DESIGN.md) | Visual guidance and design notes |

## Preview locally

From the repository root:

~~~sh
python3 -m http.server 4173 --bind 127.0.0.1
~~~

Open [the app prototype](http://127.0.0.1:4173/designs/mira-app.html), or use `designs/mira-admin-onboarding.html` in the URL. Stop the server with Ctrl+C. Python 3 is sufficient; some pages need network access for remote scripts.

## Working with agents

Read [AGENTS.md](AGENTS.md) for repository instructions and [HANDOVER.md](HANDOVER.md) for updating the prototype or your application with an agent.
