# TaskFlow TODO Application

TaskFlow is a planned TODO management application with a Go REST API and
MySQL persistence. Its end-user workflow is organized around a Kanban
board with To Do, In Progress, and Done columns.

> **Current status:** The Go application and frontend are not implemented.
> This repository contains a black-box API test scaffold, but no Go module,
> database migrations, Docker configuration, or running service. The API
> and Kanban behavior described here are requirements, not shipped features.

## Table of contents

-   [Overview](#overview)
-   [Product scope](#product-scope)
-   [Technology direction](#technology-direction)
-   [Repository contents](#repository-contents)
-   [API design](#api-design)
-   [Data model](#data-model)
-   [Kanban workflow](#kanban-workflow)
-   [Development status](#development-status)
-   [Roadmap](#roadmap)

## Overview

## Product scope

The product supports both personal and collaborative task management.
Users can manage a private board on their own without configuring
collaboration, and can optionally share boards with other authorized users.
Both use the same three-stage task workflow, trash and restore behavior,
and audit history. Custom workflow columns are not part of the current
scope.

The intended capabilities include task and board management, bounded list
pagination, input validation, consistent errors, audit history, operational
logging, and health/readiness checks. These are design goals until
implemented and tested. The planned identity integration uses bearer tokens
from an external OIDC-compatible provider. Personal-board use does not
require sharing; shared-board access is controlled by ownership and
membership.

## Technology direction

-   **Go** for the REST API.
-   **MySQL** for persistent task data.
-   **OpenAPI** for the API contract.
-   **DBML** for the database design.
-   **Docker** is a possible local-development tool, but no Docker
    configuration is currently present.

No Redis, OpenTelemetry, Zipkin, Prometheus, LocalStack, or load-testing
tool is currently configured in this repository.

## Repository contents

| File | Purpose |
| --- | --- |
| `README.md` | Project overview, current design, and status |
| `PRD.md` | Product requirements, scope, rules, and delivery priorities |
| `user_stories.md` | Prioritized end-user stories and acceptance criteria |
| `swagger.yaml` | Planned OpenAPI 3.0 API contract |
| `db_schema.dbml` | Planned MySQL-oriented database design |
| `tests/todo_api_test.go` | Black-box test scaffold for the TODO API workflow |

There is currently no application source tree or executable setup
command. The API test scaffold requires a running service and
`TODO_API_BASE_URL`; it skips when that variable is not set. The scaffold
currently exercises the earlier TODO-only contract and must be aligned with
the board-scoped API when implementation begins. Add build, run, and full
test instructions when the Go service and its dependencies are added.

## API design

The planned API is a versioned REST API. The current API design describes
these routes; they are not live endpoints yet:

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET`, `POST` | `/v1/boards` | List accessible boards; create a private board |
| `GET`, `PATCH` | `/v1/boards/{boardId}` | View or rename a board |
| `GET`, `POST` | `/v1/boards/{boardId}/todos` | List/search or create active tasks |
| `GET`, `PATCH`, `DELETE` | `/v1/boards/{boardId}/todos/{todoId}` | View, update, or move a task to trash |
| `POST` | `/v1/boards/{boardId}/todos/bulk/trash` | Move selected tasks to trash |
| `GET` | `/v1/boards/{boardId}/trash` | List trashed tasks |
| `POST`, `DELETE` | `/v1/boards/{boardId}/trash/{todoId}/restore`, `/v1/boards/{boardId}/trash/{todoId}` | Restore or permanently delete a task |
| `POST` | `/v1/boards/{boardId}/trash/bulk/delete` | Permanently delete selected trashed tasks |
| `GET`, `POST`, `DELETE` | `/v1/boards/{boardId}/invitations` and `/v1/boards/{boardId}/members/{userId}` | Manage invitations and membership |
| `POST` | `/v1/invitations/{invitationId}/accept` | Accept an invitation |
| `GET` | `/v1/boards/{boardId}/activity` | Review board activity |
| `GET` | `/health` | Check whether the API process is running |
| `GET` | `/ready` | Check whether the API can serve requests |

The API contract is a planned design, not a running service. Protected
endpoints require an external-provider bearer token. Task deletion moves
the task to trash; permanent deletion is a separate owner-only action.
See [PRD.md](PRD.md) for product requirements and decisions.

## Data model

The planned DBML contains users, boards, memberships, invitations, tasks,
assignees, and activity events. UUIDs, task names, statuses, board scoping,
soft deletion, and audit retention are aligned with the OpenAPI contract.
The schema is a design artifact and still requires migrations and
implementation.

For the Kanban workflow, the intended columns correspond to `pending`
(To Do), `in_progress` (In Progress), and `completed` (Done). Keep the
DBML, OpenAPI document, Go model, and migrations consistent as they are
updated for personal boards, optional collaboration, and trash behavior.

## Kanban workflow

The end-user requirements are described in [PRD.md](PRD.md) and
[`user_stories.md`](user_stories.md). They cover:

-   Viewing tasks in To Do, In Progress, and Done columns.
-   Creating a task, optionally choosing its initial status.
-   Moving tasks between columns and retaining the change after reload.
-   Validating task input and providing clear operation errors.
-   Editing, viewing, and searching tasks.
-   Moving one or many tasks to trash, restoring tasks, and permanently
    deleting tasks.
-   Managing a private board without collaboration, and optionally sharing
    a board with multiple authorized users.
-   Reviewing an audit history and maintaining safe operational logs.
-   Showing clear empty, loading, and error states.

The current repository has no frontend or board implementation. The API
and data model must support loading tasks and persisting status changes
for the board to work.

## Development status

There are no application build or run commands because this repository
does not contain Go source code, `go.mod`, SQL migrations, or Docker
configuration. The API test scaffold can be compiled independently and
will exercise the TODO workflow when pointed at a running service.
Expand coverage as implementation begins to include input validation,
pagination, database failures, and the Kanban board's empty and error
states.

## Roadmap

-   [ ] Select and configure the external identity provider and invitation
    delivery.
-   [ ] Validate the OpenAPI and DBML designs and implement matching
    migrations and API behavior.
-   [ ] Implement the Go REST API and MySQL persistence.
-   [ ] Add the Kanban board and core task-management interactions.
-   [ ] Add migrations, local development configuration, and automated tests.
-   [ ] Implement cursor pagination and health/readiness endpoints.
-   [ ] Add structured operational logging and verify validation,
    permissions, trash, audit, and collaboration with tests.
