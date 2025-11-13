# 2. Context: Twiin as Framework
- **Twiin:** Framework for standards and architectural choices (network connections, FHIR R4, GTK definitions).  
- **GTKs (Validated Twiin Nodes):** Concrete implementations.  
- Twiin defines the framework; standards and protocols follow.

| Domain | Standard / Technology | Description |
|--------|---------------------|-------------|
| Data Model | FHIR R4 | HL7 Release 4, basis for healthcare data exchange |
| Transport & Security | FHIR REST API, mTLS 1.3, OAuth2.0, NEN7510 | Authentication and authorization for API traffic |
| Identification | URA, UZI number, eIDAS | Provider and patient identity |
| VC Protocols | OIDC4VCI, OID4VP | Verifiable credential issuance and presentation |