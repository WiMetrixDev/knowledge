# Web Application Architecture

## Intro

This document breaks down WiMetrix's web application architecture. We will describe the pieces that make up our architecture. We will also detail how these pices fit in together to create make sure our web applications are fast, secure, and reliable.

### The Pieces:

- **ApiSix**: Api gateway, Ingress controller
- **KeyCloak**: Authentication and Authorization provider
- **REST Services**: Microservice based REST API network
- **Frontend Application**: React based Single Page Applications (SPA)

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

ApiSix offers these features by integrating with popular 3rd party libraries. This modular design enables us to integrate existing tools and workflows into ApiSix with ease.

ApiSix can be configured through a REST API, and it also comes with a web-based dashboard.

### Where it fits in

- Enables a singular pathway for cross application communication (frontend-to-backend, backend-to-backend)
- Exposes our backend services and frontend applications over the network securely
- Secure backend service calls through an Auth Provider (See: [KeyCloak](#keycloak))
- Enable zero config load balancing
- Provide simple setups for logging, monitoring, and observability

## KeyCloak

### Auth For The Frontend

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

### Setting up KeyCloak

- Deploy the KeyCloak container
- Access the admin UI (the default port is `8080`)
- Create a realm called `wimetrix`. We will use them realm for our auth
- Setup `Users`, `Groups`, and `Realm Roles`
- Minimum and Maximum `Refresh Token` age can be changed from `Realm settings > Sessions > SSO Session Settings`
- Minimum and Maximum `Access Token` age can be changed from `Realm Settings > Tokens > Access Tokens`
- Go to `Realm Settings > Token > Refresh Tokens`
  - Set `Revoke Refresh Token` to `true`
  - Set `Refresh Token Max Reuse` to `1`

## REST Services

- **JavaScript**: Our backend programming language of choice
- **TypeScript**: Type-safe JavaScript
- **Node.js**: JavaScript Runtime for the backend services
- **Fastify**: Node.js framework for the backend services

## Frontend Applications

- **JavaScript**: The programming language of the web
- **TypeScript**: Type-safe JavaScript
- **React**: Frontend library for JavaScript
