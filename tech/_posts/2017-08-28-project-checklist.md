---
title: Software Project Checklist
tags: Short
---

If you lead a software project, remember that the role entrusts you with more than the [craft or
sullen art] of software construction. You also need to manage project **delivery** and ensure code
**quality**. Most developers understand what these mean but it helps to breaks these down to
concrete tasks, so here goes:

##### Managing Delivery

Professional software is written for users and we must ensure it gets delivered to them,
<mark>predictably</mark>. This requires us to:

- Define a versioning scheme (or [pick](http://semver.org) one). 
- Define relevant delivery *channels* (e.g. `beta`, `stable`, `nightly`, `snapshot`, etc.).
- Commit to a release *cadence* on each *active* channel (e.g. a `beta` release every two weeks).
- Communicate upcoming and released [changes].
- Maintain an up-to-date [bill of materials].

The software we deliver, transitively delivers, third-party libraries. Our *software supply chain*
must only use <mark>reputable</mark> vendors and we're responsible for tracking and relaying
relevant changes (e.g. security fixes) in our supply chain to our users.

##### Ensure Code Quality

Project source code, like a cherished garden, requires constant upkeep and weeding to maintain its
quality. *Quality* is a compound attribute that includes overall design adherence, code-style
conformance, automated test coverage, known defect counts and many other things. Well meaning
contributors can compromise quality unless we:

- Automate static code analysis (use [bots] and linters) to focus code-reviews on *substance*.
- Measure and track automated test coverage *diligently*. 
- Enforce a commit *acceptance criteria*.

The key to a stress-free quality guard is enabling <mark>local</mark> evaluation of the acceptance
criteria. That is, contributors should be able to verify, *in their own work-space*, if their
proposed changes meet the project *minimum* quality criteria.

[changes]: http://keepachangelog.com/en/1.0.0/
[craft or sullen art]: https://en.wikipedia.org/wiki/In_my_Craft_or_Sullen_Art
[bill of materials]: https://en.wikipedia.org/wiki/Bill_of_materials
[bots]: https://docs.sonarqube.org/display/PLUG/GitHub+Plugin
