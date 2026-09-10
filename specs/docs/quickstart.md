> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Quickstart

> Quickstart guide for Xata's PostgreSQL platform

export const AgentButtons = ({prompt}) => {
  const [copied, setCopied] = useState(null);
  const timerRef = useRef(null);
  useEffect(() => () => clearTimeout(timerRef.current), []);
  const copy = id => {
    const done = state => {
      setCopied(state);
      clearTimeout(timerRef.current);
      timerRef.current = setTimeout(() => setCopied(null), 2000);
    };
    if (!navigator.clipboard) {
      done(`failed-${id}`);
      return;
    }
    navigator.clipboard.writeText(prompt).then(() => done(id), () => done(`failed-${id}`));
  };
  const label = (id, name) => copied === id ? 'Copied prompt!' : copied === `failed-${id}` ? 'Copy failed' : name;
  const buttonClasses = 'flex min-h-11 cursor-pointer items-center justify-center gap-2 rounded-lg border border-gray-200 bg-transparent px-4 py-3 text-sm font-semibold text-gray-900 no-underline transition hover:border-primary hover:text-primary dark:border-gray-800 dark:text-gray-100 dark:hover:border-primary-light dark:hover:text-primary-light';
  const encoded = encodeURIComponent(prompt);
  return <div className="not-prose my-6 grid grid-cols-2 gap-3 md:grid-cols-4">
      <button type="button" className={buttonClasses} onClick={() => copy('claude')}>
        <svg width="17" height="17" viewBox="0 0 74 68" fill="none" xmlns="http://www.w3.org/2000/svg" className="shrink-0" aria-hidden="true">
          <path d="M15.007 48.5312L29.367 40.4796L29.607 39.7779L29.367 39.3901H28.665L26.262 39.2423L18.057 39.0207L10.941 38.7253L4.048 38.3559L2.311 37.9866L0.684 35.8444L0.851 34.7733L2.311 33.7946L4.399 33.9793L9.019 34.2932L15.95 34.7733L20.977 35.0688L28.424 35.8444H29.607L29.774 35.3643L29.367 35.0688L29.053 34.7733L21.882 29.9165L14.12 24.7827L10.054 21.828L7.855 20.3322L6.746 18.9287L6.266 15.8631L8.262 13.6656L10.941 13.8502L11.625 14.0349L14.342 16.1217L20.145 20.6092L27.722 26.1862L28.831 27.1095L29.275 26.7956L29.33 26.574L28.831 25.743L24.71 18.3008L20.311 10.7293L18.352 7.5899L17.835 5.7063C17.65 4.9307 17.521 4.2843 17.521 3.4903L19.794 0.4063L21.051 0L24.081 0.4063L25.357 1.5143L27.242 5.8171L30.291 12.5945L35.022 21.8095L36.408 24.5426L37.147 27.0726L37.425 27.8482H37.905V27.405L38.293 22.2158L39.014 15.8447L39.716 7.6453L39.957 5.337L41.102 2.5669L43.376 1.0711L45.15 1.9206L46.61 4.0073L46.406 5.3554L45.538 10.9879L43.838 19.8151L42.729 25.7245H43.376L44.115 24.9858L47.109 21.0154L52.136 14.7367L54.353 12.2436L56.941 9.492L58.604 8.1809H61.746L64.056 11.6157L63.021 15.1614L59.787 19.2611L57.107 22.7329L53.263 27.9036L50.86 32.0402L51.082 32.3726L51.655 32.3172L60.341 30.4705L65.035 29.621L70.635 28.6608L73.167 29.8427L73.444 31.043L72.446 33.4991L66.458 34.9765L59.436 36.38L48.975 38.8545L48.846 38.9469L48.994 39.1315L53.706 39.5748L55.721 39.6856H60.655L69.84 40.3688L72.243 41.957L73.684 43.896L73.444 45.3734L69.748 47.257L64.758 46.0751L53.115 43.3051L49.123 42.3079H48.569V42.6403L51.895 45.8905L57.994 51.3936L65.627 58.485L66.015 60.2393L65.035 61.6243L64 61.4766L57.292 56.4351L54.704 54.1637L48.846 49.233H48.458V49.7501L49.807 51.726L56.941 62.4369L57.31 65.724L56.793 66.7951L54.945 67.4414L52.912 67.0721L48.735 61.2181L44.429 54.6254L40.955 48.7159L40.53 48.956L38.478 71.024L37.517 72.151L35.299 73L33.451 71.597L32.472 69.3251L33.451 64.8376L34.634 58.9836L35.595 54.3299L36.464 48.5497L36.981 46.6291L36.944 46.4999L36.519 46.5553L32.158 52.5386L25.523 61.4951L20.274 67.109L19.018 67.6076L16.837 66.4812L17.04 64.4683L18.26 62.677L25.523 53.4435L29.903 47.7187L32.731 44.4131L32.712 43.933H32.546L13.252 56.4536L9.814 56.8968L8.336 55.5118L8.52 53.2403L9.223 52.5016L15.026 48.5128L15.007 48.5312Z" fill="#D97757" />
        </svg>
        <span aria-live="polite">{label('claude', 'Claude Code')}</span>
      </button>
      <a href={`cursor://anysphere.cursor-deeplink/prompt?text=${encoded}`} className={buttonClasses}>
        <svg width="16" height="16" viewBox="0 0 466.73 532.09" fill="none" xmlns="http://www.w3.org/2000/svg" className="shrink-0" aria-hidden="true">
          <path d="M457.43,125.94L244.42,2.96c-6.84-3.95-15.28-3.95-22.12,0L9.3,125.94c-5.75,3.32-9.3,9.46-9.3,16.11v247.99c0,6.65,3.55,12.79,9.3,16.11l213.01,122.98c6.84,3.95,15.28,3.95,22.12,0l213.01-122.98c5.75-3.32,9.3-9.46,9.3-16.11v-247.99c0-6.65-3.55-12.79-9.3-16.11h-.01ZM444.05,151.99l-205.63,356.16c-1.39,2.4-5.06,1.42-5.06-1.36v-233.21c0-4.66-2.49-8.97-6.53-11.31L24.87,145.67c-2.4-1.39-1.42-5.06,1.36-5.06h411.26c5.84,0,9.49,6.33,6.57,11.39h-.01Z" fill="currentColor" />
        </svg>
        Cursor
      </a>
      <a href={`codex://new?prompt=${encoded}`} className={buttonClasses}>
        <svg width="17" height="17" viewBox="0 0 77 76" fill="none" xmlns="http://www.w3.org/2000/svg" className="shrink-0" aria-hidden="true">
          <path d="M29.412 27.6639V20.4438C29.412 19.8358 29.64 19.3796 30.172 19.0758L44.688 10.7159C46.664 9.576 49.02 9.0443 51.452 9.0443C60.572 9.0443 66.348 16.1124 66.348 23.6361C66.348 24.168 66.348 24.7761 66.272 25.3841L51.224 16.5679C50.312 16.0361 49.4 16.0361 48.488 16.5679L29.412 27.6639ZM63.308 55.7841V38.5317C63.308 37.4673 62.852 36.7075 61.94 36.1756L42.864 25.0797L49.096 21.5074C49.628 21.2038 50.084 21.2038 50.616 21.5074L65.132 29.8675C69.312 32.2997 72.124 37.4673 72.124 42.4832C72.124 48.259 68.704 53.5791 63.308 55.7835V55.7841ZM24.928 40.5842L18.696 36.9364C18.164 36.6326 17.936 36.1764 17.936 35.5683V18.8485C17.936 10.7166 24.168 4.5601 32.604 4.5601C35.797 4.5601 38.76 5.6245 41.269 7.5243L26.297 16.1888C25.385 16.7205 24.929 17.4805 24.929 18.5447V40.5848L24.928 40.5842ZM38.342 48.336L29.412 43.3201V32.6805L38.342 27.6647L47.272 32.6805V43.3201L38.342 48.336ZM44.08 71.44C40.888 71.44 37.924 70.376 35.416 68.4762L50.388 59.8119C51.3 59.2802 51.756 58.5202 51.756 57.4558V35.4158L58.064 39.0636C58.596 39.3673 58.824 39.8234 58.824 40.4315V57.1513C58.824 65.2833 52.516 71.44 44.08 71.44V71.44ZM26.068 54.4924L11.551 46.1324C7.371 43.7001 4.56 38.5325 4.56 33.5167C4.56 27.6647 8.056 22.4207 13.451 20.2165V37.5445C13.451 38.6087 13.908 39.3687 14.819 39.9004L33.82 50.9202L27.588 54.4924C27.056 54.796 26.6 54.796 26.068 54.4924ZM25.232 66.9564C16.644 66.9564 10.336 60.4963 10.336 52.5163C10.336 51.9082 10.412 51.3001 10.488 50.6921L25.46 59.3563C26.372 59.8882 27.284 59.8882 28.196 59.3563L47.272 48.3368V55.5566C47.272 56.1649 47.044 56.621 46.512 56.9247L31.995 65.2847C30.019 66.4245 27.663 66.9564 25.232 66.9564H25.232ZM44.08 76C53.276 76 60.952 69.4642 62.7 60.7999C71.212 58.5957 76.684 50.6157 76.684 42.484C76.684 37.1637 74.405 31.9961 70.301 28.272C70.681 26.6758 70.909 25.0797 70.909 23.4844C70.909 12.6165 62.092 4.484 51.908 4.484C49.857 4.484 47.881 4.7876 45.904 5.472C42.484 2.1279 37.772 0 32.604 0C23.408 0 15.733 6.5356 13.984 15.1999C5.472 17.4041 0 25.3841 0 33.5159C0 38.8362 2.28 44.0037 6.384 47.7279C6.004 49.324 5.776 50.9202 5.776 52.5155C5.776 63.3833 14.592 71.516 24.776 71.516C26.828 71.516 28.804 71.212 30.78 70.528C34.2 73.872 38.912 76 44.08 76Z" fill="currentColor" />
        </svg>
        Codex
      </a>
      <button type="button" className={buttonClasses} onClick={() => copy('other')}>
        <svg width="17" height="17" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg" className="shrink-0" aria-hidden="true">
          <rect x="2" y="2" width="16" height="16" rx="4" stroke="currentColor" strokeWidth="1.5" opacity="0.6" />
          <path d="M7 10h6M10 7v6" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" opacity="0.6" />
        </svg>
        <span aria-live="polite">{label('other', 'Other')}</span>
      </button>
    </div>;
};

Xata is a PostgreSQL platform that provides instant Copy-on-Write branching, data masking, and separation of storage from compute. It's designed for modern teams running PostgreSQL at scale, with features like zero-downtime schema changes, realistic production clones, and cloud-agnostic deployment options.

## Let your agent do it

The fastest way to get started is to hand the setup to your coding agent. Paste this prompt into Claude Code, Cursor, Codex, or whichever agent you use:

```text theme={null}
Help me get started with Xata by following: https://xata.io/docs/ai-agents/onboarding.md
```

<AgentButtons prompt="Help me get started with Xata by following: https://xata.io/docs/ai-agents/onboarding.md" />

Your agent installs the CLI, signs you in, creates a project and branch, connects your app, and walks you through Xata's branching workflow, explaining each step along the way.

## Or do it yourself

Prefer to click and type yourself? The steps below take about five minutes.

## 1. Sign up

Create your free Xata account at [console.xata.io](https://console.xata.io). You can sign up with GitHub, Google, or your email address.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/getting-started/signup.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=78a6aedcfd60b488bb3bf3f59fe29542" alt="Xata sign-up page" className="rounded-lg" width="2880" height="1508" data-path="images/getting-started/signup.png" />

## 2. Create a project

After signing in, create a new project. A project is the top-level container for all your branches and Postgres instances.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/getting-started/create-project.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=acaf41caa836a535bb5198463c3a6cdb" alt="Create project page" className="rounded-lg" width="2880" height="1508" data-path="images/getting-started/create-project.png" />

## 3. Create a branch

Within your project, create your first branch (e.g., `main`). A branch is a Postgres cluster—a collection of one or more Postgres instances, which can include a primary and optional replicas. When creating your main branch, you can choose the Postgres version, region, and instance size to fit your needs. This branch can serve as your production branch or a staging environment. You can branch off `main` at any time to create isolated development or test environments.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/getting-started/create-branch.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=21bebd986e32b3139204ac9d9faa5930" alt="Create branch page" className="rounded-lg" width="2880" height="1508" data-path="images/getting-started/create-branch.png" />

## 4. Install and configure the Xata CLI

Install the Xata CLI:

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
```

Authenticate with your Xata account:

```bash theme={null}
xata auth login
```

Initialize your project by running this command in your project directory:

```bash theme={null}
xata init
```

This will create a `.xata` directory with your project configuration.

## 5. Insert sample data

Let's insert some sample data using `psql`. If you don't have `psql` installed:

<CodeGroup>
  ```bash macOS theme={null}
  ## If you are using bash, replace `~/.zshrc` with `~/.bash_profile`
  brew install libpq
  echo 'export PATH="/opt/homebrew/opt/libpq/bin:$PATH"' >> ~/.zshrc
  source ~/.zshrc
  ```

  ```bash Ubuntu theme={null}
  sudo apt install postgresql-client-17
  ```

  ```bash Windows theme={null}
  Download from [PostgreSQL official site](https://www.postgresql.org/download/windows/).
  ```
</CodeGroup>

Connect to `psql` with your dev branch connection string

```sh theme={null}
psql `xata branch url`
```

First, create the required tables:

```sql theme={null}
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  price NUMERIC(7,2) NOT NULL
);

CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE order_items (
  id SERIAL PRIMARY KEY,
  order_id INT REFERENCES orders(id),
  product_id INT REFERENCES products(id),
  qty INT NOT NULL
);
```

Then run the following SQL to insert sample data:

```sql theme={null}
INSERT INTO products(name,price) SELECT LEFT(md5(i::text),8),(random()*90+10)::numeric(7,2) FROM generate_series(1,10)i;
WITH o AS (INSERT INTO orders DEFAULT VALUES RETURNING id) INSERT INTO order_items(order_id,product_id,qty) SELECT o.id,pid,(1+floor(random()*3))::int FROM o,(SELECT id pid FROM products ORDER BY random() LIMIT 5)p;
```

## 6. Create a development branch with the CLI

Create a new branch from `main`:

```bash theme={null}
xata branch create --name dev --parent-branch `xata branch get id`
```

This command will automatically checkout the new branch for you, with the data from `main`.

## 7. Next steps: choose your workflow

Now that you have a base branch and development branch to try out, explore what the rest of the platform has to offer.

* **[Set up a production clone](/docs/tutorials/create-production-clone):** Learn how to use Xata for production clones, feature branches, and collaborative development.
* **[Schema changes](/docs/tutorials/schema-change):** Learn how to safely apply and roll back schema changes with zero downtime.
* **[Migrate to Xata](/docs/migrations/aws-rds):** Get set up for production by migrating your existing PostgreSQL database to Xata.

***

**Need help?** Reach out to the Xata team at [info@xata.io](mailto:info@xata.io) or join our Discord community.
