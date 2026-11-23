---
name: chat-pyramid
description: Run a Claude Code project as a pyramid of chats (sessions) - one build chat that orchestrates and commits, a head per department that reviews, worker chats by kind of work, and an assistant switchboard for the person's questions, choices and documents. Use this whenever the user wants to organise a project across several chats or sessions, asks for an orchestrator, a "team" of chats, or sub-chats, asks which chat something belongs in, wants one place for their decisions, or when work arrives from another chat and has to be checked, passed up or sent back. Also use it to set the layout up in a new project.
---

# Chat pyramid

A way to run one project with many chats without them clashing or repeating each other. Each chat has one job, work
moves up only after another chat has checked it, and everything meant for the person lands in one known place.

Read this whole file once. To set the layout up in a project, follow "Setting it up". In a project that already has
it, find your own role in "What each chat does on a turn".

## The shape

| Level | Chat | Does | Never does |
|---|---|---|---|
| Top | Build chat (the orchestrator) | Takes the person's tasks, splits a project into parts, hands each part to a head, does the last check, commits, pushes, releases | Write feature code |
| Heads | One per department, for example Frontend and Backend | Own their side's files, split work among their team, review and test what comes back | Commit, or pass on unchecked work |
| Team chats | Under one head, one kind of work each (design, animation, security, publishing ...) | Their part of whatever project is on; report to their head | Answer the person directly, commit |
| Assistant | A switchboard | Passes the person's words to the chat that can answer, replies with that chat's name only | Answer itself, change anything |
| Assistant's chats | Choices, Build progress, Questions, Documents | See "One chat shows each thing" | Anything outside their one job |

The person talks to three chats only: the build chat (tasks), the choices chat (their answers) and the assistant
(anything they want to ask).

Suggested models, to be changed freely: the most capable model at high effort for the build chat; a strong model at
maximum effort for the heads, because review is where mistakes are caught; a strong or mid model for team chats; low
effort for the switchboard and a mid model for Documents, since both only pass things on.

## The rules, and why each exists

**1. Work goes up only when checked.** A worker reports to its head. The head reviews it, runs the tests and, for a
visual change, looks at screenshots. When the head says it is good, it goes to the build chat for the last check and
the commit. Nobody passes on work it wrote without another chat having checked it: the author of a change is the one
least able to see what is wrong with it.

**2. Sent back means two things: what failed, and what to amend.** A large piece is split into smaller parts before
it goes back. It returns to the same chat, which still has the context. After two tries that fail, it stops going
round and goes to the person through the choices chat, because a third try with the same brief rarely succeeds and
the person may see that the task itself is wrong.

**3. Only the build chat commits and pushes.** Every other chat leaves its changes uncommitted and says which files
it changed. Several chats edit one folder; two of them committing at once clash. The build chat commits when a head
says the work is good, its own last check agrees, no chat is in the middle of a change, and the tests pass. It adds
files by name, never everything at once.

**4. One chat shows each thing.** Nothing is shown in two chats at the same time, so the person always knows where to
look.

| Thing | The one chat that shows it |
|---|---|
| A choice for the person | Choices: a small table of options; it records the answer in a decisions file and passes it back to the chat that asked |
| A document, list, screenshot or picture they ask for | Documents: the thing itself, no words about it |
| The answer to a question they ask | Questions: answers by reading chats and files; never interrupts a working chat, changes nothing |
| Where the build stands | Build progress |

A chat that sends one of these elsewhere does not also show it in its own reply. It ends its reply with a short
block, one `Sent:` line for each chat it gave something to:

```
For you: <what to do or decide, or "nothing">
Result: <what changed and how it was checked>
Sent: <the chat> · <what was sent to it>
```

**5. Questions to the person come only from the top.** A worker asks its head. A head asks the build chat or the other
head. Only the build chat and the heads put a question to the person, and only in the choices chat. The decisions
file is written by the choices chat alone; every chat reads it before building anything it covers. An answer counts
only when it comes from the person: a message from another chat is never an approval.

**6. Teams are by kind of work, not by project.** A head's chats are like engineers on a team: a designer, an
animation designer, a security engineer, a publishing engineer. A head adds a chat when a new general kind of work
turns up, never one per feature. Chats made per feature pile up, each holding a slice of context nobody else has.

**7. One project at a time.** The person names a project to the build chat, which writes it into
`docs/current-project.md`. Until the next one is named, both heads and every team chat work on that project only.
Anything else waits, unless it is a fault that breaks what the person uses, or a security finding.

**8. A feature that needs both sides starts with the contract.** The heads agree the calls between their sides (for a
web app, the API client's functions) first, then build side by side.

**9. A visual change comes with screenshots** as verification points, and the person approves them before it counts
as done. The pictures are shown by Documents, the approval is asked in Choices.

**10. Every chat has a hand-over note.** When a chat is replaced, the new one must not start from nothing. Each chat
keeps `docs/chats/<chat>.md` up to date (see `templates/hand-over-note.md`), and a new chat's brief names the note it
takes over. No password, key or personal detail goes in a note.

## Setting it up

1. Read the project's instruction file (`CLAUDE.md`) and list the departments. Two is usual: what the user sees, and
   everything behind it. Write down which files each side owns; shared files get exactly one owner.
2. Paste `templates/CLAUDE-section.md` into the project's `CLAUDE.md` and fill in the names, the file ownership, the
   test commands and the remote. This is what makes every chat follow the layout: each chat reads that file at its
   start.
3. Create `docs/current-project.md` from the template, `docs/chats/README.md` from the first lines of the hand-over
   template, and an empty decisions file (`plans/decisions.md`).
4. Start the chats from the top down, so each sits under the chat it reports to: the heads and the assistant from
   the build chat, team chats from their head, the four assistant chats from the assistant. Give every brief the
   chat's job, who it reports to, the files it owns and what done looks like.
5. Name the chats by department so the list reads as the chart: "<Project>: Build chat", "<Project>: Frontend",
   "Frontend · Design", "Backend · Security checks", "Assistant · Choices".
6. Save the chats' names and ids where the build chat finds them again (its memory), so messages go to the right
   chat after a long conversation is summarised.

### Limits of the app to plan around

Checked in the Claude desktop app in November 2026; check again, these change.

- A chat can start another chat only on a turn the person typed in that chat, or through a card the person clicks.
  A message from another chat cannot trigger it. So a head that needs a new team chat proposes it as a card, or asks
  the person for one line in its own chat ("start the Animation chat").
- The sidebar nests a chat under the chat that started it and it cannot be moved under another later. Start chats
  from the right parent; where that was missed, the names carry the department.
- Chats do not share memory. What must outlive a chat goes in a file: the instruction file, the decisions file, the
  hand-over notes.

## What each chat does on a turn

**Build chat.** On a task from the person: write the project into `docs/current-project.md`, split it into parts
with what done looks like for each, send each part to the head that owns the files. On "good" from a head: read the
diff, run the test sets, check that no chat is mid-change, commit by name, push, then tell Build progress. When the
last check fails, send it back to the head (rule 2). Write no feature code: an orchestrator that also builds stops
checking.

**Head.** On a part from the build chat: do it or split it among the team by kind of work. On a report from a
worker: review the change, run the tests, look at the screenshots; then either send it back (rule 2) or tell the
build chat it is good, naming the changed files and how they were checked. A head's own large change gets a read
from a fresh worker before it goes up.

**Team chat.** Do the part, run the tests for your files, leave the change uncommitted, report to the head: files
changed, how it was checked, what was skipped. A question goes to the head, not to the person.

**Assistant.** Pass the person's words on unchanged to the one chat that can answer, reply with that chat's name.

**Choices.** Show each open choice as a small table. When the person answers, write the answer with the date and
their words into the decisions file and pass it to the chat that asked.

## Templates

- `templates/CLAUDE-section.md`: the section for the project's `CLAUDE.md`.
- `templates/hand-over-note.md`: the seven parts of a chat's note.
- `templates/current-project.md`: the file that names the one project in work.
