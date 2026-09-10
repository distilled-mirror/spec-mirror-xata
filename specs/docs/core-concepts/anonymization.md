> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Data Anonymization

> Sensitive data anonymization and masking for PostgreSQL

Data anonymization and masking provide significant benefits for ephemeral database environments, such as those created for development, testing, and staging. By transforming sensitive data into anonymized or masked formats, these techniques ensure that developers and testers work with realistic but secure datasets that do not expose actual personally identifiable information (PII) or confidential data.

This dramatically reduces the risk of data breaches and compliance violations, aligning with data protection regulations like GDPR and HIPAA. Furthermore, anonymization and masking simplify data handling in non-production environments by offering developers realistic data-sets based on the production data.

## How it works

`xata clone` is a tool for copying data efficiently between different PostgreSQL instances. Note that data cloning is different from branching. Branching is faster but only works within the Xata platform. `clone` can be used to copy data from other Postgres providers (e.g. AWS RDS) to Xata, as well as to copy data between different Xata instances.

`xata clone` offers the ability to anonymize and mask the data on the fly while it is being copied. The process for cloning is typically set up on a periodic schedule (for example, nightly).

## Transformers basics

The primary mechanism to configure anonymization are *transformers*. Transformers typically work at the column level and anonymize or mask the data depending on the column type and purpose.

For example, an "email" transformer can be used to anonymize email addresses, and is guaranteed to generate syntactically correct email addresses (`info@xata.io -> a2asd112@example.com`). Dedicated transformers are possible for many types of data, like names, addresses, phone numbers, IP addresses, and so on.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/transformers.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=4ddd457c3b6a94098ab9d6d89d004681" alt="Transformers" className="rounded-lg" width="1126" height="590" data-path="images/transformers.png" />

A transformer can have configuration options, for example, whether to keep the domain part of the email address (`info@xata.io -> a2asd112@xata.io`).

Transformers can be **deterministic** which means that the same input value will always generate the same output value. For example, `info@xata.io` always results `a2asd112@example.com` even if it shows up multiple times in the database. This is particular important for maintaining data integrity in relational databases.

Transformers can **mask** data. Masking means that parts of the input are hidden (`info@xata.io -> i***o@xata.io`).

Transformers can use **templating** to implement conditional logic (e.g. don't anonymize particular values) or to generate composed values (e.g. `LastName, FirstName`).

### Supported transformers

`xata clone` uses [pgstream](https://github.com/xataio/pgstream) under the hood, a project for PostgreSQL CDC and replication. `pgstream` implements it's own transformers library, but it can also import transformers from other open source projects, in particular: [Greenmask](https://www.greenmask.io/), [NeoSync](https://github.com/nucleuscloud/neosync) and [go-masker](https://github.com/ggwhite/go-masker).

The supported transformers are documented [here](/docs/opensource/pgstream/docs/transformers#supported-transformers).

It is also possible to write your own **custom transformers** in the Go programming language.

## Getting started

To create an anonymized production clone in Xata, follow these steps:

1. **Create and checkout the production clone branch:**

```bash theme={null}
xata branch create --name production-clone
xata checkout production-clone
```

2. **Configure anonymization transforms:**

```bash theme={null}
xata clone config --source-url postgres://user:pass@host:5432/prod_db --mode prompt
```

Follow the prompts to specify which columns should be transformed.

3. **Clone your production database into the production clone branch:**

```bash theme={null}
xata clone start --source-url postgres://user:pass@host:5432/prod_db 
```

This will copy your production data into the production clone branch, applying the anonymization rules you configured.
