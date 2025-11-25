# 4. OAuth 2.0 Token Exchange

This is the **IETF / OAuth standard** that allows a client to submit an
external token (like a JWT Verifiable Presentation) to the token
endpoint and receive an **Access Token** in return.

## 4.1 Requesting an Access Token using a VP

-   Any "security token" can be submitted.

-   A **JWT-VP** is technically a JWT, so it can be used here.

**Important:** Both examples of RFC 7523 and RFC 8693 require that the
JWT (whether it is an assertion, VC, or VP) is always included in the
**HTTP POST body**, encoded as application/x-www-form-urlencoded.\
It is **not** placed in an HTTP header.

## 4.2 Parameter subject_token

-   The VP (in JWT format) can be placed here as the subject_token.

-   To stay aligned with the TA NP, we choose RFC 7523, where a single
    assertion (a statement expressed as a JWT) is included as a
    parameter when requesting an access token.

Example request from RFC 7523 --- JWT Bearer Token Request:

```http
POST /token
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer&assertion=JWT&client_id=GtK-id
```

*Formally, RFC 7523 does **not** explicitly support exchanging arbitrary
token types (such as VCs and VPs). RFC 8693 does allow this.*

Example request from RFC 8693:

```http
POST /token
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:token-exchange&subject_token=JWT&subject_token_type=urn:ietf:params:oauth:token-type:jwt
```

Replace \<JWT\> with a JWT-encoded Verifiable Presentation.

## 4.3 Successful Response

-   If the submitted token (VP) is accepted, the server returns an
    **Access Token**.
