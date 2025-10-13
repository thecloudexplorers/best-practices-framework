# Sjabloon voor Architectuur Definitie Document

> Documentstructuur en leeswijzer:
>
> - Hoofdstukken 1-3: Bedrijfscontext & vereisten
> - Hoofdstukken 4-6: Architectuur & beveiliging
> - Hoofdstukken 7-10: Oplevering & operatie
>
> Pas voor elk hoofdstuk en subhoofdstuk vanaf hoofdstuk 4 het beleid **“Indien van toepassing”**
> toe. Als een hoofdstuk of subhoofdstuk niet van toepassing is, vermeld dit dan in
> **Bijlage 1: Lijst niet van toepassing**. Na vermelding kan het hoofdstuk of subhoofdstuk worden
> verwijderd om zo de leesbaarheid te verbeteren.
>
> Voor alle hoofdstukken vanaf hoofdstuk 4: **definieer of verwijs naar de aanpak**. Om een
> **single source of truth** te behouden, moedigt dit sjabloon aan om te verwijzen naar bestaande
> documenten of processen.
>
> Voorbeelden:
>
> - **Architectuurprincipes** – Verwijs naar het architectuurbeleid van de organisatie en de
> referentiearchitectuur.
> - **Teststrategie** – Verwijs naar het testbeleid van de organisatie.
> - **Security & Compliance** – Verwijs naar het beveiligingsbeleid van de organisatie.
>
> **OPMERKING:** Het is de verantwoordelijkheid van de auteur om ervoor te zorgen dat alle
> verwijzingen actueel **en** toegankelijk zijn voor de doelgroep van dit document.

---

## 1. Managementsamenvatting

> Beschrijf het doel, de scope en de verwachte resultaten. Vermeld waarde, kosten/baten,
> belangrijkste risico's en de gevraagde beslissingen (go/no‑go, budget, tijdlijn).
> Houd dit tot maximaal één pagina.

## 2. Stakeholders, scope en aannames

> Leg vast wie betrokken is en wat hun belangen zijn. Definieer wat binnen scope en buiten scope
> valt, afhankelijkheden, en expliciete aannames en beperkingen (inclusief hoe/wanneer u deze zult
> valideren). Voeg een korte RACI of eigenaar per onderwerp toe.

### 2.1 Stakeholders

> Identificeer wie beslist, wie verantwoordelijk is voor gegevens en risico, wie het geheel uitvoert
> en wie de architectuurstandaarden bewaakt. Leg per stakeholder vast:
>
> - Rol & Naam/Organisatie (wie)
> - Verantwoordelijkheid (wat zij bezitten; gebruik R/A/C/I indien nuttig)
> - Belangrijke beslissingen (waarvoor zij moeten tekenen)
> - Benodigde input (wat u van hen nodig hebt om verder te gaan)
> - Stage‑gate‑goedkeuringen (bijv. HLD‑goedkeuring, Security Design Review, Go‑live)
>
> Houd het kort. Als iemand het project kan blokkeren (CISO, DPO, Architectuurboard), moeten zij
> hier worden opgenomen. Tip: de taken van de DPO komen uit artikel 39 van de AVG; de CISO zorgt
> doorgaans voor BIO‑afstemming en ondertekent risicobeoordelingen.
>
> | Rol                   | Naam / Organisatie         | Verantwoordelijkheid (R/A/C/I)                | Belangrijke beslissingen                                             | Benodigde input                                | Stage‑gate‑goedkeuringen                                                       |
> | ---------------------- | --------------------------- | -------------------------------------------- | --------------------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------ |
> | **Business Owner**     | {Jane Doe}                  | **A** voor zakelijke uitkomsten & budget    | Scope, financiering, service‑KPI’s                              | Businesscase, doel‑KPI’s, budgetlimieten      | HLD‑goedkeuring, Go‑live                                                      |
> | **Data Owner**         | {Afdelingshoofd Sociale Diensten} | **A** voor rechtmatige verwerking & retentie | Gegevenscategorieën, retentie, delen                       | Gegevensclassificatie, DPIA‑input             | HLD‑gegevenssectie, DPIA‑goedkeuring                                            |
> | **CISO**               | {CISO‑naam}                 | **A** voor informatie‑risico (BIO)        | Rest‑risico accepteren; crypto‑ & netwerkpostuur           | Dreigingsmodel, koppeling van controls aan BIO        | Security Design Review, Go‑live risicobeoordeling. ([Digital Government][1]) |

### 2.2 Scope

> Beschrijf wat er in scope en uit scope is per domein, processen, systemen, data en interfaces.
> Wees expliciet om scope creep te voorkomen.
>
> Neem vijf subsecties op:
>
> - Domeinen (bijv. burgerdossierafhandeling, vergunningen)
> - Bedrijfsprocessen (bijv. “Aanvraag registreren”, “Beoordelen”, “Beslissen”, “Informeren”)
> - Systemen (nieuw en bestaand; Azure en niet‑Azure)
> - Gegevens & Integraties (PII‑categorieën, Digikoppeling/eHerkenning/DigiD, BRP, enz.)
> - Omgevingen (Dev/Test/Acc/Prod), regio’s en tenancy (Landing Zone/subscripties)
>
> Scopebeschrijving (één alinea):
> "Dit project levert een digitaal casemanagement‑systeem gehost op Azure binnen de Landing Zone van
> de gemeente, dat REST‑API’s blootstelt aan interne systemen en de BRP consumeert via
> Digikoppeling. De productie draait in West‑Europa met North Europe als Disaster Recovery."
>
> *Voorbeeld:*
>
> | Categorie                 | In scope                                                                                                                   | Buiten scope                                |
> | ------------------------ | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
> | **Domeinen**              | Intake & tracking van sociale‑dienstendossiers                                                                                     | Backoffice‑uitbetaling van financiële steun      |
> | **Processen**            | Intake, verificatie, beslissing, notificatie                                                                               | Bezwaar‑ en beroepsprocedure           |
> | **Systemen (Azure)**      | App Service (API/UI), Azure SQL, Key Vault, Application Gateway (WAF), API Management, Log Analytics, Monitor, Service Bus | AKS (geen containers in versie 1)                   |
> | **Systemen (Extern)**   | BRP‑lookup via Digikoppeling; gemeentelijke SSO via Entra ID / DigiD‑broker                                                    | Legacy documentarchief (alleen lezen via URL) |
> | **Gegevens**                 | PII: naam, adres, BSN (geminimaliseerd), casusmetadata                                                                         | Gescande documenten OCR (toekomstfase)        |
> | **Interfaces**           | REST `/api/v1/cases` (intern), SOAP (BRP via Digikoppeling)                                                              | Batch‑export naar datawarehouse             |
> | **Omgevingen/Regio’s** | Dev/Test/Acc/Prod in West‑Europa; DR in North Europe                                                                       | On‑prem DR                                  |
> | **Tenancy**              | Gemeentelijke tenant; productiesubscriptie in Landing Zone                                                               | Partnertenant‑hosting                      |

### 2.3 Aannames

> Schrijf toetsbare aannames die, indien onjuist, kosten, tijd of ontwerp veranderen. Elke aanname
> bevat:
>
> - Verklaring (duidelijk + toetsbaar)
> - Categorie (cloudkeuze, budget, tijdlijn, leverancier, beleid/standaard)
> - Bewijs / bron (wie het zei / link naar document)
> - Eigenaar (wie valideert) & uiterste datum
> - Risico indien onjuist (impact)
> - Mitigatie / Plan B (wat u zult doen)
> - Status (Ongecontroleerd / Gecontroleerd / Weerlegd → wordt risico/probleem)
> - Stem cloud‑/architectuuraannames af op CAF en WAF zodat reviewers weten dat u Microsoft's
> baseline guidance gebruikt.
>
> *Voorbeeld:*
>
> | ID   | Aanname (toetsbaar)                                                                                            | Categorie        | Bewijs / bron                                 | Eigenaar            | Valideren voor | Risico indien onjuist                            | Mitigatie / Plan B                                                    | Status                                     |
> | ---- | ---------------------------------------------------------------------------------------------------------------- | --------------- | --------------------------------------------- | ---------------- | ------------- | ---------------------------------------- | ---------------------------------------------------------------------- | ------------------------------------------ |
> | A‑01 | **Azure Landing Zone (beleid, RBAC, netwerken) is beschikbaar bij projectstart** en voldoet aan de basis‑CAF‑richtlijnen. | Cloudkeuze    | EA zegt "klaar" in kickoff; CAF‑landingzone‑documentatie | Platformeigenaar   | 2025‑10‑15  | Vertraging 4–6 weken; herwerken netwerk & beleid | Minimale landing zone opzetten via Bicep in projectsubscription; time‑box 2 weken | Ongecontroleerd. ([Microsoft Learn][1])         |
> | A‑02 | **Digikoppeling‑connectiviteit met BRP** is beschikbaar met geldige PKIoverheid‑certificaten en mTLS.                        | Leverancier/Standaard | Integratieteamplan; Forum Standaardisatie      | Integratieleider | 2025‑10‑22  | Geen burgers‑lookup; UAT geblokkeerd           | BRP mocken; overschakelen naar uitgestelde verificatieworkflow                      | Ongecontroleerd. ([forumstandaardisatie.nl][2]) |
> | A‑03 | **DPO levert goedgekeurde gegevensclassificatie & DPIA‑uitkomst** vóór afronding van de ontwikkeling.                                | Beleid/AVG     | DPO‑e‑mail; taken volgens art. 39 AVG                    | DPO              | 2025‑11‑01  | Gegevensmodel herwerken; go‑live‑risico          | Anonieme testdata gebruiken; PII‑functionaliteit bevriezen                          | Ongecontroleerd. ([AVG][3])                    |

[1]: https://www.nldigitalgovernment.nl/overview/information-security__trashed/government-information-security-baseline/?utm_source=chatgpt.com "Government information security baseline"
[2]: https://www.forumstandaardisatie.nl/open-standaarden/digikoppeling?utm_source=chatgpt.com "Digikoppeling"
[3]: https://gdpr-info.eu/art-39-gdpr/?utm_source=chatgpt.com "Art. 39 GDPR – Tasks of the data protection officer"

## 3. Doelen & vereisten

### 3.1 Organisatiedoelen

> Definieer 3 tot 5 **meetbare** doelen (OKR/KPI) met een nulmeting, doelwaarde, deadline en
> meetmethode. Koppel elk doel aan een zakelijke waarde en een eigenaar.

### 3.2 Functionele vereisten (hoog niveau)

> Beschrijf vereisten als **verifieerbare capaciteiten** of **user stories**. Geef per vereiste:
>
> - ID
> - Beschrijving
> - Prioriteit (MoSCoW)
> - Acceptatiecriteria
>
> *Voorbeeld:*
>
> | ID     | Vereiste                                      | Prioriteit          | Acceptatiecriteria                                                                                                                                                 |
> | ------ | --------------------------------------------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
> | FR‑001 | Burgers kunnen online aanvragen indienen en volgen    | Should         | Tracking‑ID binnen 5 s; bevestigings‑e‑mail binnen 2 m; aanvraag binnen 60 s in de personeelswachtrij; auditlog (gebruiker, tijdstempel, IP) wordt vastgelegd. |
> | FR‑002 | Personeelsworkflow met SLA‑timers                   | Must  | SLA‑timer start bij aanmaken case; visuele aftelling; waarschuwing bij 80 % van de SLA en overtredingsalert bij 100 %; dagelijkse rapportage SLA‑naleving per team.         |

### 3.3 Niet‑functionele vereisten (hoog niveau)

> Specificeer **concrete, meetbare** drempel waarden voor beschikbaarheid, prestaties (P95),
> schaalbaarheid, beveiliging, privacy, toegankelijkheid en compliance. Voeg de verificatiemethode
> (meting/test) en minimumnormen (bijv. TLS‑versie) toe.
>
> *Voorbeeld:*
>
> | ID      | Vereiste            | Doel         | Verificatie                                                                                                                                                 |
> | ------- | ---------------------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
> | NFR‑001 | Beschikbaarheid           | ≥ 99,9 %        | Synthetische uptime‑monitoring; maandrapport toont ≥99,9 %; kwartaal failover‑oefening en post‑mortem; Service Level Objectives worden gevolgd. |
> | NFR‑002 | Prestaties (P95 API)  | ≤ 500 ms       | Loadtest (JMeter/Gatling) bij ≥3× verwachte piek gedurende 30 min met P95 ≤500 ms en foutpercentage <1 %; APM‑dashboard‑screenshots toegevoegd aan testrapport.          |
> | NFR‑003 | Schaalbaarheid            | +3× piekbelasting  | Stress‑test tot 3× baseline; autoscale triggert binnen 5 min; queue‑achterstand onder gedefinieerde drempel; geen aanhoudende CPU >80 % gedurende >15 min tijdens de test.          |
> | NFR‑004 | Beveiliging & privacy     | AVG/BIO‑conform | SAST/DAST schoon (geen Hoge/Kritieke bevindingen); penetratietest met 0 hoge bevindingen voor go‑live; DPIA goedgekeurd; TLS 1.2+ afgedwongen; TDE/SSE ingeschakeld; toegangsreviews geslaagd. |

### 3.4 Beperkingen

> Leg harde/blijvende beperkingen vast (cloudkeuze, standaarden, licenties) en aannames met
> validatiepunt en fallback. Koppel elk item aan een risico/mitigatie.
>
> *Voorbeeld:*
>
> | ID      | Beperking      | Opmerkingen                 | Risico/Mitigatie                                                                 |
> | ------- | --------------------------- | --------------------- | -------------------------------------------------------------------------------- |
> | CST‑001 | Cloud: alleen Azure           | Organisatiestandaard | Gebruik containerisatie om lock‑in te verminderen en portabiliteit te behouden waar mogelijk.  |
> | ASM‑001 | Bestaande IdP: Entra ID      | MFA vereist          | Geen back‑up‑IdP gedefinieerd in de huidige cloudstrategie → definieer failover‑auth‑plan.    |

## 4. Architectuur

> **Executive View (waarom):** Doelarchitectuur met duidelijke voordelen (betrouwbaarheid,
> beveiliging, kostenbeheersing, prestaties), gebaseerd op **Azure Landing Zones** en de pijlers
> van **Well‑Architected**.

### Product/component keuzes

> Beschrijf de belangrijkste keuzes (PaaS vs. IaaS, App Service vs. AKS, SQL vs. Cosmos DB,
> Front Door vs. WAF, enz.) en koppel deze aan de vereisten

### 4.1 Context & logische architectuur

> *Diagram‑placeholder:* context en logische componenten (gebruikers, kanalen, web/API/verwerking,
> data, integraties, vertrouwensgrenzen).

### 4.2 Componentcatalogus (overzicht)

> - **Frontends:** Portal (Static Web Apps), medewerkers‑UI (App Service).
> - **API’s & verwerking:** App Service/AKS, Functions, Service Bus.
> - **Data:** Azure SQL/Cosmos DB, Storage (Blob/Files).
> - **Security:** Entra ID, Key Vault, Private Endpoints, WAF.
> - **Platform:** Landing zones (platform‑ vs. applicatiesubscripties), hub‑spoke‑netwerken, beleid
> & RBAC.
>
> **Implementation View (hoe):**\
> **Subscripties & resource‑organisatie:** Management‑groepen → subscripties (Platform versus
> applicatie‑LZ's) → resourcegroepen per workload/lifecycle.\
> **Netwerk:** Hub‑spoke‑VNets, subnets (web/app/data), UDR’s; ingress via App Gateway (WAF), egress
> via Azure Firewall; Private DNS; Private Endpoints voor PaaS.\
> **Compute:** App Service (Linux) met autoscale; AKS waar orkestratie vereist is; Functions voor
> asynchrone taken.\
> **Data:** Azure SQL (TDE, Entra ID‑auth), Cosmos DB (partitioneringsstrategie), Storage
> (lifecycle‑beleid).

## 5. Security & compliance

> **Executive View (waarom):** Risicoreductie en compliance: **BIO** (toegangscontrole, logging,
> continuïteit, cryptografie), **AVG** (gegevensminimalisatie, DPIA/DSR), **Forum Standaardisatie**
> (Digikoppeling, DigiD/eHerkenning).

### 5.1 Identiteit & toegang

> Beschrijf authN/authZ‑flows (Entra ID, DigiD/eHerkenning waar van toepassing), en ga in op:
>
> - Rollen en/of RBAC
> - Privileged Identity Management (PIM)
> - Just‑in‑Time (JIT)
> - Just Enough Administration (JEA)
> - Managed Identities
> - Geheimbeheer (gebruik van Key Vault)

### 5.2 Gegevensbescherming

> Specificeer encryptie tijdens transport/at rest, sleutelbeheer (MMK/CMK, rotatie), datamasking in
> niet‑productie, logging/audit voor gevoelige acties en PII‑beperkingen (gegevensminimalisatie).

### 5.2.1 BIV-classificatie

> Geef de BIV-classificatie (Beschikbaarheid, Integriteit, Vertrouwelijkheid) aan voor het systeem.
> Specificeer eventuele aanvullingen of afwijkingen ten opzichte van de genoemde BIV-classificatie.
> Gebruik onderstaande tabel als referentie.

| Niveau | Vertrouwelijkheid     | Integriteit | Beschikbaarheid |
| ------ | --------------------- | ----------- | --------------- |
| Geen   | Openbaar              | Niet zeker  | Niet nodig      |
| Laag   | Bedrijfsvertrouwelijk | Beschermd   | Noodzakelijk    |
| Midden | Vertrouwelijk         | Hoog        | Belangrijk      |
| Hoog   | Strikt vertrouwelijk  | Absoluut    | Essentieel      |

### 5.3 Netwerkbeveiliging

> Definieer segmentatie (NSG/UDR), expositiepunten (WAF, Front Door, APIM), DDoS‑bescherming,
> egress‑controles, Private Link en IP‑allowlists. WAF, NSG’s, DDoS‑bescherming, Private
> Link/Endpoints; beperk beheer‑paths (VPN/ER, IP‑allowlisting).

### 5.4 Compliance‑mapping (voorbeeld)

| Vereiste                        | Control (samenvatting)                                           |
| ------------------------------- | ---------------------------------------------------------------- |
| BIO – Toegangscontrole          | RBAC least‑privilege, MFA, PIM                                   |
| BIO – Logging & monitoring      | Centrale logs, onveranderlijkheid/retentie, audittrails          |
| AVG – DSR/recht op verwijdering | Geautomatiseerd verwijderen/exporteren, administratieve workflow |
| Forum Standaardisatie           | Digikoppeling voor G2G, DigiD/eHerkenning voor login             |

> **Implementation View (hoe):** Concrete policies (Azure Policy), minimum‑TLS, ciphersuites,
> WAF‑regels, logretentie (bijv. 180 dagen), Defender for Cloud‑plannen, auditconfiguratie.

## 6. Data & integratie

> **Executive View (waarom):** Beschrijf datadomeinen, privacy‑impact en kritieke integraties.

### 6.1 Datamodellen & classificatie

> Bied een ERD/JSON‑model op hoog niveau. Label elke entiteit met classificatie, bron/doelsystemen,
> kwaliteitsvereisten en metadata/herkomst.

### 6.2 Retentie & privacy

> Definieer retentieperioden, juridische basis, archivering, DPIA‑referentie, DSR‑proces
> (toegang/export/verwijderen) en audittrail (wie deed wat, wanneer).

### 6.3 Interfaces & contracten

> Specificeer API‑contracten (OpenAPI), versies en backward‑compatibiliteit,
> SLA/rate‑limiting/throttling, retry/backoff & idempotency, foutcodes, beveiliging
> (mTLS/OAuth/sleutels in Key Vault) en Digikoppeling waar verplicht. Voeg test/acceptatie‑endpoints
> en smoke‑tests toe.

## 7. Oplevering & operatie

> **Executive View (waarom):** Leg uit wat de releasecadans is, operationele KPI’s en
> kostenbeheersing.

### 7.1 DevOps & CI/CD

> Definieer branchingstrategie, PR‑checks, pipeline‑definities (build→test→security→deploy),
> kwaliteitsgates (SAST/SCA/containerscan), artefactopslag, promotieflow en handmatige
> goedkeuringen voor productie.

### 7.2 Infrastructure as Code & omgevingen

> Documenteer de IaC‑structuur (mappen, modules), variabelen/geheimen (Key Vault/variabelen),
> drift‑detectie, beleid als code, tagging/naamgevingsconventies en parity tussen omgevingen
> (Dev/Test/Pre‑prod/Prod).

### 7.3 Observability

> Beschrijf monitoring (App Insights, Log Analytics), dashboards (KPI’s, SLA’s), alerting (drempels,
> escalatie), tracing (gedistribueerd) en logretentie/archivering (security, compliance).
> **Implementation View (hoe):** Pipeline‑YAML, migraties (EF/DACPAC), slot‑swap/blue‑green/canary;
> alertrules (drempels), runbooks (incident, certificaatrotatie, sleutelverval).

## 8. Beschikbaarheid, DR & backup

> Definieer RTO/RPO en beschrijf HA‑ontwerp (AZ‑redundantie, minimaal 2 exemplaren), DR‑topologie
> (active‑passive/active‑active), failover/fallback‑proces, oefenkalender en backupstrategie (scope,
> retentie, encryptie en herstelprocedures). Wijs eigenaarschap toe.
>
> *Voorbeeld:*
>
> - **HA:** ≥2 instanties voor kritieke diensten; AZ‑redundantie.
> - **DR:** Secundaire regio (active‑passive), SQL geo‑replicatie; **RTO/RPO**‑doelen;
> failover‑playbooks en oefeningen.
> - **Back‑ups:** Geautomatiseerde DB‑back‑ups, Recovery Services Vault; periodieke restore‑tests.

## 9. Teststrategie

> Definieer de testpiramide en dekking voor FR/NFR. Beschrijf beleid voor testdata
> (geanonimiseerd/synthetisch), omgevingen, tools, performance/soak/chaos‑tests, securitytests
> (SAST/DAST/pen‑test) en exitcriteria (releasegate). Leg rapportage & traceerbaarheid vast
> (vereiste → test).
>
> - **Unit/Contract/Integratie/E2E**
> - **Performance/Soak/Chaos**
> - **Security:** Dreigingsmodellering, pen‑testcadans, dependency‑ & containerscans.

## 10. Risico’s & besluiten

> Onderhoud en verwijs naar een risicoregister (impact, waarschijnlijkheid, mitigatie, eigenaar,
> streefdatum) en open issues/afhankelijkheden. Leg besluiten vast in ADR’s en verwijs hiernaar.
> Beschrijf wijzigingsbeheer (wie/hoe keurt goed).

## 11. Bijlagen

> - **Bijlage 1: Niet van toepassing lijst:** Hoofdstukken/subhoofdstukken die niet van toepassing
> zijn.
> - **Bijlage 2: Woordenlijst & acroniemen:** Definieer domeintermen en afkortingen.
> - **Bijlage 3: Referenties (gezaghebbend):** Links naar standaarden, beleid en best practices.
> - **Bijlage 4: Documentbeheer:** Versiebeheer, goedkeuringsprocessen en archivering.
