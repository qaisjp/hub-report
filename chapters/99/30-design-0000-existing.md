\chapter{Design}

# Successful websites and their APIs {#sec:bg-api-analysis}

In this section we analyse a number of successful websites and their APIs. Some of these are software repositories, but not necessarily all of them.

We discuss a number of the following features for each site:

- **API** Does the website have an API?
    - **Architecture.** Is the API and/or website RESTful? What is the URL scheme like?
    - **Use of public APIs.** Does it use its own public API?
- **Public access?** Does the API and/or website allow public access?
- **PWA.** Is the website a progressive web application?
- **Naming / scoping.** How does the system handle name clashes?

Decisions throughout this report will call back to these discussions.

## GitHub (github.com)

GitHub has two main APIs, [REST API v3] and [GraphQL API v4], in addition to supporting "[webhooks](https://developer.github.com/webhooks/)".

Their documentation describes why GitHub uses GraphQL:

> "GitHub chose GraphQL for our API v4 because it offers significantly more flexibility for our integrators. The ability to define precisely the data you want—and only the data you want—is a powerful advantage over the REST API v3 endpoints. GraphQL lets you replace multiple REST requests with a single call to fetch the data you specify." [@GitHubGraphQLAPI]

[REST API v3]: https://developer.github.com/v3/
[GraphQL API v4]: https://developer.github.com/v4/

GitHub also provides GitHub Package Registry, as well as an "Actions Marketplace".

## npm (npmjs.com)

Example page https://www.npmjs.com/package/leftpad

Does not use its own public API, but actually uses GitHub's API to fetch and display the number of open issues and pull requests.

## Rust Package Registry (crates.io)

crates.io is a progressive web application (or a single page application? TODO) that uses the [Ember.js](https://emberjs.com/) web framework.

It uses its own public API which is available at https://crates.io/api.

Statistics at https://crates.io/api/v1/summary

Search query for "lastfm" - https://crates.io/api/v1/crates?page=1&per_page=10&q=lastfm - we can talk about pagination here too.

Clicking on the "rustfm" package on the search results for "lastfm" triggers several API calls, listed in [@tbl:cratespublend]. The user is presented with a "Loading..." indicator blocking the entire page whilst all this information is being fetched, despite the user not necessarily needing to know all this information.

| Path | Description |
|-----|-----------|
| `/` | Crate metadata, statistics, links and versions |
| `/versions` | Version metadata and stats (the same data is included above) |
| `/0.1.2/authors` | Author names and email addresses |
| `/owner_user` | Profile data for the users that own the crate |
| `/owner_team` | Profile data for the teams that own the crate |
| `/0.1.2/dependencies` | ID and versions of the crate's dependencies |
| `/downloads` | Statistics about individual download entries |

: Public endpoints on `https://crates.io/api/v1/crates/rustfm` {#tbl:crates-publend}

This pop-in effect results in poor user experience, and can be combat in one or more of the following ways:

1. Using a single endpoint to return all the data necessary.
2. Rendering data as son as it is available, and showing a seamless loading indicator for information that is still being fetched.
3. Lazy loading statistics - only fetching this data when the user scrolls down to the statistics section.
4. Enabling HTTP/2 for the APi, so that requests can be multiplexed into a single connection.

---------------------------------------------------------------------------
Method   Path               Description
-------- ------------------ -----------------------------------------------
`DELETE` `/`                Logout the authenticated user

`GET`    `/begin`           Initiate authentication process using GitHub OAuth

`GET`    ```                Complete authentication process using GitHub OAuth,
         /authorize?        returning session information
            code=<code>&
            state=<state>
         ```

---------------------------------------------------------------------------

: Private endpoints on `https://crates.io/api/private/session` {#tbl:crates-privend}

[Table @tbl:creates-privend] shows a number of private endpoints.

## pypi.org

Example page https://pypi.org/project/alive-progress/

Does not use its own public API. Accesses GitHub's public unauthenticated API to fetch and display statistics like number of stars, forks, and open issues / pull requests.

Uses an internal API to fetch dynamic information. Each endpoint provides HTML code that is transcluded into the page to:

- show the available dropdown items depending on who is logged in - https://pypi.org/_includes/current-user-indicator/
- present persistent notifications that can be dismissed - https://pypi.org/_includes/session-notifications/
- present contextual alerts (warnings, errors, success messages and other info) - https://pypi.org/_includes/flash-messages/

The public API is https://warehouse.readthedocs.io/api-reference/ and these endpoints aren't used on the website.

JSON endpoints include:

- retrieving metadata and other info about an individual project - https://warehouse.readthedocs.io/api-reference/stats/#get--stats-
- retrieving metadata and other info about a specific version of an individual project - https://warehouse.readthedocs.io/api-reference/json/#get--pypi--project_name---version--json
- retrieving stats: total size of all packages, and size of top packages - https://warehouse.readthedocs.io/api-reference/stats/#get--stats-