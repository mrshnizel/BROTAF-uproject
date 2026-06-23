# BROTAF — Collaboration Workflow (Git LFS Locking + Multi-User Editing)

We use **two modes**. Pick the right one for the situation:

| Situation | Use |
|---|---|
| You're **both online** doing small live tweaks together | **Multi-User session** |
| You're working **alone / async**, or on a **big binary asset** | **Git lock (Check Out)** |

**Golden rule:** Git is the only permanent record. A Multi-User session is lost
when the server closes unless someone **Persists + Commits** it.

---

## MODE 1 — Live co-editing (Multi-User)

### Start of session
1. Everyone CLOSE the editor.
2. Everyone sync to the SAME baseline:
   ```
   git pull
   git lfs pull
   ```
3. Host: open project -> Multi-User Browser -> **Launch Server** -> create session.
4. Friend: open project -> join the host's session (host Tailscale IP).

### During session
- Edit freely together. No Git locking needed — Multi-User handles concurrency.
- Do NOT Git-lock an asset that's being live-edited in the session.

### End of session  (THIS IS THE STEP PEOPLE FORGET)
1. Pick ONE person to be the committer.
2. That person: Multi-User Browser -> **Persist Session Changes** (flushes live
   edits to real .uasset files on disk).
3. That person submits to Git:
   ```
   git add -A
   git commit -m "Session: <what changed>"
   git push
   ```
4. Everyone else: LEAVE the session, then:
   ```
   git pull
   git lfs pull
   ```
   (Do NOT also persist+commit — only the one committer does, or you get conflicts.)

---

## MODE 2 — Async / big changes (Git lock = padlock)

1. `git pull` first.
2. In Unreal: right-click asset -> Revision Control -> **Check Out**  (locks it 🔒).
3. Edit, then **Submit Content** (commit + push). This releases the lock.
4. See who has what locked:  `git lfs locks`

---

## Don't mix modes on the same asset at the same time
- Multi-User = synchronous (both online now).
- Git lock = asynchronous (taking a file while away).
Never have one person live-editing an asset that another has Git-locked.
