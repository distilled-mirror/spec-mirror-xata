> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Scheduled Clone

> Sample Kubernetes cron job to periodically copy the data from production to your production clone.

This contains a sample Kubernetes manifest file for setting up a periodic task to run Xata Clone.

## Kubernetes spec

```yaml theme={null}
apiVersion: batch/v1
kind: CronJob
metadata:
  name: xata-clone
  namespace: xata-clone          # change if needed
spec:
  schedule: "0 0 * * *"   # change if needed
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: xata-clone
              image: ghcr.io/xataio/xata:latest-pg18
              env:
                - name: XATA_CLI_SOURCE_POSTGRES_URL
                  valueFrom:
                    secretKeyRef:
                      name: xata-secrets
                      key: source-postgres-url
                - name: XATA_API_KEY
                  valueFrom:
                    secretKeyRef:
                      name: xata-secrets
                      key: XATA_API_KEY
                - name: XATA_ORGANIZATION_ID
                  valueFrom:
                    secretKeyRef:
                      name: xata-secrets
                      key: XATA_ORGANIZATION_ID
                - name: XATA_PROJECT_ID
                  valueFrom:
                    secretKeyRef:
                      name: xata-secrets
                      key: XATA_PROJECT_ID
                - name: XATA_DATABASE_NAME
                  valueFrom:
                    secretKeyRef:
                      name: xata-secrets
                      key: XATA_DATABASE_NAME
                - name: XATA_BRANCH_ID
                  valueFrom:
                    secretKeyRef:
                      name: xata-secrets
                      key: XATA_BRANCH_ID
              command:
                - /bin/bash
                - -c
                - |
                  set -e

                  xata init \
                    --organization $XATA_ORGANIZATION_ID \
                    --project $XATA_PROJECT_ID \
                    --database $XATA_DATABASE_NAME \
                    --branch $XATA_BRANCH_ID

                  xata status --json

                  echo "Starting clone..."
                  xata clone start --source-url "$XATA_CLI_SOURCE_POSTGRES_URL" --validation-mode=relaxed
          restartPolicy: OnFailure
```

## Setting up the tasks

Create a dedicated namespace for the task (if you'd like, you can also use an existing one):

```sh theme={null}
kubectl create namespace xata-clone
```

### Step 1: Create secret

The task requires several env variables. Assuming you have the xata CLI already configured on your computer, you can create a secret with all the relevant information like this:

```sh theme={null}
kubectl create secret generic xata-secrets \
  --namespace xata-clone \
  --from-literal=source-postgres-url="$RDS_URI" \
  --from-literal=XATA_API_KEY="$(xata keys organization create)" \
  --from-literal=XATA_ORGANIZATION_ID="$(xata organization get id)" \
  --from-literal=XATA_PROJECT_ID="$(xata project get id)" \
  --from-literal=XATA_BRANCH_ID="$(xata branch get id)" \
  --from-literal=XATA_DATABASE_NAME="postgres"
```

The above assumes branch name is `main` and the database name is `postgres`, but you should adjust to your case.

### Step 2: Create task

Assuming you saved the spec file above as `xata-clone-crontask.yaml`, you can create it with:

```sh theme={null}
kubectl apply -f xata-clone-crontask.yaml
```

### Step 3: Test the task

Create a test job manually based on the task:

```sh theme={null}
kubectl create job -n xata-clone --from=cronjob/xata-clone xata-clone-manual
```

And watch the logs:

```sh theme={null}
kubectl logs -l job-name=xata-clone-manual -f -n xata-clone
```

If there are any errors, you can delete the whole task (`kubectl delete -f xata-clone-crontask.yaml`), fix the issue, and repeat Step 2.
