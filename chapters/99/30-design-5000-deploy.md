# Deployment {#sec:design-deployment}

Deployment is an important part of creating a web platform — merely building an application is insufficient, it should be easy to deploy locally (for development) as well as to deploy in production. A web application that is difficult to deploy has the following issues:

- badly maintained, since new developers are unable to pick up the project and start hacking immediately
- slow development, because existing developers have to train new developers on how to get started, or write tricky documentation

## Development (local) {#sec:design-deployment-dev}


## Production (remote) {#sec:deployment-prod}

We intend for the same Docker image created in [@sec:design-deployment-dev] to be used in production. This keeps the development environment as close to the production environment as possible. TODO: **why** is this important? why why why why why