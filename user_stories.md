# ToDo Application — User Stories

This document describes what users should be able to do in the ToDo application and how we’ll know each feature is working as expected. The stories are grouped by area, with a priority assigned to help guide implementation.

**Priority guide**
- **Must:** Needed for the core experience.
- **Should:** Useful, but the first version can work without it.
- **Could:** A possible improvement for a later release.

---

## Epic A — Boards and access

### US-01: Use a personal board
**Priority: Must**

As a user, I want my own private board so I can organize my tasks without having to invite anyone.

**Acceptance criteria**
- When I sign in and open my board, I can view and manage my tasks without creating a team.
- Other users cannot view or change my private board.
- When I return later, my saved tasks and their statuses are still there.

### US-02: Share a board
**Priority: Should**

As a board owner, I want to invite other people to my board so we can work from the same task list when needed.

**Acceptance criteria**
- After I send an invitation, I can see whether it is pending, accepted, or failed.
- People who have not been authorized to join cannot see the board.
- Once an invitation is accepted, the person can open the board as a member.
- I can keep using the board privately if I have not invited anyone.

### US-03: Manage board members
**Priority: Should**

As a board owner, I want to see who has access to my board and manage those members when necessary.

**Acceptance criteria**
- Board settings show current members and invitations that are still pending.
- When I remove a member, they can no longer access the board.
- Members who are not the owner cannot change the board’s membership.
- If an invitation or membership change fails, I get a clear message and the displayed membership remains accurate.

### US-04: Keep boards private to the right people
**Priority: Must**

As a user, I want board access to be limited to people who are allowed to use it, so my tasks stay private.

**Acceptance criteria**
- Owners and authorized members can access the boards they are allowed to use.
- Anyone without access is blocked from viewing a board, its tasks, its trash, or its activity history.
- Removing someone’s access takes effect on their next request, even if they viewed the board earlier.

---

## Epic B — Create and manage tasks

### US-05: View the Kanban board
**Priority: Must**

As a user, I want to see my tasks grouped into **To Do**, **In Progress**, and **Done**, so I can quickly understand where things stand.

**Acceptance criteria**
- Each active task appears in the column that matches its saved status.
- All three columns remain visible, even when one has no tasks.
- Task cards show available summary details, such as a due date or assignee.
- While the board is loading, I see a loading indicator instead of an empty board.
- If the board fails to load, I see an error and can retry without losing the last confirmed view.

### US-06: Add a task
**Priority: Must**

As a user, I want to add a task to my board so I can keep track of work that needs to be done.

**Acceptance criteria**
- Users with edit access can create a task with a valid name and, where supported, a status.
- If no status is provided, the task starts in **To Do** (`pending`).
- A missing name, a name containing only spaces, or a name longer than 255 characters is rejected with a helpful message.
- Unsupported statuses or fields are rejected, and no task is created.
- If saving fails, the app tells me and does not show the task as saved.
- On a shared board, other authorized members can see the new task after refreshing.

### US-07: View task details
**Priority: Must**

As a user, I want to open a task and see its details so I know what needs to be done.

**Acceptance criteria**
- Opening an active task shows its name and current status.
- If the task has a description, due date, or assignees, those details are shown when the feature is supported.
- If the task does not exist or I do not have access to its board, I get a clear response without seeing someone else’s private information.

### US-08: Edit a task
**Priority: Must**

As a user, I want to update a task when its details change so the board stays up to date.

**Acceptance criteria**
- Users with edit access can update supported fields using valid values, and the saved changes appear on the board.
- An update with no editable fields or with unsupported fields is rejected with a clear message.
- An invalid name or status prevents the update from being saved.
- Fields I leave out of a partial update keep their existing values.
- On a shared board, authorized members can see the change after refreshing.

### US-09: Move a task between columns
**Priority: Must**

As a user, I want to move tasks between To Do, In Progress, and Done so the board reflects my progress.

**Acceptance criteria**
- Users with edit access can move a task to any of the three columns.
- After a successful move, the task stays in its new column when the board is reloaded.
- If the requested status is invalid or saving fails, the task stays in its last saved column and I’m told the move was not saved.

### US-10: Search and browse tasks
**Priority: Must**

As a user, I want to search for tasks and browse a large board without having to scan every card.

**Acceptance criteria**
- Searching for part of a task name shows matching active tasks in their respective columns.
- If nothing matches, the app says so and lets me clear the search.
- Clearing the search brings back all active tasks I’m allowed to access.
- If I don’t specify a page size, the app returns 20 tasks. Page sizes from 1 to 100 are accepted.
- A page size outside that range or an invalid cursor is rejected with a clear message.
- If loading another page fails, I can retry without losing results that were already loaded successfully.

---

## Epic C — Trash and recovery

### US-11: Move a task to trash
**Priority: Must**

As a user, I want to move a task I no longer need to trash, while keeping the option to restore it later.

**Acceptance criteria**
- Before moving an active task to trash, the app asks me to confirm or cancel.
- After I confirm and the move succeeds, the task disappears from the active board and appears in trash.
- If I cancel, the task stays on the board unchanged.
- If the move fails, the task remains active and the app tells me it was not moved.

### US-12: Move several tasks to trash
**Priority: Should**

As a user, I want to move multiple selected tasks to trash in one go so I can tidy up the board more quickly.

**Acceptance criteria**
- I can review the selected tasks before confirming the bulk action.
- Cancelling leaves every selected task unchanged.
- After confirmation, the app reports the result for each task: successful moves appear in trash, while failed ones remain active.
- Tasks that are missing or unavailable are never reported as successfully moved.

### US-13: Review trash and restore a task
**Priority: Must**

As a user, I want to review trashed tasks and restore anything I removed by mistake.

**Acceptance criteria**
- Trash is shown separately from the active board.
- Restoring a task returns it to the status it had before it was trashed.
- If restoring fails, the task remains in trash and the app explains that it could not be restored.
- On a shared board, authorized members can see a restored task after refreshing.

### US-14: Permanently delete a task
**Priority: Should**

As a board owner, I want to permanently delete a task from trash when I’m sure I no longer need it.

**Acceptance criteria**
- Permanent deletion is available to the board owner for tasks in trash.
- The app warns that deletion cannot be undone and lets me confirm or cancel.
- Cancelling leaves the task in trash.
- After a successful deletion, the task is no longer available to restore.
- If I’m not the owner, or deletion fails, the app does not report success and explains the problem.

### US-15: Permanently delete several trashed tasks
**Priority: Could**

As a board owner, I want to permanently delete several selected tasks at once so I can clear out old items more efficiently.

**Acceptance criteria**
- I can review the selected tasks before confirming or cancelling the action.
- Cancelling leaves all selected tasks in trash.
- After confirmation, the app reports the result for each task; failed deletions remain in trash.
- Non-owners cannot perform this action and receive a clear permission message.

---

## Epic D — Activity and reliable saving

### US-16: Review board activity
**Priority: Should**

As a user, I want to see a history of changes to my board so I can understand what happened and, on a shared board, who made each change.

**Acceptance criteria**
- The activity history records task creation, edits, status changes, trashing, restoration, and permanent deletion, along with the task and timestamp.
- Invitations, members joining, and member removals also appear in the history.
- On a private board, the activity identifies the actor as me.
- On a shared board, each recorded change identifies the member who made it.
- When a bulk action has mixed results, the history makes it clear which tasks were successfully changed.
- A task’s previous activity remains available to authorized users after it is trashed or permanently deleted.
- People without board access cannot view its activity history.

### US-17: Know whether an action was saved
**Priority: Must**

As a user, I want clear feedback after each action so I know whether my changes were actually saved.

**Acceptance criteria**
- Success messages reflect what was saved, not just what I attempted to do.
- Validation errors explain what I need to fix, and invalid changes are not saved.
- Permission, missing-data, and service errors are explained in a clear and safe way. I can retry when it makes sense.
- Changes that have not been saved successfully are never shown as confirmed.

### US-18: Pick up where I left off
**Priority: Must**

As a user, I want my board to remember saved changes so I can return later and continue working from the same state.

**Acceptance criteria**
- After I create, edit, move, trash, or restore a task successfully, reloading the board shows the saved result.
- On a shared board, members see the latest saved changes after refreshing.
- If loading or saving fails, the app preserves the last confirmed state and does not treat an unsuccessful change as saved.
