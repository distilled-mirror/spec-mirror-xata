> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# SQL Editor

> Execute and manage database queries across your branches

![SQL Editor with saved queries in the sidebar, open queries in tabs, and query results below the editor](https://mintcdn.com/xata/fDJrx8iLz9Shdmol/images/platform/sql-editor.png?fit=max\&auto=format\&n=fDJrx8iLz9Shdmol\&q=85\&s=3a8fae40ff1a764bb838e98433a1366c)

Xata's built-in SQL Editor provides a comprehensive environment to explore your data. Run SQL statements, generate SQL with natural language, view and export your results, and save your queries for later use.

The SQL Editor page is organized into a resizable sidebar of saved queries and a workspace with a tab for each open query, split into the editor and the results panel. Both splits can be dragged to resize, and the sidebar collapses automatically on smaller screens.

## Run statements

Execute SQL queries directly in the browser with syntax highlighting and auto-completion.

* Autocomplete makes it easy to create queries without intimate schema knowledge.
* Run multiple statements separately or all at once.
* Select from multiple databases in the branch to run queries against.
* Perform exploratory queries as well as DDL statements.
* Highlight a portion of the editor and use **Run selected** to execute only the highlighted text.
* The run button reflects execution state: **ready**, **running**, **completed**, or **error**.

### Keyboard shortcuts

| Action | Shortcut |
| - | - |
| Run query | `⌘` + `Enter` |
| Save query | `⌘` + `S` |
| Open Ask AI | `⌘` + `K` |
| Rename query | `⌘` + `Shift` + `R` |
| New query | `⌘` + `J` |

## Work in tabs

Queries open in tabs, so you can keep several queries open and switch between them without losing your work. Selecting a query in the sidebar opens it in a new tab, or switches to its tab if it is already open.

* Each tab keeps its own edits, undo history, cursor position, and results.
* A running query keeps running when you switch to another tab. A spinner on the tab shows it is still running.
* Drag tabs to reorder them, or move the focused tab with `Alt` + `Shift` + `←` / `→`.
* Close a tab with its close button, a middle-click, or `Delete` when the tab is focused. Right-click a tab to **Close**, **Close others**, **Close to the right**, or **Close all**.
* When there are more tabs than fit in the tab bar, the rest are listed in a dropdown at the end of the bar.
* Open tabs are restored when you reload the page.

Closing a tab with unsaved changes asks for confirmation first, and you are warned before leaving the page while any open query has unsaved changes.

## Generate SQL with Ask AI

Use AI-powered SQL generation to create queries from natural language descriptions. Ask AI opens as an anchored popover in the editor so you can iterate on prompts without leaving your current statement.

![SQL generation interface showing natural language to SQL conversion](https://mintcdn.com/xata/pwZgANRwCfo4oBhA/images/platform/query-generate-sql.png?fit=max\&auto=format\&n=pwZgANRwCfo4oBhA\&q=85\&s=51539c37cf457694285cb30c7cb8e02a)

Open Ask AI from the editor toolbar or with `⌘K`, describe what you want in plain English, and insert the generated SQL into the editor.

## Save and manage queries

Every query can be saved for later use. The saved queries sidebar supports search, rename and delete actions from a context menu, and shows a marker on queries with unsaved changes.

### Draft and save-as

![Save query button](https://mintcdn.com/xata/pwZgANRwCfo4oBhA/images/platform/query-save.png?fit=max\&auto=format\&n=pwZgANRwCfo4oBhA\&q=85\&s=e60ef30815b3cbafd426eff6aaf65114)

When you click **New query** or the **+** button in the tab bar, a new tab opens in a draft state labeled with a **Draft** badge. Open drafts are listed under **Drafts** in the sidebar. Drafts are not persisted until you save them.

* With **Auto-save** off, use **Save as...** to name and create the query. The save button highlights when there are unsaved changes.
* With **Auto-save** on, the draft is saved as a query as soon as you start typing, then persisted automatically as you type. A **Saving** / **Saved** indicator reflects the current state.

Toggle **Auto-save** from the editor toolbar. The preference is remembered across sessions.

### Toolbar actions

The query toolbar includes quick actions for save, rename, delete, and Ask AI, along with a save status indicator. Renaming is also available by clicking the query name.

## View results

![Query results panel](https://mintcdn.com/xata/pwZgANRwCfo4oBhA/images/platform/query-results.png?fit=max\&auto=format\&n=pwZgANRwCfo4oBhA\&q=85\&s=443fdfe1afd34cfa8a1566a18648f15f)

The results panel sits below the editor and supports multiple result types in tabbed views.

* **Multi-statement tabs** — when multiple statements are run, each result is shown in its own tab.
* **Explain plans** — `EXPLAIN` output is auto-detected and rendered in a dedicated monospace view instead of a table.
* **Statement success** — non-SELECT statements (INSERT, UPDATE, CREATE TABLE, etc.) show a success message with the number of affected rows.
* **Status and row counts** — each tab shows the statement status and the number of rows returned.
* **Full-screen mode** — expand the results panel to occupy the full workspace.

### Display options

Customize how rows are rendered from the results toolbar. These preferences are stored in local storage.

* **Striped rows** — alternate row backgrounds for readability (enabled by default).
* **Compact density** — reduce row padding to show more data at once.
* **Row limit** — cap the number of rows rendered client-side. Choose between 100, 500, 1000, or no limit. When a result exceeds the limit, a notice indicates the cap.

### Export results

* **Copy as JSON** — copy the active tab's rows to your clipboard as JSON.
* **Download as CSV** — export the active tab's rows to a CSV file.

## Performance warnings

When a statement may be slow or expensive, a non-blocking, dismissible warning floats at the bottom of the editor. Dismissed warnings are remembered in local storage.

## Editor preferences

The following preferences persist in local storage and apply across sessions on the same browser:

* Auto-save
* Performance warnings
* Striped rows
* Compact density
* Row limit
* Open tabs


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
