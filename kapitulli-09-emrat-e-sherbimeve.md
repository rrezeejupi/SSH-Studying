# Kapitulli 9 — Emrat e Shërbimeve (Name Services)

## Hyrje

Në çdo sistem të shpërndarë, resurset — kompjuterët, shërbimet, objektet remote, fajllat, përdoruesit — duhet të mund të gjenden, të referohen dhe të përdoren nga programe të ndryshme që mund të ndodhen në pjesë të tjera të rrjetit. Për ta bërë këtë në mënyrë praktike, sistemet nuk i referohen resurseve drejtpërdrejt me adresat e tyre "të vërteta" (p.sh. adresa IP), sepse ato janë të vështira për t'u mbajtur mend dhe mund të ndryshojnë me kohën. Në vend të kësaj, përdoren **emra** — string-je të lexueshme nga njerëzit — të cilët më pas **përkthehen (mapohen)** në adresa të kuptueshme për kompjuterët. Ky kapitull shpjegon konceptin e emrave dhe adresave, rolin e shërbimeve të emrave (Name Services), kërkesat që duhet t'i plotësojë një shërbim i tillë, konceptet themelore mbi të cilat funksionon, si dhe shembullin më të njohur të një shërbimi emrash në praktikë: **DNS (Domain Name System)**.

---

## Ueb faqet më të klikuara — një shembull motivues

Për të kuptuar pse na duhet një shërbim emrash, mjafton të shohim se si funksionon aksesimi i faqeve më të vizituara në internet. Ne shkruajmë një emër të lexueshëm (p.sh. `www.google.com`), por në sfond, kompjuteri ynë duhet ta shndërrojë atë emër në një adresë IP numerike përpara se të mund të lidhet me serverin përkatës.

| Ueb faqja            | Adresa IP (shembull) |
|-----------------------|------------------------|
| www.facebook.com      | 173.252.120.6          |
| www.google.com        | 216.58.208.110         |
| www.uni-pr.edu        | 74.208.146.82          |
| www.rks-gov.com       | 104.28.7.99            |
| www.instagram.com     | 54.164.123.247         |
| www.hotmail.com       | 65.55.77.28            |
| www.wikipedia.com     | 91.198.174.192         |
| www.gmail.com         | 216.58.208.101         |

Vërejmë menjëherë dy gjëra:

1. **Askush nuk i mban mend adresat IP** — të gjithë mbajmë mend `www.google.com`, jo `216.58.208.110`.
2. **Adresat mund të ndryshojnë** — një kompani mund ta zhvendosë shërbimin e saj në një server tjetër (pra me IP tjetër), pa e ndryshuar emrin që përdoruesit e njohin. Kjo tregon pse ndarja mes "emrit" dhe "adresës" është thelbësore për fleksibilitetin e sistemeve të shpërndara.

Ky është pikërisht problemi që e zgjidh **shërbimi i emrave (name service)**: ai mban dhe menaxhon lidhjen (mapimin) mes emrave të lexueshëm dhe adresave përkatëse, si dhe e përditëson atë lidhje kur adresat ndryshojnë — pa e prishur përvojën e përdoruesit.

---

## Emrat dhe adresat e shërbimeve

Në sisteme të shpërndara bëjmë dallim të qartë mes dy koncepteve themelore:

### Emri (Name)

Emri është **një varg bitesh (string) që i referohet një entiteti** — pra emri identifikon "çfarë" është diçka, jo "ku" gjendet.

- Formati i emrit është i tillë që të jetë **i lehtë për t'u kuptuar, lexuar dhe mbajtur mend** nga njerëzit.
- Shembull: `google.com`.

### Adresa (Address)

Adresa është **një varg bitesh që përmban informacion lokacioni** — pra tregon "si arrihet" objekti, në cilin lokacion konkret të rrjetit gjendet.

- Formati i adresës është i tillë që të **procesohet lehtë nga kompjuterët** (numra, jo tekst i lexueshëm nga njeriu).
- Shembull: `216.58.206.174` (adresa IP e google.com).

### Pse bëhet kjo ndarje?

Në sistemet e shpërndara, emrat përdoren për t'iu referuar shumë llojeve të resurseve: **kompjuterëve, shërbimeve, objekteve remote, fajllave dhe përdoruesve**. Ndarja mes emrit (identitet i qëndrueshëm, i lexueshëm) dhe adresës (lokacion konkret, teknik) e bën të mundur që:

- Njerëzit të punojnë me emra të kuptueshëm, ndërsa
- Sistemi (në sfond) të kujdeset për gjetjen e adresës aktuale që i korrespondon atij emri.

Kjo ndarje e emrit nga adresa është baza mbi të cilën ndërtohet çdo **shërbim emrash**.

---

## Emrat e shërbimeve (Name Services)

Në sistemet e shpërndara, **Emri i Shërbimit (Name Service)** është një shërbim i posaçëm, qëllimi kryesor i të cilit është:

> **Të sigurojë emërtime të njëtrajtshme dhe të qëndrueshme të resurseve**, duke u mundësuar programeve ose shërbimeve të tjera që t'i lokalizojnë ato dhe të marrin informacionin e nevojshëm për të bashkëpunuar.

Me fjalë të tjera, name service-i është "libri i adresave" i distribuar i sistemit: kur një program di emrin e një resursi, name service-i i mundëson të gjejë gjithçka tjetër të nevojshme (adresën, portin, atributet) për t'u lidhur me atë resurs.

### Benefitet e një shërbimi të emrave

1. **Lokalizimi i resurseve** — mundëson gjetjen e vendndodhjes aktuale të një resursi duke ditur vetëm emrin e tij.
2. **Emërtim uniform** — resurset e ndryshme (kompjuterë, shërbime, fajlla, përdorues) mund të emërtohen sipas një skeme të njëtrajtshme e të kuptueshme.
3. **Adresa të pavarura nga pajisjet** — emri i një shërbimi mbetet i njëjtë edhe nëse resursi lëviz në një pajisje tjetër fizike; vetëm lidhja (binding) mes emrit dhe adresës përditësohet, jo vetë emri që përdoruesit e njohin.

---

## Kërkesat për emrat e shërbimeve

Një shërbim emrash i mirëprojektuar duhet t'i plotësojë disa kërkesa themelore:

- **Emra të thjeshtë dhe të kuptueshëm** — emrat duhet të jenë të lehtë për t'u shkruar, lexuar dhe mbajtur mend nga njerëzit.
- **Numër infinit (i pakufizuar) i emrave** — sistemi duhet të mund të gjenerojë e menaxhojë sasi praktikisht të pakufizuar emrash, pasi numri i resurseve në një sistem të shpërndarë vazhdimisht rritet.
- **Strukturë** — organizimi i hapësirës së emrave duhet të mundësojë:
  - **Subnete (nën-hapësira) të ngjashme, pa u përplasur** — pjesë të ndryshme të sistemit (p.sh. organizata të ndryshme) duhet të mund të krijojnë emra pa rrezik konflikti me emra të krijuar diku tjetër.
  - **Grupimi i emrave të afërm** — emra që i përkasin të njëjtit kontekst logjik (p.sh. i njëjti domen apo organizatë) duhet të mund të grupohen bashkë, në mënyrë logjike dhe hierarkike.
- **Lejon ri-strukturimin e "pemëve" të emrave** — struktura e emrave duhet të jetë mjaftueshëm fleksibël sa të lejojë riorganizim (p.sh. zhvendosje e një nën-organizate nga një degë e hierarkisë në tjetrën) pa e shkatërruar tërë sistemin.
- **Besueshmëria** — shërbimi i emrave duhet të jetë i qëndrueshëm dhe i disponueshëm vazhdimisht, pasi çdo dështim i tij pengon lokalizimin e praktikisht të gjitha resurseve që varen prej tij.

---

## Konceptet themelore për emrat e shërbimeve

Për të kuptuar funksionimin e brendshëm të një shërbimi emrash, duhen njohur katër koncepte themelore:

### 1. Name space (hapësira e emrave)

Name space-i **definon bashkësinë e emrave të mundshëm dhe lidhjen (relacionin) mes tyre**. Ekzistojnë dy lloje kryesore të strukturimit:

- **Hierarkike** — emrat organizohen si një pemë, ku çdo emër përbëhet nga një varg segmentesh të lidhura hierarkikisht. Shembull tipik: emrat e fajllave në Windows/Unix (p.sh. `/home/user/dokument.txt`), ku çdo nivel i pemës "posedon" nën-emrat brenda vetes. DNS-i, siç do ta shohim më poshtë, është gjithashtu një hapësirë emrash hierarkike.
- **Flat (e sheshtë)** — emrat nuk kanë strukturë të brendshme; çdo emër trajtohet si një njësi e vetme, pa nën-ndarje logjike (p.sh. një numër identifikimi unik).

Organizimi hierarkik është shumë më i përdorur në praktikë sepse lejon shpërndarjen e administrimit (çdo degë e pemës mund të menaxhohet në mënyrë të pavarur) dhe shkallëzueshmëri më të lehtë.

### 2. Bindings (lidhjet)

Binding-u është **mapimi ndërmjet emrave dhe vlerave** (p.sh. mes një emri dhe adresës IP përkatëse, ose mes një emri dhe një grupi atributesh). Kjo lidhje:

- Mund të **implementohet duke përdorur tabela** (p.sh. tabela emër → vlerë), të mbajtura nga name server-at.
- Nuk është domosdoshmërisht statike — një emër mund të lidhet me vlera të ndryshme në momente të ndryshme kohore (p.sh. kur një server zëvendësohet me një tjetër).

### 3. Resolution (zgjidhja/rezolucioni i emrit)

Resolution-i (procesi i "zgjidhjes" së emrit) është **procedura që, kur thirret me një emër si input, kthen vlerën korresponduese** (p.sh. adresën IP). Ky është hapi qendror funksional i çdo name service-i: klienti jep emrin, shërbimi e "zgjidh" atë dhe kthen informacionin e nevojshëm (adresë, port, atribute etj.).

### 4. Name server

Name server-i **specifikon implementimin konkret të një mekanizmi që është i disponueshëm në rrjet** për të kryer resolution-in. Pra, name server-i është entiteti (procesi/serveri) real që ruan tabelat e binding-ut dhe u përgjigjet kërkesave për zgjidhje emrash.

---

## Funksionimi i emrave të shërbimeve

Në praktikë, një klient që di emrin e një shërbimi (p.sh. `www.google.com`) i drejtohet **Naming Service-it**, i cili mban një tabelë të tipit:

| Emri               | IP              | Atribute |
|--------------------|-----------------|----------|
| www.google.com     | 66.102.11.104   | ...      |
| www.hotmail.com    | 100.109.23.104  | ...      |
| ...                | ...             | ...      |

Klienti dërgon një kërkesë me emrin e resursit te Naming Service-i; ky i fundit kërkon në tabelën e vet (ose e drejton kërkesën tek një name server tjetër, siç do të shohim te navigimi) dhe kthen adresën IP (dhe eventualisht atribute shtesë) përkatëse. Vetëm pasi ta ketë marrë këtë adresë, klienti mund të lidhet drejtpërdrejt me resursin (serverin) e synuar.

Ky proces është themeli mbi të cilin ndërtohet e gjithë komunikimi në internet: **askush nuk komunikon drejtpërdrejt me emra — çdo komunikim real ndodh mbi adresa, të cilat merren përmes një procesi resolution-i.**

---

## Qasja në një resurs përmes URL-së

Le ta ndjekim hap pas hapi procesin e plotë të aksesimit të një resursi ueb, duke filluar nga një URL konkrete:

```
http://www.cdk3.net:8888/WebExamples/earth.html
```

Ky proces kalon nëpër disa hapa të njëpasnjëshëm:

1. **URL-ja fillestare**: `http://www.cdk3.net:8888/WebExamples/earth.html`. Kjo përmban emrin e host-it (`www.cdk3.net`), portin (`8888`) dhe rrugën (pathname) drejt fajllit (`WebExamples/earth.html`).

2. **DNS lookup** (kërkimi në DNS): emri `www.cdk3.net` i dërgohet sistemit DNS, i cili e "zgjidh" atë emër dhe kthen **Resource ID**-në — kombinimin e (numrit IP, numrit të portit, dhe pathname-it):
   - Numri IP: `138.37.88.61`
   - Porti: `8888`
   - Pathname: `WebExamples/earth.html`

3. **ARP lookup** (Address Resolution Protocol): pasi është marrë adresa IP, kjo duhet të përkthehet më tej në një **adresë të rrjetit fizik (Ethernet)** — pra adresën MAC të pajisjes brenda rrjetit lokal (p.sh. `2:60:8c:2:b0:5a`) — për t'u mundësuar dërgimin real të paketave në shtresën e lidhjes së të dhënave.

4. **Lidhja (Socket)**: duke pasur tashmë adresën e rrjetit (Ethernet), krijohet një **socket** — kanali i komunikimit — mes klientit dhe **web server-it**, përmes të cilit dërgohet kërkesa HTTP dhe merret fajlli i kërkuar (`file`).

Ky proces tregon qartë se aksesimi i një resursi "me emër" (URL) është në thelb një **zinxhir resolution-esh** — nga emri i lexueshëm nga njeriu, deri te adresa IP, deri te adresa fizike e rrjetit, deri te lidhja aktuale me serverin.

---

## Navigimi

**Navigimi** është akti që **lidh shumë emra shërbimesh njëri me tjetrin**, në mënyrë që të zgjidhet një emër i veçantë për resursin korrespondues. Me fjalë të tjera, kur name space-i është hierarkik dhe shërbimi i mbahet nga shumë name server-a të ndryshëm (secili përgjegjës për një pjesë të hapësirës së emrave), procesi i gjetjes së emrit final kalon nëpër disa server-a radhazi — ky proces quhet navigim.

### Llojet e navigimit

Ekzistojnë tri strategji kryesore se si klienti dhe name server-at bashkëpunojnë për të kryer resolution-in kur ai kërkon kalimin nëpër shumë server-a:

#### 1. Navigimi Iterativ (Iterative Navigation)

- Klienti i drejtohet **serverit të parë (NS1)** me kërkesën e tij.
- NS1 **nuk e njeh** vetë përgjigjen e plotë, prandaj **e kthen klientit adresën e serverit tjetër** (NS2) që mund ta ndihmojë më tej.
- Klienti pastaj i drejtohet vetë **NS2**, i cili sërish mund t'i kthejë adresën e një serveri tjetër (NS3).
- Klienti vazhdon në këtë mënyrë, duke iu drejtuar vetë çdo serveri të ri, derisa të marrë përgjigjen përfundimtare.
- **Karakteristikë kyçe**: klienti është ai që "lundron" në mënyrë aktive nëpër server-a — çdo hap i ndërmjetëm kthehet te klienti, i cili vendos vetë hapin tjetër.

#### 2. Navigimi Jo-rekursiv i kontrolluar nga serveri (Non-recursive, server-controlled)

- Klienti i drejtohet NS1.
- Në vend që t'i kthejë klientit adresën e serverit tjetër, **vetë NS1 e kontakton NS2** (dhe eventualisht NS3) në emër të klientit, duke i "kaluar" kërkesën nga një server te tjetri.
- Përgjigja përfundimtare më pas kthehet mbrapsht te klienti nëpër të njëjtin zinxhir server-ash.
- **Karakteristikë kyçe**: server-at komunikojnë mes tyre pa e përfshirë klientin në çdo hap — por procesi ende zhvillohet "hap pas hapi", i drejtuar nga server-i, jo nga vetë origjina si një thirrje e vetme rekursive e plotë.

#### 3. Navigimi Rekursiv i kontrolluar nga serveri (Recursive, server-controlled)

- Klienti i drejtohet **vetëm një herë** NS1-it, me kërkesën fillestare.
- NS1, nëse nuk e ka vetë përgjigjen, e **thërret rekursivisht** NS2-in (dhe ky, nëse duhet, NS3-in), duke pritur secilën përgjigje para se të përgjigjet vetë.
- Kur NS1 e merr më në fund përgjigjen përfundimtare (pas gjithë zinxhirit të thirrjeve të brendshme), ia **kthen atë drejtpërdrejt klientit** në një përgjigje të vetme.
- **Karakteristikë kyçe**: klienti bën **vetëm një kërkesë** dhe merr **vetëm një përgjigje** — gjithë kompleksiteti i kalimit nëpër server-a të shumtë fshihet nga klienti dhe menaxhohet tërësisht nga vetë server-at.

> **Krahasim praktik**: në navigimin iterativ, klienti bën më shumë punë (disa kërkesa), por server-at kanë më pak ngarkesë procesimi për klient. Në navigimin rekursiv, klienti bën vetëm një kërkesë të thjeshtë, por serveri i parë merr përsipër tërë ngarkesën e koordinimit me server-at e tjerë. DNS-i real në praktikë përdor një kombinim të të dyjave: klientët zakonisht bëjnë kërkesa **rekursive** te resolver-i lokal, ndërsa resolver-i më pas komunikon në mënyrë **iterative** me server-at e tjerë të DNS-it (root, TLD, autoritativ).

---

## Mapimi

Lidhja (binding) mes emrave dhe adresave nuk është domosdoshmërisht një-me-një. Në praktikë ndeshim dy raste tipike:

### Shumë emra → një adresë

Disa emra të ndryshëm mund të mapohen (të përkthehen) në **të njëjtën makinë** (të njëjtën adresë IP). Shembull: `www.google.com` dhe `youtube.com` mund të mapojnë në të njëjtën infrastrukturë/makinë (p.sh. kur i njëjti ofrues i shërbimit i strehon shumë domene në të njëjtin server ose grup serverash).

### Një emër → shumë adresa

E kundërta gjithashtu është e mundur: një emër i vetëm mund të mapojë në **shumë makina të ndryshme**. Shembull: `www.yahoo.com` mund të mapojë në shumë servera të ndryshëm — kjo është teknikë e zakonshme për **balancim ngarkese (load balancing)** dhe **tolerancë ndaj gabimeve (fault tolerance)**: kur një server nuk përgjigjet ose është i mbingarkuar, kërkesa mund të shkojë te një tjetër me të njëjtin emër.

Ky fleksibilitet i mapimit "shumë-me-shumë" tregon edhe një herë pse ndarja mes emrit dhe adresës është kaq e rëndësishme: emri mbetet i qëndrueshëm dhe i kuptueshëm, ndërsa mapimi te adresa (ose adresat) konkrete mund të ndryshohet lirisht nga administratorët, sipas nevojave operacionale, pa ndikuar te përdoruesit.

---

## DNS (Domain Name System)

**DNS paraqet strukturën e të gjithë emrave në internet.** Është shembulli më i madh dhe më i njohur i një shërbimi emrash në praktikë, dhe zbaton pikërisht konceptet e trajtuara më lart: name space hierarkik, bindings, resolution, dhe name server-a të shpërndarë.

### Struktura hierarkike e DNS-it

DNS-i organizohet si një **pemë (hierarki)**, ku çdo nivel përfaqëson një "domen":

- Në **rrënjë (root)** të pemës qëndron domeni bosh (".") i paemërtuar.
- Në nivelin e parë poshtë rrënjës gjenden **domenet e nivelit të lartë (Top-Level Domains — TLD)**, si p.sh.:
  - `arpa` (përdoret për resolution të kundërt, adresa → emër)
  - `com` (organizata komerciale)
  - `edu` (institucione arsimore)
  - `net`
  - `uk`, `fr` (domene kombëtare/gjeografike)
  - etj.
- Nën secilin TLD gjenden **nën-domene** të organizatave specifike, p.sh. nën `com` gjendet `yahoo`, nën `edu` gjenden `mti` dhe `uni-pr`, e kështu me radhë (p.sh. `rks-gov`).
- Nën këto nën-domene, organizatat mund të krijojnë nivele shtesë (p.sh. `cs` si nën-domen i `uni-pr` për departamentin e shkencave kompjuterike) dhe përfundimisht **hostet** konkrete, si `www` (serveri ueb i asaj organizate).

Kështu, një emër i plotë DNS, si `www.cs.uni-pr.edu`, lexohet nga e djathta në të majtë si një shteg (path) nëpër pemë: `edu` → `uni-pr` → `cs` → `www`.

Kjo strukturë hierarkike i plotëson pikërisht kërkesat e diskutuara më sipër për një name service të mirë: lejon **grupimin e emrave të afërt** (të gjitha nën-domenet e `uni-pr.edu` grupohen bashkë), shmang **përplasjet e emrave** (dy organizata të ndryshme mund të kenë të dyja një host `www`, pasi ai është i kualifikuar plotësisht nga domeni prind), dhe lejon **administrim të decentralizuar** — çdo organizatë e menaxhon vetë degën e vet të pemës.

### Si punon DNS — procesi hap pas hapi

Kur një aplikacion (p.sh. shfletuesi ueb) ka nevojë të zgjidhë një emër si `www.uni-pr.edu` në adresën e tij IP, ndiqet ky proces:

1. **Kërkesa e klientit**: aplikacioni ia dërgon emrin një **DNS resolver-i** lokal (zakonisht i konfiguruar te kompjuteri i përdoruesit ose te ofruesi i internetit).

2. **Kontrolli i cache-it lokal**: resolver-i fillimisht kontrollon nëse e ka tashmë përgjigjen të ruajtur në **cache** (nga një kërkesë e mëparshme). Nëse po, e kthen menjëherë përgjigjen, pa kontaktuar rrjetin — kjo e përshpejton ndjeshëm procesin dhe zvogëlon ngarkesën mbi serverat DNS.

3. **Pyetja te root server-i**: nëse përgjigja nuk gjendet në cache, resolver-i i drejtohet një **root server-i** DNS (server-i i nivelit më të lartë të pemës). Root server-i nuk e njeh adresën konkrete, por i tregon resolver-it se cili **TLD server** (p.sh. server-i për `.edu`) është përgjegjës për vazhdimin e kërkimit.

4. **Pyetja te TLD server-i**: resolver-i i drejtohet më pas TLD server-it përkatës (p.sh. server-it të `.edu`), i cili nga ana e vet e drejton resolver-in te **name server-i autoritativ** i domenit specifik (p.sh. te name server-i i `uni-pr.edu`).

5. **Pyetja te serveri autoritativ**: name server-i autoritativ i domenit `uni-pr.edu` e mban vetë mapimin final (binding-un) mes `www.uni-pr.edu` dhe adresës së tij IP reale, dhe ia kthen këtë përgjigje resolver-it.

6. **Kthimi i përgjigjes te klienti**: resolver-i ia kthen adresën IP të gjetur aplikacionit që bëri kërkesën fillestare, dhe njëkohësisht **e ruan atë në cache** për një kohë të caktuar (Time To Live — TTL), për t'i shërbyer më shpejt kërkesave të ardhshme për të njëjtin emër.

7. **Lidhja me resursin**: tani që aplikacioni e ka adresën IP, mund të vazhdojë me procesin e njohur më sipër (ARP lookup për adresën fizike, krijimi i socket-it, dhe komunikimi real me serverin).

### Vërejtje mbi strategjinë e navigimit në DNS

Ky proces zbaton në praktikë llojet e navigimit të diskutuara më sipër:

- Komunikimi mes **klientit dhe resolver-it lokal** është zakonisht **rekursiv** — klienti bën një kërkesë të vetme dhe pret një përgjigje të vetme (finale), pa u marrë vetë me detajet e kalimit nëpër root, TLD dhe server-a autoritativë.
- Komunikimi mes **resolver-it dhe server-ave të tjerë të DNS-it** (root → TLD → autoritativ) është zakonisht **iterativ** — resolver-i vetë i drejtohet radhazi secilit server, duke marrë çdo herë vetëm "drejtimin" për hapin tjetër, derisa të arrijë përgjigjen përfundimtare.

Kjo ndarje e roleve e bën DNS-in efikas: klientët e thjeshtë (kompjuterët, telefonat) nuk kanë nevojë të njohin strukturën e brendshme të DNS-it, ndërsa resolver-at (të cilët janë të specializuar e shpesh e ruajnë informacionin në cache) kujdesen për gjithë kompleksitetin e navigimit nëpër hierarkinë e server-ave.

---

## Përmbledhje

- Në sistemet e shpërndara bëjmë dallim mes **emrit** (string i lexueshëm nga njeriu, që identifikon "çfarë" është një entitet) dhe **adresës** (string i procesueshëm nga kompjuteri, që tregon "ku" gjendet ai entitet).
- **Shërbimi i emrave (Name Service)** siguron emërtim uniform dhe të qëndrueshëm të resurseve, duke i lejuar programet t'i lokalizojnë ato dhe të marrin informacionin e nevojshëm — duke ofruar lokalizim të resurseve, emërtim uniform dhe pavarësi nga pajisja fizike.
- Një shërbim i mirë emrash duhet të ofrojë emra të thjeshtë, numër praktikisht të pakufizuar emrash, strukturë që shmang përplasjet dhe lejon grupim e ri-strukturim, si dhe besueshmëri të lartë.
- Konceptet themelore janë: **name space** (hierarkik ose flat), **bindings** (mapimi emër–vlerë, shpesh si tabela), **resolution** (procedura e zgjidhjes së emrit në vlerë), dhe **name server** (implementimi konkret në rrjet).
- Aksesimi i një resursi përmes një **URL**-je kalon nëpër disa hapa resolution-i: URL → DNS lookup (Resource ID: IP, port, pathname) → ARP lookup (adresa fizike/Ethernet) → lidhja (socket) me serverin.
- **Navigimi** është procesi i lidhjes së shumë emrave/server-ash për të gjetur resursin final, dhe ka tri variante: **iterativ** (klienti drejton çdo hap), **jo-rekursiv i kontrolluar nga serveri** (server-at komunikojnë mes tyre hap pas hapi), dhe **rekursiv i kontrolluar nga serveri** (klienti bën vetëm një kërkesë, gjithçka tjetër fshihet nga ai).
- **Mapimi** emër–adresë nuk është domosdoshmërisht një-me-një: mund të ketë **shumë emra për një adresë** (p.sh. disa domene në të njëjtin server) ose **një emër për shumë adresa** (p.sh. për balancim ngarkese).
- **DNS** është shembulli më i njohur i një name service: organizohet si pemë hierarkike (root → TLD si `.com`, `.edu`, `.net` → domene organizatash → hoste), dhe resolution-i kryhet përmes një zinxhiri kërkesash (resolver → root → TLD → server autoritativ), duke kombinuar kërkesa rekursive (klient–resolver) me kërkesa iterative (resolver–server-a të tjerë), me përdorim të cache-it për efikasitet.

---

## Pyetje për vetë-kontroll

1. Cili është dallimi themelor mes një **emri** dhe një **adrese** në kontekstin e sistemeve të shpërndara? Jepni nga një shembull për secilin.
2. Cilat janë benefitet kryesore që ofron një shërbim i emrave (Name Service)?
3. Numëroni dhe shpjegoni shkurtimisht katër kërkesat themelore që duhet t'i plotësojë një shërbim i mirë emrash.
4. Çka është një **name space** dhe cilat janë dy llojet kryesore të organizimit të tij? Jepni shembuj për secilin.
5. Shpjegoni dallimin mes **binding**-ut dhe **resolution**-it.
6. Përshkruani hapat që kalon një klient nga shkrimi i një URL-je deri te lidhja aktuale me web server-in (përfshirë DNS lookup dhe ARP lookup).
7. Krahasoni tri llojet e navigimit: iterativ, jo-rekursiv i kontrolluar nga serveri, dhe rekursiv i kontrolluar nga serveri. Cili prej tyre e ngarkon klientin më shumë me punë, dhe cili e fsheh më shumë kompleksitetin nga klienti?
8. Jepni nga një shembull praktik për mapimin "shumë emra → një adresë" dhe "një emër → shumë adresa".
9. Përshkruani strukturën hierarkike të DNS-it, duke filluar nga rrënja e deri te një host konkret si `www.uni-pr.edu`.
10. Shpjegoni procesin hap pas hapi se si funksionon një kërkim (lookup) DNS, duke përfshirë rolin e cache-it, root server-ëve, TLD server-ëve dhe server-ëve autoritativë.
