# Binome and Mira design prototype

This repository contains interactive prototypes for Binome and Mira. Use them as a shared design reference when updating the application in its own repository.

The HTML files are updated as the design evolves. Point your agent to this repository to check the latest design changes and apply relevant updates in your application.

## What's here

| File | Purpose |
|---|---|
| [Designs/mira-app.html](Designs/mira-app.html) | App prototype, including Home, Tasks, Routines, and Mira |
| [Designs/mira-admin-onboarding.html](Designs/mira-admin-onboarding.html) | Administrator setup |
| [DESIGN.md](DESIGN.md) | Visual guidance and design notes |

## Preview locally

From the repository root:

~~~sh
python3 -m http.server 4173 --bind 127.0.0.1
~~~

Open [the app prototype](http://127.0.0.1:4173/Designs/mira-app.html), or use `Designs/mira-admin-onboarding.html` in the URL. Stop the server with Ctrl+C. Python 3 is sufficient; some pages need network access for remote scripts.

## Working with agents

Read [AGENTS.md](AGENTS.md) for repository instructions and [HANDOVER.md](HANDOVER.md) for updating the prototype or your application with an agent.
