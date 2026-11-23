---
title: 'Slow AI'
date: '2026-11-23T09:00:00-05:00'
layout: post
---

Bigger, faster, better, stronger, one shot. What if we did something different?

I started experimenting this weekend with what's possible when you're not chasing the frontier edge, and instead asking what's possible given time. In part spurred by Harper's [recent meditations](https://harper.blog/2026/09/22/break-away/) on what it looks like to design for unlimited tokens. Call it Slow AI.

My preferred form factor these days, as I've [written](https://laughingmeme.org/2026/03/24/coding-and-blogging-on-vacation.html) [about](https://laughingmeme.org/2026/05/22/the-importance-of-tools-and-the-hedonic-treadmill-of-feels-like-magic.html), is something I can drive from my phone with partial, continuously fractured attention. I'm not looking for a cockpit for agents running on my device, I'm looking for a coding tamagotchi. And Claude Code has done a pretty solid job of this. But it comes with Claude's models and Claude's token math, and I wanted to play with my own.

Initial kit:

- [LunaRoute](https://www.lunaroute.com/): open weight models, pay for concurrency not tokens. Once you've paid for a lane, patience is free. (We were discussing a similar approach at work this week, where an engineer referred to it as [FreeCell](https://en.wikipedia.org/wiki/FreeCell) rate limiting. I expect this will become a lot more common for agentic workflows.)
- Fly.io [Sprites](https://fly.io/sprites): good price, plus blazing fast spin up, and spin down to zero when idle. I'm not sitting there watching these things, I don't need to keep a machine hot until I get around to checking. Plus built in connections for handling access to resources.
- [OpenCode](https://opencode.ai): web and mobile UIs, pluggable providers. (Also looking at this at work where providers are prescribed)

LunaRoute was straightforward to sign up for, though it could have done a better job of explaining itself to an agent, who then explained things to me in small words.

First bump in the road: Claude told me to install OpenCode v1, but LunaRoute assumed v2. The upgrade was painless once I figured out what was going on.

Given I already had a Fly.io account, Sprites were a delight to get started with.

OpenCode worked fine. Its server and its web UI fought over the Sprite's limited ports, but it worked. It worked with LunaRoute, where I'm primarily using GLM 5.3. It handled waiting gracefully enough when concurrency limits were hit. And it took a stab at building itself a keep awake solution using Sprites' task APIs, so the boxes don't sleep on long running sessions. (Scale to zero being convenient for me, but alien to agents.) I tried tuning it with a building mode to increase its autonomy and stability, and to target the background models on LunaRoute. Mixed results.

Surprisingly, even with access to OpenCode's full repo, the agents weren't great at understanding and manipulating its internals, and I never really got it to the point where it gracefully survived how transient Sprites are.

And finally, OpenCode didn't have a mobile app. It had a wait list for an official one, and a plethora of third party open source options, none of which were great. Plus the web UI was janky. It felt like it was built to be the web UI for a terminal, even though it had a native API to speak.

So I pivoted to Jesse's [evener](https://github.com/prime-radiant-inc/evener), which was built for long running tasks. It comes with all the belt and suspenders you expect from [Superpowers](https://github.com/obra/superpowers) (especially once I installed Superpowers), something GLM benefits from. It has a web UI that is still in need of some love, but feels web native.

The agents were shockingly good at manipulating evener's internals, even though it's an incredibly obscure tool. The documentation is apparently agent ergonomic, and it felt like asking an LLM to do TypeScript, or some other task heavily represented in the training set. We were able to bang out a keep alive system for long running tasks, a scheduled tasks tool that uses GitHub Actions as our outside clock, and a set of skills for manipulating these tools and Sprites.

And with the exact same model behind it, it was just nicer to work with. I was less fatigued after talking to this thing. Which from Jesse isn't a surprise.

Happy with LunaRoute. Pretty happy with Sprites, though their GitHub gateway, one of the reasons I picked them, can't do private repos, and I ended up working around it with extra orgs and PATs. OpenCode was kind of meh. Evener was fun, but I didn't get a chance to play with the mobile client, as Jesse was mid gutting it yesterday.
