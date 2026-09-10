> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Make Schema Changes

> Learn how to safely apply and roll back schema changes in Xata using the CLI.

You'll use the `xata roll` commands to manage migrations, and an example with changing column types.

To better understand the use case and our novel approach to the challenge of schema changes, see the [schema changes core concept](/docs/core-concepts/schema-changes) page for more details.

`xata roll` is embedding [pgroll](https://pgroll.com) behind the scenes and additional details around schema changes can be found in the [pgroll documentation](https://pgroll.com/docs/latest).

## 1. Prerequisites

* A Xata account ([sign up here](https://console.xata.io))
* The [Xata CLI](/docs/cli) installed:
  ```bash theme={null}
  curl -fsSL https://xata.io/install.sh | bash
  ```
* Access to your Xata PostgreSQL branch

## 2. Create a Xata project and branch

In the Console, create a new project and then click the **Create main branch** button to create the PostgreSQL instance.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/create-new-branch.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=cbb47662022af582812118fea4057b47" alt="Create new branch" className="rounded-lg" width="3010" height="2050" data-path="images/create-new-branch.png" />

Since this is a production setup, we recommend using at least 1 replica. It's a good idea to start with a similar instance size as you are running in your current PostgreSQL provider. You will be able to adjust it later.

## 3. Configure the Xata CLI

Authenticate the CLI by running:

```sh theme={null}
xata auth login
```

Initialize the project by running:

```sh theme={null}
xata init
```

You would typically run `xata init` command in your application source directory, or in any directory/repository where you'd like to keep the configuration. It will create a `.xata` folder with several configuration files.

Follow the instructions from the prompt to select the project and branch that you created at the previous step.

## 4. Prepare a migration directory

Initialize migration tracking on your current Xata branch:

```sh theme={null}
xata roll init
```

This command sets up migration history for your branch.

## 5. Create initial tables

First, let's create a migration file for our initial schema:

```sh theme={null}
cat > .xata/migrations/001_initial_schema.yaml << 'EOL'
operations:
  - create_table:
      name: products
      columns:
        - name: id
          type: serial
        - name: name
          type: text
        - name: price
          type: numeric(7,2)
      constraints:
        - name: products_pk
          type: primary_key
          columns:
            - id
  - create_table:
      name: orders
      columns:
        - name: id
          type: serial
        - name: created_at
          type: timestamptz
          default: now()
      constraints:
        - name: orders_pk
          type: primary_key
          columns:
            - id
  - create_table:
      name: order_items
      columns:
        - name: order_id
          type: int
        - name: product_id
          type: int
        - name: qty
          type: int
      constraints:
        - name: order_items_pk
          type: primary_key
          columns:
            - order_id
            - product_id
        - name: order_items_order_id_fk
          type: foreign_key
          columns:
            - order_id
          references:
            table: orders
            columns:
              - id
        - name: order_items_product_id_fk
          type: foreign_key
          columns:
            - product_id
          references:
            table: products
            columns:
              - id
EOL
```

Apply the migration:

```sh theme={null}
xata roll migrate --complete true
```

## 6. Insert sample data

Now let's insert some sample data using psql:

```sh theme={null}
psql `xata branch url`
```

Then run the following SQL to insert sample data:

```sql theme={null}
INSERT INTO products(name,price) SELECT LEFT(md5(i::text),8),(random()*90+10)::numeric(7,2) FROM generate_series(1,10)i;
WITH o AS (INSERT INTO orders DEFAULT VALUES RETURNING id) INSERT INTO order_items(order_id,product_id,qty) SELECT o.id,pid,(1+floor(random()*3))::int FROM o,(SELECT id pid FROM products ORDER BY random() LIMIT 5)p;
```

## 7. View and pull changes

You can view the changes in your branch details page in the [Xata Console](https://console.xata.io). The changes will be tracked automatically.

To pull the latest migrations locally, run:

```sh theme={null}
xata roll pull
```

This will write the complete schema history as .yaml files to the `.xata/migrations` directory.

## 8. Create a new schema change

Let's create a migration that:

1. Changes the `qty` column in the `order_items` table from integer to float
2. In the `up` function (current schema → new schema):
   * Converts any quantity less than 1 to 1.0
   * Converts all other values from integer to float
3. In the `down` function (new schema → current schema):
   * Uses the column value directly for rollback

Create the migration file with this command:

```sh theme={null}
cat > .xata/migrations/002_alter_order_items.yaml << 'EOL'
operations:
  - alter_column:
      table: order_items
      column: qty
      type: float
      up: |
        CASE 
          WHEN qty < 1 THEN 1.0
          ELSE qty::float
        END
      down: qty
EOL
```

## 9. Start the migration

Start the migration using:

```sh theme={null}
xata roll migrate
```

This command starts the migration but leaves it uncompleted, allowing you to roll back if needed.

## 10. Roll back the migration

When a migration has started, it is serving up both version of the schema simultaneously. You can instantly revert any schema changes during an active migraiton by rolling back the changes. To test rolling back, run:

```sh theme={null}
xata roll rollback
```

## 11. Check migration status

Let's start the migration again and check its status:

```sh theme={null}
xata roll migrate
xata roll status
```

The migration should be `In progress` and you can view all statuses available [here](https://pgroll.com/docs/latest/cli/status).

## 12. Complete the migration

To finalize the migration, run:

```sh theme={null}
xata roll complete
```

In a production scenario, you would only complete the migration when the application code is up to date with the database changes.

## 13. (Optional) Add to CI/CD with GitHub Actions

To automate schema changes in your CI/CD pipeline, you can use our GitHub Actions workflow. This workflow creates a dedicated Xata branch for each pull request, allowing you to test database changes in isolation.

For details about setting up the workflow and available configuration options, see our [GitHub Actions PR workflow documentation](/docs/automations/ga-pr).

## 14. Summary

* Use `xata roll migrate` to apply migrations
* Use `xata roll status` to check migration state
* Use `xata roll rollback` to undo migrations if needed
* Use `xata roll complete` to finalize migrations when your application is ready
* For more details on up/down migrations, see the [pgroll documentation](https://pgroll.com/docs/latest).
* See the [CLI reference](/docs/cli/roll) for all available `xata roll` commands.
