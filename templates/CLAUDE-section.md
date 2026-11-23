## How the chats work together

The chats work as a pyramid with the build chat as orchestrator at the top. The person talks to three chats: the
build chat (tasks), the choices chat (their answers) and the Assistant (anything they want to ask).

| Level | Chat | Model | Does |
|---|---|---|---|
| Top | "<Project>: Build chat", the orchestrator | <most capable, high effort> | Takes tasks, splits a project into parts, hands each part to a head, does the last check on what they say is good, commits, pushes, releases. Writes no feature code |
| Head | "<Project>: <Department A>" | <strong, max effort> | <files and areas it owns>. Splits work among its chats, reviews and tests what comes back |
| Head | "<Project>: <Department B>" | <strong, max effort> | <files and areas it owns>. Splits work among its chats, reviews and tests what comes back |
| Team chats | Under one head | <strong or mid> | One kind of work each. They take their part of whatever project is on, and report to their head |
| Assistant | "<Project>: Assistant", a switchboard | <low effort> | Passes the person's words to the chat that can answer and replies with that chat's name only. Never answers itself, changes nothing |
| Its chats | "Assistant · Choices", "Assistant · Build progress", "Assistant · Questions", "Assistant · Documents" | <strong or mid> | Choices: every question for the person, as a small table; writes the answers to `plans/decisions.md` and passes them back. Build progress: where the build stands. Questions: answers by reading chats and files, changes nothing. Documents: hands over the thing asked for, with no words about it |

| Head | Its team, by kind of work |
|---|---|
| <Department A> | "<A> · <kind of work>", "<A> · <kind of work>" |
| <Department B> | "<B> · <kind of work>", "<B> · <kind of work>" |

- A head adds a chat to its team when a new general kind of work turns up, never one per project.
- One project at a time. The person names it to the build chat, which writes it into `docs/current-project.md`.
  Until the next one is named, both heads and every chat on their teams work on that project only; anything else
  waits, unless it is a fault that breaks what the person uses or a security finding.
- Work goes up only when checked. A worker reports to its head; the head reviews it, runs the tests and, for a
  visual change, looks at the screenshots; when the head says it is good it goes to the build chat for the last
  check and the commit. Nobody passes on work it wrote without another chat having checked it.
- Sent back means: what failed and what to amend, split into smaller parts when it is large. After two tries it
  goes to the person through the choices chat.
- A feature that needs both sides: the heads agree the calls between them first, then build side by side.
- A worker asks its head, a head asks the build chat or the other head. Only the build chat and the heads put
  questions to the person, and only in the choices chat. `plans/decisions.md` is written by the choices chat only.
- Starting a chat: a chat can start another only on a turn the person typed in that chat. A head that needs a new
  chat proposes it as a card for the person to click, or asks for one line in its own chat. The new chat's brief
  names the hand-over note in `docs/chats/` when it takes over a job.

## Commits

- Only the build chat commits and pushes. Every other chat leaves its changes uncommitted and says what it changed.
- The build chat commits when a head says a piece of work is good and its own last check agrees: no chat is in the
  middle of a change, and these pass: `<test command 1>`, `<test command 2>`. It adds files by name, then pushes to
  `<remote and branch>`; never a forced push.

## Where each thing is shown

Each thing is shown in one chat only. A chat that sends one of these elsewhere does not also show it in its own
reply: it writes the `Sent:` line and stops.

| Thing | The one chat that shows it |
|---|---|
| A choice for the person | "Assistant · Choices" |
| A document, a list, a screenshot or a picture they ask for | "Assistant · Documents" |
| The answer to a question they ask | "Assistant · Questions" |
| Where the build stands | "Assistant · Build progress" |

The block at the end of a reply, one `Sent:` line for each chat that was given something, none when nothing was sent:

```
For you: <what to do or decide, at most 20 words; else "nothing">
Result: <what changed and how it was checked, at most 15 words>
Sent: <the chat> · <what was sent to it>
```
