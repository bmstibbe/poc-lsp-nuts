# 3. Verifiable Credentials and Presentations

## 3.1 Standards and Protocols

| **Domain**         | **Standard**                   | **Description** |
|-------------------|--------------------------------|----------------|
| VC Data Model      | W3C Verifiable Credentials Data Model v2.0 | Defines structure, semantics, and lifecycle of credentials. [Link](https://www.w3.org/TR/vc-data-model/) |
| JWT-VC Representation | W3C JWT-VC                  | JWT-based Verifiable Credential representation defined by W3C VC Data Model v1.1 (03-03-2022). [Link](https://www.w3.org/TR/vc-jwt/) |
| Issuance Protocol  | OpenID for Verifiable Credential Issuance (OID4VCI) | Credential request and issuance (issuer ↔ holder/wallet) |
| Presentation Protocol | W3C JWT-VP                   | Credential presentation from holder to verifier. [Link](https://www.w3.org/TR/vp-jwt/) |
| Digital Signature  | RFC 7515 (JWS) / RFC 7518 (algorithms) | Cryptographic signing of the JWT payload (RS256/ES256) |

## 3.2 JWT-Based Verifiable Credentials and Presentations — Fields, Meaning, and Example

A W3C **JWT-based Verifiable Credential (JWT-VC)** is a Verifiable Credential represented as a signed JSON Web Token (JWT) using the W3C VC Data Model v2.0 and JWS-based Data Integrity proof method.  

Unlike SD-JWT VC, the W3C JWT-VC format does **not** support selective disclosure. All credential attributes are included directly within the JWT.

A JWT-VC consists of:

1. **Standard JWS header** (alg, typ, kid)  
2. **Standard JWT payload** (issuer, subject, timestamps, vc object)  
3. **JWS signature** using the issuer's key  

### 3.2.1 JWT Header (JWS)

| Field | Meaning | Example / Comment |
|-------|---------|-----------------|
| alg   | Cryptographic algorithm used to sign JWT | "RS256" |
| typ   | Type of token | "JWT" |
| kid   | Key identifier for signing key | "did:x509:0:sha256:WE4P5dd8DnLH...subject:O:OLVG%20Oost::san:otherName:2.16.528.1.1003.1.3.5.5.5:1-987654321-S-12345678-00.000-00000000" |
| x5c   | Inline certificate chain for key validation (base64 DER) | Used in X.509-based VCs |

**Example JSON Header:**
```json
{
  "alg": "RS256",
  "typ": "JWT",
  "x5c": [
    "MIID...<base64 DER leaf certificate>...",
    "MIID...<base64 DER intermediate CA>...",
    "MIID...<base64 DER root CA>..."
  ],
  "kid": "did:x509:0:sha256:WE4P5dd8DnLH...subject:O:OLVG%20Oost::san:otherName:2.16.528.1.1003.1.3.5.5.5:1-987654321-S-12345678-00.000-00000000"
}
```

The JWT Header contains the X.509 certificate chain and uses a SHA-256
thumbprint as the Key ID of the leaf certificate.

### 3.2.2 JWT Payload Fields for VC\'s (W3C JWT-VC)

Below is a complete table of the **minimal used payload fields** in a
W3C JWT-VC, including required and optional fields.

**JWT Payload Field Table**

| Field   | Meaning                                             | Example / Comment |
|---------|----------------------------------------------------|-----------------|
| `iss`   | Identifier of the issuer (usually a DID)          | `"iss": "did:<method>:xxxxx"` |
| `sub`   | Identifier of the subject to whom the credential refers | `"sub": "did:web:healthcare.example"` |
| `jti`   | Unique identifier for the credential (JWT ID)     | `"jti": "urn:uuid:123e4567-e89b-12d3-a456-426614174000"` |
| `iat`   | Issued-at timestamp (seconds since epoch)         | `"iat": 1730876100"` |
| `nbf`   | Not-before timestamp; credential is valid from this moment | `"nbf": 1730876100"` |
| `exp`   | Expiration timestamp; credential should not be accepted after this | `"exp": 1762412100"` |
| `vc`    | Verifiable Credential object including W3C fields (`@context`, `type`, `credentialSubject`) | See structure below |

---



### 3.2.3 The VC Object Structure (W3C-Compliant)

The vc object always contains:

| Field               | Meaning                                                         | Example / Comment |
|--------------------|-----------------------------------------------------------------|-----------------|
| `@context`         | Defines the semantics and vocabulary for interpreting the credential | `["@context": "https://www.w3.org/ns/credentials/v2"]` |
| `type`             | Array defining the credential type; must include `VerifiableCredential` | `["VerifiableCredential", "HealthcareOrganizationCredential"]` |
| `credentialSubject`| Domain-specific attributes of the subject. No sensitive identifiers such as BSN | `{ "organizationName": "OLVG", "uraNumber": "12345678", "authorizationRole": "practitioner" }` |

---


### 3.2.4 Media type

JWT-based Verifiable Credentials use the media type application/jwt.\
Implementations and transport layers SHOULD use this content type when
transmitting a JWT VC directly.

When a JWT VC is transported using **OAuth 2.0 mechanisms** (e.g., Token
Exchange or POST requests to a token endpoint), the transport protocol
**requires** the use of:

Content-Type: application/x-www-form-urlencoded

This requirement originates from OAuth 2.0, not from the VC-JWT
specification itself.

### 3.2.5 Verification steps (high level)

When a verifier receives a JWT-based Verifiable Credential or Verifiable
Presentation, it typically **SHOULD**:

1.  **Verify the JWS signature**\
    Check the alg header and validate the signature using the issuer's
    public key and/or certificate chain.

2.  **Parse the JWT payload**\
    Extract claims, including the embedded vc or vp object.

3.  **Validate the credential type and metadata**\
    Using the type field and the contents of the vc object, verify the
    credential type, mandatory domain-specific claims, claim semantics,
    and any additional validation rules defined by the credential's
    schema or trust framework.

## 3.3 JWT-VC Examples

For the first two Verifiable Credentials, the issuer is derived from a
certificate issued by the UZI Register PKIoverheid.

-   In the context of Verifiable Credential issuance, the **issuer (iss)
    represents the leaf certificate** that was issued to the healthcare
    provider or organization.

-   The **subject (sub) refers to the holder's certificate or
    identity**, typically a healthcare professional registered in the
    UZI register.

-   Trust is anchored in the **UZI PKI trust chain**: although the iss
    claim points to the leaf certificate, the credential's authenticity
    is verified via the root and intermediate CAs, ensuring
    authoritative trust.

**Key point:**\
iss = leaf certificate (the entity issuing the VC)\
sub = the credential subject (the healthcare provider)\
Verification still relies on the **full PKI chain** back to the UZI
trust anchor.

### 3.3.1 HealthcareOrganizationVC (W3C JWT-VC)

**Attributes**

-   id --- DID of the healthcare organization

-   subject:O --- organization name

-   healthcraeOrganizationId - A unique identifier assigned to a
    healthcare organization, typically based on the national URA used in
    the Dutch healthcare system for organizational identification.

-   sanOtherName --- derived from UZI Private Server Certificate

```json
{
  "iss": "did:x509:0:sha256:WE4P5dd8DnLHSkyHaIjhp...::subject:O:Healthcare%20Example::san:otherName:2.16.528.1.1003.1.3.5.5.5:1-987654321-S-12345678-00.000-00000000",
  "sub": "did:web:healthcare.example",
  "iat": 1731379200,
  "nbf": 1731379200,
  "exp": 1762915200,
  "jti": "urn:uuid:0d1600bb-4e9b-4d89-b77f-002ff1c1a9ad",
  "vc": {
    "@context": ["https://www.w3.org/2018/credentials/v1"],
    "type": ["VerifiableCredential", "X509Credential"],
    "credentialSubject": {
      "id": "did:web:healthcare.example",
      "organizationName": "Healthcare Example",
      "healthcareOrganizationId": "12345678",
      "san:otherName": "2.16.528.1.1003.1.3.5.5.5:1-987654321-S-12345678-00.000-00000000"
    }
  }
}

**Note**: \"san:otherName\"

This refers to the SubjectAltName.OtherName attribute, which carries
specific meaning in the healthcare sector. Below is how the
subjectAltName.otherName is structured in certificates issued by the UZI
register.

PKIoverheid specifies a subjectAltName.otherName using an OID-like
structure: \<OID_CA\>:\<Subject_ID\>

-   \<OID_CA\> and \<Subject_ID\> are separated by a hyphen (:).

-   \<OID_CA\> represents the OID of the issuing CA, following the
    structure \<PKIoverheid\>.\<Domain\>.\<TSP\>.\<CA\>.

    -   For the UZI Register 'Zorgverlener' CA (the mandator), this is:
        **2.16.528.1.1003.1.3.5.5.2**

    -   For the UZI Register 'Medewerker op Naam' CA, this is:
        **2.16.528.1.1003.1.3.5.5.3**

    -   For the UZI Register Server CA, this is:
        **2.16.528.1.1003.1.3.5.5.5**

```{=html}
<!-- -->
```
-   \<Subject_ID\> is a unique identifier within the TSP domain. The UZI
    register defines it to include multiple numbers that have meaning
    within the healthcare sector and uniquely identify the subject as a
    healthcare professional within a given subscriber.

**SubjectAltName.otherName value** (\<Subject_ID\>):\
The \<Subject_ID\> in the UZI register is a composite field, with
components separated by hyphens:

\<Subject_ID\> =
\<version\>-\<UZI_number\>-\<pas_type\>-\<subscriber_number\>-\<role\>-\<AGB_code\>

Example: **1-987654321-S-12345678-00.000-00000000**

The HealthcareOrganizationVC is signed with the UZI-register Private
Server certificate of the Healthcare Provider (UZI-register Private
Server CA G1) with algorithm: RSA-256. The issuer (a DID based on the
CA) and the signer are linked through the leaf certificate.

### 3.3.2 PatientEnrollmentVC (W3C JWT-VC)

**Attributes**

-   id --- did:uuid:33b859cb-1f68-4051-b767-246b9fc2e129 -- The unique
    identifier of the subject (the patient) in a pseudonymous form. This
    DID represents the patient and is used to link credentials without
    exposing sensitive personal identifiers.

-   patientId - The Dutch Citizen Service Number (BSN) of the patient.
    This is sensitive personal data and should only be included in the
    VC payload for point-to-point verification, never as a publicly
    resolvable identifier.

-   verification - An object containing details about how the patient
    identity has been verified. This includes information about the
    verifying organization, the method, evidence, and timestamp of
    verification

-   registeredBy - The identifier of the healthcare organization that
    performed or authorized the verification. Typically a URA or other
    official organization ID.

-   verifiedAt - The timestamp when the patient verification was
    completed.

-   method - The procedure used to verify the patient's identity

-   evidence - The evidence presented during verification, such as a
    passport or national ID card.

-   sanOtherName --- derived from UZI-register Zorgverlener Certificate

{

\"iss\":
\"did:x509:0:sha256:WE4P5dd8DnLHSkyHaIjhp4udlkF9LqoKwCvu9gl38jk::subject:O:
Zorginstelling%20Example::san:otherName:2.16.528.1.1003.1.3.5.5.3:1-886654321-M-12345678-00.000-00000000\",

\"sub\": \"did:uuid:33b859cb-1f68-4051-b767-246b9fc2e129\",

\"iat\": 1730876000,

\"nbf\": 1730876100,

\"exp\": 1762412100,

\"jti\": \"urn:uuid:32fb679f-1875-4a78-b8be-27e13f40ef49\",

\"vc\": {

\"@context\": \[

\"<https://www.w3.org/ns/credentials/v1>\"

\],

\"type\": \[

\"VerifiableCredential\",

\"X509Credential\"

\],

\"credentialSubject\": {

\"id\": \"did:uuid:33b859cb-1f68-4051-b767-246b9fc2e129\",

\"patientId\": \" bsn:87654321\",

\"verification\": {

\"registeredBy\": {

\"healthcareOrganizationId\": \"12345678\"

},

\"verifiedAt\": \"2025-11-20T09:52:12Z\",

\"method\": \"in-person-identity-check\",

\"evidence\": \"passport-or-national-id-card\",

\"san:otherName\": \"2.16.528.1.1003.1.3.5.5.5:1-
886654321-M-12345678-00.000-00000000\"

}

}

}

}

The PatientEnrollmentVC is signed with the UZI-Pas certificate of the
Healthcare Provider (UZI-register Zorgverlener/Medewerker op naam CA G3)
with algorithm: RSA-256. The issuer (a DID based on the CA) and the
signer are linked through the leaf certificate.

### MandateVC (W3C JWT-VC)

**Attributes**

-   id --- The unique identifier of the subject for this credential. In
    this case, it represents the system, organizational unit, or entity
    receiving the authorization rule (the \"grantee\").

-   autorisatieregel::context --- The context or domain in which the
    authorization rule applies. In this example, it refers to a
    medication-related access context.

-   grantedBy --- An object describing who issued or authorized the
    permission.

-   personId - DID of the healthcare professional who granted the
    authorization, typically linked to a UZI-pass or employee
    certificate.

-   san:otherName - A structured, internal identifier derived from the
    UZI-register or certificate.

{

\"iss\":
\"did:x509:0:sha256:WE4P5dd8DnLHSkyHaIjhp4udlkF9LqoKwCvu9gl38jk::subject:O:
Zorginstelling%20Example::san:otherName:2.16.528.1.1003.1.3.5.5.3:1-882354372-Z-12345678-01.015-00000000\",

\"sub\": \"did:web:gtk.1234\",

\"iat\": 1541493724,

\"nbf\": 1541493724,

\"exp\": 1573029723,

\"jti\": \"urn:uuid:9877e792-d51b-46a1-9d37-c748e531c57a\",

\"vc\": {

\"@context\": \[

\"https://www.w3.org/ns/credentials/v1\"

\],

\"type\": \[

\"VerifiableCredential\",

\"X509Credential\"

\],

\"credentialSubject\": {

\"id\": \"did:web:gtk.1234\",

\"autorisatieregel::context\": \[

\"https://goedbeheerdziekenhuis/autorisatieregels/medicatiecontext/v2\"

\],

\"grantedBy\": {

\"personId\": \"did:uzi:882354372\",

\"roleCode\": \"01.015\",

\"uraNumber\": \"12345678\"

},

\"san:otherName\":
\"2.16.528.1.1003.1.3.5.5.5:1-882354372-Z-12345678-01.015-00000000\"

}

}

}

The MandateVC is signed with the UZI-Pas certificate of the Healthcare
Provider (UZI-Pas Zorgverlener CA G3) with algorithm: RSA-256.

**Notes**

-   **Audience** is not needed, because the subject equals the holder.

-   The cnf **claim** can optionally bind the credential
    cryptographically to the holder.

-   All timestamps (iat, nbf, exp) and jti should always be included in
    production credentials for validity and uniqueness.

## 3.4 Standard VP Claims

A W3C JWT Verifiable Presentation (JWT-VP) is a cryptographically signed
object that presents one or more Verifiable Credentials (VCs) to a
verifier. Using JWT allows standard cryptographic validation.

  -----------------------------------------------------------------------------------
  **Field**   **Meaning**                                  **Example / Comment**
  ----------- -------------------------------------------- --------------------------
  iss         Issuer of the VP; usually the holder         \"did:web:gtk.1234\"
              presenting the credentials.                  

  aud         Audience; the verifier intended to receive   \"did:web:gtk:lsp:1235\"
              the VP.                                      

  iat         Issued At; timestamp when VP was created.    1700300000

  nbf         Not Before; VP should not be considered      1700300000
              valid before this time.                      

  exp         Expiration; optional but recommended to      1700386400
              limit validity.                              

  jti         JWT ID; unique identifier for the VP.        \"vp-12345\"
  -----------------------------------------------------------------------------------

### 3.4.1 VP-specific Claims

  -----------------------------------------------------------------------------------------------------------
  **Field**                 **Meaning**                    **Example / Comment**
  ------------------------- ------------------------------ --------------------------------------------------
  vp                        Main object containing the     { \"type\": \[\"VerifiablePresentation\"\],
                            presentation data.             \"verifiableCredential\": \[\...\] }

  vp.type                   Type of the presentation       \[\"VerifiablePresentation\"\]
                            (always includes               
                            \"VerifiablePresentation\").   

  vp.verifiableCredential   Array of embedded VCs(JWT-VCs) \[\"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9\...\"\]

  nonce                     Used to prevent replay attacks \"1234567890\"
                            in some protocols.             
  -----------------------------------------------------------------------------------------------------------

## 3.5 JWT-VP Example

{

\"iss\": \"did:web:gtk.1234\",

\"jti\": \"urn:uuid:3978344f-8596-4c3a-a978-8fcaba3903c5\",

\"aud\": \"http://as.verifier.example\",

\"nbf\": 1541493724,

\"iat\": 1541493724,

\"exp\": 1573029723,

\"nonce\": \"343s\$FSFDa-\",

\"vp\": {

\"type\": \[\"VerifiablePresentation\"\],

\"verifiableCredential\": \[

\"base64(HealthcareOrganizationVC)\",

\"base64(PatientEnrollmentVC)\",

\"base64(MandateVC)\"

\]

}

}

The VP is signed with the web certificate of ZORG-ID with algorithm:
RSA-256.

## 3.6 Credential Issuance Flow (OIDC4VCI)

The issuance of **Verifiable Credentials (VCs)** follows the **OpenID
for Verifiable Credential Issuance (OIDC4VCI, draft v1.0)** standard,
which is an extension of the **OpenID Connect** and **OAuth 2.0**
protocols.

This standard defines how an **issuer** can issue a credential to a
**holder** or **wallet**, using verifiable identities (such as **DIDs**
or certificates) and a secure token exchange.

The **OIDC4VCI flow** consists of three main phases:

1.  **Issuer Discovery**

2.  **Token Request** (OAuth 2.0 Client Credentials Flow)

    a.  **Not required for machine-to-machine flows**, as the client is
        already authenticated.

3.  **Verifiable Credential Request**

### 3.6.1 Issuer Discovery

The issuer publishes a /.well-known/openid-credential-issuer document.\
This document specifies:

-   Which types of credentials are supported

-   Which cryptographic algorithms are used

-   How a client (GTK or healthcare provider) can request a credential

Example:

{

\"credential_issuer\": \"https://issuer.zorgid.example\",

\"credentials_supported\": \[

{

\"id\": \"HealthcareOrganizationVC\",

\"format\": \"jwt_vc_json\",

\"cryptographic_binding_methods_supported\": \[\"did:x509\", \"x5c\"\],

\"cryptographic_suites_supported\": \[\"RS256\", \"ES256\"\]

},

{

\"id\": \"PatientEnrollmentVC\",

\"format\": \"jwt_vc_json\",

\"cryptographic_binding_methods_supported\": \[\"did:x509\", \"x5c\"\],

\"cryptographic_suites_supported\": \[\"RS256\", \"ES256\"\]

},

{

\"id\": \"MandateVC\",

\"format\": \"jwt_vc_json\",

\"cryptographic_binding_methods_supported\": \[\"did:x509\", \"x5c\"\],

\"cryptographic_suites_supported\": \[\"RS256\", \"ES256\"\]

}

\]

}

**Key points:**

-   credential_issuer points to the same endpoint
    (https://issuer.zorgid.example) for both VC types.

-   Each entry in credentials_supported specifies **a distinct VC type**
    along with its format and supported crypto methods.

-   Clients (GTKs or healthcare providers) can query the discovery
    endpoint to see which VC types the issuer can provide.

This aligns with **OIDC4VCI discovery rules**, where an issuer can
support multiple credential types under the same issuer URL.

### 3.6.2 Verifiable Credential Request

POST /credential/request HTTP/1.1

Host: issuer.zorgid.example

Content-Type: application/json

Authorization: Bearer \<access_token\>

{

\"type\": \"urn:nl:vc:\<XYZ\>VC\",

\"format\": \"jwt_vc_json\",

\"credentialSubject\": {

\"id\": \"did:xyz:234567821\",

...

}

}

**Notes**

-   Authorization header contains an OAuth 2.0 access token obtained via
    the **Client Credentials Flow** or wallet authentication.

-   cnf.jwk_thumbprint binds the VC to the holder's public key, ensuring
    **only the intended holder can present it**.

-   credentialSubject.id is always the DID of the entity that the VC
    represents (organization, professional, patient, or GTK).

-   The type field corresponds exactly to the VC type in the issuer's
    discovery document.
