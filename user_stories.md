# Task Flow (Kanban Board) — User Stories

This document describes what users should be able to do in the Kanban board application and how we'll know each feature is working as expected. Stories are grouped by epic, and each one has a priority to guide implementation.


## Glossary

| Term | Meaning |
|------|---------|
| **Board** | A workspace holding columns and tasks, e.g. "Website Redesign". |
| **Column** | One of the board's fixed stages of work: *To Do*, *In Progress*, *Done*. |
| **Task (card)** | A single piece of work that sits in one column. |
| **Owner** | The user who created the board. Has full control over it. |
| **Member** | A user invited to a board by its owner. All members have the same permissions. |
| **Private board** | A board that only its owner is on. Every new board starts as private. |
| **Shared board** | A board the owner has added at least one member to. The status is set automatically. |
| **Archive** | Hides finished or inactive items from the board but keeps them indefinitely. Archived items are read-only and can be unarchived at any time. |
| **Trash** | Holds deleted tasks until they are restored or the owner permanently deletes them. |
| **Permanent delete** | Removes an item for good. It cannot be recovered. |

## Roles and permissions

There are two roles: the **Owner** and **Members**. There are no separate Editor or Viewer roles, so every member has the same permissions.

| Action | Owner | Member |
|--------|:-----:|:------:|
| View board and tasks | ✅ | ✅ |
| Create, edit, move tasks | ✅ | ✅ |
| Archive, trash, restore tasks | ✅ | ✅ |
| Permanently delete tasks / empty trash | ✅ | ❌ |
| Rename board, manage members | ✅ | ❌ |
| Delete board (with double confirmation) | ✅ | ❌ |

---

## Epic A — Boards and access

### US-01: Sign up
**Priority:** Must

As a new user, I want to create an account with my name, email and password so that I can have my own boards.

**Acceptance criteria**
- The email must be in a valid format and not already registered. Otherwise an error says why.
- The password must be at least 8 characters.
- After sign-up I am logged in and taken to my (empty) board list.

### US-02: Log in and log out
**Priority:** Must

As a registered user, I want to log in and log out so that only I can reach my boards.

**Acceptance criteria**
- Correct email and password log me in and show my board list.
- A wrong email or password shows a general message ("Invalid email or password") that does not say which field was wrong.
- Logging out ends my session. Opening a board URL afterwards sends me to the login page.
- Pages that need an account cannot be reached without logging in.

### US-03: Create a board
**Priority:** Must

As a user, I want to create a board with a name so that I can organise a project's work.

**Acceptance criteria**
- The board name is required and can be up to 100 characters.
- Every board has three fixed columns: **To Do**, **In Progress** and **Done**. Columns cannot be added, renamed, reordered or deleted in v1.
- I become the board's owner.
- The new board's status is **Private**, because only I am on it.
- The new board opens right after it is created.

### US-04: View my boards
**Priority:** Must

As a user, I want to see a list of all boards I own or am a member of so that I can quickly open the one I need.

**Acceptance criteria**
- The list shows each board's name, my role, its status (**Private** or **Shared**) and when it was last updated.
- If I have no boards, I see an empty state with a "Create board" button.

### US-05: Rename a board
**Priority:** Must

As a board owner, I want to rename a board so that its name stays accurate.

**Acceptance criteria**
- The same validation applies as in US-03.
- The new name appears for all members right away (or the next time they refresh).

### US-06: Invite members
**Priority:** Should

As a board owner, I want to invite people by email so that my team can work on the board.

**Acceptance criteria**
- An invited email that belongs to a registered user adds that user to the board as a member.
- An unregistered email shows "User not found" (sending invitation emails is out of scope for v1).
- Inviting someone who is already a member shows a message and changes nothing.
- When the first member is added, the board's status changes from **Private** to **Shared** automatically.

### US-07: Remove members
**Priority:** Should

As a board owner, I want to remove a member from a board so that I control who has access.

**Acceptance criteria**
- A removed member can no longer see the board.
- The owner cannot remove themselves.
- Tasks assigned to the removed member become unassigned.
- When the last member is removed and only the owner is left, the board's status changes back to **Private** automatically.

---

## Epic B — Create and manage tasks

### US-08: Create a task
**Priority:** Must

As a board member, I want to add a task to a column so that I can track a piece of work.

**Acceptance criteria**
- The title is required and can be up to 200 characters. The description is optional and can be up to 5,000 characters.
- The new task appears at the bottom of the chosen column.
- The task records who created it and when.

### US-09: View task details
**Priority:** Must

As a board member, I want to open a task and see all its details so that I understand the work.

**Acceptance criteria**
- The details view shows the title, description, column, assignee, due date, priority, created by and created at, last updated, and the task's activity (US-26).

### US-10: Edit a task
**Priority:** Must

As a board member, I want to edit a task's title and description so that it stays accurate.

**Acceptance criteria**
- The same validation applies as in US-08.
- Changes are saved and recorded in the activity log.

### US-11: Move a task between columns
**Priority:** Must

As a board member, I want to drag a task to another column so that its status reflects progress.

**Acceptance criteria**
- Drag and drop moves the task. Keyboard users can do the same with a "Move to…" menu.
- The new column and position are saved, and stay the same after a page refresh.
- The move is recorded in the activity log ("moved from To Do to In Progress").

### US-12: Reorder tasks within a column
**Priority:** Must

As a board member, I want to reorder tasks in a column so that the most important work is at the top.

**Acceptance criteria**
- Drag and drop changes the position. The order is saved and is the same for all members.

### US-13: Assign a task
**Priority:** Should

As a board member, I want to assign a task to a board member so that everyone knows who is responsible.

**Acceptance criteria**
- Only board members can be chosen as assignees. A task has at most one assignee.
- The assignee's name or avatar shows on the card.
- The task can be unassigned.

### US-14: Set a due date
**Priority:** Should

As a board member, I want to set a due date on a task so that deadlines are visible.

**Acceptance criteria**
- The due date shows on the card.
- A task that is overdue and not in the last column (e.g. *Done*) is highlighted.
- The due date can be removed.

### US-15: Set priority
**Priority:** Should

As a board member, I want to set a priority (Low / Medium / High) on a task so that the most important tasks stand out at a glance.

**Acceptance criteria**
- Priority is optional, with one value per task.
- The priority shows on the card.
- The priority can be changed or removed.

### US-16: Search and filter tasks
**Priority:** Should

As a board member, I want to search by text and filter by assignee, priority or due date so that I can find tasks quickly.

**Acceptance criteria**
- The search matches the title and description, ignoring upper and lower case.
- Filters can be combined. Only matching tasks stay visible, and the columns stay in place.
- A "Clear filters" button restores the full view.
- Archived and trashed tasks are not included.

### US-17: Comment on a task
**Priority:** Could

As a board member, I want to comment on a task so that discussion stays with the work.

**Acceptance criteria**
- A comment cannot be empty and can be up to 2,000 characters. It shows its author and time.
- Authors can edit or delete their own comments.

### US-18: Checklist inside a task
**Priority:** Could

As a board member, I want to add checklist items to a task so that I can break it into steps.

**Acceptance criteria**
- Checklist items can be added, ticked, unticked, edited and removed.
- The card shows progress, e.g. `2 / 5`.

---

## Epic C — Archive, trash, recovery and delete

> **Rule of thumb:** *Archive* = "done with it, keep it". *Trash* = "don't want it, but I can change my mind until the owner deletes it for good". *Permanent delete* = "gone for good".

### US-19: Archive a task
**Priority:** Must

As a board member, I want to archive a task so that finished work leaves the board without being lost.

**Acceptance criteria**
- The archived task disappears from its column right away.
- The task keeps all its data (details, comments, activity).
- The archive action is recorded in the activity log.

### US-20: View and unarchive tasks
**Priority:** Must

As a board member, I want to see a board's archived tasks and unarchive them so that I can bring back work when needed.

**Acceptance criteria**
- An "Archived" view lists the archived tasks, with search, showing the date each was archived and who archived it.
- Archived tasks are read-only.
- Unarchiving returns a task to its original column, at the bottom.

### US-21: Move a task to trash
**Priority:** Must

As a board member, I want to delete a task so that mistakes or unwanted tasks are removed from the board.

**Acceptance criteria**
- "Delete" moves the task to the board's trash. It does **not** delete it permanently.
- A confirmation toast appears with an **Undo** button for 5–10 seconds.
- Archived tasks can also be moved to trash.

### US-22: View trash and restore tasks
**Priority:** Must

As a board member, I want to see trashed tasks and restore them so that I can recover something deleted by mistake.

**Acceptance criteria**
- The trash view lists each task with the date it was deleted and who deleted it.
- Tasks stay in the trash until they are restored or the owner permanently deletes them (US-23, US-24).
- A restored task goes back to where it was before (its column, or the archive if it was archived).
- Restoring is recorded in the activity log.

### US-23: Permanently delete a task
**Priority:** Must

As a board owner, I want to permanently delete a task from the trash so that unwanted data is truly removed.

**Acceptance criteria**
- This is only possible for tasks that are in the trash.
- A confirmation dialog warns: "This cannot be undone."
- The task and its comments and checklist are removed. The activity log keeps a "task permanently deleted" entry without the task's content.

### US-24: Empty trash
**Priority:** Should

As a board owner, I want to empty the whole trash at once so that I don't have to delete tasks one by one.

**Acceptance criteria**
- A confirmation dialog shows how many items will be removed.
- All tasks in the board's trash are permanently deleted.

### US-25: Delete a board
**Priority:** Should

As a board owner, I want to delete a board, with a double confirmation, so that I can remove a project I no longer need without deleting it by accident.

**Acceptance criteria**
- Only the board owner sees the "Delete board" option.
- **First confirmation:** a dialog warns "This will permanently delete the board and all its tasks. This cannot be undone." and has **Cancel** and **Continue** buttons.
- **Second confirmation:** I must type the exact board name. The **Delete** button stays disabled until the typed name matches.
- Cancelling at either step leaves the board unchanged.
- After deletion, the board and all its tasks, including archived and trashed ones, are permanently removed. The board can't be restored.
- The board disappears from every member's board list. Opening its URL shows "Board not found".

---

## Epic D — Activity and reliable saving

### US-26: Task activity history
**Priority:** Must

As a board member, I want to see a task's history so that I know who changed what and when.

**Acceptance criteria**
- The following are recorded: created, title or description edited, moved (from → to), assigned or unassigned, due date or priority changed, archived, unarchived, trashed, restored.
- Each entry shows who made the change, what changed and when (relative time, e.g. "2 hours ago", with the exact time on hover).
- Entries are shown newest first and cannot be edited or deleted by users.

### US-27: Board activity feed
**Priority:** Should

As a board member, I want to see recent activity across the whole board so that I can catch up on what the team did.

**Acceptance criteria**
- The feed shows the latest activity across all tasks and membership changes, with paging or "load more".
- Clicking an entry opens the related task, if the task still exists.
- The feed can be filtered by member.

### US-28: Handle edits by two people at once
**Priority:** Should

As a user, I want to be told when someone else changed the same task so that I don't overwrite their work without knowing.

**Acceptance criteria**
- Each task carries a version number or last-updated time. A save made against an outdated version is rejected.
- When that happens, I see the latest version and can choose to re-apply my change or throw it away.
- Moves and reorders made by different users at the same time both end up saved, with no task lost or duplicated.

### US-29: See other members' changes live
**Priority:** Could

As a board member, I want the board to update when others make changes so that I always see the current state without refreshing.

**Acceptance criteria**
- Created, edited, moved, archived and trashed tasks appear for other open sessions within a few seconds.
- A live update does not interrupt text I am currently typing.

### US-30: Undo the last action
**Priority:** Could

As a user, I want to undo my last action so that I can quickly fix a mistake.

**Acceptance criteria**
- Undo works for move, reorder, archive, trash and field edits, using Ctrl/Cmd+Z or the toast button.
- The undo itself is saved and recorded in the activity log.

---

## Out of scope for v1

- Custom columns (adding, renaming, reordering or deleting columns)
- Separate member roles, such as read-only viewers
- Sending email notifications and invitation emails
- File attachments on tasks
- Labels or tags on tasks
- Choosing a board's status by hand (it is always set from the member list)
- Swimlanes, subtasks and links between tasks
- Reports and charts (cumulative flow, cycle time)
- A mobile app (the web app should still work on small screens)
- Single sign-on (SSO) and logging in with other services (OAuth)

## Summary

| Epic | Must | Should | Could |
|------|:----:|:------:|:-----:|
| A — Boards and access | US-01–US-05 | US-06, US-07 | — |
| B — Create and manage tasks | US-08–US-12 | US-13–US-16 | US-17, US-18 |
| C — Archive, trash, recovery and delete | US-19–US-23 | US-24, US-25 | — |
| D — Activity and reliable saving | US-26 | US-27, US-28 | US-29, US-30 |
| **Total (30 stories)** | **16** | **10** | **4** |
