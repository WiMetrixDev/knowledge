# Application Architecture

## Intro

This document breaks down WiMetrix's application architecture. We will describe the pieces that make up our architecture.
We will also detail how these pieces fit in together to create make sure our web applications are fast, secure, and reliable.

### The Pieces

- **[ApiSix](#apisix)**: Api gateway, Ingress controller
- **[KeyCloak](#keycloak)**: Authentication and Authorization provider
- **[REST Services](#rest-services)**: Microservice based REST API network
- **[Frontend Applications](#frontend-applications)**: React based Single Page Applications (SPA)
- **[Mobile Apps](#mobile-applications)**: React native based mobile and tablet applications
- **[Desktop Applications](#desktop-applications)**: Electron based desktop applications written with web technologies

## Architecture Diagram

TODO

## ApiSix

[Apache ApiSix](https://apisix.apache.org/) is being used as an API Gateway.

ApiSix provides rich traffic management features like:

- Load Balancing
- Rate Limiting
- Authentication
- Authorization
- Logging
- Dynamic Upstream
- Circuit Breaking
- Observability
- Canary Release

ApiSix offers these features by integrating with popular 3rd party libraries.
This modular design enables us to integrate existing tools and workflows into ApiSix with ease.

ApiSix can be configured through a REST API, and it also comes with a web-based dashboard.

### Where it fits in

- Enables a singular pathway for cross application communication (frontend-to-backend, backend-to-backend)
- Exposes our backend services and frontend applications over the network securely
- Secure backend service calls through an Auth Provider (See: [KeyCloak](#keycloak))
- Enable zero config load balancing
- Provide simple setups for logging, monitoring, and observability

### Setting up a route with ApiSix

TODO

Example Route config:

```yaml
uri: /pack-and-ship/*
name: saad-packing-shipping-backend
plugins:
  authz-keycloak:
    _meta:
      disable: false
    client_id: apisix
    client_secret: ${{CLIENT_SECRET}}
    discovery: http://${{KEYCLOAK_IP:PORT}}/realms/wimetrix/.well-known/uma2-configuration
    lazy_load_paths: true
  cors:
    _meta:
      disable: false
    allow_credential: false
    allow_headers: "*"
    allow_methods: "*"
    allow_origins: "*"
    expose_headers: "*"
    max_age: 5
  proxy-rewrite:
    regex_uri:
      - ^/pack-and-ship/(.*)$
      - /$1
  serverless-pre-function:
    functions:
      - >-
        return function() local core = require "apisix.core"; local token =
        "test"; if not token then ngx.status = 500; ngx.say('{ "error":
        "env_missing", "error_description": "env APISIX_TOKEN not found" }');
        ngx.exit(ngx.HTTP_OK); end core.request.set_header("x-apisix-token",
        token); end
    phase: before_proxy
upstream:
  nodes:
    - host: 10.0.0.42
      port: 5006
      weight: 1
  type: roundrobin
status: 1
```

## KeyCloak

KeyCloak is an Identity and Access Management solution.
It is based on open standards like OpenID Connect, UMA, OAuth 2.0, and SAML.

KeyCloak provides:

- Single-Sign On
- User federation
- Strong authentication
- User management
- Fine-grained authorization
- Integrations with 3rd party auth/identity providers
- Social Login

KeyCloak can be managed through an Admin UI, as well as through a REST API.

### Where it fits in

[KeyCloak](https://www.keycloak.org/) handles every aspect of user authentication and authorization in our architecture.
All applications authenticate directly or indirectly through KeyCloak.

### Auth Flow

Frontend applications authenticate directly with KeyCloak thorough Openid-compliant REST API interfaces.

- Users login with a familiar username/password method from the login page
- Upon successful authentication, The application receives an `Access Token`, a `Refresh Token`, and `User Info` for the authenticated user
- `Access Token` has a short default expiry time of 3 minutes
- `Refresh Token` has a default expiry time of 30 minutes
- When `Access Token` expires, the application uses the `Refresh Token` to obtain a new one
- `Refresh Token` expiry reset with every refresh, allowing the user to stay logged-in when they are actively working on the frontend
- The user is logged out after `Refresh Token` expires (which happens after user is inactive for the duration of the token's expiry period)
- Active user sessions are visible and can be revoked from the KeyCloak admin dashboard at any time
- `Access Token` is forwarded in the Authorization header as a bearer token with every call to the backend services
- The API Gateway redirects the request to `KeyCloak` to be authorized
- The request is forwarded only if the token is valid and the user is authorized to access the route
- ApiSix which sits between the user/client and the services and redirects to KeyCloak for auth
- Backend services are only accessible through ApiSix

### User Access Management With KeyCloak

KeyCloak is an unopinionated and general purpose tool and allows many different general purpose entities.
These entities can be mixed and matched to fit specific organizational structures.

Here's our recommended setup:
Use Role-Based Access Control (RBAC) by combining `Roles`, `Groups`, and `Users`.

- **`Role`**: A specific and narrow permission. Usually maps with an action to be performed.
  - Examples: (`get production order`, `lock packing list`, `delete pack jobs`, `approve packing list`)
  - It is possible to create composite roles that combine several other roles into a super role
- **`Group`**: A user type or an organization role.
  - As the name suggest, groups combine many permissions(`Roles`) under a single name
  - Assign one or many `Roles` to each `Group`
- **`User`**: A system user. Users can be assigned one or more `Groups`.
  - A user with an assigned groups inherits all the `Roles` assigned to the group
  - `Roles` can also be mapped to `Users` directly for fine-grained control.
    - However it should be avoided if possible to make access control flow simple to follow/understand.

### Initial setup for KeyCloak

[Read here for a detailed breakdown of KeyCloak](https://www.keycloak.org/docs/latest/authorization_services/)

- Deploy the KeyCloak container
- Access the admin UI (the default port is `8080`)
- Create a realm called `wimetrix`. We will use them realm for our auth
- Setup `Users`, `Groups`, and `Realm Roles`. (See: [User Access Management](#user-access-management-with-keycloak))
- Minimum and Maximum `Refresh Token` age can be changed from `Realm settings > Sessions > SSO Session Settings`
- Minimum and Maximum `Access Token` age can be changed from `Realm Settings > Tokens > Access Tokens`
- Go to `Realm Settings > Token > Refresh Tokens`
  - Set `Revoke Refresh Token` to `true`
  - Set `Refresh Token Max Reuse` to `1`
- Add a client scope called `groups`, to return the user's groups in user info routes
  - **Type** should stay `none`
  - Set `Display on consent screen` to `false`
  - Set `Include in token scope` to `false`
  - Add a `Group Membership` mapper called `groups` (it is not present in predefined mappers)
  - Add to Token claim `groups`
  - Add to `user info` and `token introspection`
- Add a `Client Scope` called `user_id`, to return the user's id in user info routes
  - Type should stay `none`
  - Set `Display on consent screen` to `false`
  - Set `Include in token scope` to `false`
  - Add a `User Property` mapper (it is not present in predefined mappers)
  - Set `user_id` as the value for `Name` and `Token Claim Name`
  - Set `id` as the value for `Property`
  - Set `Full group path` to `false`
  - Add to `access token`, `user info` and `token introspection`
- Go to the `Client Scope` called `roles`
  - Open the `realm roles` mapper
    - Set `Add to access token` to `true`
    - Set `Add to userinfo` to `true`
  - Open the `client roles` mapper
    - Set `Add to access token` to `true`
    - Set `Add to token introspection` to `true`
- Create A client for the frontend applications
  - Set `Client ID` and `Name` to `frontend`
  - Make sure `Client Authentication` and `Authorization` Stay off
  - Keep only `Direct access grants` checked in `Authentication flow`
  - In `Client scopes`, add `user_id` and `groups` with assigned type `Default`
- Create A client for ApiSix
  - Set `Client ID` and `Name` to `apisix`
  - Set `Client Authentication` and `Authorization` to `true`
  - Uncheck everything in `Authentication flow`
  - In `Client scopes`, add `user_id` and `groups` with assigned type `Default`
  - Make
  - Setup Authorization by going to the `Authorization` tab
    - In the settings tab:
      - `Policy Enforcement` should be `Enforcing`
      - `Decision Strategy` should be `Unanimous`
      - `Remote Resource Management` should be `off`
    - In the `Resources` tab
      - Delete `Default Resource`
      - Add Resources for all routes uris
    - In the `Policies` tab
      - Define all access policies
      - Prefer Using `Role` type policies
    - In `Permissions` tab
      - Map all `Resources` to one or more `Policies`
- Go to `Authentication` > `Required actions` and uncheck all required actions

[This Postman collection](https://www.dropbox.com/scl/fi/zwkbgdl966j12u2cp1kj6/keycloak.json?rlkey=5p33nybje7m751r3ati5t15f9&dl=0)
can be used to test the auth flow (Make sure to update the Variables like `url` and `client_secret` for the collection)

TODO Add screenshots

## REST Services

The Backend applications are divided into many independent REST microservices.
Each microservice has a narrow scope and can be independently deployed.

The backend services use the following tech stack:

- **[JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)**: Our backend programming language of choice
- **[TypeScript](https://www.typescriptlang.org/)**: Type-safe JavaScript
- **[Node.js](https://nodejs.org/)**: JavaScript Runtime for the backend services
- **[Fastify](https://fastify.io)**: Node.js framework for the backend services

## Frontend Applications

Frontend Web applications are written in react as Single Page Applications.
They access the server/db through the backend services.
The frontend is decoupled from the server side logic and can be deployed independently of the backend services.

The frontend applications use the following tech stack:

- **[JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)**: The programming language of the web
- **[TypeScript](https://www.typescriptlang.org/)**: Type-safe JavaScript
- **[React](https://react.dev)**: Frontend library for JavaScript
- **[Tanstack Router](https://tanstack.com/router)**: Routing framework
- **[Material UI](https://mui.com/)**: Component Library and Design System
- **[Apache ECharts](https://echarts.apache.org/en/index.html)**: Chart and Visualization components
- **[Material React Table](https://www.material-react-table.com/)**: Data Grid and table component and hook
- **[Vite](https://vitejs.dev/)**: Dev Server and Bundler

## Mobile Applications

Mobile and Tablet applications are developed in React Native.
This enables easy cross-platform availability and code reuse with the web frontend.
It also allows us to streamline our tech stack and unify on a JavaScript/TypeScript Stack.
This also enables us to integrate the mobile applications tightly into the monorepo architecture.

The apps use the following tech stack:

- **[JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)**: The programming language of the web
- **[TypeScript](https://www.typescriptlang.org/)**: Type-safe JavaScript
- **[React](https://react.dev)**: Frontend library for JavaScript
- **[React Native](https://reactnative.dev)**: Native components and JS-Native bridge to translate javascript into native code
- **[Expo](https://expo.dev)**: React Native meta-framework providing builtin workflows and libraries
- **[React Native Paper](https://reactnativepaper.com/)**: Component Library and Design System

## Desktop Applications

We aim to avoid desktop applications, preferring instead to go with web applications.
For use-cases where a desktop applications are required, we develop them with Electron.js.
This enables us to use web technologies to build the application, which keeps our tech stack unified.

The desktop applications use the following tech stack:

- Uses the frontend tech stack. (See: [Frontend Applications](#frontend-applications))
- **[Electron](https://www.electronjs.org/)**: Cross-platform desktop apps with web technologies
- **[Electron Forge](https://www.electronforge.io/)**: Tool to scaffold, build, and publish electron applications
