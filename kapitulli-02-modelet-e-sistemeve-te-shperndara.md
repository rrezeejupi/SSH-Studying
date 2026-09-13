# Kapitulli 2 — Modelet e Sistemeve të Shpërndara

## Hyrje

Një sistem i shpërndarë (*distributed system*) përbëhet nga shumë komponentë — kompjuterë, serverë, procese, shërbime — që gjenden fizikisht në vende të ndryshme, por që bashkëpunojnë për të kryer detyra të përbashkëta, duke i dukur përdoruesit si një e tërë e vetme. Për ta studiuar dhe projektuar një sistem të tillë në mënyrë sistematike, përdoren **modele** që abstragojnë aspekte të ndryshme të tij. Çdo model përqendrohet në një pyetje të ndryshme rreth sistemit:

| Modeli | Pyetja qendrore | Çfarë përshkruan |
|---|---|---|
| **Modeli Fizik** (Physical Model) | Ku dhe me çfarë? | Infrastrukturën dhe pajisjet fizike të sistemit |
| **Modeli Arkitekturor** (Architectural Model) | Si është organizuar? | Strukturën dhe organizimin e komponentëve |
| **Modeli i Ndërveprimit** (Interaction Model) | Si komunikojnë? | Komunikimin, kohën dhe bashkëpunimin ndërmjet proceseve |
| **Modeli i Dështimit** (Failure Model) | Çfarë shkon keq? | Llojet e gabimeve dhe mënyrën e trajtimit të tyre |

Këto katër modele nuk janë të pavarura nga njëra-tjetra — përkundrazi, ato ndërthuren: struktura fizike (ku ndodhen nyjet) ndikon në arkitekturën logjike (si organizohen shërbimet), arkitektura ndikon në mënyrën se si proceset ndërveprojnë (cilat paradigma komunikimi përdoren), ndërsa modeli i ndërveprimit (sinkron a asinkron) përcakton se çfarë lloj dështimesh mund të ndodhin dhe si duhen trajtuar. Kuptimi i tyre së bashku i mundëson një inxhinieri të projektojë sisteme të shpërndara që janë të shkallëzueshme, të besueshme dhe efikase.

---

## 1. Modeli Fizik (Physical Model)

Modeli fizik përshkruan **strukturën fizike** të sistemit: cilat pajisje (nyje/kompjuterë) e përbëjnë sistemin dhe si janë të lidhura ato në rrjet. Ky është niveli më "konkret" i modelimit — nuk merret me logjikën e softuerit, por me harduerin dhe lidhjet reale.

Karakteristikat kryesore të modelit fizik janë:

- **Nyjet e sistemit** — tregon se cilët kompjuterë (serverë, klientë, pajisje mobile etj.) përbëjnë sistemin.
- **Rrjeti** — përshkruan mediumin dhe topologjinë që i lidh këto pajisje me njëra-tjetrën.
- **Elementet e harduerit** — serverë, klientë, pajisje mobile, e çdo pajisje tjetër fizike pjesë e sistemit.
- **Detajet teknike** — specifikat e kompjuterëve dhe të rrjeteve (kapaciteti, shpejtësia, lidhjet fizike).

**Shembull:** Një sistem cloud, ku shumë serverë të vendosur në qendra të ndryshme të të dhënave (data centers), në qytete apo kontinente të ndryshme, janë të lidhur me njëri-tjetrin përmes internetit — kjo është pikërisht ajo çfarë përshkruan modeli fizik: infrastruktura konkrete mbi të cilën "rri" sistemi.

**Qëllimi** i modelit fizik është të japë një pamje të qartë të infrastrukturës fizike dhe të lidhjeve të rrjetit që mbështesin sistemin e shpërndarë — pra "themelin" mbi të cilin ndërtohen të gjitha shtresat e tjera (softueri, komunikimi, logjika e aplikacionit).

---

## 2. Modeli Arkitekturor (Architectural Model)

Ndërsa modeli fizik pyet "me çfarë pajisjesh?", modeli arkitekturor pyet **"si janë të organizuara logjikisht komponentët e softuerit dhe si ndërveprojnë me njëri-tjetrin?"**. Ai përshkruan strukturën e softuerit dhe mënyrën se si komunikojnë komponentët e sistemit.

Karakteristikat kryesore:

- Përshkruan **strukturën logjike** të softuerit (jo pajisjet fizike).
- Tregon **mënyrën e komunikimit** ndërmjet komponentëve (kush flet me kë, dhe në çfarë drejtimi).

### 2.1 Modelet arkitekturore kryesore

Katër stile arkitekturore janë veçanërisht të rëndësishme dhe përdoren gjerësisht në praktikë:

1. **Client–Server**
2. **Peer-to-Peer (P2P)**
3. **Multi-Tier (Three-Tier / N-Tier)**
4. **Microservices**

Le t'i shqyrtojmë secilin veç e veç.

#### 2.1.1 Modeli Klient-Server (Client–Server Model)

Në këtë model, sistemi ndahet në dy role të qarta: **klientë** dhe **serverë**.

- Klienti dërgon kërkesa (*requests*).
- Serveri i pranon, i përpunon kërkesat, dhe kthen përgjigje (*responses*).
- Serveri zakonisht disponon më shumë burime (memorie, fuqi përpunuese, hapësirë ruajtjeje) dhe është ai që menaxhon të dhënat qendrore të sistemit.

**Shembull:** shfletuesi i internetit (klienti) i kërkon një faqe web serverit; serveri e kthen faqen si përgjigje.

*Avantazhet:*
- Menaxhim i centralizuar i të dhënave — më e lehtë të mbahet konsistenca.
- Siguri më e mirë, sepse kontrolli qëndron te një pikë qendrore.
- Mirëmbajtje më e lehtë e logjikës dhe e të dhënave.

*Disavantazhet:*
- Serveri mund të bëhet **pikë e vetme dështimi** (*single point of failure*) — nëse serveri bie, i gjithë shërbimi ndalon.

#### 2.1.2 Modeli Peer-to-Peer (P2P)

Në modelin P2P nuk ekziston ndarja e ngurtë klient/server: çdo kompjuter (peer) vepron njëkohësisht si klient **dhe** si server.

- Nuk ka server qendror.
- Çdo nyje (node) mund edhe të ofrojë, edhe të kërkojë shërbime nga nyjet e tjera.

**Shembuj:** BitTorrent, rrjetet për ndarje fajllash.

*Avantazhet:*
- Shkallëzim i mirë (*scalability*) — sa më shumë nyje bashkohen, aq më shumë burime shtohen.
- Nuk ka pikë të vetme dështimi, sepse nuk ka varësi nga një server qendror.

*Disavantazhet:*
- Menaxhim më i vështirë, sepse mungon kontrolli qendror.
- Siguri më e ulët, sepse çdo nyje mund të jetë potencialisht e pabesueshme.

#### 2.1.3 Modeli Multi-Tier (Three-Tier / N-Tier)

Ky model ndan sistemin në **shtresa funksionale** (tiers), ku çdo shtresë ka përgjegjësi të qarta dhe të veçuara nga shtresat e tjera:

1. **Presentation Layer** — ndërfaqja e përdoruesit (ajo çfarë sheh dhe me të cilën ndërvepron përdoruesi).
2. **Application Layer** — logjika e biznesit/aplikacionit (rregullat, përpunimi, vendimet).
3. **Data Layer** — baza e të dhënave, ku ruhen dhe menaxhohen të dhënat.

**Shembull:** shumica e aplikacioneve moderne web ndjekin këtë strukturë (front-end → back-end → databazë).

*Avantazhet:*
- Organizim më i mirë i kodit, sepse çdo shtresë ka rol të qartë.
- Mirëmbajtje dhe zhvillim më i lehtë — ndryshimi i një shtrese (p.sh. ndërfaqes) nuk prek domosdoshmërisht shtresat e tjera.
- Shkallëzim më i mirë, sepse çdo shtresë mund të shkallëzohet veçmas (p.sh. të shtohen më shumë serverë aplikacioni pa prekur bazën e të dhënave).

#### 2.1.4 Modeli i Mikroshërbimeve (Microservices)

Në arkitekturën microservices, aplikacioni ndahet në **shërbime të vogla dhe të pavarura**, secili me përgjegjësi specifike, që komunikojnë me njëri-tjetrin përmes API-ve (zakonisht REST mbi HTTP, ose message brokers).

- Çdo shërbim ka funksion specifik e të mirëpërcaktuar.
- Çdo shërbim mund të zhvillohet, testohet dhe të deployohet (vendoset në prodhim) **veçmas**, pa prekur shërbimet e tjera.

**Shembull:** sistemet e mëdha si Netflix ose Amazon janë ndërtuar mbi qindra mikroshërbime të pavarura (p.sh. shërbimi i pagesave, shërbimi i rekomandimeve, shërbimi i kërkimit etj.).

*Avantazhet:*
- Shkallëzim i pavarur — çdo shërbim shkallëzohet sipas nevojës së tij, pa shpenzuar burime kot në pjesët e tjera.
- Zhvillim paralel i ekipeve — ekipe të ndryshme mund të punojnë njëkohësisht mbi shërbime të ndryshme.

*Disavantazhet:*
- Kompleksitet më i madh në komunikim (shumë shërbime që duhet të bisedojnë mes tyre nëpër rrjet).
- Menaxhim më i vështirë i infrastrukturës së rrjetit dhe i vartësive ndërmjet shërbimeve.

**Qëllimi** i përgjithshëm i modelit arkitekturor është të shpjegojë organizimin logjik dhe strukturën e brendshme të sistemit — si janë ndarë përgjegjësitë dhe si komunikojnë komponentët për të realizuar funksionalitetin e plotë.

### 2.2 Rast studimi: sistem microservices për menaxhimin e studentëve

Për ta ilustruar arkitekturën microservices në praktikë, le të shqyrtojmë një detyrë tipike: një universitet dëshiron të ndërtojë një sistem modern për menaxhimin e studentëve, duke ofruar shërbimet: regjistrimi i studentëve, menaxhimi i lëndëve, kontrolli i notave, pagesa e semestrit dhe autentikimi i përdoruesve — secili funksion si mikroshërbim i pavarur.

**a) Cilat mikroshërbime mund të krijohen?**

- **Shërbimi i Autentikimit** — menaxhon login-in dhe identifikimin e studentëve.
- **Shërbimi i Studentëve** — ruan të dhënat e studentëve dhe menaxhon regjistrimin e tyre.
- **Shërbimi i Lëndëve** — menaxhon lëndët dhe regjistrimin në to.
- **Shërbimi i Notave** — ruan dhe shfaq notat e studentëve.
- **Shërbimi i Pagesave** — menaxhon pagesën e semestrit dhe pagesa të tjera.

Karakteristikë themelore e microservices: çdo mikroshërbim ka **databazën e vet**, **logjikën e vet** të aplikacionit, dhe mund të zhvillohet e përditësohet plotësisht i pavarur nga shërbimet e tjera.

**b) Si komunikojnë mikroshërbimet me njëri-tjetrin?**

Mikroshërbimet komunikojnë zakonisht përmes:
- API-ve (Application Programming Interface), më së shpeshti **REST API** mbi **HTTP requests**;
- **message brokers** (p.sh. Kafka, RabbitMQ) kur komunikimi duhet të jetë asinkron.

Në praktikë, kërkesa e studentit udhëton kështu: **Studenti → API Gateway → Mikroshërbimi përkatës**. API Gateway-ja vepron si "porta e hyrjes" e vetme që drejton çdo kërkesë te mikroshërbimi i duhur.

**c) Rrjedha e procesit kur studenti dëshiron të shohë notat**

1. Studenti hyn në aplikacion.
2. Kërkesa shkon te API Gateway.
3. API Gateway kontrollon identitetin e përdoruesit përmes Shërbimit të Autentikimit.
4. Pas verifikimit të suksesshëm, kërkesa dërgohet te Shërbimi i Notave.
5. Shërbimi i Notave merr të dhënat nga databaza e vet (databaza e notave).
6. Notat kthehen si përgjigje te API Gateway.
7. API Gateway ia dërgon përgjigjen studentit.

Skematikisht: **Studenti → API Gateway → Autentikimi → Shërbimi i Notave → Databaza e Notave → Përgjigje te studenti**.

**d) Avantazhet dhe disavantazhet**

*Avantazhe (3):*
1. **Shkallëzim i pavarur** — çdo mikroshërbim mund të zgjerohet (p.sh. me më shumë instanca) veçmas nga të tjerët.
2. **Zhvillim paralel** — ekipe të ndryshme punojnë njëkohësisht në shërbime të ndryshme, pa u penguar mes tyre.
3. **Mirëmbajtje më e lehtë** — një ndryshim në një shërbim (p.sh. Shërbimi i Pagesave) nuk ndikon në funksionimin e shërbimeve të tjera.

*Disavantazhe (2):*
1. **Kompleksitet më i madh** — menaxhimi i shumë shërbimeve të pavarura (deployim, monitorim, versionim) është më i vështirë se menaxhimi i një aplikacioni monolit.
2. **Komunikim më kompleks** — shërbimet duhet të komunikojnë vazhdimisht nëpër rrjet e përmes API-ve, gjë që sjell vonesa shtesë dhe rrezik dështimesh të pjesshme.

---

## 3. Modeli me Middleware

**Middleware** është një **shtresë ndërmjetëse (softuerike)** e vendosur ndërmjet klientëve dhe serverëve (ose, në përgjithësi, ndërmjet çdo dy komponentëve softuerikë) në një sistem të shpërndarë. Ai përdoret kur shumë klientë dhe shumë serverë duhet të komunikojnë në mënyrë të organizuar dhe koherente.

Funksionet kryesore që ofron middleware-i:

- **Komunikim** mes proceseve/aplikacioneve.
- **Menaxhim të kërkesave** — drejtimi i tyre te komponenti i duhur.
- **Siguri dhe autentifikim.**
- **Sinkronizim** të proceseve.
- **Integrim të shërbimeve** të ndryshme në një sistem koherent.

Falë kësaj shtrese, sistemi i shpërndarë "duket" për përdoruesin si një sistem i vetëm, i njësuar — kompleksiteti i shpërndarjes fshihet pas middleware-it.

**Shembuj konkretë të teknologjive middleware:** CORBA, gRPC (Google), message brokers si Kafka dhe RabbitMQ, REST API.

### 3.1 Rast studimi: sistem universitar me middleware

Një universitet dëshiron një sistem të shpërndarë për shërbimet studentore (regjistrim lëndësh, kontroll notash, pagesa semestrale, vërtetime etj.), të përdorshëm nga kompjuteri ose telefoni, ku ka disa serverë të veçantë: server për aplikacionin, server për bazën e të dhënave dhe server për pagesat. Universiteti dëshiron që komunikimi ndërmjet klientëve dhe serverëve të jetë i organizuar, i sigurt dhe i lehtë për t'u menaxhuar.

**a) Pse ky sistem mund të ndërtohet me modelin middleware?**

Sepse ka shumë komponentë heterogjenë (klientë nga PC/telefon, server aplikacioni, server databaze, server pagesash) që duhet të komunikojnë mes tyre. Middleware vepron si shtresë ndërmjetëse që:
- lidh komponentë të ndryshëm në një sistem të vetëm,
- e bën komunikimin më të rregullt,
- rrit sigurinë dhe kontrollin,
- thjeshton menaxhimin e përgjithshëm të sistemit të shpërndarë.

**b) Rrjedha e komunikimit**

1. Studenti hyn në aplikacion nga telefoni ose kompjuteri.
2. Klienti dërgon një kërkesë (p.sh. "Shfaq notat e semestrit").
3. Kërkesa shkon te middleware-i.
4. Middleware-i e kontrollon kërkesën, verifikon përdoruesin dhe vendos te cili server duhet dërguar kërkesa.
5. Kërkesa dërgohet te serveri përkatës — p.sh. te databaza për notat, te serveri i pagesave për tarifat, te serveri i aplikacionit për vërtetime.
6. Serveri e përpunon kërkesën dhe e kthen përgjigjen te middleware-i.
7. Middleware-i ia kthen përgjigjen klientit.

Skematikisht: **Klienti → Middleware → Serveri përkatës → Middleware → Klienti**.

**c) Funksionet kryesore të middleware-it në këtë sistem**

- **Komunikimi** — mundëson shkëmbimin e të dhënave mes klientëve dhe serverëve.
- **Menaxhimi i kërkesave** — pranon kërkesat dhe i drejton te serveri i duhur.
- **Siguria** — kontrollon autentikimin dhe autorizimin (kush lejohet të hyjë dhe çfarë mund të bëjë).
- **Menaxhimi i transaksioneve** — p.sh. te pagesat, siguron që veprimi të kryhet saktë dhe pa gabime.
- **Integrimi i shërbimeve** — lidh serverin e aplikacionit, databazën dhe serverin e pagesave në një sistem të vetëm.

**d) Avantazhet dhe disavantazhet e middleware-it**

*Avantazhe:*
1. **Menaxhim më i lehtë** — të gjitha kërkesat kontrollohen nga një shtresë qendrore.
2. **Siguri më e mirë** — middleware mund të kontrollojë hyrjen, lejet dhe mbrojtjen e të dhënave.
3. **Integrim më i mirë i komponentëve** — lejon që serverë e shërbime të ndryshme të punojnë së bashku pa vështirësi.

*Disavantazhe:*
1. **Rritet kompleksiteti i sistemit** — shtohet një shtresë tjetër që duhet projektuar dhe mirëmbajtur.
2. **Ndikim i mundshëm në performancë** — çdo kërkesë kalon fillimisht nëpër middleware, prandaj mund të shtohet vonesë shtesë.

---

## 4. Entitetet dhe Paradigmat Komunikuese

Për të kuptuar në thellësi si funksionon komunikimi në një sistem të shpërndarë, është e dobishme të dallojmë dy koncepte plotësuese: **entitetet** që marrin pjesë në komunikim dhe **paradigmat** (mënyrat) se si ato komunikojnë.

> **Dallimi thelbësor:** entitetet komunikuese janë **kush** komunikon; paradigmat komunikuese janë **si** komunikojnë.

**Shembull ilustrues** (nga sistemi studentor i trajtuar më lart): entitetet janë studenti, API Gateway, Shërbimi i Notave, databaza; paradigmat janë kërkesë-përgjigje, RPC, ose komunikimi me mesazhe.

### 4.1 Entitetet komunikuese

Entitetet komunikuese janë komponentët konkretë që marrin pjesë në shkëmbimin e informacionit:

- **Proceset** — një proces është një program në ekzekutim, që mund të dërgojë dhe të marrë mesazhe (p.sh. një proces në server ose një proces në kompjuterin e klientit).
- **Klientët dhe serverët** — klienti kërkon një shërbim ose burim, serveri e ofron atë; ky është ndoshta modeli më i zakonshëm i komunikimit në praktikë.
- **Nyjet (Nodes)** — pajisje ose kompjuterë të lidhur në rrjet që komunikojnë mes tyre (kompjuterë, serverë, pajisje mobile, pajisje IoT).
- **Objekte ose komponentë të shpërndarë** — në disa sisteme komunikimi bëhet mes objekteve softuerike (p.sh. objekte në CORBA, shërbime web, mikroshërbime).
- **Shërbimet/mikroshërbimet** — në arkitekturat moderne, entitetet komunikuese janë shërbime të pavarura që shkëmbejnë të dhëna përmes API-ve ose mesazheve.

### 4.2 Paradigmat komunikuese

Paradigmat komunikuese tregojnë mekanizmin konkret se si realizohet komunikimi ndërmjet entiteteve. Ekzistojnë disa paradigma kryesore:

1. **Komunikimi me mesazhe (Message Passing)** — paradigma më bazë: një proces dërgon një mesazh dhe një proces tjetër e merr atë. Shembull: klienti dërgon kërkesë te serveri dhe merr përgjigje.

2. **Kërkesë-Përgjigje (Request-Reply)** — një entitet dërgon një kërkesë dhe pret një përgjigje. Shembull tipik: HTTP, modeli klient-server; përdoret gjerësisht në aplikacionet web dhe API.

3. **Remote Procedure Call (RPC)** — një proces thërret një funksion/procedurë që ndodhet fizikisht në një kompjuter tjetër, sikur ai funksion të ishte lokal. Shembuj: RPC klasike, gRPC.

4. **Remote Method Invocation (RMI)** — e ngjashme me RPC-në, por e orientuar drejt objekteve (jo funksioneve të thjeshta), sidomos e njohur nëpërmjet Java RMI-së; lejon thirrjen e metodave të një objekti që ndodhet në një makinë tjetër.

5. **Komunikimi i orientuar në mesazhe (Message-Oriented Communication)** — komunikimi bëhet përmes radhëve të mesazheve (*message queues*) ose brokerëve, p.sh. RabbitMQ, Kafka. Ky model përdoret kur sistemi duhet të jetë më fleksibël dhe jo domosdoshmërisht sinkron.

6. **Publish/Subscribe** — një entitet "publikon" informacion, ndërsa entitetet e interesuara "abonohen" (subscribe) dhe e marrin atë automatikisht. Shembull: sistemet e njoftimeve, evente në sisteme të shpërndara.

7. **Komunikimi sinkron** — dërguesi pret derisa marrësi t'i përgjigjet, para se të vazhdojë më tej. Është më i thjeshtë për t'u kuptuar dhe implementuar, por mund të jetë më i ngadaltë, sepse bllokon dërguesin gjatë pritjes.

8. **Komunikimi asinkron** — dërguesi nuk pret menjëherë përgjigjen, por vazhdon punën tjetër. Është më i përshtatshëm për sisteme të mëdha, sepse rrit shkallëzimin dhe fleksibilitetin e sistemit.

9. **Komunikimi me rrjedhë të të dhënave (Streaming)** — të dhënat dërgohen vazhdimisht si një rrjedhë e pandërprerë, jo si mesazhe të veçuara. Shembuj: video streaming, sensorë IoT, monitorim në kohë reale.

---

## 5. Modele Konkrete Komunikimi mes Klientit dhe Serverit

Përtej paradigmave abstrakte, në praktikë komunikimi klient-server merr disa forma konkrete organizative:

### 5.1 Thirrjet tek një server individual nga shumë klientë

Rasti më i thjeshtë: disa klientë dërgojnë kërkesa (requests) tek **një server i vetëm** përmes rrjetit. Serveri i pranon kërkesat, i përpunon dhe u kthen përgjigje (responses) klientëve. Kjo është skema klasike klient-server, ku ekziston vetëm një pikë qendrore shërbimi.

### 5.2 Shërbimi i ofruar nga shumë serverë

Kur ngarkesa ose kërkesat për besueshmëri rriten, shërbimi mund të ofrohet nga një **grup serverësh** (server cluster) në vend të një serveri të vetëm:

- **Klienti (Client)** — aplikacioni ose përdoruesi që dërgon kërkesa në rrjet.
- **Server Cluster** — grup serverësh që ofrojnë të njëjtin shërbim; mund të organizohen si *replicated servers* (serverë të replikuar, që mbajnë kopje identike të të dhënave) ose si *distributed service nodes* (nyje të shpërndara shërbimi, që ndajnë punën mes tyre).
- **Komunikimi server-me-server** — serverët shkëmbejnë mesazhe mes tyre për sinkronizim të të dhënave, koordinim dhe replikim.

Çdo server mund të përpunojë vetë kërkesën e marrë dhe të kthejë përgjigje, ndërsa në sfond serverët bashkëpunojnë mes tyre për të mbajtur sistemin koherent.

**Shembuj realë:** web server clusters, cloud services, baza të dhënash të shpërndara (distributed databases), arkitekturat microservices.

Në përmbledhje: klientët komunikojnë me serverët, ndërsa serverët bashkëpunojnë mes tyre për ta ofruar shërbimin në mënyrë koherente dhe të besueshme.

### 5.3 Shërbimi i ofruar përmes një serveri Web Proxy

Në këtë skemë, ndërmjet klientit dhe serverit Web futet një ndërmjetës — **serveri proxy**:

1. Klienti dërgon kërkesë për një faqe ose shërbim.
2. Kërkesa shkon te serveri proxy (jo direkt te serveri web).
3. Proxy-ja e analizon kërkesën.
4. Proxy-ja komunikon me një nga serverët web të vërtetë.
5. Serveri web e kthen përgjigjen te proxy-ja.
6. Proxy-ja ia dërgon përgjigjen klientit.

Proxy-ja mund të shërbejë për shpërndarje ngarkese (load balancing), për caching, ose për filtrim/siguri, duke qëndruar si ndërmjetës transparent ndërmjet klientëve dhe serverëve të vërtetë.

---

## 6. Shtresat e Softuerit dhe të Harduerit në Sistemet e Shpërndara

Funksionimi i një sistemi të shpërndarë organizohet zakonisht në disa **shtresa (layers)**, që shkojnë nga hardueri fizik deri te aplikacioni që përdoruesi e sheh. Ky organizim shtresor e bën sistemin më të kuptueshëm, më të lehtë për t'u zhvilluar dhe për t'u mirëmbajtur, sepse çdo shtresë e fsheh kompleksitetin e shtresave nën të.

Nga poshtë lart, shtresat janë:

1. **Shtresa e Harduerit (Hardware Layer)** — shtresa më e ulët; përbëhet nga kompjuterët, serverët, procesorët (CPU), memoria (RAM), pajisjet e ruajtjes (SSD/HDD) dhe rrjeti fizik (kabllo, router, switch). Funksioni i saj: siguron infrastrukturën fizike mbi të cilën ekzekutohen programet dhe komunikojnë pajisjet.

2. **Shtresa e Sistemit Operativ (Operating System Layer)** — menaxhon harduerin dhe u ofron shërbime programeve mbi të. Shembuj: Linux, Windows Server, Unix. Funksionet kryesore: menaxhimi i proceseve, menaxhimi i memories, menaxhimi i pajisjeve, komunikimi në rrjet.

3. **Shtresa Middleware** — shtresë ndërmjetëse (softuerike) që mundëson komunikimin ndërmjet komponentëve në sistemin e shpërndarë. Funksionet: komunikim mes aplikacioneve, menaxhim kërkesash, sinkronizim proceseshë, siguri dhe autentifikim. Shembuj middleware: RPC, REST API, message queues, CORBA, gRPC. Rëndësia e saj: e bën sistemin e shpërndarë të duket, për përdoruesin fundor, si **një sistem i vetëm**, i njësuar.

4. **Shtresa e Aplikacionit (Application Layer)** — shtresa më e lartë, ku ndodhen aplikacionet që përdoren drejtpërdrejt nga përdoruesit. Shembuj: aplikacione web, sisteme cloud, baza të dhënash të shpërndara, aplikacione mobile. Funksioni: ofron shërbimet që përdoruesi i shfrytëzon direkt.

Rendi vertikal i plotë (nga lart poshtë) është: **Aplikacione/Shërbime → Middleware → Sistemi Operativ (Platform) → Harduer kompjuterik dhe rrjeti**. Vlen të theksohet se termi "Platform" përfshin bashkimin e sistemit operativ me harduerin themelor mbi të cilin ai funksionon, dhe se pikërisht middleware-i është shtresa që "fsheh" shpërndarjen fizike të sistemit prej syve të programuesit të aplikacionit.

---

## 7. Modeli i Ndërveprimit (Interaction Model)

Modeli i ndërveprimit përshkruan **mënyrën se si proceset komunikojnë dhe bashkëpunojnë** në një sistem të shpërndarë. Ai merr në konsideratë veçanërisht dimensionin kohor: vonesat e rrjetit, sinkronizimin ndërmjet proceseve, dhe mënyrën e dërgimit e marrjes së mesazheve.

Elementet kryesore që studion modeli i ndërveprimit:
- komunikimi me mesazhe,
- sinkronizimi i proceseve,
- vonesa në rrjet (latency).

Qëllimi i tij është të na ndihmojë të kuptojmë saktësisht **si bashkëpunojnë** proceset e ndryshme brenda sistemit — jo vetëm çfarë dërgojnë, por edhe **kur** dhe me çfarë garancish kohore.

### 7.1 Renditja e ngjarjeve dhe marrëdhënia "happened-before"

Një nga vështirësitë themelore të sistemeve të shpërndara është se **nuk ekziston një orë globale e përbashkët** e sinkronizuar në mënyrë perfekte për të gjitha proceset. Prandaj, për të përcaktuar rendin e ngjarjeve, përdoret koncepti i Leslie Lamport-it, i njohur si marrëdhënia **"happened-before"** (ndodhi para).

Për ta vizualizuar këtë, përdoret një diagram hapësirë-kohë (space-time diagram):

- **Proceset** paraqiten si vija horizontale (p.sh. X, Y, Z, A), secila prej të cilave përfaqëson ekzekutimin e një procesi ndërsa koha fizike rrjedh nga e majta në të djathtë.
- **Ngjarjet** (events) janë pikat mbi këto vija — kryesisht ngjarje të dy llojeve: `send` (një proces dërgon mesazh) dhe `receive` (një proces merr mesazh). Numrat pranë pikave (1, 2, 3, 4…) tregojnë rendin **lokal** të ngjarjeve brenda po atij procesi.
- **Mesazhet** paraqiten si shigjeta që lidhin një ngjarje `send` në një proces me një ngjarje `receive` në një proces tjetër (p.sh. m₁, m₂, m₃).

**Rregulli themelor** i çdo komunikimi është: ngjarja `send` ndodh **gjithmonë** përpara ngjarjes `receive` përkatëse të po atij mesazhi:
$$ \text{send}(m) \rightarrow \text{receive}(m) $$

Nga ky rregull rrjedh **marrëdhënia "happened-before" (→)** e Lamport-it:
- Nëse dy ngjarje `a` dhe `b` ndodhin **në të njëjtin proces**, dhe `a` ndodh para `b`-së, atëherë `a → b`.
- Nëse `a` është dërgimi (send) i një mesazhi dhe `b` është marrja (receive) e po atij mesazhi, atëherë `a → b`.
- Marrëdhënia është **tranzitive**: nëse `a → b` dhe `b → c`, atëherë `a → c` — pra ekziston një zinxhir ngjarjesh që lidh `a` me `c` edhe kur ato ndodhin në procese të ndryshme (p.sh. `send m1 → receive m1 → send m2 → receive m2`).

**Ngjarjet konkurrente (concurrent events):** disa ngjarje në sistem nuk kanë asnjë lidhje direkte apo indirekte mes tyre (nuk ka asnjë zinxhir mesazhesh që i lidh) — këto quhen ngjarje konkurrente. Për to, **nuk mund të dihet logjikisht** cila ndodhi para tjetrës — koncepti "para" thjesht nuk aplikohet. Ky fenomen është shumë i zakonshëm në sistemet e shpërndara, sepse proceset ekzekutohen në mënyrë të pavarur nga njëri-tjetri.

**Pikat t₁, t₂, t₃** në diagram përfaqësojnë momente të caktuara të kohës fizike globale, dhe përdoren për të treguar cilat ngjarje kanë ndodhur deri në atë moment specifik, në secilin proces.

**Ideja qendrore** që del nga i gjithë ky diagram: në një sistem të shpërndarë proceset punojnë në mënyrë të pavarur, komunikojnë vetëm me anë të mesazheve, dhe rendi i vërtetë (logjik) i ngjarjeve përcaktohet nga marrëdhënia happened-before — jo domosdoshmërisht nga koha fizike absolute, e cila në praktikë nuk është kurrë plotësisht e sinkronizuar mes nyjeve. Kjo është baza teorike mbi të cilën ndërtohen mekanizma si **Lamport Logical Clocks** (orët logjike të Lamport-it), **Vector Clocks** (orët vektoriale), si dhe algoritmet e renditjes së ngjarjeve (event ordering) në përgjithësi.

> **Pse na duhen orë logjike?** Sepse orët fizike janë të pasigurta dhe të pasinkronizuara në sistemet e shpërndara, vonesat në komunikim janë të pashmangshme mes nyjeve, dhe për shumë aplikacione (p.sh. transaksionet bankare) renditja e sakta e ngjarjeve/mesazheve është kritike. Orët logjike zgjidhin këtë problem duke kapur renditjen **logjike** (jo domosdoshmërisht kronologjike absolute) të ngjarjeve, mbi bazën e parimit happened-before.

### 7.2 Modeli sinkron kundrejt modelit asinkron

Modeli i ndërveprimit ka dy variante themelore, që dallohen nga fakti nëse ekzistojnë apo jo **kufij të njohur/të garantuar në kohë**:

**Sistemet e shpërndara sinkrone (Synchronous):**
- Dihen kufijtë maksimalë të kohës për operacionet dhe komunikimin.
- Koha e ekzekutimit të çdo hapi, vonesa e transmetimit të mesazhit, dhe shpejtësia e devijimit të orës (*clock drift rate*) të secilës nyje kanë **kufij të njohur dhe të garantuar**.
- Kjo lejon projektim më të thjeshtë të algoritmeve (p.sh. mund të vendosen "timeout"-e të sakta, sepse dihet me siguri sa kohë maksimalisht mund të zgjasë një operacion).

**Sistemet e shpërndara asinkrone (Asynchronous):**
- Nuk ka kufij të garantuar për kohën e ekzekutimit, vonesën e mesazheve, apo devijimin e orës.
- Koha e ekzekutimit, vonesa e transmetimit të mesazhit dhe devijimi i orës mund të jenë **arbitrare** (të papërcaktuara paraprakisht).
- Kjo është më realiste për shumicën e rrjeteve publike (si interneti), ku vonesat mund të ndryshojnë shumë, por e bën projektimin e algoritmeve më të vështirë (p.sh. nuk mund të dallohet me siguri absolute nëse një proces ka dështuar, apo thjesht është ngadalësuar).

---

## 8. Modeli i Dështimit (Failure Model)

Modeli i dështimit përshkruan **llojet e gabimeve** që mund të ndodhin në një sistem të shpërndarë, si dhe mënyrën se si sistemi mund t'i njohë dhe t'i trajtojë ato. Meqë komponentët e sistemit janë të shumtë dhe fizikisht të shpërndarë, mundësia e dështimeve të pjesshme (vetëm disa komponentë dështojnë, jo të gjithë njëherësh) është shumë më e lartë sesa në një sistem të centralizuar — dhe pikërisht kjo e bën modelimin e dështimeve kaq të rëndësishëm.

### 8.1 Llojet kryesore të dështimeve

- **Crash failure** — një komponent (proces, server, nyje) thjesht **ndalon së punuari** dhe nuk vazhdon më asnjë veprim (nuk përgjigjet fare). Është lloji "më i pastër" i dështimit, sepse komponenti nuk kryen veprime të gabuara — thjesht ndalon.

- **Omission failure** — një mesazh **nuk dërgohet** ose **nuk merret**, ndonëse pjesa tjetër e sistemit vazhdon të funksionojë normalisht. Mund të ndodhë ose në anën e dërguesit (mesazhi humbet para se të dalë), ose në anën e marrësit (mesazhi mbërrin por nuk përpunohet).

- **Timing failure** — përgjigja vjen, por **shumë vonë**, jashtë kufijve kohorë të pritur/garantuar (relevante veçanërisht në sistemet sinkrone, ku ekzistojnë kufij kohorë eksplicitë që duhen respektuar).

- **Byzantine failure** (arbitrare) — komponenti sillet në mënyrë **të paparashikueshme**: mund të dërgojë të dhëna të gabuara, kontradiktore, apo edhe me qëllim të keq (p.sh. si pasojë e një sulmi). Ky është lloji më i rëndë dhe më i vështirë për t'u trajtuar i dështimeve, sepse sistemi nuk mund të dijë me siguri nëse informacioni i marrë është i saktë.

**Qëllimi** i studimit të modelit të dështimit është t'i ndihmojë inxhinierët të projektojnë sisteme **më të besueshme dhe tolerante ndaj gabimeve** (fault-tolerant) — sisteme që vazhdojnë të funksionojnë siç duhet (ose të degradojnë me hijeshi) edhe kur ndonjë pjesë e tyre dështon.

### 8.2 Prishjet arbitrare të proceseve dhe kanaleve; "Armiku" dhe kanalet e sigurta

Përtej dështimeve "natyrale" (crash, omission, timing), sistemet e shpërndara duhet të mendohen edhe nga këndvështrimi i **sigurisë**, ku dështimet mund të shkaktohen nga një palë e tretë me qëllim të keq:

- **Proceset dhe kanalet mund të pësojnë heqje ose prishje arbitrare** — pra jo vetëm të humbin/vonojnë mesazhe rastësisht, por të manipulohen qëllimisht.

- **Modeli i "Armikut" (the enemy / adversary):** kur një mesazh `m` udhëton nga procesi `p` te procesi `q` përmes një kanali komunikues, një "armik" (sulmues) mund të ndërhyjë në kanal, të marrë një kopje të mesazhit `m`, dhe/ose ta zëvendësojë atë me një mesazh të falsifikuar `m'` përpara se ai të mbërrijë te `q`. Ky model përfaqëson kërcënimet tipike të sigurisë në rrjet: përgjimin (eavesdropping) dhe manipulimin (tampering) e komunikimit.

- **Kanalet e sigurta (Secure Channels):** për t'u mbrojtur nga "armiku", përdoren kanale të sigurta ndërmjet dy palëve (principalëve) — p.sh. Principali A (me procesin p) dhe Principali B (me procesin q) komunikojnë përmes një **kanali të sigurt**, i cili garanton (zakonisht me anë të kriptografisë) që mesazhet të mos mund të përgjohen apo të ndryshohen pa u zbuluar gjatë transmetimit.

---

## 9. Tipet e Sistemeve të Shpërndara dhe Shembuj Nga Bota Reale

Për të kuptuar këto modele në kontekst praktik, është e dobishme të njihen kategoritë kryesore të sistemeve të shpërndara që hasen sot, si dhe shembuj konkretë të tyre.

### 9.1 Tri kategoritë kryesore

1. **Distributed Computing** (llogaritje të shpërndara) — përfshin cluster-ët, sistemet GRID dhe cloud computing.
2. **Distributed Information Systems** (sisteme informacioni të shpërndara).
3. **Distributed Pervasive Systems** (sisteme të shpërndara gjithëpërfshirëse) — përfshin sistemet P2P, rrjetet e sensorëve (sensor networks), etj.

### 9.2 Lojërat online me shumë lojtarë (Multiplayer)

Shembuj nga jeta e përditshme të sistemeve të shpërndara në kohë reale: Counter-Strike, Shah (online), Biliardo, Fortnite, Minecraft, Apex Legends, Red Dead Redemption, FIFA, etj. Këto kërkojnë sinkronizim shumë të shpejtë ndërmjet shumë klientëve dhe serverëve për të ruajtur një gjendje loje koherente për të gjithë lojtarët njëkohësisht.

### 9.3 Sistemet multimediale të shpërndara

Shembuj: TV live ose e incizuar, sisteme P2P, Skype, telefonia IP, webcasting (media të vazhdueshme — audio/video), Zoom, Google Classroom, Google Meet. Këto sisteme kanë kërkesa të veçanta për vonesë të ulët (low latency) dhe transmetim të vazhdueshëm (streaming) të të dhënave.

### 9.4 Cloud Computing

**Cloud computing** është një stil kompjuterike i bazuar në internet, ku burime të përbashkëta (kompjutimi, ruajtja, softueri, informacioni) u ofrohen përdoruesve dhe pajisjeve **sipas kërkesës**, njësoj siç ofrohet rrjeti elektrik — pa pasur nevojë përdoruesi të dijë apo të menaxhojë vetë infrastrukturën fizike pas tij. Është një model ku burimet janë **dinamikisht të shkallëzueshme** dhe shpesh **të virtualizuara**, të ofruara si shërbim mbi internet.

Sipas përkufizimit të **NIST** (National Institute of Standards and Technology, https://www.nist.gov/), cloud computing karakterizohet nga:

- **5 karakteristika thelbësore:** shërbim sipas kërkesës (*on-demand self-service*), qasje e gjerë në rrjet (*broad network access*), grumbullim burimesh (*resource pooling*), elasticitet i shpejtë (*rapid elasticity*) dhe shërbim i matshëm (*pay-per-use / measured service*).
- **4 modele vendosjeje (deployment models):** Public, Private, Community dhe Hybrid.
- **3 modele shërbimi (service models):** IaaS, PaaS dhe SaaS.

#### Modelet e vendosjes (deployment models)

- **Public Cloud** — shërbim komercial, i hapur për këdo. Shembuj: Amazon AWS, Microsoft Azure, Google App Engine.
- **Community Cloud** — i shpërndarë mes disa organizatave me interesa/nevoja të njëjta. Shembull: Google's "Gov Cloud".
- **Private Cloud** — i shpërndarë brenda vetëm një organizate. Shembull: data center i brendshëm i një kompanie të madhe.
- **Hybrid Cloud** — kombinim i dy ose më shumë prej modeleve të mësipërme.

#### Modelet e shërbimit (service models)

- **Infrastructure as a Service (IaaS)** — ofrohet infrastruktura themelore (serverë virtualë, ruajtje, rrjet), ndërsa klienti menaxhon vetë sistemin operativ dhe aplikacionet mbi të.
- **Platform as a Service (PaaS)** — ofrohet një platformë e gatshme (sistem operativ, mjedis zhvillimi, baza të dhënash), ku klienti vendos vetëm aplikacionin e vet.
- **Software as a Service (SaaS)** — ofrohet vetë aplikacioni i gatshëm për përdorim direkt nga përdoruesi fundor (p.sh. Gmail, Office 365).

### 9.5 Cluster-ët

Cluster-ët janë grupe kompjuterësh të lidhur ngushtë, të karakterizuar nga:
- harduer i standardizuar (rack, blade servers),
- përdorim zakonisht nga **një organizatë e vetme**.

### 9.6 Scaling up dhe Data Center

**Scaling up** (zmadhimi) i referohet rritjes së kapacitetit të sistemit — qoftë duke shtuar burime te një makinë e vetme (scale up/vertical), qoftë duke shtuar më shumë makina (scale out/horizontal).

Një **qendër e të dhënave (Data Center)** moderne mund të jetë tejet e madhe: për shembull, mund të ketë deri në **10,000 rack**, secili me **100 bërthama (cores)** — që do të thotë deri në **1,000,000 core në total**. Data center-at përmbajnë qindra apo mijëra racks të lidhur me rrjete shumë të mëdha e komplekse.

### 9.7 Arkitektura Edge-Server

Arkitektura Edge-Server është një model ku disa serverë të vendosur **në skajet e rrjetit** (edge servers), pra sa më afër fizikisht përdoruesve, përdoren për të ofruar shërbime ose përmbajtje, në vend që çdo kërkesë të udhëtojë deri te një server qendror shpesh shumë larg.

Karakteristikat kryesore:
- Serverët vendosen pranë përdoruesve, në "skajet" e rrjetit.
- Ulet vonesa (*latency*), sepse të dhënat janë fizikisht më afër përdoruesit.
- Shpërndahet ngarkesa nga serveri kryesor te shumë serverë edge.
- Rritet performanca dhe shkallëzueshmëria e përgjithshme e sistemit.

**Shembull përdorimi:** rrjetet CDN (Content Delivery Network), si Cloudflare apo Akamai, përdorin edge servers për të shpërndarë përmbajtje web (video, imazhe, faqe) shumë më shpejt se sikur çdo kërkesë të shkonte te një server qendror i vetëm.

**Shembull i thjeshtë:** një përdorues në Kosovë kërkon një video; kërkesa nuk shkon te serveri kryesor në SHBA, por te një edge server më i afërt në Evropë, i cili e ka tashmë një kopje të videos dhe ia dërgon atë shumë më shpejt.

### 9.8 Teknologjia Blockchain

**Blockchain** (zinxhiri i blloqeve) është një teknologji që ruan informacionin në mënyrë të sigurt dhe **të shpërndarë** në shumë kompjuterë njëkohësisht (jo në një bazë të dhënash qendrore).

- Informacioni ruhet në njësi të quajtura **blloqe** (blocks).
- Çdo bllok lidhet kriptografikisht me bllokun paraardhës, duke formuar një **zinxhir** (chain).
- Kur shtohet një bllok i ri, informacioni i mëparshëm **nuk mund të ndryshohet lehtë** — kjo është vetia e "pandryshueshmërisë" (immutability), thelbësore për besueshmërinë e sistemit.

*Karakteristikat kryesore:*
- **Siguri e lartë** — përdor kriptografi për të mbrojtur të dhënat dhe lidhjet mes blloqeve.
- **I decentralizuar** — asnjë institucion i vetëm nuk e kontrollon rrjetin.
- **Transparent** — transaksionet mund të verifikohen nga çdo pjesëmarrës i rrjetit.

*Fusha përdorimi:* kriptovaluta (p.sh. Bitcoin), kontrata inteligjente (smart contracts), sisteme financiare, gjurmimi i produkteve në zinxhirin e furnizimit (supply chain).

#### Shtimi i një blloku: konsensus i shpërndarë

Sfida themelore e blockchain-it është: kush vendos se cili bllok i ri të shtohet në zinxhir dhe kush validon atë? Ekzistojnë tri qasje kryesore, secila me tradeoff-et e veta:

1. **Zgjidhje e centralizuar** — një entitet i vetëm vendos se cili validues mund të shtojë bllokun tjetër. Kjo është e thjeshtë, por **nuk përputhet** me filozofinë themelore të decentralizimit që qëndron pas idesë së blockchain-it.

2. **Zgjidhje e shpërndarë (me leje / permissioned)** — një grup i përzgjedhur dhe relativisht i vogël serverësh (mund të jenë vetëm disa dhjetëra) arrijnë bashkërisht një **konsensus** për të vendosur se cili validues vazhdon më tej. E rëndësishme: asnjë server individual nuk duhet të jetë domosdoshmërisht i besueshëm, mjafton që **rreth 2/3 e tyre** të sillen sipas specifikimeve, dhe sistemi mbetet i saktë (kjo lidhet me tolerancën ndaj dështimeve bizantine — Byzantine Fault Tolerance).

3. **Zgjidhje e decentralizuar (pa leje / permissionless)** — pjesëmarrësit angazhohen bashkërisht në një proces zgjedhjeje të liderit (leader election); vetëm lideri i zgjedhur lejohet të shtojë një bllok të ri me transaksione të validuara. Zgjedhja e liderit në shkallë të gjerë, në mënyrë të decentralizuar, që të jetë e drejtë, e qëndrueshme dhe e sigurt njëkohësisht, **nuk është aspak e thjeshtë** — kjo është pikërisht sfida që zgjidhin algoritme si Proof-of-Work apo Proof-of-Stake.

---

## 10. Buffering kundrejt Caching

Këto dy koncepte hasen shpesh në sistemet e shpërndara dhe ngatërrohen lehtë, ndaj vlen të qartësohen krahasimisht.

**Buffering** është një teknikë për ruajtjen **e përkohshme** të të dhënave që transmetohen nga një proces dërgues te një proces marrës — në memorien lokale ose në memorien sekondare (disk) — derisa procesi marrës të jetë gati t'i përdorë ato. Kur lexohen të dhëna nga një fajll, ose kur dërgohen mesazhe nëpër rrjet, është më efikase që të dhënat të përpunohen në blloqe (chunks) më të mëdha njëherësh; këto blloqe ruhen përkohësisht në buffer, në memorien e procesit marrës, dhe **bufferi lirohet** menjëherë pasi të dhënat janë konsumuar nga procesi.

**Caching** është një teknikë për **optimizimin e qasjes** në objekte të të dhënave që ndodhen në distancë (remote), duke mbajtur një **kopje** të tyre në memorien lokale ose në memorien sekondare. Qasja në pjesë të objektit të largët përkthehet atëherë në qasje te kopja lokale — më e shpejtë. Ndryshe nga buffering, kopja lokale në cache **mund të ruhet për sa kohë ka hapësirë memorie e disponueshme**, dhe jo domosdoshmërisht vetëm derisa të "konsumohet" një herë; për këtë arsye nevojitet një **algoritëm menaxhimi i cache-it** dhe një **strategji lirimi** (eviction policy, p.sh. LRU) për të vendosur çfarë të mbahet e çfarë të hiqet kur memoria mbushet.

**Dallimi kryesor, në një fjali:**
- *Buffering* = ruajtje e përkohshme **gjatë** vetë procesit të transferimit të të dhënave.
- *Caching* = ruajtje e **kopjeve** të të dhënave për qasje më të shpejtë në të ardhmen (pas transferimit fillestar).

**Avantazhet e caching-ut:**
- Rrit disponueshmërinë duke zvogëluar ngarkesën mbi server.
- Ofron performancë më të mirë duke zvogëluar vonesën (latencën) — të dhëna të kërkuara shpesh gjenden gati, lokalisht.

**Disavantazhi kryesor i caching-ut:**
- Rrezik për **të dhëna të vjetruara (stale data)** — nëse cache-i nuk përditësohet siç duhet, përdoruesi mund të marrë informacion që nuk pasqyron më gjendjen aktuale të burimit origjinal.

---

## 11. Shkallëzueshmëria (Scalability) në Sistemet e Shpërndara

### 11.1 Teknikat për të arritur shkallëzimin

Shkallëzimi (scaling) në sistemet e shpërndara arrihet kryesisht përmes dy strategjive plotësuese:

**1. Ndarja e të dhënave dhe e llogaritjeve nëpër makina të shumta:**
- **Zhvendosja e llogaritjeve te klientët** — pjesë e përpunimit kryhet drejtpërdrejt në pajisjen e klientit (p.sh. Java applets dhe skripte të ndryshme), duke ulur ngarkesën mbi server.
- **Shërbime të decentralizuara emërtimi**, si DNS (Domain Name System).
- **Sisteme të decentralizuara informacioni**, si WWW (World Wide Web).

**2. Replikimi dhe caching-u:**
- Krijimi i kopjeve të të dhënave në makina të ndryshme (replikim).
- Serverë fajllash dhe baza të dhënash të replikuara.
- Webfaqe të pasqyruara (Mirrored Websites) — kopje identike të një sajti në serverë të ndryshëm gjeografikë.
- Cache të uebit — në shfletues dhe në proxy servers.
- Cache i fajllave — në server dhe në klient.

### 11.2 Probleme me shkallëzueshmërinë administrative

Përtej sfidave teknike, shkallëzimi has edhe pengesa **organizative/administrative**, të tilla si politika konfliktuese lidhur me përdorimin (dhe rrjedhimisht pagesën), menaxhimin dhe sigurinë e burimeve kur ato ndahen mes organizatave të ndryshme. Shembuj tipikë:

- **Gridet kompjuterike** (computational grids) — ndarja e burimeve të shtrenjta (fuqi llogaritëse) midis domeneve të ndryshme administrative, secili me rregullat e veta.
- **Pajisje të përbashkëta në shkallë të gjerë** — p.sh. si të kontrollohet, menaxhohet dhe përdoret një radioteleskop i përbashkët, i ndërtuar si një rrjet sensorësh i shpërndarë e i madh.
- **Sisteme për ndarjen e fajllave** (p.sh. bazuar në BitTorrent), ku nuk ka autoritet qendror që kontrollon përmbajtjen apo sjelljen e pjesëmarrësve.
- **Bisedimet peer-to-peer** (p.sh. versionet e hershme të Skype-it), po ashtu pa strukturë administrative qendrore.
- **Transmetim audio i asistuar nga përdoruesit** (p.sh. Spotify në disa nga mekanizmat e tij P2P).

### 11.3 Nga thread-et te sistemet e shpërndara: evoluimi i paralelizmit

Kur mendojmë për paralelizëm dhe komunikim, është e dobishme të shohim si evoluon kompleksiteti kur lëvizim nga niveli më i imët i paralelizmit deri te sistemet plotësisht të shpërndara:

1. **Fije të ndryshme (threads) në të njëjtën bërthamë (core)** — komunikimi është shumë i shpejtë (memorie e përbashkët), por i kufizuar te po ai proces/core.
2. **Bërthama të ndryshme (cores) në të njëjtin CPU** — komunikimi ende përdor memorie të përbashkët, por me pak më shumë vonesë (cache coherence mes core-ve).
3. **CPU të ndryshëm në një sistem multi-procesorësh** — komunikimi kërkon mekanizma më kompleksë (p.sh. bus i përbashkët, memorie e ndarë NUMA).
4. **Makina të ndryshme në një sistem të shpërndarë** — komunikimi bëhet vetëm përmes rrjetit dhe mesazheve, pa memorie fizike të përbashkët; këtu shtohen sfida të reja: vonesa rrjeti, dështime të pjesshme, mungesë e një ore globale, dhe nevoja për protokolle eksplicite komunikimi (siç u shqyrtuan në seksionet e mësipërme).

Pra, sa më larg lëvizim nga "brenda po asaj bërthame" drejt "makina të ndryshme fizike", aq më shumë rritet kompleksiteti i komunikimit dhe aq më shumë humbasim garanci (si memorie e përbashkët, orë e sinkronizuar, etj.) që na duhet t'i zëvendësojmë me mekanizma eksplicitë softuerikë.

### 11.4 Orkestrimi i shkallëzueshëm në Cloud dhe Edge

Një pyetje moderne dhe qendrore për kërkuesit në fushë: si sigurohet që një aplikacion i shpërndarë, i përbërë nga shumë mikroshërbime, të shpërndahet në mënyrë të përshtatshme nëpër të gjitha nyjet e disponueshme, si në Cloud ashtu edhe në Edge?

**Përgjigja: Orkestrimi (i shkallëzueshëm).** Në thelb, nevojitet një sistem global që monitoron vazhdimisht gjendjen e sistemit dhe vendos ku të vendosen llogaritjet (ose mikroshërbimet). Një sistem i tillë orkestrimi duhet të:

- monitorojë vazhdimisht nyjet në cloud dhe në edge,
- vendosë ku duhet të ekzekutohen mikroshërbimet,
- shpërndajë ngarkesën në mënyrë efikase,
- zhvendosë shërbimet kur ka mbingarkesë, dështime, ose vonesa,
- optimizojë performancën, afërsinë me përdoruesin, koston dhe besueshmërinë.

Aplikacioni i shpërndarë menaxhohet kështu nga një mekanizëm global që vendos, në mënyrë inteligjente, se ku të vendosen llogaritjet ose mikroshërbimet në të gjitha nyjet e disponueshme. Në praktikë, kjo zakonisht realizohet me platforma orkestrimi si **Kubernetes** ose zgjidhje të ngjashme, të projektuara posaçërisht për mjedise hibride cloud-edge.

---

## 12. Detyra Numerike — Llogaritje Praktike

Ky seksion mbledh detyrat sasiore që ilustrojnë si aplikohen konceptet e mësipërme përmes formulave dhe llogaritjeve konkrete.

### 12.1 Koha e transmetimit të një mesazhi

**Problemi:** Në një sistem të shpërndarë, një mesazh prej 8 MB dërgohet nga një nyje te tjetra, përmes një rrjeti me shpejtësi 16 Mbps. Të gjendet koha e transmetimit.

**Zgjidhja:**

Shënojmë: koha e transmetimit $T_t$, madhësia e mesazhit $m$, shpejtësia e rrjetit $v$.

$$T_t = \frac{m}{v}$$

Meqë shpejtësia e rrjetit jepet në megabit (Mbps), ndërsa madhësia e mesazhit jepet në megabajt (MB), duhet fillimisht të konvertojmë njësitë — dhe meqë 1 MB = 8 Mb:

$$8 \text{ MB} = 8 \times 8 \text{ Mb} = 64 \text{ Mb}$$

Tani zbatojmë formulën:

$$T_t = \frac{64 \text{ Mb}}{16 \text{ Mbps}} = 4 \text{ sekonda}$$

**Rezultati:** koha e transmetimit është **4 sekonda**.

### 12.2 Speedup dhe Efficiency (shpejtim dhe efikasitet paralelizmi)

**Problemi:** Një detyrë ekzekutohet në 1 kompjuter për 100 sekonda. Kur ekzekutohet në 5 nyje, koha bëhet 25 sekonda. Të gjenden Speedup-i dhe Efficiency-a.

**Zgjidhja:**

Speedup-i mat sa herë më shpejt ekzekutohet detyra kur përdoren më shumë nyje, krahasuar me një nyje të vetme:

$$\text{Speedup} = \frac{\text{koha me 1 nyje}}{\text{koha me } n \text{ nyje}} = \frac{100}{25} = 4$$

Efficiency-a mat sa "efikasisht" përdoren nyjet shtesë — pra sa afër jemi shpejtimit teorik ideal (që do të ishte $n$-fish, në rastin tonë $5\times$, po të mos kishte fare humbje nga koordinimi paralel):

$$\text{Efficiency} = \frac{\text{Speedup}}{n} = \frac{4}{5} = 0.8 = 80\%$$

**Rezultati:** Speedup = **4**, Efficiency = **80%**. Kjo tregon se, ndonëse shpejtimi është domethënës (4×), ai nuk arrin shpejtimin teorik ideal prej 5× — pjesë e kapacitetit "humbet" në koordinim/komunikim mes nyjeve, gjë krejt normale në sistemet reale të shpërndara.

### 12.3 Disponueshmëria totale e sistemit

**Problemi:** Një server ka disponueshmëri $A = 0.95$ (95%). Një sistem përbëhet nga $n = 2$ serverë paralelë (redundantë). Të gjendet disponueshmëria totale e sistemit.

**Arsyetimi:** Nëse serverët punojnë **paralelisht si redundancë** (d.m.th. mjafton që të paktën një nga ta të funksionojë që shërbimi të jetë i disponueshëm), atëherë sistemi dështon vetëm nëse **të dy** serverët dështojnë njëkohësisht. Probabiliteti që një server i vetëm të dështojë është $(1-A)$; probabiliteti që **të dy** të dështojnë njëkohësisht (supozuar dështime të pavarura) është $(1-A)^n$.

**Zgjidhja:**

$$D_t = 1 - (1-A)^n$$

Me $A = 0.95$ dhe $n = 2$:

$$D_t = 1 - (1 - 0.95)^2 = 1 - (0.05)^2 = 1 - 0.0025 = 0.9975$$

**Rezultati:** Disponueshmëria totale e sistemit është $0.9975 = 99.75\%$ — dukshëm më e lartë se disponueshmëria e vetëm 95% të një serveri të vetëm. Ky është pikërisht argumenti themelor pse **redundanca** (serverë të shumtë, replikim) përmirëson ndjeshëm besueshmërinë e sistemeve të shpërndara.

### 12.4 Vonesa totale në modelin Klient-Server

**Problemi:** Një kërkesë klient-server ka: vonesë rrjeti = 20 ms, përpunim në server = 15 ms, kthim përgjigjeje = 20 ms. Të gjendet koha totale.

**Zgjidhja:** Kur hapat ndodhin **në mënyrë sekuenciale** (njëri pas tjetrit), koha totale është thjesht shuma e tyre:

$$\text{Koha totale} = \text{vonesë rrjeti} + \text{përpunim në server} + \text{kthim përgjigjeje}$$
$$\text{Koha totale} = 20 + 15 + 20 = 55 \text{ ms}$$

**Rezultati:** koha totale e një kërkese të plotë klient-server është **55 milisekonda**. Ky lloj llogaritjeje është thelbësor kur projektohen sisteme me kërkesa strikte për kohën e përgjigjes (p.sh. sisteme në kohë reale, ose SLA-të e shërbimeve cloud).

---

## Përmbledhje

- Sistemet e shpërndara studiohen përmes **katër modeleve** plotësuese: **Fizik** (infrastruktura dhe pajisjet), **Arkitekturor** (organizimi logjik i komponentëve), **i Ndërveprimit** (komunikimi dhe koha) dhe **i Dështimit** (gabimet dhe besueshmëria).
- Arkitekturat kryesore softuerike janë **Client-Server**, **Peer-to-Peer**, **Multi-Tier** dhe **Microservices** — secila me tradeoff-et e veta ndërmjet centralizimit, shkallëzimit, sigurisë dhe kompleksitetit të menaxhimit.
- **Middleware-i** është shtresa softuerike që fsheh kompleksitetin e shpërndarjes dhe e bën sistemin të duket i njësuar për përdoruesin, duke ofruar komunikim, siguri, menaxhim kërkesash dhe integrim shërbimesh.
- Komunikimi kuptohet më mirë duke dalluar **entitetet** (kush komunikon: procese, klientë/serverë, nyje, objekte, mikroshërbime) nga **paradigmat** (si komunikojnë: message passing, request-reply, RPC/RMI, message-oriented, publish/subscribe, sinkron/asinkron, streaming).
- Software-i i një sistemi të shpërndarë organizohet në **shtresa**: Hardware → Sistem Operativ (Platform) → Middleware → Aplikacione.
- **Modeli i ndërveprimit** dallon sistemet **sinkrone** (kufij të njohur kohorë) nga ato **asinkrone** (kufij arbitrarë); marrëdhënia **"happened-before"** e Lamport-it lejon renditjen logjike të ngjarjeve edhe pa orë fizike të sinkronizuara, duke lindur konceptet e **ngjarjeve konkurrente** dhe të **orëve logjike**.
- **Modeli i dështimit** klasifikon gabimet në **crash**, **omission**, **timing** dhe **Byzantine (arbitrare)**; siguria shtesë kërkon mbrojtje nga "armiku" në kanal, të realizuar përmes **kanaleve të sigurta**.
- Tema moderne si **Cloud Computing** (IaaS/PaaS/SaaS, Public/Private/Community/Hybrid), **Edge Computing**, **Blockchain** (dhe mekanizmat e konsensusit të centralizuar/të shpërndarë/të decentralizuar) dhe **orkestrimi** (p.sh. Kubernetes) tregojnë si aplikohen këto modele themelore në sisteme reale, në shkallë të gjerë.
- **Buffering** ruan përkohësisht të dhëna gjatë transferimit; **caching** ruan kopje për qasje të shpejtë të ardhshme — me përfitim në disponueshmëri/performancë, por rrezik të dhënash të vjetruara.
- **Shkallëzimi (scaling)** arrihet me ndarje të llogaritjeve dhe me replikim/caching, por përballet edhe me pengesa administrative (politika, pronësi, siguri) përveç atyre teknike.
- Llogaritjet praktike (koha e transmetimit, speedup/efficiency, disponueshmëria, vonesa totale) tregojnë si matëhen sasior performanca dhe besueshmëria e sistemeve të shpërndara.

## Pyetje për vetë-kontroll

1. Cili nga katër modelet (Fizik, Arkitekturor, i Ndërveprimit, i Dështimit) do të përdorje për të analizuar pse një server bie shpesh nga mbingarkesa e rrjetit fizik, dhe pse?
2. Shpjegoni pse arkitektura Peer-to-Peer nuk ka "pikë të vetme dështimi", ndërsa modeli Client-Server ka.
3. Në një arkitekturë microservices, pse çdo mikroshërbim duhet të ketë databazën e vet, në vend që të gjithë të ndajnë të njëjtën databazë qendrore?
4. Çfarë roli luan middleware-i në një sistem të shpërndarë, dhe pse thuhet se ai e "fsheh" shpërndarjen nga përdoruesi?
5. Shpjegoni ndryshimin ndërmjet komunikimit sinkron dhe asinkron, dhe jepni nga një shembull praktik për secilin.
6. Çfarë do të thotë marrëdhënia "happened-before", dhe pse dy ngjarje mund të jenë "konkurrente" (concurrent) në një sistem të shpërndarë?
7. Dallojini katër llojet e dështimeve (crash, omission, timing, Byzantine) me nga një shembull konkret për secilin.
8. Cili është ndryshimi themelor ndërmjet buffering-ut dhe caching-ut?
9. Një mesazh prej 12 MB duhet të dërgohet përmes një rrjeti me shpejtësi 24 Mbps — sa është koha e transmetimit? (Përdorni të njëjtën metodë si në seksionin 12.1.)
10. Nëse një server ka disponueshmëri 0.90 dhe sistemi ka 3 serverë paralelë redundantë, sa është disponueshmëria totale e sistemit?
