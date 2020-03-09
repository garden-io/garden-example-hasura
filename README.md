# Hasura Example

This is a simple application that demonstrate how to run a Hasura project on Kubernetes with Garden. Adapted from the [`gatsby-postgres-graphql` example app](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/gatsby-postgres-graphql).

You need to have access to a Kubernetes cluster to run this project. If you're running the project locally, you'll also need Docker.

## Project Structure

The project contains the following [Garden modules](https://docs.garden.io/using-garden/adding-modules):

* A Gatsby frontend, configured for hot reloading with Garden.
* The Hasura GraphQL Engine.
* A Postgres database and tasks for initialising and clearing it.

The configuration for each module is in the corresponding `garden.yml` file.

## Running the project

### Step 1 - Install Garden

Follow the [instructions here](https://docs.garden.io/basics/installation) to install Garden.

### Step 2 - Deploy the project

#### Local Kubernetes

**If you're running Kubernetes locally**, run the following command to deploy the project with hot reloading enabled:

```sh
garden dev --hot frontend
```

#### Remote Kubernetes

**For developing against a remote cluster**, update the variables in the project level `garden.yml`:

```yaml
kind: Project
name: hasura-example
# ...

environments:
  # ...
  - name: remote
    variables:
      hasura-hostname: hasura.${local.env.USER || local.username}-hasura-example.my-domain.com # <--- Set this to your own domain for remote development

providers:
  # ...
  - name: kubernetes
    context: my-context # <--- Set this to our Kubernetes context
    defaultHostname: ${local.env.USER || local.username}-hasura-example.my-domain.com # <--- Set this to your own domain
```

And run Garden with:

```console
garden dev --hot frontend --env remote
```

In this case, you'll neither need to have Kubernetes or Docker running on your machine.

---

In both cases, Garden will build all the services and deploy them to a Kubernetes cluster. It will also run tasks to populate the database.

Note that since the `frontend` depends on the `db-init` task, Garden will not deploy the `frontend` until the database is ready and initialised.

Garden is now watching your code for changes in hot reload mode. If you for example make changes to the `frontend` it will update immediately.


### Step 3 - Track the `author` table

Open the Hasura console at `http://hasura.local.app.garden` (or the `hasura-hostname` you specified if running remotely), select the **DATA** tab and click the **Track** button to track the `author` table that was generated with the `db-init` task.

Open the app at `http://hasura-example.local.app.garden/` (or the `defaultHostname` you specified if running remotely) to see it in action.