# 2. Context: Twiin as Framework

-   Twiin acts as the overarching framework for standards and
    architectural choices (network connections, FHIR R4, GTK
    definitions).

-   GTKs (Validated Twiin Nodes): Concrete implementations under the
    Twiin framework.

-   Twiin provides the framework; standards and protocols derive from
    it.

**Used standards:**

| Domain            | Standard / Technology       | Description |
|------------------|----------------------------|------------|
| Data Exchange     | FHIR R4                    | HL7 FHIR Release 4, basis for healthcare data exchange. |
| Transport & Security | RESTful API, mTLS 1.3, OAuth2.0, NEN7510 | Authentication and authorization for API traffic |
| Identification    | URA, UZI-number, BSN       | Provider and patient identity |
| VC Protocols      | OIDC4VCI                   | Verifiable credential issuance |
| Retrieving Medication Data | MP 9.3 Nictiz: FHIR search for Administration Agreements | The integration in the PoC will initially focus on retrieving instances of medication agreements in accordance with the Nictiz MP 9.3 information standard. Format: GET `[base]/MedicationDispense?category=http://snomed.info/sct|422037009` |

