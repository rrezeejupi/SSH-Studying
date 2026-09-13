# Kapitulli 8 — Ueb Shërbimet (Web Services)

> **Lënda:** Sistemet e Shpërndara
> **Ligjërues:** Prof. Dr. Isak Shabani

## Përmbajtja

1. [Çka janë Ueb Shërbimet](#çka-janë-ueb-shërbimet)
2. [Evolucioni i Ueb Shërbimeve](#evolucioni-i-ueb-shërbimeve)
3. [Komponentët e Ueb Shërbimeve: SOAP, WSDL dhe UDDI](#komponentët-e-ueb-shërbimeve-soap-wsdl-dhe-uddi)
4. [Modeli i Ueb Shërbimeve](#modeli-i-ueb-shërbimeve)
5. [Arkitektura e kërkesës të një Ueb Shërbimi](#arkitektura-e-kërkesës-të-një-ueb-shërbimi)
6. [Zbatimi dhe ndërlidhja e Ueb Shërbimeve](#zbatimi-dhe-ndërlidhja-e-ueb-shërbimeve)
7. [Koha e përgjigjes në funksion të ngarkesës](#koha-e-përgjigjes-në-funksion-të-ngarkesës)
8. [Modeli i sigurisë së Ueb Shërbimeve](#modeli-i-sigurisë-së-ueb-shërbimeve)
9. [Ueb Shërbimet dhe standardet](#ueb-shërbimet-dhe-standardet)
10. [Teknologjia e Ueb Shërbimeve](#teknologjia-e-ueb-shërbimeve)
11. [Si funksionojnë Ueb Shërbimet](#si-funksionojnë-ueb-shërbimet)
12. [Zbulimi i Ueb Shërbimit](#zbulimi-i-ueb-shërbimit)
13. [Relacionet ndërmjet Ueb Shërbimeve](#relacionet-ndërmjet-ueb-shërbimeve)
14. [Tregu i Ueb Shërbimeve](#tregu-i-ueb-shërbimeve)
15. [Modelet arkitekturale të Ueb Shërbimeve](#modelet-arkitekturale-të-ueb-shërbimeve)
16. [Komunikimi ndërmjet aplikacioneve në platforma të ndryshme](#komunikimi-ndërmjet-aplikacioneve-në-platforma-të-ndryshme)
17. [REST Ueb Shërbimet](#rest-ueb-shërbimet)
18. [Ndërtimi i një Ueb Shërbimi RESTful — shembull praktik](#ndërtimi-i-një-ueb-shërbimi-restful--shembull-praktik)
19. [Siguria e Ueb Shërbimeve REST](#siguria-e-ueb-shërbimeve-rest)
20. [Dallimi ndërmjet REST dhe SOAP](#dallimi-ndërmjet-rest-dhe-soap)
21. [Trendet e evoluimit të teknologjive të integrimit](#trendet-e-evoluimit-të-teknologjive-të-integrimit)
22. [Rast studimi: sistemi e-Biblioteka FIEK–SEMS](#rast-studimi-sistemi-e-biblioteka-fiek–sems)
23. [Përmbledhje](#përmbledhje)
24. [Pyetje kontrolli](#pyetje-kontrolli)

---

## Çka janë Ueb Shërbimet

**Ueb Shërbimet (Web Services)** janë një teknologji e shpërndarë që realizon ndërveprim dhe bashkëpunim ndërmjet **ofruesve të shërbimit** dhe **klientëve**, nëpërmjet rrjetit (zakonisht Internetit). Në thelb, ato janë komponentë softuerikë që u lejojnë aplikacioneve të ndryshme, të shkruara në gjuhë të ndryshme dhe të vendosura në platforma të ndryshme, të "flasin" me njëra-tjetrën sipas rregullave të përbashkëta e të standardizuara.

Karakteristikat kryesore të Ueb Shërbimeve janë:

- **Njësi të vogla kodi** — secili shërbim kryen një numër të kufizuar, të mirëpërcaktuar detyrash (parimi i granularitetit të vogël dhe përgjegjësisë së qartë).
- **Bazohen në protokolle komunikuese të bazuara në XML** — formati XML shërben si "gjuhë e përbashkët" për shkëmbimin e të dhënave.
- **Pavarësi nga platforma** — janë të pavarura nga sistemi operativ dhe nga gjuha programuese me të cilën janë zhvilluar klienti dhe serveri.
- **Mundësojnë shkëmbimin e të dhënave** ndërmjet aplikacioneve heterogjene, pa marrë parasysh se si janë ndërtuar ato nga brenda.

Arkitektura funksionale bazë e Ueb Shërbimeve përshkruhet nga tre role që bashkëveprojnë përmes mesazheve XML: **klientët**, **XML Ueb Shërbimet** (si ndërmjetës/ndërfaqe) dhe **serverat** që ofrojnë funksionalitetin real.

> **Përkufizim i thjeshtuar:** Ueb Shërbimi është një **ndërfaqe** e vendosur ndërmjet kodit të aplikacionit dhe përdoruesit (apo aplikacionit tjetër) që e shfrytëzon atë kod. Ai lejon qasje në funksione në largësi (remote) përmes Internetit, pa qenë nevoja që klienti të dijë detaje se si është implementuar funksioni nga brenda.

---

## Evolucioni i Ueb Shërbimeve

Zhvillimi historik i Ueb-it mund të kuptohet përmes tre fazave (dimensioneve) që janë shtuar njëra pas tjetrës:

| Faza | Përshkrimi |
|---|---|
| **Connectivity (Lidhshmëria)** | Faza fillestare — thjesht lidhja e kompjuterëve në rrjet. |
| **Presentation (Prezantimi)** | Ueb-i si mjet për **shfletim** (*Browse the Web*) — faqe statike apo dinamike që lexohen nga njerëzit. |
| **Programmability (Programueshmëria)** | Ueb-i shndërrohet në platformë ku aplikacionet **programojnë dhe komunikojnë automatikisht** me njëri-tjetrin (*Program the Web*) — kjo është faza ku lindin Ueb Shërbimet. |

Pra, evolucioni shkon nga një Ueb që **lexohet nga njerëzit** (dokumente HTML të lidhura me hiperlidhje) drejt një Ueb që **përdoret nga programet**, ku aplikacione të ndryshme thërrasin funksione të njëra-tjetrës në mënyrë të automatizuar, pa ndërhyrje njerëzore në çdo hap.

---

## Komponentët e Ueb Shërbimeve: SOAP, WSDL dhe UDDI

Arkitektura klasike e Ueb Shërbimeve (e njohur edhe si "stack-u SOAP") ndërtohet mbi katër komponentë themelorë, secili me rol të veçantë:

- **XML** — paraqet **formatin e shënimeve** (të dhënave) që bartet nga një aplikacion në tjetrin. Është "gjuha" e përbashkët mbi të cilën ndërtohen të tre komponentët e tjerë.
- **SOAP (Simple Object Access Protocol)** — është protokolli i thjeshtë, i bazuar në XML, që u mundëson aplikacioneve **shkëmbimin e informacioneve** përmes HTTP (ose protokolleve tjera).
- **WSDL (Web Services Description Language)** — bazohet gjithashtu në XML dhe shërben për **përshkrimin** e Ueb Shërbimit: çfarë funksionaliteti ofron, si komunikohet me të dhe ku mund të qaset (adresa).
- **UDDI (Universal Description, Discovery and Integration)** — siguron mekanizmin për **regjistrimin, kategorizimin dhe gjetjen** e Ueb Shërbimeve që ofrohen në treg.

Në vijim shpjegohet secili komponent në detaje.

### SOAP

**SOAP** është protokoll komunikimi ndërmjet aplikacioneve, i bazuar në XML, i cili u mundëson aplikacioneve të komunikojnë edhe përmes Internetit. Në teknologjinë e Ueb Shërbimeve, SOAP qëndron si **protokolli i standardizuar** për paketimin e porosive (mesazheve) që shkëmbehen ndërmjet aplikacioneve.

**Prejardhja:** SOAP është propozuar fillimisht nga një konsorcium kompanish — Microsoft, UserLand, DevelopMentor, IBM, Ariba, Commerce One, Compaq, HP, IONA, Lotus, SAP — dhe u dorëzua në W3C në maj të vitit 2000. Më 9 janar 2001, W3C publikoi draftin e parë publik të versionit SOAP 1.2, të hartuar nga grupi punues i XML Protocol; specifikimi zyrtar gjendet në `http://www.w3.org/TR/soap12/`.

**Motivimi për krijimin e SOAP-it:** Shumë aplikacione të shpërndara komunikonin më parë duke përdorur **RPC (Remote Procedure Call)** ndërmjet objekteve të shpërndara, si **DCOM** dhe **CORBA**. Problemi ishte se:

1. HTTP nuk ishte i dizajnuar posaçërisht për këto objekte, prandaj thirrjet RPC nuk ishin të lehta për t'u përdorur mbi Internet.
2. Metodat RPC hasnin në probleme sigurie — shumica e firewall-eve dhe proxy-ve i bllokonin, sepse përdornin porte jo-standarde.
3. HTTP, në anën tjetër, përkrahej nga të gjithë shfletuesit dhe serverat e Internetit.

Për këto arsye, SOAP u krijua si një protokoll i thjeshtë, bazuar në HTTP dhe XML, që mund ta zëvendësonte RPC-në klasike në një mjedis të hapur si Interneti.

**Karakteristikat kryesore të SOAP-it:**

- Është protokoll komunikimi kërkesë/përgjigje (dhe mund të mbështesë edhe multicast e forma të tjera).
- Është projektuar për t'u përdorur mbi HTTP, por nuk kufizohet vetëm në të — mund të transportohet edhe nëpërmjet SMTP, FTP, TCP etj.
- Nuk është i lidhur me ndonjë komponent apo teknologji specifike.
- Nuk është i lidhur me ndonjë gjuhë programimi specifike.
- Bazohet plotësisht në XML.
- Është i thjeshtë dhe i zgjerueshëm (extensible).

Pikërisht sepse SOAP-i formatizon dhe përshkruan mesazhet në mënyrë standarde, ai mundëson komunikim ndërmjet dy aplikacioneve pavarësisht arkitekturës dhe gjuhës programuese me të cilën janë ndërtuar. SOAP klasifikohet edhe si dokument XML (i quajtur "SOAP envelope" — zarf SOAP) i cili zakonisht enkapsulohet brenda një protokolli të shtresës më të ulët, më së shpeshti HTTP mbi portin 80 — pikërisht sepse ky port zakonisht nuk bllokohet nga firewall-et.

#### Struktura e zarfit SOAP (SOAP Envelope)

Një mesazh SOAP përbëhet nga një **zarf (envelope)** që ka dy pjesë kryesore:

```
Zarfi SOAP
 ├── Koka SOAP (SOAP Header)     — opsionale
 └── Trupi SOAP (SOAP Body)
       ├── Përmbajtja e mesazhit
       └── Gabimet SOAP (SOAP Fault) — opsionale
```

- **Zarfi SOAP** përshkruan përmbajtjen e mesazhit, mënyrën se si duhet përpunuar, dhe përmban informacionin për destinacionin final të mesazhit.
- **Koka (Header)** përmban të dhëna shtesë rreth mesazhit, por vetë nuk konsiderohet pjesë e "ngarkesës" (payload) të mesazhit. Për shembull, koka mund të përmbajë:
  - informacion për shfrytëzuesin që bën kërkesën (ID e përdoruesit, fjalëkalimi, certifikata X.509 apo të dhëna të tjera identifikuese);
  - informacion rreth transaksionit, gjendjes dhe "rrugëtimit" të mesazhit (kush e ka përpunuar mesazhin SOAP deri në atë pikë).
- **Trupi (Body)** përmban **ngarkesën fitimprurëse** (payload) të mesazhit — pra vetë informacionin që dëshirohet të shkëmbehet — dhe, opsionalisht, seksionin **SOAP Fault**, që përmban informacion rreth gabimeve eventuale gjatë përpunimit.

**Shembull i thjeshtë i një dokumenti SOAP XML** që kërkon çmimin e një libri:

```xml
<env:Envelope>
  <env:Body>
    <m:GetCmimi>
      <Item>Libri 50 Euro</Item>
    </m:GetCmimi>
  </env:Body>
</env:Envelope>
```

**Shembull i një kërkese HTTP POST me SOAP** (kërkim i çmimit të një aksioni):

```
POST /StockQuote HTTP/1.1
Host: www.stocksserver.com
Content-Type: text/xml; charset="utf-8"
Content-Length: nnnn
SOAPAction: "Some-URI"

<env:Envelope xmlns:env="http://www.w3.org/2001/06/soap-envelope">
  <env:Body>
    <m:GetStockQuote xmlns:m="Some-URI"
        env:encodingStyle="http://www.w3.org/2001/06/soap-encoding">
      <symbol>SUNW</symbol>
    </m:GetStockQuote>
  </env:Body>
</env:Envelope>
```

**Shembulli përkatës i përgjigjes HTTP:**

```
HTTP/1.1 200 OK
Content-Type: text/xml; charset="utf-8"
Content-Length: nnnn

<env:Envelope xmlns:env="http://www.w3.org/2001/06/soap-envelope">
  <env:Body>
    <m:GetStockQuoteResponse xmlns:m="Some-URI"
        env:encodingStyle="http://www.w3.org/2001/06/soap-encoding">
      <price>5.00</price>
    </m:GetStockQuoteResponse>
  </env:Body>
</env:Envelope>
```

Vërehet qartë se koka SOAP mund të mbajë informacione shtesë (autentifikim, transaksion), ndërsa trupi mban vetë kërkesën (`GetStockQuote`) apo përgjigjen (`GetStockQuoteResponse`) me të dhënat aktuale.

### WSDL

**WSDL (Web Services Description Language)** është gjuhë e bazuar në XML që shërben për **përshkrimin** e Ueb Shërbimeve. Për të krijuar një Ueb Shërbim, duhet të hartohet një dokument WSDL që e përshkruan atë shërbim. Ky dokument vendoset në server ose publikohet në regjistrin UDDI, në mënyrë që të jetë i gjetshëm nga klientë të mundshëm.

**Procesi i përdorimit:** Klienti i interesuar në një shërbim së pari merr një kopje ose referencë të dokumentit WSDL (p.sh. duke kërkuar në regjistër), më pas e "kupton" kontratën e përshkruar në të, krijon një kërkesë SOAP në bazë të asaj kontrate, dhe e dërgon atë tek serveri.

**Elementet kryesore të një dokumenti WSDL:**

| Element | Roli |
|---|---|
| `definitions` | Definon emrin e shërbimit dhe deklaron namespace-t (hapësirat e emrave) që përdoren në dokument. |
| `types` | Përshkruan tipet e të dhënave (bazuar në XML Schema) që përdoren nga klienti dhe serveri. |
| `message` | Definon emrin e mesazhit request/response dhe elementet përbërëse të tij. |
| `portType` | Definon kombinimin e elementeve të mesazhit në formë operacionesh (input/output) — pra "çfarë operacionesh ofrohen". |
| `binding` | Përcakton specifikat e detajuara se si lidhen (bëhen "bind") mesazhet që transmetohen — p.sh. me cilin protokoll transporti. |
| `service` | Definon adresën (URL-në) konkrete ku është i vendosur/publikuar shërbimi. |

Struktura hierarkike mund të përmblidhet kështu: `definitions` përmban dokumentimin, `types` (bazuar në XML Schema), `message` (input/output), `portType` (operacione input/output) dhe `binding`, ndërsa `service` grupon "port"-et që tregojnë ku aksesohet praktikisht shërbimi.

### UDDI

**UDDI (Universal Description, Discovery and Integration)** ka lindur si bashkëpunim ndërmjet Microsoft, IBM dhe Ariba, me qëllim të ndihmojë adaptimin dhe përdorimin e standardeve të Ueb Shërbimeve.

UDDI është, në thelb, një **regjistër** (registry) që shërben për **ruajtjen dhe gjetjen** e informacioneve mbi Ueb Shërbimet e disponueshme. Regjistri publik i UDDI-së funksionon në mënyrë konceptualisht të ngjashme me **DNS-in** (Internet Domain Name Service) — ashtu siç DNS-i "përkthen" emra në adresa IP, UDDI-ja ndihmon që klientët të "gjejnë" shërbime sipas kritereve të caktuara.

**Arkitektura e UDDI-së** përfshin dy porta kryesore:
- **Porta UDDI për publikim** — përdoret nga ofruesit e shërbimeve për të regjistruar shërbimet e tyre.
- **Porta UDDI për kërkim** — përdoret nga klientët potencialë për të kërkuar shërbime.

Të dyja portat komunikojnë me **Regjistrin e Bizneseve UDDI**, i cili ruan të dhënat në tri kategori kryesore, të organizuara sipas metaforës së "faqeve" telefonike:

1. **Faqet e bardha (White Pages)** — informacione themelore kontaktuese: emri i biznesit, adresa, të dhëna kontaktuese, emri i faqes Ueb dhe numri identifikues i biznesit.
2. **Faqet e verdha (Yellow Pages)** — klasifikimi i biznesit: lloji i biznesit, lokalizimi dhe produktet, lloji i industrisë, numri identifikues i biznesit etj.
3. **Faqet e gjelbërta (Green Pages)** — të dhëna **teknike** për shërbimet e biznesit: si të bashkëveprohet me to, përshkrime të proceseve të biznesit etj. (Këtu përfshihen praktikisht referencat drejt dokumenteve WSDL.)

### Si lidhen SOAP, WSDL dhe UDDI

Tre komponentët nuk funksionojnë të izoluar, por bashkëveprojnë në një cikël logjik:

```
                    UDDI Regjistri
                   ↗              ↖
        (publikon)                (kërkon/gjen)
        ↙                                      ↘
Ana e Shërbimit                          Ana e Aplikacionit
(SOAP procesuesi)  ← HTTP Kërkesa ────  (SOAP klienti)
                   ── HTTP Përgjigje →
                   
          (të dyja palët përdorin dokumentin WSDL
           për ta "kuptuar" kontratën e shërbimit)
```

Në thelb: **UDDI** përgjigjet pyetjes *"ku ta gjej shërbimin?"*, **WSDL** përgjigjet pyetjes *"si duhet ta thërras këtë shërbim?"*, ndërsa **SOAP** është "gjuha" konkrete me të cilën bëhet vetë thirrja dhe merret përgjigjja.

**Përfitimet kryesore të këtyre komponentëve** të kombinuar janë:

- Ekspozimi i funksionalitetit ekzistues në rrjet, pa e rishkruar atë.
- Lidhja e aplikacioneve heterogjene — pra ndërveprimi (interoperability) ndërmjet platformave të ndryshme.
- Përdorimi i një protokolli të standardizuar, të pranuar gjerësisht.
- Kosto e ulët e komunikimit, sepse shfrytëzohet infrastruktura ekzistuese e Internetit (HTTP).

---

## Modeli i Ueb Shërbimeve

Modeli i përgjithshëm i funksionimit të Ueb Shërbimeve ndjek tri hapa logjikë themelorë:

1. **Publikon** — ofruesi i shërbimit e regjistron (publikon) përshkrimin e shërbimit të tij te një "agjension zbulues" (discovery agency), p.sh. një shërbim direktorie.
2. **Gjen** — kërkuesi i shërbimit kërkon dhe **gjen** përshkrimin e shërbimit të duhur në atë agjension.
3. **Lidhet** — pasi ka gjetur shërbimin e përshtatshëm, kërkuesi **lidhet** (bind) me ofruesin dhe realizohet ndërveprimi aktual, tipikisht përmes mesazheve XML/SOAP.

Një shembull ilustrues (nga materiali): një klient që kërkon një taksi përmes celularit dërgon kërkesën te "kërkuesi i shërbimit"; ky bashkëvepron (XML/SOAP) me "ofruesin e shërbimit" (shërbimi i taksisë), i cili nga ana e vet e ka publikuar përshkrimin e shërbimit të tij te një agjension zbulues (p.sh. shërbimi i direktorisë ose reklamimi me "faqe të verdha"). Në këtë mënyrë, tri rolet — **kërkuesi**, **ofruesi** dhe **agjensioni zbulues** — bashkëveprojnë për të mundësuar gjetjen dhe përdorimin dinamik të shërbimit.

---

## Arkitektura e kërkesës të një Ueb Shërbimi

Procesi i plotë, hap pas hapi, i vendosjes dhe përdorimit të një Ueb Shërbimi duke përdorur UDDI-në si direktorium, mund të përshkruhet kështu:

1. **Vendosja e Ueb Shërbimeve duke përdorur UDDI-në** — ofruesi i Ueb Shërbimeve regjistron shërbimin te Direktoriumi i Ueb Shërbimeve (UDDI).
2. **Kthimi i dokumentit UDDI** — direktoriumi i kthen klientit informacionin e regjistruar.
3. **Kërkesa për përshkrimin e Shërbimeve** — klienti kërkon përshkrimin e detajuar (WSDL) të shërbimit të interesit.
4. **Kthimi i WSDL-së për përshkrimin e Shërbimeve** — direktoriumi/ofruesi i kthen klientit dokumentin WSDL.
5. **Qasja e funksionalitetit të Ueb Shërbimeve duke përdorur teknologjinë SOAP** — klienti, tashmë i pajisur me WSDL, dërgon kërkesën aktuale SOAP dhe merr përgjigjen — pra realizon thirrjen e vërtetë të funksionit.
6. **Publikimi i Ueb Shërbimeve duke përdorur UDDI-në** — (nga ana e provajderit) shërbimi vazhdon të mbahet i regjistruar dhe i përditësuar në UDDI.

Aktorët kryesorë në këtë arkitekturë janë **Klienti i Ueb Shërbimeve**, **Direktoriumi i Ueb Shërbimeve** (UDDI) dhe **Provajderi i Ueb Shërbimeve** — tre role që korrespondojnë saktësisht me triadën "kërkues – regjistër – ofrues" të përmendur më lart.

---

## Zbatimi dhe ndërlidhja e Ueb Shërbimeve

Në praktikë, Ueb Shërbimet nuk përdoren të izoluara, por **ndërlidhen** me njëri-tjetrin për të formuar zgjidhje më komplekse. Për shembull, një skenar tipik i udhëtimit mund të përfshijë disa Ueb Shërbime të pavarura që bashkëpunojnë:

- Shërbimi Ueb për **huazim të veturave**,
- Shërbimi Ueb për **hotele**,
- Shërbimi Ueb për **ajroport (aeroport)**,
- Shërbimi Ueb për **shitje elektronike**.

Këto shërbime komunikojnë me njëri-tjetrin dhe me klientë të ndryshëm (përfshirë pajisje me burime të kufizuara, si telefona që përdorin WML) përmes protokolleve të ndryshme, kryesisht **HTML/SOAP/XML** dhe **HTTP/HTML**. Kjo tregon fleksibilitetin e Ueb Shërbimeve: i njëjti "ekosistem" shërbimesh mund t'u shërbejë njëkohësisht klientëve të llojeve shumë të ndryshme (shfletues desktop, aplikacione biznesi, pajisje mobile), sepse çdo klient komunikon me shërbimin përmes një ndërfaqeje të standardizuar, pavarësisht se si është ndërtuar vetë klienti nga brenda.

---

## Koha e përgjigjes në funksion të ngarkesës

Një aspekt shumë i rëndësishëm praktik i Ueb Shërbimeve është **performanca** — sesi koha e përgjigjes (response time) ndryshon në varësi të ngarkesës së shfrytëzuesve (numrit të kërkesave të njëkohshme).

Materiali paraqet një krahasim eksperimental të disa **teknikave të lidhjes/transportit** për një skenar me 50 shfrytëzues, ku ngarkesa varion nga 5% deri në 100%:

- `ASMX`
- `ASMX_SOAPExtn`
- `WS_TCP_Binary`
- `WS_TCP_SOAP`
- `IIS_HTTP_Binary`
- `IIS_HTTP_SOAP`
- `WS_HTTP_Binary`
- `WS_HTTP_SOAP`

**Vërejtja kryesore** që del nga ky lloj eksperimenti është se koha e përgjigjes **rritet me rritjen e ngarkesës**, por shkalla e rritjes ndryshon dukshëm sipas kombinimit teknik të përdorur:

- Variantet **binare** (p.sh. `WS_TCP_Binary`) priren të jenë më efikase se variantet e bazuara në **SOAP/XML**, sepse XML-i sjell kosto shtesë (overhead) për shkak të natyrës së tij tekstuale, të fjalëpasur (verbose) dhe të nevojës për parsim (analizë sintaksore).
- Transporti mbi **TCP** direkt mund të jetë më i shpejtë se ai mbi **HTTP**, sepse HTTP-ja shton shtresa shtesë protokolli.
- Zgjedhja e teknologjisë së saktë të transportit dhe enkodimit (binar kundrejt SOAP/XML) ka ndikim të drejtpërdrejtë në shkallëzueshmërinë (scalability) e sistemit nën ngarkesë të lartë.

Kjo është arsyeja pse, në praktikë, arkitektët e sistemeve zgjedhin me kujdes protokollin e transportit dhe formatin e mesazheve, duke balancuar **ndërveprimin** (që SOAP/XML e ofron mirë) me **performancën** (që formatet binare e ofrojnë më mirë).

---

## Modeli i sigurisë së Ueb Shërbimeve

Siguria e Ueb Shërbimeve mund të adresohet në **tri nivele** kryesore, secili me qasje dhe kompromis të vetin:

### 1. Niveli i sigurisë platformë/transport

Ky nivel arrihet duke ofruar siguri **pikë-për-pikë** (point-to-point), ku vetë kanali transportues ndërmjet dy pikave skajore — Klientit të Ueb Shërbimit dhe vetë Ueb Shërbimit — përdoret për të siguruar komunikimin (p.sh. me anë të TLS/SSL). Ky është niveli më i thjeshtë për t'u implementuar, por siguron vetëm "linjën" e komunikimit, jo domosdoshmërisht vetë mesazhin gjatë gjithë rrugës së tij (p.sh. kur mesazhi kalon nëpër ndërmjetës të shumtë).

### 2. Niveli i sigurisë së aplikacionit

Në këtë nivel, vetë **aplikacioni** merr përsipër sigurinë dhe përdor veçori të përshtatura sipas nevojave të tij. Disa qasje konkrete:

- Aplikacioni mund të përdorë një **kokë të përshtatur SOAP** për të kaluar kredencialet e përdoruesit (p.sh. një "tiketë", emër përdoruesi ose licencë), në mënyrë që çdo Ueb Shërbim ta njohë përdoruesin që bën kërkesën.
- Aplikacioni ka fleksibilitet për të gjeneruar objektin e vet që përmban rolet e nevojshme të autorizimit.
- Aplikacioni mund të **enkriptojë** vetëm pjesët që dëshiron (jo domosdoshmërisht gjithë mesazhin), megjithëse kjo kërkon menaxhim të sigurt të çelësave (memorie të siguruar me çelës) dhe njohuri të thelluara të kriptografisë nga ana e zhvilluesve.
- Një teknikë alternative dhe e zakonshme është përdorimi i **SSL** për konfidencialitet dhe integritet, i kombinuar me kokën e përshtatur SOAP për vetë autentifikimin.

### 3. Niveli i sigurisë së mesazhit

Ky është niveli **më fleksibël dhe më i fuqishëm** për sigurinë e Ueb Shërbimeve, sepse siguria "udhëton" bashkë me vetë mesazhin (jo vetëm me kanalin e transportit), duke mbetur e vlefshme edhe kur mesazhi kalon nëpër shumë ndërmjetës të ndryshëm përpara se të arrijë destinacionin final. Kjo qasje mundëson enkriptim dhe nënshkrim (signature) selektiv të pjesëve të mesazhit, në përputhje me standarde si WS-Security.

> **Krahasim i shpejtë:** Siguria e transportit (niveli 1) është më e thjeshtë por mbron vetëm "linjën"; siguria e mesazhit (niveli 3) është më komplekse për t'u implementuar, por mbron vetë të dhënat pavarësisht rrugës që ato kalojnë — prandaj konsiderohet zgjidhja më e fuqishme për zinxhirë komunikimi me shumë ndërmjetës.

---

## Ueb Shërbimet dhe standardet

Siç u tha edhe më herët, Ueb Shërbimi vepron si një **shtresë abstraksioni** ndërmjet kodit të aplikacionit dhe aplikacionit klient që e përdor atë kod, duke bërë ndarjen (izolimin) nga platforma dhe nga detajet specifike të gjuhës programuese që thërret kodin.

```
Kodi i Aplikacionit  ──[Platforma + gjuha]──►  Ueb Shërbimi  ──[Platforma + gjuha]──►  Aplikacioni i Klientit
```

Kjo shtresë e standardizuar mundëson që funksionet e aplikacionit të aksesohen nga **çdo gjuhë programuese** që mbështet Ueb Shërbimet, pavarësisht se me çfarë gjuhe/platforme është ndërtuar vetë shërbimi nga brenda. Kjo veti — **abstraksioni dhe standardizimi** — është ajo që bën të mundur ndërveprimin (interoperability) real ndërmjet sistemeve heterogjene.

---

## Teknologjia e Ueb Shërbimeve

Teknologjinë e Ueb Shërbimeve mund ta konceptojmë si një **stack shtresash**, ku secila shtresë ndërtohet mbi shtresën poshtë saj:

```
┌────────────────────────────────────────────┐
│  PROCESET: Zbulimi, Agregimi, Koreografia   │
├────────────────────────────────────────────┤
│  PËRSHKRIMET: Përshkrimi i Ueb Shërbimeve   │
│              (WSDL)                          │
├────────────────────────────────────────────┤
│  MESAZHET: SOAP + shtesa                    │
│  (Besueshmëria, Korrelacioni, Transaksionet)│
├────────────────────────────────────────────┤
│  KOMUNIKIMET: HTTP, SMTP, FTP, JMS, IIOP...│
└────────────────────────────────────────────┘
     (mbi teknologji bazë: XML, DTD, Skema)
```

- Në **bazë** qëndron teknologjia themelore: **XML**, **DTD** dhe **skemat** (XML Schema), të cilat përshkruajnë strukturën e vlefshme të dokumenteve.
- Shtresa e **Komunikimeve** përdor protokolle standarde të Internetit (HTTP, SMTP, FTP, JMS, IIOP etj.) si "transportues" fizik të mesazheve.
- Shtresa e **Mesazheve** bazohet në **SOAP**, i pasuruar me shtesa për besueshmëri, korrelacion dhe transaksione.
- Shtresa e **Përshkrimeve** përdor **WSDL** për të përshkruar formalisht se çfarë ofron shërbimi.
- Në **majë** qëndrojnë **Proceset** — funksionalitete më të avancuara si zbulimi (discovery), agregimi (kombinimi i shumë shërbimeve) dhe koreografia (orkestrimi i ndërveprimeve mes shërbimeve).

Kjo strukturë me shtresa tregon qartë ndarjen e përgjegjësive: çdo shtresë zgjidh një problem specifik (transport, formatim mesazhi, përshkrim, orkestrim) dhe mbështetet mbi shtresat poshtë saj, pa u varur nga detajet e implementimit të tyre.

---

## Si funksionojnë Ueb Shërbimet

Një Ueb Shërbim është, në thelb, **një strukturë e bazuar mbi porosi (mesazhe)**. Kërkesa e vetme themelore që i vihet një Ueb Shërbimi është që ai të jetë në gjendje të **dërgojë dhe të pranojë porosi**, duke përdorur kombinime të protokolleve standarde të Internetit.

Forma më e rëndomtë e Ueb Shërbimeve është thirrja e procedurave që punojnë në server, ku logjika e porosisë mund të përmblidhet thjesht si:

> Kërkesa: *"Thirre këtë nën-rutinë me këto argumente."*
> Përgjigjja: *"Ja ku janë rezultatet e nën-rutinës së thirrur."*

Nga ana strukturore, një Ueb Shërbim përbëhet zakonisht nga disa komponentë kryesorë brenda një **Aplikacioni Ueb Server**:

- **Kodi i Aplikacionit** — logjika e vërtetë e biznesit.
- **Serveri Proxy** — ndërmjetëson dhe kthen mesazhet SOAP/XML në thirrje të kodit të aplikacionit (dhe anasjelltas).
- **Shërbimi që dëgjon** (listener) — komponenti që pret dhe pranon kërkesat hyrëse në portin/adresën e caktuar.

### Grupet kryesore për standardizimin e Ueb Shërbimeve

Tri organizata kryesore përcaktojnë standardet e Ueb Shërbimeve:

| Organizata | Roli |
|---|---|
| **W3C** (World Wide Web Consortium) | Forca kryesore pas numrit më të madh të standardeve të pranuara në hapësirën e Ueb Shërbimeve, duke përfshirë edhe HTML-në. |
| **OASIS** (Organization for the Advancement of Structured Information Standards) | Burimi kryesor i specifikimeve nga ka rrjedhur XML-i (versioni bashkëkohor) dhe standardi UDDI. |
| **WS-I** (Web Services Interoperability Organization) | Vepron si grup "pararojë" (pioneer/watchdog) për të siguruar interoperabilitetin dhe funksionimin e drejtë të implementimeve të standardeve të Ueb Shërbimeve. |

---

## Zbulimi i Ueb Shërbimit

**Zbulimi (discovery)** i një Ueb Shërbimi nënkupton **aktin e gjetjes** së përshkrimit të një shërbimi — i cili mund të mos ketë qenë i njohur më parë — por që plotëson kushtet e kërkuara të funksionalitetit. Qëllimi i zbulimit është gjetja e Ueb Shërbimit **sa më adekuat** për nevojën konkrete. Zbulimi mund të jetë **manual** (dikush kërkon me dorë në një katalog) ose **automatik** (agjentë softuerikë e bëjnë kërkimin dhe përzgjedhjen vetë, sipas kritereve).

Procesi i zbulimit ndodh në katër hapa:

1. **Hapi 1:** Ofruesi dhe kërkuesi komunikojnë me njëri-tjetrin (secili "publikon" përshkrimin WSD/WSDL të tij dhe merr atë të palës tjetër).
2. **Hapi 2:** Ofruesi dhe kërkuesi pajtohen për **semantikën** që do të përdorin gjatë komunikimit (pra kuptimin e përbashkët të termave dhe operacioneve).
3. **Hapi 3:** Ofruesi dhe kërkuesi ua dërgojnë **agjentëve** të tyre respektivë përshkrimet WSD dhe semantikën e rënë dakord, që do të përdoren gjatë shkëmbimit të mesazheve.
4. **Hapi 4:** **Agjenti ofrues** dhe **agjenti kërkues** fillojnë shkëmbimin real të mesazheve SOAP, sipas interesit dhe rregullave të përcaktuara nga krijuesit e tyre.

Kjo tregon se zbulimi nuk është thjesht "gjetje e një adrese", por një proces në të cilin dy palë duhet të arrijnë **mirëkuptim të përbashkët** (semantik dhe strukturor) përpara se komunikimi funksional të mund të fillojë.

---

## Relacionet ndërmjet Ueb Shërbimeve

Në arkitekturën e Ueb Shërbimeve, ka rëndësi të madhe edhe **lidhja logjike** (relacioni) ndërmjet vetë shërbimeve.

- Kur themi se **X është në relacion me Y**, kjo përcakton lidhje ndërmjet koncepteve X dhe Y, në atë mënyrë që çdo X është njëkohësisht edhe Y.
- Kur themi se koncepti **X është Y**, nënkuptojmë se çdo veçori e Y është njëkohësisht veçori e X.
- Megjithatë, meqë X mbetet konceptualisht i ndryshëm nga Y, vetitë e X mund të jenë (pjesërisht) të ndryshme nga vetitë e Y.

**Shembull konkret:** Në konceptin e përgjithshëm të "shërbimit", themi se çdo shërbim ka një identifikues. Në konceptin më specifik të "Ueb Shërbimit", themi se çdo Ueb Shërbim e ka identifikuesin e vet në formën e një **URI (Uniform Resource Identifier)**.

Sa herë që krijohet një Ueb Shërbim, supozojmë se atij i shoqërohet automatikisht një identifikues. Prania e këtij identifikuesi na siguron që Ueb Shërbimi ekziston aktualisht — mungesa e tij (ose "zhdukja" e referencës) tregon se shërbimi është shkatërruar/hequr nga qarkullimi.

---

## Tregu i Ueb Shërbimeve

Sipas një analize të komitetit **IDC** (International Data Corporation), tregu global i Ueb Shërbimeve ka njohur rritje shumë të shpejtë gjatë viteve 2000:

| Viti | Vlera e tregut (miliardë €) |
|---|---|
| 2003 | 0.88 |
| 2004 | 1.84 |
| 2005 | 3.60 |
| 2006 | 4.96 |
| 2007 | 6.48 |
| 2008 | 8.16 |
| 2009 | 11.92 |

Pra, nga rreth **0.88 miliardë euro** në vitin 2003, tregu arriti në rreth **11.92 miliardë euro** deri në vitin 2009 — një rritje prej rreth 13 herësh brenda gjashtë vitesh. Kjo trajektore ilustron qartë se sa shpejt Ueb Shërbimet u bënë një komponent qendror i integrimit softuerik ndër-organizativ gjatë asaj periudhe, duke justifikuar edhe investimet e mëdha të industrisë në standarde si SOAP, WSDL dhe UDDI.

---

## Modelet arkitekturale të Ueb Shërbimeve

Arkitektura e Ueb Shërbimeve mund të shikohet nëpërmjet **katër modelesh** të ndryshme, secili i emërtuar në mënyrë që të nxjerrë në pah konceptin kyç përkatës të tërë arkitekturës. Këto modele nuk janë alternativa reciprokisht përjashtuese, por **këndvështrime plotësuese** të të njëjtit sistem.

### Modeli message-oriented (i orientuar nga mesazhi)

Ky model **fokusohet në mesazhet** vetë — strukturën e tyre dhe transportin e tyre — pa u marrë me arsyet pse dërgohen mesazhet apo me kuptimin e thellë të përmbajtjes së tyre.

Elementet themelore të këtij modeli janë:

- **Agjenti** — entiteti që merr dhe dërgon mesazhe (ka "origjinë" dhe kryen "procese").
- **Mesazhi** — ka **trup** (body) dhe **kokë/koka** (header/headers).
- **Transporti i mesazhit** — mekanizmi që e "ofron" (dërgon) mesazhin te destinacioni.

Thelbi i modelit të mesazheve rrotullohet, pra, rreth: (1) agjentit, (2) strukturës së mesazhit (koka + trupi) dhe (3) mekanizmit të shpërndarjes së mesazhit. Duhen marrë parasysh edhe detaje shtesë, si roli i "linjave të veprimit" — pra si duhet të sillen agjentët në nivelet e ndryshme të modelit të mesazheve.

### Modeli service-oriented (i orientuar nga shërbimi)

Ky model **fokusohet në aspektet e shërbimeve** — veprimet, funksionalitetet dhe rolet e agjentëve — më shumë sesa në vetë strukturën teknike të mesazheve.

Pika kyçe: në sistemet e shpërndara, **shërbimet nuk mund të realizohen pa përdorimin e mesazheve**, por e kundërta nuk qëndron — mesazhet nuk kanë nevojë domosdoshmërisht të jenë të lidhur me ndonjë shërbim. Pra, modeli service-oriented "qëndron mbi" modelin message-oriented, duke i shtuar kuptim dhe qëllim.

Konceptet kryesore:

- **Shërbimi** realizohet nga një **agjent** dhe shfrytëzohet nga një **agjent tjetër**.
- Shërbimet janë të "ndërmjetësuara" nga kuptimi i shkëmbimit të mesazheve ndërmjet agjentëve kërkues dhe atyre ofrues (agjenti "sinjalizon" shërbimin dhe shërbimi "përshkruhet" përmes meta-dhënave).
- Një aspekt shumë i rëndësishëm i shërbimeve është lidhja me **botën reale** — shërbimet shpërndahen kryesisht për të ofruar funksionalitet real, jo thjesht abstrakt.
- Ky model prezanton edhe konceptin e **pranuesit të shërbimit** — një person ose organizatë që "posedon/kontrollon" shërbimin dhe ka përgjegjësi ndaj tij.
- Modeli lejon gjithashtu përdorimin e **meta-dhënave** (meta-data) për të përshkruar vetë shërbimin.

### Modeli resource-oriented (i orientuar nga burimi)

Ky model **fokusohet në burimet** (resources) që ekzistojnë dhe që kanë një pronar. Konceptet themelore:

- Çdo **resurs** (burim) ka një **URI** — identifikues unik.
- Një resurs **mund të ketë** një ose më shumë **përfaqësime (reprezentime)** — p.sh. JSON, XML, HTML.
- Resursi **posedohet** (kontrollohet) nga një **Person ose Organizatë**.

> Ky model, siç do të shihet më poshtë, është pikërisht baza konceptuale e stilit arkitekturor **REST**.

### Modeli i politikave zhvilluese (policy-oriented)

Ky model **fokusohet në kushtëzimet** e sjelljes së agjentëve dhe shërbimeve — pra rregullat që kufizojnë ose udhëzojnë veprimet.

- **Politikat** përcaktohen nga një **Person ose Organizatë**, dhe zbatohen ndaj një **subjekti** (agjentit).
- Politikat kanë lidhje me **resurset** (mbi çfarë zbatohen) dhe me **aksionet** (çfarë veprimesh lejohen/kufizohen).
- Politikat zhvilluese hartohen për resurse dhe **aplikohen te agjentët** që tentojnë të qasen në to.
- Ato vendosen ose zhvillohen nga njerëzit që kanë përgjegjësi mbi ato resurse.
- Mund të miratohen për të adresuar çështje sigurie, cilësie (kualiteti), menaxhimi dhe aplikimi të shërbimeve.

> **Përmbledhje e katër modeleve:** *message-oriented* përgjigjet pyetjes "si transmetohet informacioni?", *service-oriented* përgjigjes pyetjes "çfarë funksionaliteti ofrohet dhe nga kush?", *resource-oriented* përgjigjet pyetjes "çfarë të dhënash ekzistojnë dhe si identifikohen?", ndërsa *policy-oriented* përgjigjet pyetjes "çfarë rregullash e kufizojnë sjelljen?". Së bashku, këto katër këndvështrime formojnë një pamje të plotë të arkitekturës së Ueb Shërbimeve.

---

## Komunikimi ndërmjet aplikacioneve në platforma të ndryshme

Që dy aplikacione të komunikojnë me sukses ndërmjet tyre — pavarësisht platformës mbi të cilën janë ndërtuar — duhet të plotësohen disa kushte paraprake:

1. Duhet të ekzistojë **lidhja fizike** (rrjeti) ndërmjet tyre.
2. Duhet të ekzistojë një **"marrëveshje"** e përbashkët për **formatin** e shkëmbimit të të dhënave (p.sh. a bëhet me XML apo diçka tjetër).
3. Duhet të ekzistojë një **"marrëveshje"** e përbashkët për **kuptueshmërinë** e të dhënave (p.sh. a kemi të bëjmë me prezantim HTML apo me një transaksion biznesi).
4. Duhet të ekzistojë një **"marrëveshje"** e përbashkët për **mënyrën dhe formën** e dërgimit të mesazheve ndërmjet palëve.

Ueb Shërbimet e plotësojnë pikërisht këtë nevojë: sigurojnë "gjuhën" (XML), "protokollin" (SOAP/HTTP) dhe "përshkrimin e kontratës" (WSDL) të nevojshëm që dy sisteme heterogjene të bashkëveprojnë pa dyshim mbi format apo kuptim.

### Shembulli .NET ↔ J2EE / Java

**Microsoft .NET** është një platformë që mbështet drejtpërdrejt krijimin e Ueb Shërbimeve XML (XML Microsoft Web Services). Nëpërmjet .NET-it ofrohet mundësia që aplikacione, procese dhe faqe Ueb të ekspozohen si Ueb Shërbim i bazuar në XML — shpesh vetëm përmes shtimit të një rreshti kodi ose një atributi, aplikacioni "kthehet" në Ueb Shërbim brenda mjedisit .NET.

Kur dy aplikacione janë ndërtuar në platforma krejt të ndryshme (p.sh. njëri në **Java**, tjetri në **.NET**), shkëmbimi i informacionit realizohet përmes **XML** si formati i përbashkët — pavarësisht se secili aplikacion e "kupton" XML-in sipas mekanizmave të veta të brendshme.

Një shembull konkret arkitekture ndër-platformore është **JNBridgePro**, e cila mundëson komunikim të dyanshëm ndërmjet Java Virtual Machine (JVM) dhe .NET Runtime:

- Gjatë **zhvillimit (development time)**, një plugin (p.sh. plugin për Eclipse ose Visual Studio .NET) gjeneron një **proxy** në gjuhën tjetër (p.sh. gjenerohet një "Java Proxy" për ta thirrur nga ana .NET, ose një ".NET Proxy" për ta thirrur nga ana Java).
- Gjatë **ekzekutimit (run time)**, komunikimi realizohet në mënyrë të sigurt përmes **Shared Memory**, **TCP/Binary** ose **HTTP/SOAP**, sipas konfigurimit.

Kjo qasje lejon që klasat .NET të thirren drejtpërsëdrejti si të ishin objekte Java (dhe anasjelltas), duke fshehur kompleksitetin e komunikimit ndër-platformor pas një shtrese proxy-sh të gjeneruara automatikisht.

---

## REST Ueb Shërbimet

### Çka është REST

**REST (Representational State Transfer)** përshkruan një **bashkësi principesh arkitekturale** me anë të të cilave të dhënat mund të transmetohen përmes një ndërfaqeje të standardizuar, tipikisht **HTTP**.

Karakteristika themelore e REST-it, në krahasim me SOAP-in:

- REST **nuk përmban ndonjë shtresë shtesë mesazhesh** (si SOAP Envelope) — fokusohet thjesht në dizajnimin e rregullave për krijimin e shërbimeve **pa gjendje** (stateless).
- Qasja në burime (resurse) bëhet përmes një **URI unik** për të marrë një **reprezentim** (përfaqësim) të atij burimi.
- Me çdo reprezentim të ri të burimit, **klienti duhet të transferojë vetë gjendjen** (pra gjendja nuk ruhet nga serveri ndërmjet kërkesave).
- Operacionet standarde të HTTP-së që performohen mbi burimin janë: **GET, PUT, DELETE, POST** dhe **HEAD**.
- Reprezentimi (formati i të dhënave) mund të jetë në format **JSON** ose **XML**.

Duke iu përmbajtur këtyre kufizimeve, REST lejon arritjen e disa vetive shumë të vlerësuara në sistemet e shpërndara:

- **Arritshmëri / shkallëzueshmëri** (scalability)
- **Thjeshtësi** (simplicity)
- **Modifikueshmëri** (modifiability)
- **Shikueshmëri** (visibility)
- **Transportueshmëri** (portability)
- **Besueshmëri** (reliability)

### API, REST dhe RESTful Ueb Shërbimet

Në një kuptim të përgjithshëm, **një Ueb Shërbim është vetëm një lloj API** (Application Programming Interface) që siguron informacione mbi Ueb.

- **API REST** ose **API RESTful** është një API që është **në përputhje me principet e REST-it**.
- REST zakonisht implementohet duke përdorur teknologji Ueb (HTTP), por, teorikisht, **REST nuk kërkon domosdoshmërisht** përdorimin e teknologjisë Ueb — parimet e tij mund të zbatohen edhe mbi protokolle të tjera.

### Kufizimet e stilit arkitekturor të REST

Stili arkitekturor REST përshkruhet formalisht nga **gjashtë kufizime (constraints)**:

1. **Ndërfaqe uniforme** (Uniform Interface) — mënyra e ndërveprimit me burimet standardizohet (p.sh. gjithmonë përmes URI + metodave standarde HTTP).
2. **Pa gjendje** (Stateless) — çdo kërkesë duhet të përmbajë të gjithë informacionin e nevojshëm; serveri nuk ruan gjendjen e klientit ndërmjet kërkesave.
3. **Mundësia e keshit** (Cacheable) — përgjigjet duhet të mund të shënohen (nënkuptueshëm ose eksplicit) si të keshueshme ose jo, për të përmirësuar performancën.
4. **Klient-Server** (Client-Server) — ndarje e qartë përgjegjësish ndërmjet klientit (ndërfaqja e përdoruesit) dhe serverit (ruajtja e të dhënave/logjika).
5. **Sistemi me shtresa** (Layered System) — klienti nuk mund (dhe s'ka nevojë) të dijë nëse komunikon direkt me serverin final apo me një ndërmjetës (proxy, gateway).
6. **Code on Demand** (opsional) — serveri mund, opsionalisht, t'i dërgojë klientit kod ekzekutues (p.sh. JavaScript) për të zgjeruar funksionalitetin e tij përkohësisht.

### Struktura e një Ueb Shërbimi REST

Struktura themelore e një Ueb Shërbimi REST bazohet te koncepti i **URI** që identifikon një **resurs**, dhe ai resurs mund të ketë disa **reprezentime** të ndryshme (p.sh. Reprezentimi 1, 2, 3, 4 — që mund të korrespondojnë p.sh. me JSON, XML, HTML etj., ose me gjendje/versione të ndryshme të burimit).

Mbi këtë resurs veprojnë katër **metoda uniforme** standarde HTTP:

| Metoda HTTP | Veprimi mbi burimin |
|---|---|
| **GET** | Merr (lexon) reprezentimin aktual të burimit. |
| **PUT** | Zëvendëson/përditëson tërësisht burimin. |
| **POST** | Krijon një burim të ri (ose kryen një veprim jo-idempotent). |
| **DELETE** | Fshin burimin. |

### Pamja e procesit të një arkitekture të bazuar në REST

Në një sistem real, kërkesat HTTP të një klienti (agjent përdorues) mund të kalojnë nëpër disa hallka ndërmjetëse përpara se të arrijnë te **serverët burimorë** (origin servers):

```
Agjenti Përdorues → Proxy → Gateway → Serverët Burimorë
       (lidhësi i klientit + keshi)      (lidhësi i serverit + keshi)
```

Elementet kryesore këtu janë:

- **Proxy** — ndërmjetës që përcjell kërkesat, shpesh me funksion keshimi.
- **Gateway** — ndërmjetës që "përkthen" ndërmjet protokolleve të ndryshme (p.sh. mund të komunikojë edhe me protokolle jo-HTTP, si `wais`).
- **Lidhësi i klientit** dhe **lidhësi i serverit**, secili me keshin (cache) e vet, që ndihmojnë në ruajtjen e përgjigjeve për ripërdorim, në përputhje me kufizimin "Cacheable" të REST-it.

---

## Ndërtimi i një Ueb Shërbimi RESTful — shembull praktik

Materiali ilustron krijimin e një Ueb Shërbimi RESTful "**Përshëndetje, Studentë!**" duke përdorur **Spring Boot** (Java). Ky shembull tregon në praktikë si zbatohen principet e REST-it të shpjeguara më sipër.

**Skenari:**

1. Ndërtohet një shërbim që pranon kërkesa **HTTP GET** në adresën:
   `http://localhost:8080/pershendetje`
2. Shërbimi përgjigjet me një reprezentim **JSON**:
   ```json
   {"id":1,"content":"Pershendetje, Studentë!"}
   ```
3. Përgjigjja mund të personalizohet me një parametër opsional `emri` në query string:
   `http://localhost:8080/pershendetje?emri=Përdorues`
4. Vlera e parametrit `emri` mbishkruan vlerën e paracaktuar `"Bota"` dhe reflektohet në përgjigje:
   ```json
   {"id":1,"content":"Pershendetje, Përdorues!"}
   ```

**Skedarët kryesorë** të projektit:

- `Pershendetje.java` — klasa "model" (POJO) që mban `id` dhe `content`.
- `PershendetjeController.java` — kontrolluesi REST (i shënuar `@RestController`) që definon endpoint-in `@GetMapping("/Pershendetje")`.
- `RestServiceApplication.java` — klasa kryesore që niset me `@SpringBootApplication`.
- `PershendetjeControllerTest.java` — testet automatike që verifikojnë sjelljen e endpoint-it (me dhe pa parametër).
- `pom.xml` (ose `build.gradle`) — konfigurimi i varësive Maven/Gradle (p.sh. `spring-boot-starter-web`, `spring-boot-starter-test`).

**Fragment ilustrues i kontrolluesit:**

```java
@RestController
public class PershendetjeController {
    private static final String template = "Pershendetje, %s!";
    private final AtomicLong counter = new AtomicLong();

    @GetMapping("/Pershendetje")
    public Pershendetje Pershendetje(
            @RequestParam(value = "emri", defaultValue = "Bota") String emri) {
        return new Pershendetje(counter.incrementAndGet(),
                String.format(template, emri));
    }
}
```

Vërehet qartë zbatimi i principeve REST: **URI** i qartë (`/Pershendetje`), metoda **GET** për leximin e një burimi, **reprezentim JSON**, dhe shërbim **pa gjendje** (çdo kërkesë trajtohet e pavarur, `counter` rritet vetëm si shembull ilustrues, jo si gjendje sesioni klienti).

---

## Siguria e Ueb Shërbimeve REST

Siguria është element **shumë kritik** për çdo Ueb Shërbim, përfshirë ato REST. Duhet theksuar se, ndryshe nga sa mund të supozohet, as **specifikimet XML-RPC** as ato **SOAP** në vetvete **nuk parashikojnë ndonjë mekanizëm sigurie apo autentifikimi të qartë** — siguria duhet shtuar veçmas, mbi këto protokolle bazë.

Duke përdorur specifikime shtesë sigurie, aplikacionet mund të angazhojnë komunikim të sigurt, të dizajnuar që të funksionojë mbi skeletin e përgjithshëm të Ueb Shërbimeve. Elementet kryesore që hyjnë në lojë përfshijnë:

- **Ueb Shërbimet e Sigurisë 1.0 (WS-Security 1.0)** — specifikim standard për sigurinë e mesazheve SOAP.
- **SAML (Security Assertion Markup Language)** — gjuhë markuese për mbrojtje të sigurt, e përdorur shpesh për autentifikim dhe autorizim ndër-domain.
- **Fshehtësia (Confidentiality)** — garantimi që të dhënat nuk lexohen nga palë të paautorizuara (tipikisht përmes enkriptimit).
- **Autentifikimi (Authentication)** — verifikimi i identitetit të palës që bën kërkesën.
- **Siguria e rrjetit (Network Security)** — masat mbrojtëse në nivel infrastrukture/transporti (p.sh. TLS/SSL, firewall).

---

## Dallimi ndërmjet REST dhe SOAP

Krahasimi më i drejtpërdrejtë ndërmjet dy qasjeve del qartë në tabelën vijuese:

| Kriteri | **REST** | **SOAP** |
|---|---|---|
| Njoftimi për ndryshime | Nëse ndryshon diçka në njërën anë, duhet njoftim | Nuk ka nevojë për njoftim nëse ndryshon diçka (kontrata WSDL e "rigid" formalizon çdo gjë paraprakisht) |
| Tooling/middleware | I nevojshëm minimal — mjafton mbështetja për HTTP | Kërkon mbështetje të rëndësishme nga tooling/middleware |
| Besueshmëria e statusit | Jo gjithmonë e besueshme — p.sh. HTTP DELETE mund të kthejë status OK edhe pse burimi nuk është fshirë realisht | I besueshëm |
| Përshtatshmëria | Më i përshtatshëm për komunikim **point-to-point**, ose kur ndërmjetësit nuk kanë rol kyç | I përshtatshëm mirë për **shërbime ndërmjetësuese** (komplekse, me shumë hallka) |
| Kufizime mbi payload | Nuk ka kushtëzime mbi payload (formë e lirë) | Payload duhet t'i përmbahet rreptësisht skemës së SOAP |
| Trajtimi i gabimeve | Ka trajtim të integruar të gabimeve (kodet standarde HTTP) | Nuk ka mekanizëm të integruar për trajtim gabimesh (jashtë SOAP Fault) |
| Transporti | I lidhur ngushtë me modelin HTTP për transport | SMTP dhe HTTP janë të dyja shtresa protokolli valide për transportin e SOAP |
| Vështirësia e zhvillimit | Lehtë për t'u zhvilluar dhe implementuar | Vështirë për t'u zhvilluar dhe implementuar |

> **Konkluzioni thelbësor:** **SOAP është një protokoll**, ndërsa **REST është një model arkitekturor** (stil dizajni). Kjo është dallimi konceptual më i rëndësishëm: SOAP përcakton rregulla strikte formati dhe komunikimi, ndërsa REST përcakton parime dizajni që mund të zbatohen në mënyra disi fleksibël mbi infrastrukturën ekzistuese të Ueb-it (HTTP).

---

## Trendet e evoluimit të teknologjive të integrimit

Nëse i vendosim teknologjitë e integrimit softuerik në një bosht kohor (nga vitet 1960 deri tani), duke krahasuar **kompleksitetin e integrimit** me **natyrën e bashkimit** (coupling), vërehet një evolucion i qartë:

```
1960 ─────────────────────────────────────────────► Tani

EDI → RPC → CORBA → EAI → SOA/SOAP → REST
```

Ku:

- **EDI** — Electronic Data Interchange
- **RPC** — Remote Procedure Call
- **CORBA** — Common Object Request Broker Architecture
- **EAI** — Enterprise Application Integration
- **SOA / SOAP** — Service-Oriented Architecture / Simple Object Access Protocol
- **REST** — Representational State Transfer

Trendi i përgjithshëm tregon se, me kalimin e kohës, teknologjitë kanë tenduar drejt **uljes së kompleksitetit të integrimit** dhe **lidhjes më të lirshme (loose coupling)** ndërmjet sistemeve — nga integrimet e ngurta dhe të kushtueshme si EDI/CORBA, drejt qasjeve më të thjeshta, më të lehta për t'u zhvilluar dhe më të përhapura si REST.

---

## Rast studimi: sistemi e-Biblioteka FIEK–SEMS

Si shembull ilustrues i zbatimit praktik të REST Ueb Shërbimeve, materiali paraqet komunikimin ndërmjet sistemit **Biblioteka-FIEK** dhe sistemit të studentëve **SEMS** në Universitetin e Prishtinës (UP).

### Konteksti dhe qëllimi

Sistemi i Bibliotekës duhet të thërrasë të dhëna nga aplikacioni SEMS (sistemi i menaxhimit të studentëve), me qëllim që të mundësojë:

- Krijimin e grupeve, shfrytëzuesve dhe ndarjen e roleve të tyre;
- Regjistrimin, rezervimin, huazimin dhe alokimin e librave;
- Kërkimin e librave në katalog;
- Rezervimin e librave nga distanca, përmes URL-it / Internetit;
- Njoftimin me e-mail për datën e kthimit të librave.

### Arkitektura e sistemit e-Biblioteka

Arkitektura përfshin një **Service Stack REST** që lidh bazën e të dhënave të e-Bibliotekës me sistemin SEMS (i vendosur në serverat e UP-së), duke lejuar që përdoruesit (nëpërmjet shfletuesit) — studentë, fakultete, administrata e bibliotekës universitare dhe përdorues të tjerë — të qasen në funksionalitetin e kombinuar. Praktikisht, sistemi i Bibliotekës **lexon të dhënat e studentëve** nga baza e SEMS-it në kohë reale, përmes thirrjeve REST, në vend që t'i mbajë ato të dyfishuara lokalisht.

### Rrjedha logjike e aplikacionit

Rrjedha e përdoruesit nëpër sistem mund të përmblidhet kështu:

1. Nga **Ballina**, përdoruesi mund të shkojë te *Qasja në Sistem*, *Regjistrimi në Sistem*, *Kërkimi i Librit* ose *Kontakti*.
2. Nëse përdoruesi tenton regjistrimin, sistemi kontrollon: **"A jeni student?"**
   - Nëse **PO** → sistemi **komunikon me SEMS duke përdorur Ueb Shërbimin REST**, për të verifikuar/marrë të dhënat e studentit.
   - Nëse **JO** → përdoruesi plotëson vetë **shënimin e të dhënave personale** (regjistrim manual, jo-student).
3. Nëse regjistrimi (përmes SEMS ose manual) kryhet **me sukses**, përdoruesi fiton **qasje në sistem** dhe më pas **përdorim të plotë të sistemit**.
4. Nëse regjistrimi **dështon**, procesi kthehet mbrapa te hapi i shënimit të të dhënave.

Kjo rrjedhë tregon një përdorim tipik të REST Ueb Shërbimeve në praktikë: si **burim i verifikimit të identitetit** (a është personi student i regjistruar në SEMS), duke shmangur nevojën për mirëmbajtjen e dyfishuar të të dhënave të studentëve në dy sisteme të veçanta.

### Forma për krijimin e llogarisë së re

Kur regjistrimi bëhet, formulari mbledh të dhëna si: **Numri i ID** (ID-ja e studentit në SEMS, ose një ID e gjeneruar nga biblioteka nëse personi nuk është student), **Numri i letërnjoftimit**, **Emri**, **Mbiemri**, **Email**, **Telefoni**, **Biblioteka**, **Fakulteti**, **Profesioni** (zgjedhur nga listë rënëse), **Përdoruesi** (username) dhe **Fjalëkalimi** (me konfirmim).

### Elemente teknike ilustruese

**Pseudokodi për krijimin e Ueb API** (anën e serverit, ASP.NET Web API), që tregon si mund të ndërtohet një endpoint i mbrojtur me autentifikim dhe që kërkon të dhëna nga baza SEMS:

```
namespace Biblioteka
[HMACAuthentication]
public class EmriAPItController : ApiController
    [HttpGet]
    public KomunikimiSEMS KerkoStudentin(long id)
        using (var context = new SEMS_testEntities())
        var tblRegjistriSt = context.tblRegjistriStudenteves
            .FirstOrDefault(p => p.RegjistriID == id);
        var fakultetiID = context.tblRegjistriFakultetets
            .FirstOrDefault(rf => rf.RegjistriID == id);
```

**Mekanizmi i autentifikimit për qasje në API** ndjek logjikën klasike të kontrollit të kredencialeve:

- **Mungesa e kredencialeve** → asnjë aksion nuk merret → kërkesa refuzohet për shkak të parimeve të autentifikimit → përgjigje e paautorizuar (**HTTP 401**), e shtuar si kundërshtim (challenge) — metoda e aksionit **nuk ekzekutohet**.
- **Kredenciale të gabuara** → `Context.ErrorResult` vendoset si rezultat i paautorizuar → shtohet kundërshtim → metoda **nuk ekzekutohet**.
- **Kredenciale të vlefshme** → `Context.Principal` vendoset në një **principal të autentifikuar** → kërkesa autorizohet me sukses (identiteti është autentik) → **metoda e aksionit vepron dhe prodhon një përgjigje** (mesazh përgjigjeje real).

**Pseudokodi për thirrjen e Ueb API** nga ana e klientit (p.sh. me `HttpClient` në .NET):

```csharp
public async Task<eBiblioteka.SEMS.KomunikimiSEMS> RunAsync(long regjistriId)
{
    using (var client = new HttpClient())
    {
        client.BaseAddress = new Uri("http://fleteparaqitja.uni-pr.edu/");
        client.DefaultRequestHeaders.Accept.Clear();
        client.DefaultRequestHeaders.Accept.Add(
            new MediaTypeWithQualityHeaderValue("application/json"));

        // HTTP GET
        HttpResponseMessage response =
            await client.GetAsync("api/EmriAPIt/" + regjistriId);
    }
}
```

Ky shembull i plotë — nga rrjedha e biznesit, deri te kodi konkret i kërkesës HTTP GET dhe përgjigjes JSON — tregon në mënyrë praktike si zbatohen në një sistem real universitar të gjitha konceptet e mësuara: **URI**, **metodat HTTP (GET)**, **reprezentimi JSON**, **autentifikimi**, dhe **komunikimi pa gjendje** ndërmjet dy sistemeve informatike të pavarura (Biblioteka dhe SEMS).

---

## Përmbledhje

- **Ueb Shërbimet** janë komponentë softuerikë të shpërndarë, të pavarur nga platforma dhe gjuha programuese, që mundësojnë komunikim automatik ndërmjet aplikacioneve përmes protokolleve të bazuara në XML.
- Evolucioni i Ueb-it ka kaluar nga **lidhshmëria** (connectivity), te **prezantimi** për njerëz (browse the web), drejt **programueshmërisë** (program the web) — themeli konceptual i Ueb Shërbimeve.
- Arkitektura klasike e Ueb Shërbimeve ndërtohet mbi katër komponentë: **XML** (formati), **SOAP** (protokolli i mesazheve), **WSDL** (përshkrimi i shërbimit) dhe **UDDI** (regjistri/direktoriumi për gjetjen e shërbimeve) — të tre të fundit bashkëveprojnë në një cikël "publiko → gjej → lidhu".
- **Modeli i Ueb Shërbimeve** dhe **arkitektura e kërkesës** përshkruajnë hap pas hapi si një klient gjen, merr përshkrimin, dhe përdor një shërbim, ndërsa **performanca** (koha e përgjigjes në funksion të ngarkesës) tregon se zgjedhja e formatit (binar kundrejt SOAP/XML) ndikon drejtpërdrejt në shkallëzueshmëri.
- **Siguria** e Ueb Shërbimeve mund të realizohet në tre nivele — transport, aplikacion, mesazh — secili me kompromise të ndryshme ndërmjet thjeshtësisë dhe fuqisë mbrojtëse.
- Standardizimin e fushës e udhëheqin kryesisht **W3C**, **OASIS** dhe **WS-I**.
- **Zbulimi** i shërbimeve dhe **relacionet** ndërmjet tyre (bazuar te identifikuesit URI) mundësojnë ndërveprim dinamik dhe automatik ndërmjet sistemeve.
- Ekzistojnë katër **modele arkitekturale** plotësuese të Ueb Shërbimeve: **message-oriented**, **service-oriented**, **resource-oriented** dhe **policy-oriented (i politikave zhvilluese)** — secili nxjerr në pah një aspekt tjetër të së njëjtës arkitekturë.
- **REST** është stil arkitektural (jo protokoll) i bazuar në HTTP, që përdor URI për identifikimin e burimeve, metodat standarde HTTP (**GET, POST, PUT, DELETE**) për veprime mbi to, dhe reprezentime tipikisht në **JSON** ose XML — duke respektuar gjashtë kufizime arkitekturore (ndërfaqe uniforme, pa gjendje, cacheable, klient-server, sistem me shtresa, code on demand).
- Krahasuar me **SOAP**, REST është më i thjeshtë, më i lehtë për zhvillim, i lidhur direkt me HTTP-në, por më pak formal dhe më pak i "besueshëm" në kuptimin e kontratës strikte — ndërsa SOAP ofron formalitet, transaksione dhe përshtatshmëri më të mirë për skenarë komplekse, ndërmjetësues.
- Rasti praktik i sistemit **e-Biblioteka FIEK–SEMS** ilustron zbatimin real të një Ueb Shërbimi RESTful për verifikim identiteti dhe shkëmbim të dhënash ndërmjet dy sistemeve universitare të pavarura.

---

## Pyetje kontrolli

1. Çka është një Ueb Shërbim dhe cilat janë katër karakteristikat themelore që e dallojnë nga një aplikacion "i zakonshëm"?
2. Shpjegoni rolin e secilit prej tre komponentëve SOAP, WSDL dhe UDDI, dhe si bashkëveprojnë ata në një cikël të plotë "publiko–gjej–lidhu".
3. Cilat janë dy pjesët kryesore të një zarfi (envelope) SOAP, dhe çfarë lloj informacioni mban secila?
4. Përshkruani gjashtë hapat e arkitekturës së kërkesës së një Ueb Shërbimi, duke përdorur UDDI si direktorium.
5. Cilat janë tri nivelet e sigurisë së Ueb Shërbimeve, dhe cili prej tyre konsiderohet më fleksibël e më i fuqishëm? Pse?
6. Krahasoni katër modelet arkitekturale të Ueb Shërbimeve (message-oriented, service-oriented, resource-oriented, policy-oriented) — çfarë fokusi ka secili?
7. Cilat janë gjashtë kufizimet e stilit arkitekturor REST? Shpjegoni kufizimin "Stateless" me fjalët tuaja.
8. Krijoni një tabelë (ose përshkrim me fjalë) të tri dallimeve më të rëndësishme ndërmjet REST dhe SOAP, duke shpjeguar pse SOAP është protokoll ndërsa REST është model arkitekturor.
9. Në rastin e sistemit e-Biblioteka FIEK–SEMS, çfarë ndodh nëse kredencialet e dërguara në kërkesën HTTP janë të pasakta? Përshkruani sekuencën e ngjarjeve deri te përgjigjja që merr klienti.
10. Pse koha e përgjigjes rritet me rritjen e ngarkesës së shfrytëzuesve, dhe pse variantet binare të transportit priren të performojnë më mirë se ato të bazuara në SOAP/XML?
