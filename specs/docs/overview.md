> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Xata Documentation

<div className="not-prose relative overflow-hidden px-6 py-12 lg:px-12 lg:py-16">
  <section className="mx-auto max-w-6xl">
    <div className="grid gap-10 lg:grid-cols-[1.1fr_0.9fr] lg:items-end">
      <div>
        <p className="mb-4 text-sm font-semibold text-primary dark:text-primary-light">
          Xata documentation
        </p>

        <h1 className="max-w-3xl text-4xl font-semibold tracking-normal text-gray-950 dark:text-white sm:text-5xl">
          Branch-native Postgres
        </h1>

        <p className="mt-6 max-w-2xl text-lg leading-8 text-gray-600 dark:text-gray-300">
          Build with real production data safely. Xata gives teams instant Postgres branches, anonymized clones, and controlled schema changes for faster development workflows.
        </p>

        <div className="mt-8 flex flex-col gap-3 sm:flex-row">
          <a href="quickstart" className="inline-flex items-center justify-center rounded-lg bg-primary px-5 py-3 text-sm font-semibold text-white transition hover:bg-primary/90">
            Get started
          </a>

          <a href="core-concepts/branching" className="inline-flex items-center justify-center rounded-lg border border-gray-200 px-5 py-3 text-sm font-semibold text-gray-900 transition hover:border-primary hover:text-primary dark:border-gray-800 dark:text-gray-100 dark:hover:border-primary-light dark:hover:text-primary-light">
            Explore branching
          </a>
        </div>
      </div>

      <div className="rounded-lg border border-gray-200 bg-white/70 p-3 shadow-sm dark:border-gray-800 dark:bg-white/[0.03]">
        <img src="https://mintcdn.com/xata/W3vk-RcC5XaWRdBV/images/overview/branching-workflow.png?fit=max&auto=format&n=W3vk-RcC5XaWRdBV&q=85&s=2ede29e09a667af025047ad9b730cf6e" alt="Workflow diagram showing Production Postgres in AWS, GCP, or Azure connected to a Xata production clone and development database branches." className="h-auto w-full rounded-lg" width="1084" height="1270" data-path="images/overview/branching-workflow.png" />
      </div>
    </div>
  </section>

  <section className="mx-auto mt-16 max-w-6xl">
    <div className="grid gap-4 md:grid-cols-2 xl:grid-cols-3">
      <a href="core-concepts/branching" className="group rounded-lg border border-gray-200 p-5 transition hover:border-primary hover:bg-primary/5 dark:border-gray-800 dark:hover:border-primary-light dark:hover:bg-primary/10">
        <p className="text-sm font-semibold text-primary dark:text-primary-light">Core concept</p>
        <h2 className="mt-3 text-xl font-semibold text-gray-950 dark:text-white">Branching</h2>
        <p className="mt-2 text-sm leading-6 text-gray-600 dark:text-gray-400">Create copy-on-write database branches in seconds without duplicating storage.</p>
      </a>

      <a href="tutorials/create-production-clone" className="group rounded-lg border border-gray-200 p-5 transition hover:border-primary hover:bg-primary/5 dark:border-gray-800 dark:hover:border-primary-light dark:hover:bg-primary/10">
        <p className="text-sm font-semibold text-primary dark:text-primary-light">Tutorial</p>
        <h2 className="mt-3 text-xl font-semibold text-gray-950 dark:text-white">Production Clone</h2>
        <p className="mt-2 text-sm leading-6 text-gray-600 dark:text-gray-400">Build realistic staging and development data sets with PII removed.</p>
      </a>

      <a href="ai-agents/overview" className="group rounded-lg border border-gray-200 p-5 transition hover:border-primary hover:bg-primary/5 dark:border-gray-800 dark:hover:border-primary-light dark:hover:bg-primary/10">
        <p className="text-sm font-semibold text-primary dark:text-primary-light">Guide</p>
        <h2 className="mt-3 text-xl font-semibold text-gray-950 dark:text-white">AI Agents</h2>
        <p className="mt-2 text-sm leading-6 text-gray-600 dark:text-gray-400">Give coding agents isolated Postgres branches for safe migrations, tests, and data work.</p>
      </a>

      <a href="core-concepts/schema-changes" className="group rounded-lg border border-gray-200 p-5 transition hover:border-primary hover:bg-primary/5 dark:border-gray-800 dark:hover:border-primary-light dark:hover:bg-primary/10">
        <p className="text-sm font-semibold text-primary dark:text-primary-light">Core concept</p>
        <h2 className="mt-3 text-xl font-semibold text-gray-950 dark:text-white">Schema Changes</h2>
        <p className="mt-2 text-sm leading-6 text-gray-600 dark:text-gray-400">Apply and roll back PostgreSQL schema changes with controlled migration workflows.</p>
      </a>

      <a href="api-reference" className="group rounded-lg border border-gray-200 p-5 transition hover:border-primary hover:bg-primary/5 dark:border-gray-800 dark:hover:border-primary-light dark:hover:bg-primary/10">
        <p className="text-sm font-semibold text-primary dark:text-primary-light">Reference</p>
        <h2 className="mt-3 text-xl font-semibold text-gray-950 dark:text-white">API Reference</h2>
        <p className="mt-2 text-sm leading-6 text-gray-600 dark:text-gray-400">Manage organizations, projects, branches, and platform resources programmatically.</p>
      </a>

      <a href="cli" className="group rounded-lg border border-gray-200 p-5 transition hover:border-primary hover:bg-primary/5 dark:border-gray-800 dark:hover:border-primary-light dark:hover:bg-primary/10">
        <p className="text-sm font-semibold text-primary dark:text-primary-light">Reference</p>
        <h2 className="mt-3 text-xl font-semibold text-gray-950 dark:text-white">CLI Reference</h2>
        <p className="mt-2 text-sm leading-6 text-gray-600 dark:text-gray-400">Work with auth, projects, branches, clone, roll, stream, and status commands.</p>
      </a>
    </div>
  </section>

  <section className="mx-auto mt-16 max-w-6xl border-t border-gray-200 pt-10 dark:border-gray-800">
    <div className="grid gap-x-8 gap-y-6 md:grid-cols-2">
      <a href="platform/project" className="flex items-start justify-between gap-4 rounded-lg p-3 transition hover:bg-gray-100 dark:hover:bg-white/[0.04]">
        <span>
          <span className="block font-semibold text-gray-950 dark:text-white">Platform</span>
          <span className="mt-1 block text-sm text-gray-600 dark:text-gray-400">Projects, branches, logs, metrics, extensions, and compute.</span>
        </span>

        <span className="text-primary dark:text-primary-light">Read</span>
      </a>

      <a href="migrations/aws-rds" className="flex items-start justify-between gap-4 rounded-lg p-3 transition hover:bg-gray-100 dark:hover:bg-white/[0.04]">
        <span>
          <span className="block font-semibold text-gray-950 dark:text-white">Migrations</span>
          <span className="mt-1 block text-sm text-gray-600 dark:text-gray-400">Move PostgreSQL data from AWS, GCP, Azure, Neon, Supabase, or self-hosted sources.</span>
        </span>

        <span className="text-primary dark:text-primary-light">Read</span>
      </a>

      <a href="automations/ga-pr" className="flex items-start justify-between gap-4 rounded-lg p-3 transition hover:bg-gray-100 dark:hover:bg-white/[0.04]">
        <span>
          <span className="block font-semibold text-gray-950 dark:text-white">Automations</span>
          <span className="mt-1 block text-sm text-gray-600 dark:text-gray-400">Add Xata branches and clone workflows to CI and Kubernetes.</span>
        </span>

        <span className="text-primary dark:text-primary-light">Read</span>
      </a>

      <a href="ai-agents/overview" className="flex items-start justify-between gap-4 rounded-lg p-3 transition hover:bg-gray-100 dark:hover:bg-white/[0.04]">
        <span>
          <span className="block font-semibold text-gray-950 dark:text-white">AI Agents</span>
          <span className="mt-1 block text-sm text-gray-600 dark:text-gray-400">Use Claude Code, Codex, Cursor, Copilot, and other coding agents with isolated branches.</span>
        </span>

        <span className="text-primary dark:text-primary-light">Read</span>
      </a>

      <a href="quickstarts/drizzle" className="flex items-start justify-between gap-4 rounded-lg p-3 transition hover:bg-gray-100 dark:hover:bg-white/[0.04]">
        <span>
          <span className="block font-semibold text-gray-950 dark:text-white">Integrations</span>
          <span className="mt-1 block text-sm text-gray-600 dark:text-gray-400">Connect with Drizzle, Railway, and standard PostgreSQL tooling.</span>
        </span>

        <span className="text-primary dark:text-primary-light">Read</span>
      </a>

      <a href="changelog" className="flex items-start justify-between gap-4 rounded-lg p-3 transition hover:bg-gray-100 dark:hover:bg-white/[0.04]">
        <span>
          <span className="block font-semibold text-gray-950 dark:text-white">Changelog</span>
          <span className="mt-1 block text-sm text-gray-600 dark:text-gray-400">Track platform, console, and CLI updates.</span>
        </span>

        <span className="text-primary dark:text-primary-light">Read</span>
      </a>

      <a href="https://xata.io/contact-support" className="flex items-start justify-between gap-4 rounded-lg p-3 transition hover:bg-gray-100 dark:hover:bg-white/[0.04]">
        <span>
          <span className="block font-semibold text-gray-950 dark:text-white">Support</span>
          <span className="mt-1 block text-sm text-gray-600 dark:text-gray-400">Get help with setup, migration, or production usage.</span>
        </span>

        <span className="text-primary dark:text-primary-light">Contact</span>
      </a>
    </div>
  </section>
</div>
