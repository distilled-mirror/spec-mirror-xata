> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Queries

> Execute and manage database queries across your branches

<img src="https://mintcdn.com/xata/pwZgANRwCfo4oBhA/images/platform/query-view.png?fit=max&auto=format&n=pwZgANRwCfo4oBhA&q=85&s=baf38a34998ae2a310abf6c2ab5d8549" alt="Query view showing the database query interface with SQL editor and results" className="rounded-lg" width="3826" height="1872" data-path="images/platform/query-view.png" />

Xata's built-in query interface provides a comprehensive environment to explore your data. Run SQL statements, generate SQL with natural language, view and export your results, and save your queries for later use.

The queries page is organized into a resizable sidebar of saved queries and a workspace that splits the SQL editor and the results panel. Both splits can be dragged to resize, and the sidebar collapses automatically on smaller screens.

## Run statements

Execute SQL queries directly in the browser with syntax highlighting and auto-completion.

* Autocomplete makes it easy to create queries without intimate schema knowledge.
* Run multiple statements separately or all at once.
* Select from multiple databases in the branch to run queries against.
* Perform exploratory queries as well as DDL statements.
* Highlight a portion of the editor and use **Run selected** to execute only the highlighted text.
* The run button reflects execution state: **ready**, **running**, **completed**, or **error**.

### Keyboard shortcuts

| Action       | Shortcut            |
| ------------ | ------------------- |
| Run query    | `⌘` + `Enter`       |
| Save query   | `⌘` + `S`           |
| Open Ask AI  | `⌘` + `K`           |
| Rename query | `⌘` + `Shift` + `R` |
| New query    | `⌘` + `J`           |

## Generate SQL with Ask AI

Use AI-powered SQL generation to create queries from natural language descriptions. Ask AI opens as an anchored popover in the editor so you can iterate on prompts without leaving your current statement.

<img src="https://mintcdn.com/xata/pwZgANRwCfo4oBhA/images/platform/query-generate-sql.png?fit=max&auto=format&n=pwZgANRwCfo4oBhA&q=85&s=51539c37cf457694285cb30c7cb8e02a" alt="SQL generation interface showing natural language to SQL conversion" className="rounded-lg" width="1708" height="1138" data-path="images/platform/query-generate-sql.png" />

Open Ask AI from the editor toolbar or with `⌘K`, describe what you want in plain English, and insert the generated SQL into the editor.

## Save and manage queries

Every query can be saved for later use. The saved queries sidebar supports search, rename and delete actions from a context menu, and shows a marker on queries with unsaved changes.

### Draft and save-as

<img src="https://mintcdn.com/xata/pwZgANRwCfo4oBhA/images/platform/query-save.png?fit=max&auto=format&n=pwZgANRwCfo4oBhA&q=85&s=e60ef30815b3cbafd426eff6aaf65114" alt="Save query button" className="rounded-lg" width="1220" height="222" data-path="images/platform/query-save.png" />

When you click **New Query**, the editor opens in a draft state labeled with a **Draft** badge. Drafts are not persisted until you save them.

* With **Auto-save** off, use **Save as...** to name and create the query. The save button highlights when there are unsaved changes.
* With **Auto-save** on, drafts are persisted automatically as you type and a **Saving** / **Saved** indicator reflects the current state.

Toggle **Auto-save** from the editor toolbar. The preference is remembered across sessions.

### Toolbar actions

The query toolbar includes quick actions for save, rename, delete, and Ask AI, along with a save status indicator. Renaming is also available by clicking the query name.

## View results

<img src="https://mintcdn.com/xata/pwZgANRwCfo4oBhA/images/platform/query-results.png?fit=max&auto=format&n=pwZgANRwCfo4oBhA&q=85&s=443fdfe1afd34cfa8a1566a18648f15f" alt="Query results panel" className="rounded-lg" width="3498" height="1226" data-path="images/platform/query-results.png" />

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
