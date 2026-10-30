# TaskFlow Product Requirements Document

## 1. Document purpose

This document defines the user-facing product scope and behavior for
TaskFlow. The OpenAPI contract in `swagger.yaml` and the MySQL data model in
`db_schema.dbml` describe the planned API and persistence design that support
these requirements. They are design artifacts, not evidence that the
application has been implemented.

## 2. Product vision

TaskFlow helps an individual organize work on a private Kanban board and
optionally share a board with other people. Users can create, organize, find,
and update tasks, recover tasks removed by mistake, and understand what
changed. A person must be able to use core task-management features without
creating a team or inviting anyone.

## 3. Goals and success criteria

### Goals

- Provide a useful personal task board for a single user.
- Allow a board owner to share a board with authorized users.
- Organize tasks into To Do, In Progress, and Done.
- Support task search, bounded pagination, and durable saves.
- Make task removal recoverable by default, with an explicit permanent
  deletion action.
- Make board and task activity understandable to authorized users.
- Protect private data and provide clear feedback for invalid, denied, or
  failed operations.

### Success criteria

- A user can use a private board without inviting members.
- Valid task changes persist and appear after a reload.
- Invalid input and unauthorized access do not change or disclose saved data.
- Users can recover a task from trash; permanent deletion requires explicit
  confirmation and cannot be undone.
- Authorized users can determine who performed each recorded board change.

## 4. Users and terminology

- **User:** An authenticated person using TaskFlow.
- **Board owner:** The user who owns a board and controls board membership.
- **Board member:** A user invited and authorized by the owner to access a
  shared board.
- **Board:** A named collection of tasks. A board may be used privately or
  shared with multiple members.
- **Personal board:** A board whose only member is its owner.
- **Task:** A unit of work with a name, status, optional description, optional
  due date, and optional member assignees.
- **Trash:** The board's recoverable holding area for tasks removed from its
  active workflow.
- **Activity history:** A durable, user-visible record of successful changes
  to a board, its membership, and its tasks.

## 5. Scope

### In scope

- Authenticated users and private board ownership.
- Creating and listing boards, and viewing and renaming an owned or shared
  board.
- Optional board invitations, invitation acceptance, member listing, and
  member removal by the owner.
- Task creation, retrieval, editing, status changes, and partial-name
  search.
- Fixed statuses: `pending` (To Do), `in_progress` (In Progress), and
  `completed` (Done).
- Cursor-based pagination for task, board, trash, member, and activity lists.
- Moving one task or a selected group of tasks to trash.
- Listing trash, restoring tasks to their saved workflow status, and
  permanently deleting trashed tasks individually or in a selected group.
- User-visible activity history for successful board, task, and membership
  changes.
- Clear validation, permission, loading, empty, and failure feedback.
- Structured operational logs that support troubleshooting without recording
  credentials or full sensitive request bodies.

### Out of scope for the current release

- Custom workflow columns or custom statuses.
- Real-time push updates. Shared-board changes become visible when a member
  refreshes or reloads the board.
- Public boards or access by non-members.
- Comments, attachments, product notifications other than invitation
  delivery, recurring tasks, and integrations.
- Authentication credential management or password storage by the TaskFlow
  API. Authentication is delegated to an external identity provider.

## 6. Product rules

1. A user can own one or more boards. Creating a board makes the creator its
   owner and only member; no invitation is required.
2. Only the board owner and active members can view or change a board and its
   tasks, trash, or activity history.
3. The owner can invite and remove members. An invited user gains access only
   after accepting the invitation. Members can create and manage tasks but
   cannot manage membership or permanently delete tasks.
4. The owner and members can move tasks to trash, restore them, and view
   board activity. Only the owner can permanently delete tasks.
5. Task status is one of `pending`, `in_progress`, or `completed`. A new task
   defaults to `pending`.
6. Task names are required, cannot contain only whitespace, and are limited
   to 255 characters.
7. Board names are required, cannot contain only whitespace, and are limited
   to 100 characters.
8. Task description is optional and limited to 10,000 characters. Due date
   is optional. Assignees, when provided, must be active members of the
   board. Removing a member clears their assignments from that board's tasks.
9. Invitations expire seven days after creation unless accepted or revoked.
   An invitation is sent to the invited email address.
10. List page size defaults to 20 and must be between 1 and 100. Pagination
   uses an opaque cursor and stable ordering.
11. A task moved to trash retains its status and details. Restore returns it
   to its saved status. Permanent deletion cannot be undone.
12. Bulk trash and permanent-delete requests contain 1 to 100 distinct task
    IDs. The response reports the outcome for each requested task; one
    failed item does not falsely report success for that item.
13. User-visible activity records successful board, task, invitation, and membership
    changes, and each successful item in a bulk operation. Activity entries
    remain after a task is permanently deleted.
14. Timestamps are UTC. A failed operation is not represented as a successful
    change.
15. TaskFlow accepts bearer tokens issued by the configured external
    identity provider. The API does not provide registration, password, or
    token-issuance endpoints. The service resolves the token subject to a
    TaskFlow user.

## 7. Functional requirements

### 7.1 Identity and access

- Every API operation that reads or changes user or board data requires an
  authenticated user.
- The API enforces ownership and membership on every board-scoped request.
- Unauthorized requests do not reveal private board or task contents.
- Board owners can invite users by email, inspect pending invitations and
  current members, revoke pending invitations, and remove members.
- Invitations are emailed, expire after seven days, and can be accepted only
  by the authenticated account matching the invited verified email.
- An invitee must be the authenticated account matching the invited email
  to accept an invitation.

### 7.2 Boards

- An authenticated user can create a board with a name.
- A user can list boards they own or are an active member of.
- A board owner or member can view and rename a board.
- Board deletion is not supported in this release.
- A board created by one user is private until invitations are accepted.

### 7.3 Tasks and workflow

- Authorized board users can list, search, create, view, and edit tasks.
- The board presents active tasks in the three fixed status columns.
- Task creation requires a valid name; omitted status defaults to `pending`.
- Task updates are partial. Omitted fields remain unchanged; explicit null
  may clear optional description, due date, or assignees.
- Search matches part of the task name without changing task status.
- Task lists are cursor-paginated in stable ascending order by
  `(created_at, id)`.
- Tasks on shared boards show their saved changes to authorized members after
  refresh or reload.

### 7.4 Trash and deletion

- Deleting an active task through the task delete action moves it to trash;
  it does not permanently delete the record.
- Authorized board users can list trash, restore tasks, and submit bulk
  trash operations.
- The board owner can permanently delete tasks from trash, individually or
  in bulk.
- Permanent deletion requires explicit confirmation in the client. The API
  enforces owner permission and requires the task to be in trash.
- Bulk responses report each task's success or failure.

### 7.5 Activity history

- Authorized board users can retrieve a paginated activity history.
- Entries identify the actor, action, affected task or board, and UTC time.
- The history includes board creation and renaming; task creation, updates,
  status changes, trash, restore, permanent deletion; and
  membership/invitation changes.
- Each event identifies the actor by a stable user ID and a display-name
  snapshot so history remains understandable if membership changes.
- Bulk actions produce a distinct result entry for each successfully changed
  task.
- Activity entries are retained when a task is permanently deleted.

### 7.6 Validation and error feedback

- Invalid IDs, malformed JSON, unsupported fields, invalid task names,
  unsupported statuses, invalid page sizes, invalid cursors, invalid
  invitations, and invalid bulk requests are rejected with a stable error
  code and a safe, actionable message.
- Permission failures, missing resources, conflicts, unsupported media
  types, and service failures use distinct HTTP status codes.
- Rejected operations do not partially update a task or board. Bulk
  operations are itemized and may have mixed outcomes.
- Errors do not include credentials, sensitive request bodies, or data from
  another user's private board.

## 8. Non-functional requirements

- **Privacy and access control:** Enforce authentication and board
  authorization for every resource operation.
- **Data integrity:** Persist each successful operation and its corresponding
  activity record consistently.
- **Reliability:** Report success only after persistence succeeds; preserve
  the last confirmed state on failure.
- **Usability:** Provide loading, empty, no-results, confirmation, success,
  validation, and retryable error states as appropriate.
- **Operational logging:** Use structured logs with request/correlation ID,
  timestamp, operation, and outcome. Do not log passwords, bearer tokens,
  secrets, or full sensitive request bodies.
- **Health:** Provide process health and dependency readiness checks.
- **Time:** Return timestamps as RFC 3339 UTC date-time values.

## 9. API and data-model alignment

- API and database identifiers use UUIDs.
- API task `name` maps to database `todos.name`.
- The task status values and default are identical in API and database.
- Every task, invitation, and activity event is scoped to a board.
- Tasks use soft deletion through `deleted_at`; permanent deletion removes
  the task but preserves activity records.
- Authenticated user identity is represented by the external provider's
  stable subject; TaskFlow stores no password.
- `swagger.yaml` is the planned HTTP contract. `db_schema.dbml` is the
  planned MySQL-oriented persistence model.

## 10. Dependencies and decisions

- Select and configure an external OIDC-compatible identity provider.
- Confirm token validation, user provisioning, and invitation-email delivery
  integration before implementation.
- Confirm whether all active members may assign tasks to any board member;
  the current contract permits this.
- Select retention and privacy policies for activity history and user data.
- The application, migrations, and service are not implemented in this
  repository; implementation and integration tests remain future work.

## 11. Delivery priorities

### Must — personal-board MVP

- Authentication integration and access control.
- Create/list/view/rename a board.
- Task board viewing, create, view, update, status changes, search, and
  cursor pagination.
- Validation and reliable error feedback.
- Move one task to trash, list trash, restore, and persist changes.
- Health and readiness endpoints.

### Should — collaboration and governance

- Invitations, invitation acceptance, member listing and removal.
- Bulk move to trash.
- Activity history.
- Permanent deletion of a trashed task by the board owner.

### Could — efficiency enhancements

- Bulk permanent deletion.
- Optional task assignment conveniences beyond selecting current board
  members.

Priorities are product guidance; API and schema structures may be designed
ahead of implementation where necessary to keep contracts coherent.

## 12. Definition of done

A feature is done when:

- Its user-facing acceptance criteria are met.
- Success, validation, permission, not-found, and relevant failure paths are
  tested.
- No unauthorized operation exposes or changes data.
- Successful changes persist and are reflected after reload.
- Activity history matches successful state changes.
- OpenAPI and database documentation match the implemented behavior.
- User-facing errors do not reveal secrets or imply an unsuccessful
  operation succeeded.
