This project implements the Client and Resource Owner server roles of the OAuth 2.0 Client Credentials Grant.  It does this using Python and the `python-jose` package and leverages vendors to provide the Authorization Server role.

# GOALS

- Document Each Vendor 
  - how to setup the Authorization Server
  - ease of use when supporting 1 to n client registrations
  - ease of use when supporting 1 to n scopes
  - Their adherence to RFC 6749 (when possible)
  - Their adherence to RFC 6750 (when possible)
  - Their adherence to RFC 7521/7523 (when possible)
  - Token revocation capabilities
  - Token expiration and policy capabilities

- Document setting up a Client
  - How easy it is for each vendor
  - Validation of JWT's using the `python-jose` package validation

- Document setting up a Resource Server
  - Use of access tokens provided by the Authorization Server
  - Authorization Server implementation of token introspection capabilities
  - Validation of JWT's using the `python-jose` package validation

- A runner to automate the execution of each vendor implementation
- If there is time, a runner to automate the multitude and log of ways OAuth can go wrong with misconfiguration (though a pen testing tool might be better at this)

# SETUP

## Authorization Server Setup
Each of the providers below should note how the AS role is created, how to create a Client, create scopes and how enable a policy for the client to use those scopes.

For each vendor, make sure you create the following scopes as the code in this repo depends on them, `get-cats`, `put-cats`, `get-dogs`, `put-dogs`. 

### Okta
1. Create an account for free at https://okta.com
1. Follow the documentation by choosing "Client Credentials" from the [OAuth 2.0 Flow column](https://developer.okta.com/authentication-guide/implementing-authentication/).
1. Create the custom scopes... API -> Authorization Servers -> Edit
1. Create a new Access Policy to set token expiration based on scope... API -> Authorization Servers -> Edit -> Acccess Policies.
1. Use their Token Preview (tab) feature on the Authorization Server

### Auth0
### Authlete
1. Research stopped, [their token request endpoint](https://docs.authlete.com/#token-endpoint) does not support JWS/JWT authentication for Client.
### Apigee
1. It appears that [RFC 7523 is somewhat supported](https://community.apigee.com/articles/35738/rfc7523-in-apigee-edge-exchanging-jwt-for-opaque-o.html). However, that flow is only used to exchange a JWT for an opaque token to then be used to get an access token.  Apparently they don't or can't deal the the computation overhead that comes with using JWT's. 

## Client Setup
This should be a simple Python app that does the following. It should log outputat each step.

1. Generates an access token request
1. Validates the token response
1. Requests a protected resource
1. Recieves the protected resource

## Resource Owner Setup
This should be a simple Python Flask web server that implements `python-jose` as needed and logs what it is doing at each step. 

1. Recieves protected resouce request
1. Validates a token using, uses the `jwks_uri` as needed
1. Validates a token using the Authorization Server introspection endpoint
1. Responds to the Client with a protected resource

# RESULTS
The information in this section is only of use for those looking for some assistance when attempting to measure something like TCO (setup, support, maintaince) when choosing an OAuth 2.0 Authorization Server partner.  It should also be of some use for those needing vendor agnostic implementations of OAuth 2.0 Client and Resource Owner roles.

## Okta - 09/18/2018
Research into using Okta has been put on hold due to it's inability to use a JWS/JWT bearer assertion when authenticating with the authorization server at the access token request endpoint.  *Okta is limited to `client_id` and `client_secret` bearer authentication (which may or may not suffice)*.

1. Documenation - Instructions on how to create custom scopes wasn't clear.
1. AS Setup -
1. Client Registration -
1. Support Scaling Considerations - 
1. Token policies - A robust UX for creating policies and rules that can define a tokens lifetime by using an aggregation of grant types, user/app group membership and scopes. When more than one policy may apply to a requested scope, the most permissive policy is used not the most restrictive. 
1. Token Revocation - 
1. Adherence to RFC's -
1. Pricing Model - per token, AS, RS, Client...which?

## Authlete - 09/18/2018
Research into using Authlete has been put on hold for the same reason as Okta.

## Apigee - 09/18/2018
Research into using Apigee has been put on hold since they do not support RFC 7523 at the access token request endpoint.  Instead, if the client wanted to assert their idenetity with a JWT, that client must excange the JWT for an opaque token to then use at the access token request endpoint. This is extra steps I'm not interested in exploring right now.
