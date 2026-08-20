# Browser Operation

**Type** Cowork skill  ·  **Trigger** Any session driving the human's own browser through agent tooling

## What it does

Codifies the mechanics of operating a real browser on a real person's machine, clicking, typing, form filling, verification and cleanup. Platform-specific behaviour and message copy live in their own skills, this one owns only the mechanics, and every rule in it was earned live, where something broke or nearly shipped wrong the failure is recorded beside the rule.

## Flow

```
task in the human's own browser
   |
find element by reference ──> click ──> page reflowed?
   |                                       |yes, re-find first
form fields ── set value, focus, commit
   |
verify on a fresh screenshot
   |
confirm on the rendered public view, never the edit form
   |
irreversible action? ──> compose, show, wait for the yes
   |
task ends ──> close every tab opened, announce it
```

## How it runs

Clicking prefers element references over coordinates, references survive layout shifts, and any click that reflows the page invalidates every reference captured before it, so it is click one, re-find, click the next. Typing uses the form-input path wherever the surface allows it, reactive web widgets eat leading keystrokes when their state lags the render, and the working pattern is set the value, focus the field, then commit. In any message composer plain Return sends, line breaks are shift plus Return, because the wrong key fires a partial message at a real person and cannot be undone. Every field is verified on a fresh screenshot after setting it, and every change is confirmed on the rendered public view, not the edit form, a save that returned success has still lied before.

## Guardrails

Some actions are never the agent's call. Sending any message as the human, spending a paid credit, deleting anything, submitting an irreversible form and accepting consent terms all require an explicit yes in the current session, the standard is compose, screenshot, show, wait. A login wall or password prompt is always handed back to the human. When a save fails the network requests are read before any retry, a silent session expiry looks like a vague error banner and blind retries have destroyed form contents.

## Lessons carried

The first rule is manners, not security. Every tab the agent opens is closed at the end of the task, not the session, and the close is announced in one line. The rule exists because the human opened his browser one morning to a wall of tabs left over from the previous night's agent work and said so, plainly. An agent operating someone's machine is a guest on it.
