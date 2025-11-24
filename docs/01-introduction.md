# 1. Introduction

This document describes the technical standards and the use of
Verifiable Credentials (VCs) and FHIR for exchanging patient and
healthcare provider data between the Nuts network and the LSP (Landelijk
Schakelpunt).\
To ensure secure, interoperable, and policy-compliant communication, all
interaction between the two ecosystems is routed through the **GtK
(Gevalideerd Twiin Knooppunt)**.

The GtK acts as a standardized access interface that separates the
internal architecture of each domain (Nuts or LSP/AORTA) from the
external party requesting or providing data.\
There are **two distinct GtKs** involved:

1.  **GtK-Nuts**\
    -- Provides secure inbound and outbound communication for
    participants in the Nuts ecosystem.\
    -- Enforces authorization rules based on the Verifiable Credentials
    and the underlying identifier/attribute models defined within Nuts.\
    -- Translates external requests into the proper internal Nuts
    protocols and resource formats.

```{=html}
<!-- -->
```
2.  **GtK-AORTA**\
    -- Provides the standardized entry point into the LSP/AORTA
    infrastructure.\
    -- Validates the authentication and authorization metadata (such as
    tokens, signatures, or VCs) according to LSP/AORTA trust
    frameworks.\
    -- Ensures consistent routing to the appropriate LSP services, such
    as medication, professional/patient records, or consent services.

By placing a GtK on each side, both ecosystems can evolve independently
while maintaining a stable, secure interface for cross-domain data
exchange.\
The Nuts--LSP bridge therefore consists of:

-   **A Verifiable Credential--based authorization model**,

-   **FHIR-based clinical data structures**, and

-   **Two GtKs that mediate, validate, and convert the communication
    flows**.

This approach ensures that trust, authorization, and interoperability
remain consistent across the entire end-to-end exchange path, regardless
of which party initiates the communication.

![](media/image1.png){width="6.295833333333333in" height="3.54375in"}

Objectives:

-   Ensure interoperability with existing holders, issuers, and
    verifiers.

-   Avoid creating a proprietary "Nuts--LSP VC format"; align with
    recognized international standards.

-   Prepare for future implementations such as DEZI.
