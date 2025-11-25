# 7. Overview of VCs within the PoC

| VC-type                   | Issuer                              | Subject                        | Key Attributes                                   | VP Holder    |
|----------------------------|-------------------------------------|--------------------------------|-------------------------------------------------|-------------|
| PatientEnrollmentVC        | did:x509:UZI-register-Medewerker    | Pseudonym for Patiënt (did:uuid) | BSN, name, birthDate, enrolledBy, enrolledAt, registrationDate, SubjectAltName.OtherName | did:web:gtk |
| MandateVC                  | did:x509:UZI-register-Zorgverlener  | GTK (did:web:gtk)              | MandateScope, grantedBy, grantedTo, validFrom/validUntil, SubjectAltName.OtherName       | did:web:gtk |
| HealthcareOrganizationVC   | did:x509:UZI-register-Private-Server | Healthcare Organization (did:web:zorginstelling) | URA, organization name, SubjectAltName.OtherName | did:web:gtk |

**Notes:**

- Holder for Verifiable Presentations = GTK.
- TransactionVC is not used as a VC; it is only a label for messages.
- Subject and holder do not need to be the same.

