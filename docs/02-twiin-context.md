# 2. Context: Twiin as Framework

-   Twiin acts as the overarching framework for standards and
    architectural choices (network connections, FHIR R4, GTK
    definitions).

-   GTKs (Validated Twiin Nodes): Concrete implementations under the
    Twiin framework.

-   Twiin provides the framework; standards and protocols derive from
    it.

**Used standards:**

  ----------------------------------------------------------------------------------------------------------
  **Domain**       **Standard/      **Description**
                   Technology**     
  ---------------- ---------------- ------------------------------------------------------------------------
  Data Exchange    FHIR R4          HL7 FHIR Release 4, basis for healthcare data exchange.

  Transport &      RESTful API,     Authentication and authorization for API traffic
  Security         mTLS 1.3,        
                   OAuth2.0,        
                   NEN7510          

  Identification   URA, UZI-number, Provider and patient identity
                   BSN              

  VC Protocols     OIDC4VCI         Verifiable credential issuance

  Retrieving       MP 9.3 Nictiz:   The integration in the PoC will initially focus on retrieving instances
  Medication Data  FHIR search for  of medication agreements in accordance with the Nictiz MP 9.3
                   Administration   information standard. Format:\
                   Agreements       GET
                                    \[base\]/MedicationDispense?category=http://snomed.info/sct\|422037009
  ----------------------------------------------------------------------------------------------------------
