# 7. Overview of VCs within the PoC

  -------------------------------------------------------------------------------------------------------------------------------------
  **VC-type**                **Issuer**                             **Subject**                **Key Attributes**         **VP Holder**
  -------------------------- -------------------------------------- -------------------------- -------------------------- -------------
  PatientEnrollmentVC        did:x509:UZI-register-Medewerker       Pseudonim for Patiënt      BSN, name, birthDate,      did:web:gtk
                                                                    (did:uuid)                 enrolledBy, enrolledAt,    
                                                                                               registrationDate,          
                                                                                               SubjectAltName.OtherName   

  MandateVC                  did:x509:UZI-register-Zorgverlener     GTK (did:web:gtk)          MadateScope, grantedBy,    did:web:gtk
                                                                                               grantedTo,                 
                                                                                               validFrom/validUntil,      
                                                                                               SubjectAltName.OtherName   

  HealthcareOrganizationVC   did:x509:UZI-register-Private-Server   Healthcare Organization    URA, organization name,    did:web:gtk
                                                                    (did:web:zorginstelling)   SubjectAltName.OtherName   
  -------------------------------------------------------------------------------------------------------------------------------------

**Notes:**

-   Holder for Verifiable Presentations = GTK.

-   TransactionVC is not used as a VC; it is only a label for messages.

-   Subject and holder do not need to be the same.
