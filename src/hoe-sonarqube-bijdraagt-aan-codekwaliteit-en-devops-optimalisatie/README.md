# “Van Bugs naar Briljant: Hoe SonarQube je Code Laat Schitteren”

*[Joris Wittenberg, oktober 2024.](https://github.com/hanaim-devops/devops-blog-jwittenberg/tree/main/src/sonarqube-blog-post)*

<hr>
<img src="plaatjes/Sonarqube-logo.png" style="margin-left: 20px; margin-bottom: 20px;" width="500px" align="right" alt="mdbook logo om weg te halen" title="maar vergeet de alt tekst niet">

SonarQube, voorheen bekend als Sonar, is een opensourceplatform ontworpen om de kwaliteit van softwarecode te beheren. Het biedt ontwikkelaars een hulpmiddel om hoogwaardige code te leveren door inzicht te bieden in de algemene codekwaliteit. Vanuit een overzicht kunnen gebruikers eenvoudig doorklikken naar specifieke regels waar problemen zich voordoen. SonarQube is uitbreidbaar via plugins en ondersteunt een breed scala aan programmeertalen. Het platform kan code analyseren om kwaliteit gerelateerde aandachtspunten te identificeren, zoals duplicaten in code, falende testen en andere potentiële problemen. (Nauta & Nauta, 2016)

Dit onderzoek zal de hoofdvraag “Hoe kan SonarQube bijdragen aan het verbeteren van de codekwaliteit en de efficiëntie van softwareontwikkelingsprocessen?” doormiddel van de volgende deelvragen:

- Hoe draagt SonarQube bij aan het verbeteren van de codekwaliteit binnen softwareontwikkelingsprojecten?
(Impact van SonarQube op codekwaliteit en ontwikkelprocessen.)
- Welke mogelijkheden biedt SonarQube voor het detecteren en oplossen van veelvoorkomende codeproblemen?
(Specifieke functionaliteiten en analysemogelijkheden van het platform.)
- Hoe kunnen organisaties SonarQube effectief implementeren en integreren in hun bestaande ontwikkelworkflow?
(Implementatiestrategieën en integratie in tools zoals CI/CD pipelines.)

<hr>

## Inhoudsopgave

- [Hoe SonarQube bijdraagt aan codekwaliteit en DevOps optimalisatie](#hoe-sonarqube-bijdraagt-aan-codekwaliteit-en-devops-optimalisatie)
  - [Implementeren Sonarqube](#implementeren-sonarqube)
    - [Stappen voor het integreren van SonarQube in een C#-project](#stappen-voor-het-integreren-van-sonarqube-in-een-c-project)
  - [Hoe draagt SonarQube bij aan het verbeteren van de codekwaliteit binnen softwareontwikkelingsprojecten?](#hoe-draagt-sonarqube-bij-aan-het-verbeteren-van-de-codekwaliteit-binnen-softwareontwikkelingsprojecten)
    - [Detecteren en elimineren van technische schuld](#detecteren-en-elimineren-van-technische-schuld)
    - [Analyseren van codekwaliteit](#analyseren-van-codekwaliteit)
    - [Verbeteren van softwarebeveiliging](#verbeteren-van-softwarebeveiliging)
  - [Welke mogelijkheden biedt SonarQube voor het detecteren en oplossen van veelvoorkomende codeproblemen?](#welke-mogelijkheden-biedt-sonarqube-voor-het-detecteren-en-oplossen-van-veelvoorkomende-codeproblemen)
    - [1. **Detecteren van bugs en codefouten**](#1-detecteren-van-bugs-en-codefouten)
    - [2. **Rapportage en dashboardfuncties**](#2-rapportage-en-dashboardfuncties)
    - [3. **Beveiligingsanalyse**](#3-beveiligingsanalyse)
    - [4. **Opsporen van duplicaten in code**](#4-opsporen-van-duplicaten-in-code)
    - [5. **Beoordelen van codecomplexiteit**](#5-beoordelen-van-codecomplexiteit)
    - [6. **Controleren van codestandaarden**](#6-controleren-van-codestandaarden)
  - [Hoe kunnen organisaties SonarQube effectief implementeren en integreren in hun bestaande ontwikkelworkflow?](#hoe-kunnen-organisaties-sonarqube-effectief-implementeren-en-integreren-in-hun-bestaande-ontwikkelworkflow)
    - [Best Practices voor Integratie](#best-practices-voor-integratie)
    - [Voorbeeld van een CI/CD-pipeline met SonarQube-integratie](#voorbeeld-van-een-cicd-pipeline-met-sonarqube-integratie)
  - [Uitleg van de Pipeline](#uitleg-van-de-pipeline)
    - [1. Build Stage](#1-build-stage)
    - [2. Test Stage](#2-test-stage)
    - [3. Quality Stage (SonarQube)](#3-quality-stage-sonarqube)
    - [Variabelen](#variabelen)
    - [Mogelijke Fouten](#mogelijke-fouten)
    - [Samenvatting](#samenvatting)
  - [Conclusie](#conclusie)
  - [Bronnen](#bronnen)

<hr>

## Implementeren Sonarqube
Voordat op de deelvragen wordt ingegaan, wordt de implementatie van SonarQube in een ontwikkelomgeving getoond.

```yaml
version: '3.7'
services:
  sonarqube:
    image: sonarqube:latest
    container_name: sonarqube
    ports:
      - "9000:9000"
    environment:
      - SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true
      - SONAR_JDBC_URL=jdbc:postgresql://db/sonar
      - SONAR_JDBC_USERNAME=sonar
      - SONAR_JDBC_PASSWORD=sonar
    depends_on:
      - db
    networks:
      - sonarnet

  db:
    image: postgres:latest
    container_name: sonarqube_db
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonar
    networks:
      - sonarnet

networks:
  sonarnet:
```

### Stappen voor het integreren van SonarQube in een C#-project

1. **Start SonarQube**:
   - Open een terminal in de map waar je het bovenstaande `docker-compose.yml` bestand hebt opgeslagen.
   - Voer het volgende commando uit om de SonarQube-server en de database te starten:
     ```bash
     docker-compose up -d
     ```
   - Controleer of de SonarQube-server draait door naar `http://localhost:9000` te gaan in je webbrowser.

2. **Installeer de SonarScanner CLI**:
   - Download de SonarScanner for .NET via de [officiële documentatie](https://docs.sonarsource.com/sonarqube/latest/analysis/scan/sonarscanner-for-msbuild/).
   - Installeer het via NuGet in je project:
     ```bash
     dotnet tool install --global dotnet-sonarscanner
     ```

3. **Configuratiebestand maken**:
   - Voeg een bestand `sonar-project.properties` toe aan de hoofdmap van je project met de volgende inhoud:
     ```properties
     sonar.projectKey=sonarqubedemo
     sonar.host.url=http://localhost:9000
     sonar.login=sonarqubedemo-token
     ```

   - Genereer een token in SonarQube:
     - Log in op `http://localhost:9000` (gebruik standaardgebruikersnaam `admin` en wachtwoord `admin`).
     - Ga naar **Mijn account > Security > Tokens** en genereer een token.

4. **Analyse uitvoeren**:
   - Open een terminal in de hoofdmap van je C#-project.
   - Initialiseer de analyse met het volgende commando:
     ```bash
     dotnet sonarscanner begin /k:"sonarqubedemo" /d:sonar.login=<your-sonarqube-token>
     ```
   - Bouw het project:
     ```bash
     dotnet build
     ```
   - Voer de analyse uit:
     ```bash
     dotnet sonarscanner end /d:sonar.login=<your-sonarqube-token>
     ```

5. **Resultaten bekijken**:
   - Ga terug naar `http://localhost:9000` en bekijk de resultaten van de analyse. Hier vind je een overzicht van de codekwaliteit, beveiligingsproblemen, technische schuld en meer.

## Hoe draagt SonarQube bij aan het verbeteren van de codekwaliteit binnen softwareontwikkelingsprojecten?

Nu de implementatie van SonarQube in een ontwikkelomgeving is toegelicht, kunnen we verder kijken naar hoe dit platform de codekwaliteit verbetert. SonarQube biedt ontwikkelaars inzicht in potentiële problemen, zoals duplicaten, technische schuld en beveiligingskwetsbaarheden. Dit stelt teams in staat om codeproblemen proactief aan te pakken en de kwaliteit van software te verbeteren.

### Detecteren en elimineren van technische schuld
SonarQube identificeert problemen zoals slechte structuur, onleesbare code en onnodige complexiteit. Door deze problemen aan te pakken, verbeteren ontwikkelteams de onderhoudbaarheid van de code en verminderen ze het risico op fouten en kwetsbaarheden.

### Analyseren van codekwaliteit
Het platform biedt uitgebreide statistieken en kwaliteitsmetingen, zoals het opsporen van code duplicaten, het beoordelen van codecomplexiteit en het controleren van formatteringsstandaarden. Dit helpt teams om een consistente en kwalitatieve codebase te behouden.

### Verbeteren van softwarebeveiliging
SonarQube controleert code op kwetsbaarheden en onveilige praktijken, zoals het gebruik van kwetsbare bibliotheken en bekende beveiligingsproblemen. Dit maakt het een onmisbaar hulpmiddel voor het waarborgen van de veiligheid en betrouwbaarheid van software.

Met deze functies bevordert SonarQube een cultuur van continue verbetering. Door voortdurend feedback te geven op basis van duidelijke kwaliteitsnormen, ondersteunt het platform ontwikkelaars bij het maken van bewuste en kwalitatieve keuzes in nieuwe en bestaande code. Dit versterkt niet alleen de codekwaliteit op korte termijn, maar verhoogt ook de efficiëntie en betrouwbaarheid van softwareontwikkelingsprojecten als geheel. (MIVOCLOUD LIMITED, 2023)

In deze deelvraag is de bredere impact van SonarQube op codekwaliteit besproken. In de volgende deelvraag zullen we ons richten op specifieke functionaliteiten voor het detecteren en oplossen van concrete codeproblemen.

## Welke mogelijkheden biedt SonarQube voor het detecteren en oplossen van veelvoorkomende codeproblemen?

SonarQube biedt een breed scala aan functionaliteiten en analysemogelijkheden die ontwikkelteams ondersteunen bij het opsporen en oplossen van veelvoorkomende codeproblemen. De belangrijkste mogelijkheden zijn:

### 1. **Detecteren van bugs en codefouten**
SonarQube voert statische code-analyse uit om fouten en bugs in de code te identificeren. Deze fouten worden gecategoriseerd op basis van ernst (bijvoorbeeld "blocker", "critical" of "minor"), zodat ontwikkelaars prioriteit kunnen geven aan het oplossen van de meest dringende problemen. Voorbeelden zijn ongedefinieerde variabelen, foutieve logica of ongeldige API-aanroepen. (Aid, 2022b)

### 2. **Rapportage en dashboardfuncties**
SonarQube biedt uitgebreide dashboards en rapportages waarmee teams real-time inzicht krijgen in de status van hun codebase. Probleemtrends kunnen worden gevolgd, en teams kunnen aangepaste regels of filters toepassen om analyses af te stemmen op specifieke projecten. (Aid, 2022b)

### 3. **Beveiligingsanalyse**
Met ingebouwde beveiligingsregels en ondersteuning voor OWASP-richtlijnen kan SonarQube veelvoorkomende beveiligingsproblemen identificeren, zoals SQL-injecties, Cross-Site Scripting (XSS) en het gebruik van kwetsbare bibliotheken. Dit draagt bij aan het waarborgen van veilige softwareontwikkeling. (Aid, 2022b)

### 4. **Opsporen van duplicaten in code**
Code duplicatie is een veelvoorkomend probleem dat de onderhoudbaarheid van software bemoeilijkt. SonarQube detecteert dubbele codeblokken en rapporteert deze, zodat ontwikkelaars ze kunnen refactoren. Dit verbetert de leesbaarheid en vermindert technische schuld. (Chakraborty & Chakraborty, 2024)

### 5. **Beoordelen van codecomplexiteit**
SonarQube analyseert cyclomatische complexiteit en andere metriek, zoals het aantal afhankelijkheden binnen een codebase. Hoge complexiteitswaarden wijzen vaak op code die moeilijk te begrijpen en te onderhouden is. Door complexiteit te verminderen, kan de algehele codekwaliteit verbeteren. (Chakraborty & Chakraborty, 2024)

### 6. **Controleren van codestandaarden**
SonarQube controleert code op naleving van vooraf ingestelde codestandaarden en conventies. Dit helpt teams om consistentie binnen de codebase te handhaven en common best practices te volgen. (Chakraborty & Chakraborty, 2024)

---
## Hoe kunnen organisaties SonarQube effectief implementeren en integreren in hun bestaande ontwikkelworkflow?

SonarQube kan een integraal onderdeel worden van een ontwikkelworkflow door integratie in CI/CD-pipelines, waarmee codekwaliteit en beveiliging automatisch worden gecontroleerd bij elke commit of build. Deze integratie maakt het mogelijk om kwaliteitscontroles consistent en vroeg in het ontwikkelproces uit te voeren, wat helpt bij het minimaliseren van technische schuld en het waarborgen van hoge codekwaliteit.

### Best Practices voor Integratie
1. **Automatische Kwaliteitscontroles**  
   Configureer SonarQube als een verplichte stap in de pipeline, zodat builds alleen slagen als de code aan bepaalde kwaliteitsnormen voldoet. (Toxigon, 2024)

2. **Gepersonaliseerde Kwaliteitsprofielen**  
   Pas de regels en standaarden aan om te voldoen aan de specifieke eisen van het project of de organisatie. (Toxigon, 2024)

3. **Continue Monitoring**  
   Gebruik SonarQube om trends te analyseren en historische inzichten te verkrijgen over de codekwaliteit. (Team, 2024)

4. **Feedback voor Ontwikkelaars**  
   Ontwikkelaars ontvangen directe feedback in hun pull requests over codeproblemen, zodat problemen sneller worden opgelost. (Team, 2024)

---

### Voorbeeld van een CI/CD-pipeline met SonarQube-integratie

Hier is een voorbeeld van een CI/CD-pipeline in YAML (bijvoorbeeld voor **GitLab CI/CD**) met SonarQube-integratie:

```yaml
stages:
  - build
  - test
  - quality

variables:
  SONAR_HOST_URL: "http://localhost:9000"
  SONAR_TOKEN: $SONARQUBE_TOKEN

build:
  stage: build
  script:
    - echo "Building the project..."
    - dotnet build

test:
  stage: test
  script:
    - echo "Running tests..."
    - dotnet test

sonarqube:
  stage: quality
  script:
    - echo "Starting SonarQube analysis..."
    - dotnet sonarscanner begin /k:"sonarqubedemo" /d:sonar.host.url=$SONAR_HOST_URL /d:sonar.login=$SONAR_TOKEN
    - dotnet build
    - dotnet sonarscanner end /d:sonar.login=$SONAR_TOKEN
  allow_failure: false
 ```
## Uitleg van de Pipeline

### 1. Build Stage
In de `build`-taak wordt de applicatie gecompileerd. Dit is een noodzakelijke stap voordat de code kan worden geanalyseerd door SonarQube. De opdracht `dotnet build` zorgt ervoor dat het project correct wordt opgebouwd.

### 2. Test Stage
De `test`-taak voert unit-tests uit om te controleren of de code functioneel correct is. Dit helpt om ervoor te zorgen dat eventuele wijzigingen in de codebase geen bestaande functionaliteit breken. De opdracht `dotnet test` voert de tests uit die in het project zijn opgenomen.

### 3. Quality Stage (SonarQube)
De `sonarqube`-taak voert een SonarQube-analyse uit en bevat drie belangrijke stappen:

- **Begin-fase**:  
  Met de opdracht `dotnet sonarscanner begin` wordt de SonarQube-analyse geïnitialiseerd. Hier wordt het project geïdentificeerd door een unieke sleutel (`/k:"sonarqubedemo"`) en wordt verbinding gemaakt met de SonarQube-server via de opgegeven URL en authenticatietoken.

- **Build-fase**:  
  De `dotnet build`-opdracht wordt opnieuw uitgevoerd. Dit zorgt ervoor dat SonarQube de gegenereerde artefacten en broncode kan analyseren tijdens de volgende fase.

- **End-fase**:  
  De opdracht `dotnet sonarscanner end` voltooit de analyse en uploadt de resultaten naar de SonarQube-server. Deze stap gebruikt opnieuw het authenticatietoken om gegevens naar de server te verzenden.

### Variabelen
In de pipeline worden de volgende variabelen gebruikt:
- **`SONAR_HOST_URL`**: Dit is de URL van de SonarQube-server, bijvoorbeeld `http://localhost:9000`.
- **`SONAR_TOKEN`**: Dit is het authenticatietoken dat wordt gebruikt om SonarQube-opdrachten uit te voeren. Dit wordt meestal als een geheime variabele ingesteld in de CI/CD-configuratie.

### Mogelijke Fouten
- Als de kwaliteitscontrole niet voldoet aan de ingestelde normen (bijvoorbeeld teveel bugs of duplicaties), zal de pipeline mislukken, tenzij `allow_failure: true` is ingesteld voor de `sonarqube`-taak.
- Controleer of de SonarQube-server toegankelijk is via de opgegeven `SONAR_HOST_URL` en of het token geldig is.

### Samenvatting
Deze pipeline zorgt ervoor dat de codekwaliteit automatisch wordt gecontroleerd bij elke commit of merge. Eventuele problemen met codekwaliteit of beveiliging worden direct gemeld, zodat ontwikkelaars snel actie kunnen ondernemen. Dit helpt organisaties om hun ontwikkelworkflow te verbeteren en continu hoogwaardige software op te leveren.

## Conclusie

SonarQube speelt een cruciale rol in het verbeteren van de codekwaliteit en het optimaliseren van DevOps-processen binnen softwareontwikkelingsprojecten. Door statische code-analyse, integratie met CI/CD-pipelines en een breed scala aan functies stelt SonarQube organisaties in staat om potentiële problemen zoals technische schuld, duplicaten en beveiligingsrisico's vroegtijdig te identificeren en op te lossen.

De onderzoeksvraag “Hoe kan SonarQube bijdragen aan het verbeteren van de codekwaliteit en de efficiëntie van softwareontwikkelingsprocessen?” kan worden beantwoord met de volgende inzichten:

1. **Verbeteren van codekwaliteit**  
   SonarQube biedt ontwikkelaars geautomatiseerde tools om codeproblemen proactief te identificeren en aan te pakken. Door continue feedback en kwaliteitscontroles worden consistente en leesbare codebases gewaarborgd.

2. **Efficiëntie in ontwikkelingsprocessen**  
   Dankzij de integratie in CI/CD-pipelines wordt codekwaliteit een vast onderdeel van het ontwikkelproces. Problemen worden direct in de pipeline opgespoord, waardoor tijd wordt bespaard en teams zich kunnen richten op innovatie.

3. **Bevordering van samenwerking en verantwoordelijkheid**  
   Door directe feedback en rapportages bevordert SonarQube een cultuur van kwaliteit en verantwoordelijkheid binnen ontwikkelteams. Dit draagt bij aan betere samenwerking en snellere opleveringen.

Kortom, SonarQube helpt organisaties om software van hogere kwaliteit op te leveren, risico’s te beperken en de efficiëntie van ontwikkelworkflows te verhogen. Door de juiste implementatiestrategieën en best practices toe te passen, kunnen organisaties optimaal profiteren van de mogelijkheden die SonarQube biedt.
## Bronnen

- [Nauta, L., & Nauta, L. (2016, April 8). SonarQube. NLJUG - Nederlandse Java User Group.](https://nljug.org/java-magazine/sonarqube/#:~:text=SonarQube%20(vroeger%20Sonar%20genoemd)%20is,kwaliteit%20code%20op%20te%20leveren.) Geraadpleegd op 10 januari 2024.
- [MIVOCLOUD LIMITED. (2023, 14 juni). SonarQube: Why it is needed, what advantages and where it is used. Mivocloud.](https://mivocloud.com/en/blog/SonarQube-why-it-is-needed-what-advantages-and-where-it-is-used) Geraadpleegd op 10 januari 2024.
- [Aid. (2022b, maart 24). Improving Code Quality with Static Code Analysis Using SonarQube: A Practical Guide. Apriorit.](https://www.apriorit.com/qa-blog/765-qa-improving-code-quality-using-sonarqube) Geraadpleegd op 10 januari 2024.
- [Chakraborty, P., & Chakraborty, P. (2024, 26 juli). How to Use SonarQube for Code Quality Analysis. PixelFreeStudio Blog -.](https://blog.pixelfreestudio.com/how-to-use-sonarqube-for-code-quality-analysis/) Geraadpleegd op 10 januari.
- [Toxigon. (2024, 9 december). How to Integrate SonarQube in Your CI Pipeline: A Comprehensive Guide. Toxigon.](https://toxigon.com/integrating-sonarqube-in-your-ci-pipeline) Geraadpleegd op 10 januari.
- [Team, K. C. (2024, 26 november). How to Set Up Code Analysis with SonarQube: A Step-by-Step Guide. Kodezi Blog.](https://blog.kodezi.com/how-to-set-up-code-analysis-with-sonar-qube-a-step-by-step-guide) Geraadpleegd op 10 januari.