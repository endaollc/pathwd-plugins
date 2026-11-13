# Workflow: pathways

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATHwd each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../../../pathwd-guide/references/commands.md) gives any command's syntax and the tool that runs it.

**Covers:** pathways: guided step-by-step processes that move records through their workflow, such as "Win Job" or "Send to Accounting". Finding them, which ones the user can start where, what a pathway will ask before it runs, and running one screen by screen. Commands: `pathway-run available`, `pathway-run map`, `pathway-run start` / `submit` / `act` / `show` / `cancel`, `execute-pathway`. Each screen and how it is answered: the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [pathway-screens.md](../pathway-screens.md).

The account and session: the [`path-session` guide](../../../pathwd-guide/references/session.md). Every command's syntax: the [`path-cli` guide](../../../pathwd-guide/references/commands.md).

## Building and editing pathways: PATH's Designer

This connection only **runs** pathways. Creating or changing pathways, pathway groups and steps, and copying steps between pathways, is Designer work, done by an Admin or Designer in **PATH's Designer** (or with the PATH Designer plugin's `cli-pathways` command and `path-pathway-builder` skill, if it's installed). If the user asks to build or fix a pathway, or a run fails because the pathway itself is misconfigured, point them there.

## What to ask the user

**For listing pathways:** where the user means: their dashboard, or a record (which one). Pathways are listed only there, as in the web app; there is no list of every pathway in the account.

**For running a pathway (gather ALL before proceeding):**
1. **Which pathway** — the pathway name (e.g. "Win Job", "Send to Accounting")
2. **Which record** — a record ID or enough info to find it (e.g. "Job 12345", "the Smith invoice")

If the user hasn't specified an account ID, ask which account to use or run the [`path-session` guide](../../../pathwd-guide/references/session.md).

## How launching is checked

Every run is held to the web app's launch rules for the acting user (the record's Pathways tab, the dashboard, a folder or a task): role permission, shown on records / dashboard / folder, the pathway's starting business object, record access, and the pathway's visibility criteria. A refusal reads "<user> can't <x> — <why>, as in the web app." There is no "unrestricted" mode and no global `--mode`.

- **`--mode super-user`** (on `pathway-run start` or `execute-pathway` only, for that one run): also starts the run when the pathway's **visibility criteria** don't match the record. Every other launch rule still applies. Only users with a role that has the Designer permission may use it; others are refused. Use it only when the user asks to run a pathway the record isn't shown for.
- **`--as-user <id|email|name>`** runs as another account member, as the web's Impersonate does. Only account **Admins** can use it, never on yourself or a deactivated user. Use it to check what someone else could launch (`pathway-run available --as-user ...`) or to run as them.

## Steps

### Listing Pathways: the dashboard's and a record's

```
--account <ID> pathway-run available --from dashboard
--account <ID> pathway-run available --record-id <id>
```

The two pathway lists the web app shows: the dashboard's, and a record's Pathways tab. Each lists only what the user can start there, with each pathway's context; present the names with what each is for. `--folder-id` and `--task-id` check a folder's header and a task's pathway.

Why a pathway the user names isn't offered on a record:

```
--account <ID> pathway-run map --pathway-name "<name>" --record-id <id>
```

It says whether the user could start it there now and, if not, which launch checks fail (nothing runs).

### What a Pathway Does and Asks (before running it)

```
--account <ID> pathway-run map --pathway-id <id>
--account <ID> pathway-run map --pathway-name "Win Job" --record-id <id>
--account <ID> pathway-run map --pathway-id <id> --sub-pathways
```

Read-only: nothing runs and nothing is saved. It lists every step in the Designer's order (its step numbers) with what the step's screen shows and asks for (the fields a form collects, required ones marked `*`, selection choices, the question and its answer buttons, the message text) or what it does on its own, and where the run goes next for each outcome: Fork conditions as formula text (`if <formula> → step N` / `otherwise → …`), Yes/No answers, found / not found, List Loops (`for each record`, `↻ next record`, `after the loop`), routes that go back to an earlier step (`↺ loops back to …`), Launch Pathway steps (the sub-pathway they run) and where it ends (`■ end of pathway`). After the steps come the loops, the ends, any unreachable steps and any outcome that would make the run fail.

Lines to read: a header (`starts on a Job record · offered on: the Job record viewer`) and a count line (`10 steps: 2 screens, 8 automatic · 3 branching · 1 loop · 2 ways to end · 0 unreachable`); then each step as `<number>. "<title>" · <type> · screen | automatic | sometimes` (`sometimes`: a screen only in some cases), its `screen:` or `does:` line, and an arrow per outcome; then `Loops`, `Ends`, unreachable steps and `Problems:`.

Use it:

- **before running a pathway**, to know which screens are coming and what each one will ask, so you can collect the answers from the user up front (or script them with `--answers`);
- when the user asks **what a pathway does or asks for** ("what would the first pathway ask me for?"), or which branch a record would take;
- to **analyze** a pathway's branches, loops, ends and dead steps.

`--record-id` adds whether the user could start it on that record now (the same launch checks as `pathway-run available`) and marks the branch each Fork on the starting record takes now (`[taken now]`). Steps before a Fork can change the record, so it is a forecast, not a promise. `--sub-pathways` maps every pathway a Launch Pathway step runs as well. Only pathways the user sees in the web app can be mapped (their role holds it, or they're an Admin or Designer); a refusal reads "<user> can't see pathway … — <why>, as in the web app." The pathway's and each step's context are shown once per session, as elsewhere.

### Running a Pathway

#### Step 1: Find the Pathway

```
--account <ID> pathway-run available --record-id <id>
--account <ID> pathway-run available --from dashboard
```

The first is a particular record's Pathways tab, the second the dashboard (requests not tied to one record). Each pathway the user can start there is one line (`✓ <id>  <name>`), with its context under it when it has one. The session context already gives the dashboard list, with each pathway's context, at the start of every session; run the second only if you no longer have it. Read each pathway's context — the designer's description of what it is for — and match the user's request to it, not just to the name. If two could fit, say what each is for and ask. (Skip the call if this session already loaded that list; repeated context comes back left out.)

Opening the record (`get-record`) gives the same list under `Pathways you can start on this record`, and its `Open tasks on this record`: the tasks already on the record, each with its context and the pathway completing it runs (`completing it runs pathway <id> "<name>"`). A task's pathway counts too: if it does what the user asked, run it by completing the task (`pathway-run start --pathway-id <pathway id> --record-id <id> --task-id <task id>`), which also closes the task.

#### Step 2: Find the Record

```
# By ID
--account <ID> get-record --record-id <id>

# By search
--account <ID> search --query "<text>"
```

If the record isn't found on the first search, try other spellings and forms before asking the user (see "When a record can't be found" in the [`cli-records` workflow](../../../pathwd-guide/references/workflows/records.md)).

#### Step 3: Confirm With the User

Before running, present a clear summary:

> "I'm about to run the **Win Job** pathway on **Job JOB-1234**.
>
> This will move the job to the Won milestone. Are you sure?"

#### Step 4: Run

There are two ways to run:

**Like the web app (every screen answered)** — stops at each screen that needs a person:

```
--account <ID> pathway-run start --pathway-name "Win Job" --record-id <record_id>
--account <ID> pathway-run submit ...     # answer the paused screen
--account <ID> pathway-run show           # reprint the paused screen
--account <ID> pathway-run cancel         # cancel a paused run
```

Automatic steps run, server steps run on the server, and each screen that needs input is shown as text, with the commands it accepts under `Next:`. The [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [pathway-screens.md](../pathway-screens.md) has every screen and how to answer it (`submit`, `act`, `--select`, `--skip`, `--answers`). Before answering a screen, be sure what it is asking in business terms: the [`path-concepts` guide](../../../pathwd-guide/references/concepts.md) explains PATHwd's business objects and processes and what each kind of pathway screen asks and does (its pathways reference). Answer only from what the user told you; when a screen asks something they haven't decided, ask them rather than guess. Add `--answers '<json>'` to script the answers, `--copy-of <record id>` to run on a fresh copy (test data), and `--mode super-user` only as described above. See `pathway-run start --help` and `pathway-run submit --help`.

**Screens that take a file** (Upload File, Signature, Send Email attachments): the user will usually drop the file in the chat. Bring it into the workspace first, in the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s order ("Files on this connection"): `attach_chat_file` when the client passed it as a file input (ChatGPT); else `request_file_upload` and its curl command run where the file is (your sandbox); if that fails or you can't run code, give the user the upload link right away and wait for "done". Then answer the screen with the returned path: `pathway-run submit --file uploads/<id>/<name>`. Never say the file is attached before the command confirms it.

**Designer context on the run.** Designers can write *context* on a pathway, each of its steps and each field: their own description of what it is for. `pathway-run` puts it in front of you where it matters:

- the pathway's context (`Pathway context:`) under the "Pathway …" line when a run starts and in `pathway-run show`;
- a sub-pathway's context under the `▸ Sub-pathway "<name>" (<id>) starts` line when a Launch Pathway step starts it;
- the waiting step's context (`Step context:`) under the "Waiting for input · step …" heading;
- each field's context on a line under the field on forms (Edit Record, Create Record and its Create tab, Edit Record Field) and tables (each column, and the detail columns). A table column has no context of its own: it shows the context of the field or relationship field it's based on.

Context is given once per session: a revisited step, and a second run of the same pathway in the same session, leave out the pathway, step and field context you were already shown (`(shown earlier this session)`); pass `--with-context` to repeat it. A field's context longer than 1,000 characters is cut, naming `get-field --field-id <id>`, which prints it whole. Read the context before you answer a screen. Use it to work out what the step is for, which questions to ask the user, and what a sensible answer looks like. It is guidance, not an answer: never fill a value from context when the user hasn't decided it. Ask them.

**Headlessly (no person in the loop):**

```
--account <ID> execute-pathway --pathway-name "Win Job" --record-id <record_id>
--account <ID> execute-pathway --pathway-id <id> --record-id <record_id>
```

> **`execute-pathway` runs the pathway on PATH's headless runner**, exactly as a scheduled pathway, action or integration would: every Yes or No question is answered **No**, and every step that needs a person (Edit Record, Message, Post Note, interactive Create Record / Lookup / Edit Record Field, Signature, Scan Session, …) is **skipped** unless its auto setting lets it finish alone; Send Email sends when it has a To address, a subject and a body (every step type: the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [pathway-steps.md](../pathway-steps.md)). When the pathway has such steps, the command runs nothing and answers `NOT RUN: …` (exit 3) listing them; `--skip-screens` runs it anyway with them handled that way. A later step that needs a skipped step's record makes the run fail. The command applies the same launch rules as the record's Pathways tab and reports success once the run ends, even if a step failed, so always verify (step 5). Pathways meant for people are better run with `pathway-run start`.


Other `pathway-run` commands: `list` (saved runs) and `log [--api]` (a run's full trace: steps, screens, answers; `--api` adds every server request and reply, useful when a run failed).

#### Step 5: Verify

```
# Check the record to confirm the pathway ran
--account <ID> get-record --record-id <record_id>
```

#### Step 6: Report Back

> "Done! I ran the **Win Job** pathway on Job JOB-1234. The job is now in the **Won** milestone."

## CLI Commands Used

| Command | Purpose |
|---------|---------|
| `pathway-run available --from dashboard` / `--record-id <id>` | The dashboard's pathways / a record's: what the user can start there |
| `pathway-run map --pathway-id <id> [--record-id <id>] [--sub-pathways]` | Every step, what its screen asks and where the run goes next (branches, loops, ends), without running it |
| `pathway-run start --pathway-name <n> --record-id <id> [--mode super-user]` | Run a pathway like the web app |
| `execute-pathway --pathway-name <n> --record-id <id> [--skip-screens] [--mode super-user]` | Run a pathway on the headless runner (best for all-automatic pathways; screens skipped only with `--skip-screens`) |
| `get-record --record-id <id>` | Get record details (before and after) |
| `search --query <text>` | Find a record by search term |
| `list-records` | List records to find the target |

## Key facts and confirmations

- Every command here runs through the PATHwd tool for its area (each command's page in [the command index](../../../pathwd-guide/references/commands.md) names it).
- A run changes real data: it can move milestones, create and link records, create tasks and send notifications or emails. The user agrees to a run before it starts.
- `--pathway-name` matches a pathway's whole name, ignoring case ("win job" finds "Win Job"; "win" finds nothing). `pathway-run available` (`--from dashboard` or `--record-id <id>`) gives the exact names.
- After a run, `get-record` shows its result (milestones, fields, tasks); the user hears the outcome in business terms.
- A failed run prints the step that failed and its message; that, with the record's current state, is what to report.
- Many pathways are offered only in some milestones or to some roles: `pathway-run map --pathway-name "<name>" --record-id <id>` says why one isn't offered, and the answer to the user is what would need to change.
- `--mode super-user` needs a role with the Designer permission; `--as-user` needs an account Admin. For anyone else the web app wouldn't allow it either.
- Building or fixing a pathway is Designer work, done by an Admin or Designer in PATH's Designer.
- Pathway, step and field context is the designer's intent: it shapes what to ask and how to check an answer; the values themselves come from the user.
