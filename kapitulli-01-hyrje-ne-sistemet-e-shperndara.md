# Kapitulli 1 — Hyrje në Sistemet e Shpërndara

## Objektivat e kapitullit

Ky kapitull hedh themelet e lëndës "Sistemet e Shpërndara" dhe synon që studenti, në fund të tij, të jetë në gjendje të:

- kuptojë se si funksionon një sistem i shpërndarë dhe cilat janë parimet themelore që e karakterizojnë;
- identifikojë kur dhe përse një sistem i shpërndarë mund të dështojë, pra cilat janë burimet e mundshme të problemeve (rrjeti, orët e pasinkronizuara, dështimet e pjesshme etj.);
- njohë teknikat dhe parimet e projektimit që e bëjnë një sistem të shpërndarë të qëndrueshëm (robust) dhe që punon pa probleme edhe në prani të dështimeve pjesore.

Këto tri objektiva janë fija lidhëse e gjithë kapitullit: fillimisht përkufizohet çka është një sistem i shpërndarë, pastaj shqyrtohen këndvështrimet, qëllimet e projektimit dhe sfidat, dhe në fund kapitulli zbret në shembuj konkretë praktikë — mbi të gjitha World Wide Web-in, HTML/URL/HTTP-në, XML-në dhe shërbimet Web — si ilustrim se si këto parime zbatohen në praktikë.

---

## 1. Çka është një sistem i shpërndarë?

**Përkufizimi bazë:** Një sistem i shpërndarë është një koleksion kompjuterësh të pavarur (autonomë) që, nga këndvështrimi i përdoruesit, shfaqet si një sistem i vetëm, koherent — pra përdoruesi nuk e percepton faktin që "pas skenës" ka shumë makina fizike të veçanta që bashkëpunojnë.

Strukturalisht, një sistem i shpërndarë mund të kuptohet si një **rrjet proceseve/burimesh**, ku:

- **nyjet** e rrjetit janë proceset ose burimet (p.sh. kompjuterë, shërbime, të dhëna), dhe
- **lidhjet** ndërmjet nyjeve janë kanalet e komunikimit (rrjeti).

Nga ana funksionale, shpërndarja logjike e aftësive të një sistemi të tillë përmban katër përbërës themelorë:

1. **Procese të shumta** që ekzekutohen njëkohësisht (jo domosdoshmërisht në të njëjtën makinë);
2. **Komunikim ndërmjet proceseve**, zakonisht me anë të shkëmbimit të mesazheve nëpër rrjet;
3. **Hapësira adresash të ndara** — çdo proces ka hapësirën e vet të memories, ndryshe nga një sistem me memorie të përbashkët klasike;
4. **Qëllim i përbashkët** — proceset e shumta nuk punojnë në mënyrë të pavarur e të pakoordinuar, por bashkëpunojnë drejt një objektivi të përbashkët.

### Qëllimi i përbashkët që synohet të arrihet

Motivi themelor pas ndërtimit të sistemeve të shpërndara është potenciali i tyre i madh për të revolucionarizuar mënyrën se si llogarisim. Fusha të reja si **biollogaritja** (biocomputing — përdorimi i molekulave me origjinë biologjike për përpunim informacioni), **nanollogaritja** (nanocomputing — manipulimi i materies në shkallë atomike/molekulare) dhe **llogaritja kuantike** (quantum computing — shfrytëzimi i fenomeneve kuantike si superpozicioni dhe ndërlidhja/entanglement) tregojnë drejtimet e ardhshme të mundshme.

Por, pavarësisht nga teknologjia specifike, në themel të të gjitha këtyre qasjeve qëndron i njëjti parim i thjeshtë: **"ndaj dhe sundo" (divide-and-conquer)** — një problem i madh ndahet në nën-probleme më të vogla, dhe zgjidhet duke **shtuar më shumë hardware** që punon paralelisht mbi këto nën-probleme, në vend që të mbështetemi vetëm te rritja e fuqisë së një kompjuteri të vetëm.

### Parimi "Ndaj-dhe-Udhëheq" (Divide-and-Conquer)

Ky parim mund të vizualizohet përmes një skeme të thjeshtë: një detyrë e përgjithshme ("Punë") ndahet në nën-detyra më të vogla (P₁, P₂, P₃, …), të cilat ekzekutohen **paralelisht** nga procese ose nyje të pavarura të quajtura **"Punëtorë" (workers)**. Çdo punëtor përpunon vetëm nën-detyrën e vet dhe prodhon një rezultat të ndërmjetëm (r₁, r₂, r₃, …). Në fazën përfundimtare, këto rezultate të pjesshme **kombinohen** për të prodhuar rezultatin final ("Rezultati").

Ky model rrit ndjeshëm **shkallëzueshmërinë** dhe **efikasitetin** e sistemit, sepse shfrytëzon njëkohësisht paralelizmin (shumë punëtorë që punojnë njëherësh) dhe shpërndarjen e burimeve llogaritëse (secili punëtor mund të gjendet në një nyje/makinë të ndryshme).

---

## 2. Sistemet e shpërndara kundrejt sistemeve të decentralizuara

Është e rëndësishme të mos ngatërrohen termat "i shpërndarë" dhe "i decentralizuar" — ata përshkruajnë veti të ndryshme, jo domosdoshmërisht të kundërta.

- Në një **sistem të centralizuar**, ekziston një nyje qendrore që kontrollon dhe koordinon gjithçka.
- Në një **sistem të decentralizuar**, kontrolli dhe burimet janë shpërndarë mes disa nyjeve pa një autoritet të vetëm qendror, por lidhjet ndërmjet nyjeve mund të jenë ende relativisht të kufizuara.
- Në një **sistem të shpërndarë**, proceset dhe burimet janë të vendosura fizikisht në disa kompjuterë të lidhur nëpërmjet rrjetit.

Një pyetje konceptuale interesante që ngrihet: **kur bëhet një sistem i decentralizuar një sistem i shpërndarë?** Për shembull:

- Shtimi i **një (1)** lidhjeje shtesë ndërmjet dy nyjeve në një sistem të decentralizuar;
- Shtimi i **dy (2)** lidhjeve ndërmjet dy nyjeve të tjera;
- Në përgjithësi: a mjafton shtimi i **k > 0** lidhjeve që ta bëjë sistemin "të shpërndarë"?

Kjo pyetje tregon se kufiri ndërmjet decentralizimit dhe shpërndarjes nuk është gjithmonë i qartë — bëhet fjalë më shumë për një **spektër** sesa për një ndarje binare.

### Dy qasje alternative për ndërtimin e sistemeve të shpërndara

1. **Qasja integruese**: lidhen sisteme kompjuterike ekzistuese, tashmë të rrjetëzuara, në një sistem më të madh e koherent.
2. **Qasja zgjeruese**: një sistem kompjuterik ekzistues i rrjetëzuar zgjerohet duke i shtuar kompjuterë shtesë.

### Dy përkufizime formale

Bazuar në shkallën e shpërndarjes, mund të dallojmë:

- **Sistem i decentralizuar**: një sistem kompjuterik i rrjetëzuar në të cilin proceset dhe burimet janë **domosdoshmërisht** të shpërndara në disa kompjuterë (shpërndarja është kusht i domosdoshëm strukturor, jo çështje shkalle).
- **Sistem i shpërndarë**: një sistem kompjuterik i rrjetëzuar në të cilin proceset dhe burimet janë të shpërndara **në mënyrë të mjaftueshme** në disa kompjuterë (theksi vihet te "mjaftueshmëria" e shpërndarjes për të arritur qëllimin e sistemit, jo te fakti se ekziston ose jo shpërndarje).

---

## 3. Disa keqkuptime të zakonshme

Në literaturën dhe diskutimin popullor rreth sistemeve të shpërndara ekzistojnë disa keqkuptime të përhapura, të cilat duhet t'i njohim që të mund t'i vlerësojmë kritikisht:

- **"Zgjidhjet e centralizuara kanë gjithmonë një pikë të vetme dështimi (single point of failure)."** Në përgjithësi kjo *nuk* është e vërtetë. Duhet të bëhet dallim i qartë ndërmjet **centralizimit logjik** dhe **centralizimit fizik**. Shembulli klasik është rrënja e **Sistemit të Emrave të Domenëve (DNS — Domain Name System)**, e cila është:
  - **e centralizuar logjikisht** (nga këndvështrimi i përdoruesit funksionon si një hierarki e vetme, koherente),
  - **e shpërndarë fizikisht** (në shkallë shumë të gjerë, nëpër shumë servera anembanë botës),
  - **e decentralizuar** ndërmjet disa organizatave (asnjë organizatë e vetme nuk e kontrollon plotësisht).
- **"Zgjidhjet e centralizuara nuk shkallëzohen."** Kjo gjithashtu nuk është universalisht e vërtetë; shpesh një pikë e vetme (logjike) dështimi është aktualisht **më e lehtë për t'u menaxhuar** dhe **më e lehtë për t'u bërë e qëndrueshme (robuste)**, sepse ka vetëm një vend ku implementohen mekanizma mbrojtës të fuqishëm.

Konkluzioni i rëndësishëm është se ekzistojnë shumë keqkuptime të pabazuara lidhur me shkallëzueshmërinë, tolerancën ndaj dështimeve, sigurinë etj., dhe si studentë të sistemeve të shpërndara duhet të zhvillojmë aftësinë që t'i kuptojmë thellë këto sisteme, në mënyrë që të mund të **gjykojmë kritikisht** pretendime të tilla, në vend që t'i pranojmë si të mirëqena.

---

## 4. Këndvështrimet mbi sistemet e shpërndara

Sistemet e shpërndara janë komplekse dhe për këtë arsye nuk mund të kuptohen plotësisht nga një këndvështrim i vetëm; nevojiten disa këndvështrime plotësuese, secili duke ndriçuar një dimension të ndryshëm të problemit:

| Këndvështrimi | Çka trajton |
|---|---|
| **Arkitektura** | Organizimet e përbashkëta strukturore (p.sh. klient-server, peer-to-peer) |
| **Proceset** | Llojet e proceseve që ekzistojnë dhe marrëdhëniet ndërmjet tyre |
| **Komunikimi** | Mekanizmat për shkëmbimin e të dhënave mes proceseve |
| **Koordinimi** | Algoritme të pavarura nga aplikacioni (p.sh. sinkronizim, konsensus) |
| **Emërtimi (naming)** | Si identifikohen dhe gjenden burimet në sistem? |
| **Konsistenca dhe replikimi** | Kërkesat e performancës që imponojnë se kopje të ndryshme të të njëjtave të dhëna duhet të mbeten "të njëjta" (konsistente) |
| **Toleranca ndaj dështimeve** | Si vazhdon sistemi të funksionojë kur ndodhin dështime të pjesshme? |
| **Siguria** | Si sigurohet qasje e autorizuar vetëm për subjektet legjitime në burime? |

Këto tetë këndvështrime do të thellohen gjatë tërë lëndës; ky kapitull vetëm i prezanton si hartë orientuese.

---

## 5. Qëllimet e projektimit të sistemeve të shpërndara

Kur projektojmë një sistem të shpërndarë, katër janë qëllimet e përgjithshme që synojmë t'i arrijmë:

1. **Mbështetja e ndarjes së burimeve** (resource sharing);
2. **Transparenca e shpërndarjes**;
3. **Hapja e sistemeve** (openness);
4. **Shkallëzueshmëria** (scalability).

### 5.1 Transparenca e shpërndarjes

**Transparenca** është fenomeni përmes të cilit një sistem i shpërndarë përpiqet t'ia **fshehë** përdoruesit faktin se proceset dhe burimet e tij janë të shpërndara fizikisht në disa kompjuterë — kompjuterë të cilët mund të jenë të ndarë nga njëri-tjetri me distanca shumë të mëdha. Në thelb, sistemi duhet të "duket" si një e tërë e vetme, jo si një koleksion pjesësh të veçanta.

Transparenca nuk është një veti e vetme, por një **familje vetish**, secila duke fshehur një aspekt specifik të shpërndarjes:

- **Transparenca në qasje (access transparency)**: mundëson që burimet lokale dhe ato në distancë t'u qasemi duke përdorur **operacione identike** — d.m.th. programi nuk duhet të "dijë" nëse po thërret një burim lokal apo të largët.
- **Transparenca në lokacion (location transparency)**: mundëson qasjen në burime pa pasur nevojë të dimë lokacionin e tyre fizik ose lokacionin në rrjet (p.sh. në cilën ndërtesë gjendet serveri, ose cila është adresa e tij IP).
- **Transparenca në konkurrencë (concurrency transparency)**: mundëson që disa procese të ndryshme të operojnë njëkohësisht (konkurrentisht) mbi burime të ndara, pa interferuar me njëri-tjetrin dhe pa e vënë re njëri-tjetrin.
- **Transparenca në replikim (replication transparency)**: mundëson përdorimin e disa instancave (kopjeve) të një burimi për të rritur qëndrueshmërinë dhe performancën, pa e detyruar përdoruesin ose programuesin e aplikacionit të ketë njohuri për ekzistencën e këtyre kopjeve.
- **Transparenca në prishje (failure transparency)**: mundëson fshehjen e gabimeve/dështimeve, duke lejuar që përdoruesit dhe aplikacionet të përfundojnë punën e tyre pavarësisht nga dështimet e komponentëve harduerikë ose softuerikë.
- **Transparenca mobile (mobility transparency)**: lejon lëvizjen (zhvendosjen) e burimeve dhe klientëve brenda sistemit pa ndikuar në operacionet e përdoruesve apo programeve.
- **Transparenca në performancë (performance transparency)**: lejon që sistemi të rikonfigurohet dhe të përmirësojë performancën në varësi të ngarkesës aktuale, pa ndërhyrjen e përdoruesit.
- **Transparenca e shkallëzimit (scaling transparency)**: lejon që sistemi dhe aplikacionet të zgjerohen në shkallë (të rriten) pa qenë nevoja të ndryshohet struktura e sistemit ose algoritmet e aplikacionit.

Vlen të theksohet se transparenca e plotë në praktikë është një ideal që rrallëherë arrihet plotësisht — shpesh duhet të bëhen kompromise, sepse fshehja totale e shpërndarjes mund të vijë në kundërshtim me performancën ose me dëshirën e programuesit për të pasur kontroll mbi vendndodhjen e të dhënave.

### 5.2 Hapja (openness)

Sistemet e hapura janë ato që ofrojnë shërbime sipas rregullave standarde që përshkruajnë sintaksën dhe semantikën e këtyre shërbimeve. Kjo lejon shtimin e shërbimeve të reja dhe integrimin e komponentëve nga prodhues të ndryshëm, për sa kohë respektohen standardet e publikuara.

### 5.3 Shkallëzueshmëria (scalability)

Sistemi duhet të mbetet efikas edhe kur rritet numri i përdoruesve, burimeve ose ngarkesës — qoftë në shkallë gjeografike (përdorues të shpërndarë anembanë botës), qoftë në shkallë numerike (numri i nyjeve/klientëve).

---

## 6. Nivele të ndryshme të shpërndarjes ("Përgjegjësi të ndryshme")

Koncepti i "shpërndarjes" nuk zbatohet vetëm mes makinave të ndryshme, por shtrihet përgjatë një spektri të tërë nivelesh — nga elementet më të vogla të harduerit deri te sisteme gjigante gjeografikisht të shpërndara. Kjo shihet qartë kur krahasojmë:

- fije (threads) të ndryshme brenda së njëjtës bërthamë (core) procesori;
- bërthama (cores) të ndryshme brenda së njëjtit CPU;
- CPU të ndryshëm brenda një sistemi shumë-procesorësh (multiprocessor);
- makina të ndryshme brenda një sistemi të shpërndarë "të mirëfilltë";
- hardware standard (commodity hardware) kundrejt harduerit specifik/ekzotik;
- shkallëzimi sipas numrit të makinave kundrejt numrit të procesorëve kundrejt numrit të bërthamave;
- performanca e kufizuar nga memoria kundrejt diskut kundrejt gjerësisë së brezit të rrjetit (network bandwidth);
- modele të ndryshme programimi që u përshtaten këtyre niveleve të ndryshme.

Ky spektër na tregon se koncepti i "sistemit të shpërndarë" nuk është krejt i ndarë nga koncepti i "sistemit paralel" — ata janë skaje të një vazhdimësie ku sfida bazë (koordinimi i njësive të pavarura llogaritëse) mbetet e ngjashme, ndërsa mjetet dhe kufizimet ndryshojnë sipas nivelit.

### Implementimi praktik

Sistemet e shpërndara praktike duhet të kenë si shtyllë kryesore një **rrjet real** ndërmjet makinave fizikisht të veçanta. Megjithatë, për qëllime studimi, testimi ose zhvillimi, sisteme të tilla mund të **simulohen** edhe në një multiprocesor me memorie të përbashkët, apo madje edhe në një procesor të vetëm — pra logjika e sistemit të shpërndarë (proceset e shumta, mesazhet mes tyre) mund të emulohet softuerikisht pa kërkuar domosdoshmërisht hardware fizikisht të shpërndarë.

---

## 7. Domenet e aplikacioneve të sistemeve të shpërndara

Sistemet e shpërndara nuk janë vetëm koncept akademik — ato janë baza e pothuajse çdo shërbimi dixhital modern. Disa nga fushat kryesore të zbatimit, me shembuj konkretë:

| Domeni | Shembuj |
|---|---|
| **Financa dhe komerciale** | eCommerce (p.sh. Amazon, eBay), PayPal, bankat online dhe tregtia elektronike |
| **Shoqëria informative** | Informacioni nga Web dhe motorët kërkues, e-Books, Wikipedia; rrjetet sociale (Facebook, MySpace) |
| **Industritë kreative dhe argëtimi** | Lojëra online, muzikë dhe filma në shtëpi, përmbajtje e gjeneruar nga vetë përdoruesit (p.sh. YouTube, Flickr) |
| **Kujdesi shëndetësor** | Informatika shëndetësore, regjistrat online të pacientëve, monitorimi i pacientëve në distancë |
| **Edukimi** | e-Learning, ambiente virtuale mësimi, të mësuarit në distancë |
| **Transporti dhe logjistika** | GPS dhe sistemet për gjetjen e rrugëve, shërbime hartash (Google Maps, Google Earth) |
| **Shkenca** | Grid Computing — teknologji që mundëson bashkëpunimin mes shkencëtarëve në projekte të mëdha kërkimore |
| **Menaxhimi i ambientit** | Rrjete sensorësh për monitorimin e tërmeteve, vërshimeve ose cunameve |

### Të mirat dhe të metat e sistemeve të shpërndara

**Të mirat:**

- **Ekonomike** — shfrytëzim më efikas i kostove, pasi mund të përdoret hardware më i lirë e standard në vend të një superkompjuteri të vetëm shumë të shtrenjtë;
- **Shpejtësi** — përpunimi paralel i shumë punëve njëkohësisht rrit shpejtësinë e përgjithshme;
- **Shpërndarje e përhershme (natyrshme)** — shumë probleme të botës reale janë vetvetiu të shpërndara gjeografikisht (p.sh. degët e një banke), kështu që një zgjidhje e shpërndarë përputhet natyrshëm me problemin;
- **Qëndrueshmëri** — dështimi i një nyjeje të vetme nuk e ndal domosdoshmërisht tërë sistemin (nëse dizajni e mundëson këtë);
- **Zgjerim (shkallëzim)** — mund të shtohen nyje/burime shtesë sipas nevojës, në mënyrë inkrementale.

**Të metat:**

- **Softueri** — zhvillimi, testimi dhe korrigjimi (debugging) i softuerit të shpërndarë është shumë më kompleks se ai i një programi të vetëm;
- **Rrjeti** — sistemi bëhet i varur nga cilësia, gjerësia e brezit dhe besueshmëria e rrjetit; vonesat (latency) dhe humbjet e paketave bëhen faktorë kritikë;
- **Më shumë komponentë nën "amortizim"** — sa më shumë komponentë harduerikë/softuerikë ka sistemi, aq më i lartë është probabiliteti statistikor që të paktën një komponent të dështojë në një moment të caktuar;
- **Siguria** — sipërfaqja e sulmit rritet ndjeshëm, sepse ka shumë më tepër pika (nyje, kanale komunikimi) që duhen mbrojtur.

---

## 8. Organizimi dhe koncepti harduerik i sistemeve të shpërndara

Nga ana softuerike, një sistem i shpërndarë organizohet mbi një shtresë të quajtur **middleware**. Middleware-i shtrihet "sipër" sistemit operativ dhe rrjetit, mbi shumë makina të ndryshme, dhe i ofron aplikacionit një pamje të unifikuar, duke fshehur pjesën më të madhe të kompleksitetit të shpërndarjes (pra middleware-i është edhe mekanizmi kryesor praktik përmes të cilit realizohet transparenca e diskutuar më sipër).

Nga ana harduerike, ekzistojnë organizime të ndryshme të memories në sistemet kompjuterike të shpërndara — nga sisteme me memorie fizikisht të përbashkët (shared-memory multiprocessors) deri te sisteme ku çdo makinë ka memorien e vet lokale dhe komunikon vetëm përmes shkëmbimit të mesazheve (distributed-memory / message-passing systems). Zgjedhja e organizimit të memories ndikon direkt në modelet e programimit që mund të përdoren.

### Kushtet reale në të cilat punojnë sistemet e shpërndara

Sistemet e shpërndara në botën reale projektohen për të punuar në kushte shumë të ndryshme dhe shpesh të paparashikueshme, që lidhen me:

- **Harduerin** (lloje të ndryshme makinash, procesorësh);
- **Softuerin** (sisteme operative, gjuhë programimi, versione të ndryshme);
- **Kanalet e komunikimit** (cilësi, gjerësi brezi, vonesa e ndryshueshme);
- **Problemet e sigurisë** të sistemit;
- **Ndarjen e përgjegjësive** mes komponentëve të ndryshëm të sistemit;
- **Vendosjen (deployment)** e këtyre komponentëve në kompjuterë të lidhur në rrjet.

Këto janë elementet kryesore që merren parasysh gjatë projektimit, sepse ndikojnë direkt në tri veti kritike të sistemit: **performancën**, **qëndrueshmërinë** dhe **sigurinë**.

---

## 9. Shembuj konkretë të sistemeve të shpërndara

Sistemet e shpërndara përfshijnë një spektër shumë të gjerë shembujsh, ndër të cilët:

- **Interneti** — rrjeti global i rrjeteve, i përbërë nga intranete lokale, ofrues shërbimi interneti (ISP), një "backbone" (shtyllë kurrizore) me kapacitet të lartë, dhe lidhje të llojeve të ndryshme (p.sh. satelitore), që lidh kompjuterë desktop dhe serverë;
- **Intraneti** — rrjet i ngjashëm me internetin, por i kufizuar brenda një organizate;
- **Mobile and Ubiquitous Computing** — llogaritja mobile dhe e gjithëpranishme (pajisje portative, sensorë të integruar në ambient);
- **Computational Grids** — rrjete llogaritëse që bashkojnë burime kompjuterike të shpërndara gjeografikisht për detyra llogaritëse intensive;
- **World Wide Web** — sistemi për shpërndarjen e informacionit dhe burimeve (trajtohet në detaje më poshtë);
- **Clusters dhe Networks of workstations** — grupe kompjuterësh të lidhur ngushtë që bashkëpunojnë si një njësi e vetme;
- **Sisteme të prodhimit të shpërndarë** (distributed manufacturing systems) — p.sh. linja të automatizuara montimi;
- **Rrjete kompjuterësh të degëve** (network of branch office computers) — p.sh. sisteme informacioni që përpunojnë automatikisht porositë në kompani me degë të shumta;
- **Rrjete sistemesh të integruara (embedded)**;
- **Procesorë të specializuar**, si procesori "Cell" i ri i përdorur në PlayStation 3, që ilustron se edhe brenda një pajisjeje të vetme mund të ketë arkitekturë të shpërndarë/paralele.

### Cloud Computing

Cloud computing përfaqëson evolucionin bashkëkohor të sistemeve të shpërndara: në vend që përdoruesi të ruajë dhe përpunojë të dhëna lokalisht, ai i qaset burimeve (llogaritje, ruajtje, softuer) përmes internetit, nga infrastruktura e ofruar nga ofrues të mëdhenj (p.sh. Google, Amazon). Cloud computing lejon hostimin e një numri të madh llojesh aplikacionesh klient-server dhe ofron fuqi të lartë llogaritëse, kosto relativisht të ulëta, shkallëzueshmëri dhe disponueshmëri të lartë.

### Prirjet e Distributed Computing

Disa faktorë kryesorë po nxisin evolucionin e llogaritjes së shpërndarë:

- rritja e shpejtë e numrit të procesorëve në përdorim;
- rënia e ndjeshme e çmimit të procesorëve, falë përparimit teknologjik;
- shpërndarja gjeografike gjithnjë e më e gjerë e proceseve;
- ndarja e burimeve, siç përdoret p.sh. në rrjetet peer-to-peer (P2P);
- përshpejtimi i llogaritjeve përmes bashkimit të burimeve, si te grid computing;
- nevoja për **tolerancë ndaj gabimeve/dështimeve**, pasi sa më i madh sistemi, aq më e lartë probabiliteti i dështimit të një pjese të tij.

---

## 10. Çështje të rëndësishme që i dallojnë sistemet e shpërndara

Në krahasim me një program që ekzekutohet në një makinë të vetme, sistemet e shpërndara përballen me disa sfida themelore që nuk ekzistojnë (ose ekzistojnë në formë shumë më të lehtë) në llogaritjen jo të shpërndarë:

- **Njohuria është lokale** — çdo proces ka vetëm pamjen e vet të pjesshme mbi gjendjen globale të sistemit; asnjë nyje nuk "sheh" gjithçka njëherësh.
- **Orët nuk janë të sinkronizuara** — çdo makinë ka orën e vet fizike, e cila devijon (drift) me kalimin e kohës, kështu që nuk mund të mbështetemi te "koha globale" pa mekanizma shtesë sinkronizimi.
- **Nuk ka hapësirë adresash të përbashkët** — proceset nuk mund thjesht të lexojnë/shkruajnë drejtpërdrejt memorien e njëri-tjetrit; komunikimi bëhet vetëm përmes mesazheve.
- **Topologjia dhe rutimi (routing)** — struktura e rrjetit dhe mënyra se si udhëtojnë mesazhet ndërmjet nyjeve ndikojnë direkt në performancë dhe besueshmëri.
- **Shkallëzueshmëria** — sistemi duhet të vazhdojë të funksionojë mirë ndërsa numri i nyjeve/përdoruesve rritet ndjeshëm.
- **Toleranca ndaj gabimeve** — sistemi duhet të vazhdojë funksionimin edhe kur disa nga komponentët e tij dështojnë pjesërisht.

### Disa nën-probleme klasike të zakonshme

Nga çështjet e mësipërme lindin disa probleme algoritmikë "klasikë" që studiohen thellësisht në fushën e sistemeve të shpërndara:

- **Zgjedhja e udhëheqësit (leader election)** — si vendosin proceset se cili prej tyre do të luajë rolin koordinues, kur nuk ka autoritet qendror të paracaktuar?
- **Përjashtimi i ndërsjellë (mutual exclusion)** — si sigurohet që vetëm një proces në të njëjtën kohë të qaset në një burim kritik, kur proceset janë të shpërndara dhe nuk kanë memorie të përbashkët?
- **Sinkronizimi i kohës (clock synchronization)** — si mund të "afrohen" orët e makinave të ndryshme sa më shumë, në mungesë të një ore globale?
- **Fotografimi i gjendjes së shpërndarë (distributed snapshot)** — si kapet një "foto" konsistente e gjendjes globale të një sistemi që po ndryshon vazhdimisht dhe në mënyrë asinkrone?
- **Menaxhimi i replikave (replica management)** — si mbahen konsistente disa kopje të të njëjtave të dhëna, të vendosura në nyje të ndryshme?

Këto probleme do të trajtohen në detaje në kapitujt vijues të lëndës.

---

## 11. Nga Desktop/HPC/Grid drejt Cloud-eve të Internetit brenda 30 viteve

Evolucioni historik i llogaritjes me performancë të lartë (HPC — High-Performance Computing) ndjek një trajektore të qartë gjatë tri dekadave të fundit: nga **superkompjuterë të centralizuar**, drejt **desktopëve dhe kompjuterëve individualë**, pastaj drejt **klasterëve** dhe **grid-eve** të shpërndara gjeografikisht, e deri te **cloud computing** i sotëm.

Përpjekjet kërkimore dhe zhvillimore (R&D) në fushat e HPC-së, klasterëve, Grid Computing-ut, P2P-së dhe makinave virtuale kanë hedhur themelet konceptuale dhe teknike mbi të cilat u ndërtua cloud computing — koncept i cili u promovua gjerësisht që nga viti **2007**.

Një nga arsyet ekonomike kryesore pas këtij kalimi është vendosja e infrastrukturës llogaritëse në zona me kosto më të ulëta — për hardware, softuer, të dhëna, hapësirë fizike dhe kërkesa energjetike — çka nënkupton kalimin nga llogaritja në desktop individual drejt cloud-eve të bazuara në qendra të dhënash (data centers) të mëdha e të centralizuara gjeografikisht (por logjikisht të disponueshme kudo).

### Sfidat kryesore teknologjike për ndërtimin e sistemeve të shpërndara

Për të ndërtuar sisteme të shpërndara efikase e të besueshme, teknologjia moderne duhet të adresojë disa sfida kyçe:

- zhvillimin e **procesorëve të rinj**, efikasë për punë në rrjet;
- **skema të shkallëzueshme** të memories dhe ruajtjes së të dhënave;
- **sisteme operative të shpërndara**;
- **middleware** për **virtualizimin** e makinave;
- **modele të reja programimi** të përshtatura për paralelizëm dhe shpërndarje;
- **menaxhim efikas të burimeve** (resource management);
- vetë **zhvillimin e programeve aplikative** që dinë të shfrytëzojnë këto mundësi.

### Rritja e Internetit (numri i kompjuterëve dhe Web serverëve)

Të dhënat historike ilustrojnë në mënyrë të dukshme sa shpejt është rritur Interneti si sistemi më i madh i shpërndarë që njeh njerëzimi:

| Data | Numri i kompjuterëve | Numri i Web serverëve | Përqindja e serverëve (%) |
|---|---:|---:|---:|
| Korrik 1993 | 1,776,000 | 130 | 0.008 |
| Korrik 1995 | 6,642,000 | 23,500 | 0.4 |
| Korrik 1997 | 19,540,000 | 1,203,096 | 6 |
| Korrik 1999 | 56,218,000 | 6,598,697 | 12 |
| Korrik 2001 | 125,888,197 | 31,299,592 | 25 |
| Korrik 2003 | ~200,000,000 | 42,298,371 | 21 |
| Korrik 2005 | 353,284,187 | 67,571,581 | 19 |

Vërehet një rritje eksponenciale e numrit të kompjuterëve të lidhur, si dhe një rritje po ashtu shumë e shpejtë e numrit të Web serverëve gjatë viteve '90 dhe fillimit të viteve 2000 — dëshmi konkrete se sa i shpejtë ka qenë përhapja e sistemeve të shpërndara në shkallë globale. (Për të dhëna të përditësuara mbi përdorimin aktual të internetit mund të shihet p.sh. internetlivestats.com.)

---

## 12. Rast studimi: World Wide Web (WWW)

World Wide Web-i shërben si shembulli më i njohur dhe më ilustrues për të kuptuar konceptet e sistemeve të shpërndara në praktikë: ndarjen e burimeve, arkitekturën klient-server dhe transparencën.

### 12.1 Çka është WWW-ja?

**World Wide Web** [www.w3.org; Berners-Lee, 1991] është një sistem për **publikimin dhe përdorimin e burimeve dhe shërbimeve** nëpër internet — në thelb, një **koleksion i madh dokumentesh elektronike** të ndërlidhura. Përmes shfletuesve Web (Web browsers), përdoruesit kanë në dispozicion dokumente të llojeve të ndryshme (tekst, video, audio) dhe mund të ndërveprojnë me një gamë praktikisht të pakufizuar shërbimesh.

Web-i "lindi" në **CERN** (Qendra Evropiane për Hulumtime Bërthamore), në Zvicër, në vitin **1989**, si mjet për shkëmbimin e dokumenteve mes komunitetit të fizikanëve të lidhur me internet [Berners-Lee, 1999].

### 12.2 Tri komponentët kryesorë të WWW-së

Web-i mbështetet mbi tri standarde teknologjike themelore që bashkëveprojnë ngushtë:

1. **HTML (HyperText Markup Language)** — gjuha për specifikimin e përmbajtjes dhe paraqitjes së faqeve Web, siç shfaqen nga shfletuesit.
2. **URL/URI (Uniform Resource Locator / Identifier)** — identifikuesit që lokalizojnë/identifikojnë dokumentet dhe burimet e tjera të ruajtura si pjesë e Web-it.
3. **Arkitektura klient-server, me protokollin HTTP (HyperText Transfer Protocol)** — rregullat dhe standardet e ndërveprimit, me anë të të cilave shfletuesit dhe klientë të tjerë marrin dokumente dhe burime nga serverët e internetit.

#### HTML — evolucioni i versioneve

HTML ka evoluar përgjatë kohës:

- HTML 1.0, 2.0, 3.0 (fillimet, epoka e "Netscape Navigator");
- HTML 3.2 (kodemri "WILBUR", standardizuar nga W3C më 1994);
- HTML 4.01 (kodemri "COUGAR", 1999) — solli mbështetjen për CSS;
- XHTML 1.0 (2000);
- HTML5 (2008), i konceptuar fillimisht edhe si "XHTML 2".

**HTML4 kundrejt HTML5** — krahasim i shkurtër:

| HTML 4 | HTML 5 |
|---|---|
| Mungonin shumë elemente që HTML5 do t'i sillte | U shtuan elemente semantike të reja si `<nav>`, `<header>`, `<footer>`, `<menu>` etj. |
| Menaxhim i dobët i gabimeve, për shkak të rregullave jo strikte | Rregulla strikte kodimi që mundësojnë menaxhim më të mirë të gabimeve |
| Përmbajtja multimediale kërkonte shtesa (plugins) të jashtme | U definuan elemente të dedikuara si `<audio>`, `<video>`, `<canvas>` |

#### URL ose URI

URL-ja u standardizua në vitin **1994** nga Tim Berners-Lee dhe grupi punues për URI brenda **IETF** (Internet Engineering Task Force) — shih RFC 1738.

Forma e përgjithshme e një HTTP URL është:

```
protokolli://emriIServerit[:port][/rrugaFajllit][?pyetje][#fragment]
```

Kjo strukturë shihet lehtë përmes shembujve konkretë:

| URL | Rruga e fajllit (Path) | Pyetja (Query) | Fragmenti |
|---|---|---|---|
| `http://fiek.uni-pr.edu` | (implicite/default) | (asnjë) | (asnjë) |
| `https://mail.google.com/mail/u/0/#inbox` | `mail/u/0` | (asnjë) | `inbox` |
| `https://www.google.com/?q=prishtina` | (implicite/default) | `prishtina` | (asnjë) |

**Shembull i shpjeguar hap pas hapi** (marrë nga një pyetje diskutimi mbi transparencën): `https://www.w3schools.com/java/java_intro.asp`

- Pjesa para shenjës `:` tregon **protokollin** e përdorur — këtu `https`.
- Pjesa ndërmjet `//` dhe `/` tjetër përfaqëson **domenin e hostit** — këtu `www.w3schools.com`.
- Pjesa e mbetur tregon **rrugën** drejt burimit specifik brenda atij hosti — këtu `java/java_intro.asp`.

Është me rëndësi konceptuale që emri i hostit (p.sh. `www...`) është i pavarur nga vendndodhja fizike reale e serverit — kjo siguron **transparencë në lokacion**: adresa konkrete e makinës që e ekzekuton shërbimin nuk përfshihet në URL, kështu që organizata mund ta zhvendosë shërbimin në një server tjetër fizik pa ndikuar në aksesin e përdoruesve. Megjithatë, nëse ofrimi i shërbimit i **kalon një organizate tjetër**, atëherë emri i domenit (pra vetë URL-ja) duhet të ndryshojë — pra transparenca në lokacion ka kufij: fsheh vendndodhjen fizike, por jo pronësinë/identitetin organizativ.

#### HTTP (HyperText Transfer Protocol)

HTTP-ja përfaqëson mënyrën se si komunikojnë shfletuesi dhe serveri Web. Karakteristikat kryesore:

- funksionon sipas modelit **kërkesë–përgjigje** (request-response);
- metodat kryesore janë **GET** (kërkim/marrje e një burimi) dhe **POST** (dërgim i të dhënave te serveri);
- meqë shfletuesit nuk mund të shfaqin çdo lloj përmbajtje, çdo kërkesë përmban një listë të tipeve të preferuara të përmbajtjes që klienti mund ta përpunojë;
- klasifikimi i llojeve të përmbajtjes bëhet përmes **MIME types** (Multi-Purpose Internet Mail Extensions), të shprehura si `Content-Type: tipi/nëntipi` (p.sh. `text/html`, `image/png`).

#### Faqet dinamike

Ndryshe nga faqet statike, **faqet dinamike** kanë një skriptë procesuese që ekzekutohet në anën e **serverit**: përmbajtja e kërkuar varet nga të dhënat hyrëse të vetë përdoruesit, dhe serveri i procesin këto të dhëna përpara se t'i kthejë përgjigjen.

- **CGI (Common Gateway Interface)** — një program që ekzekutohet nga serveri për të gjeneruar përmbajtje Web dinamike për klientin.
- Ekziston edhe kodi që ekzekutohet **brenda vetë shfletuesit** të klientit — p.sh. **JavaScript** (kod që shkarkohet automatikisht), **AJAX** (kërkesa asinkrone pa rifreskim të faqes) ose **Applet** (Java).

#### Të mirat dhe të metat e Web-it

**Të mirat:**

- publikimi i lehtë i burimeve/informacionit;
- struktura e hyperteksteve, e cila mundëson organizimin dhe lidhjen efikase të sasive shumë të mëdha informacioni;
- arkitektura e vetë sistemit (klient-server, e standardizuar dhe e hapur).

**Të metat:**

- disa lidhje (linqe) na çojnë drejt faqeve që janë zhvendosur ose fshirë ("broken links");
- në disa raste, motorët kërkues nuk kthejnë rezultatet e pritura nga përdoruesi.

---

## 13. Hyrje në XML (Extensible Markup Language)

XML-i lidhet ngushtë me HTML-në, por i shërben një qëllimi tjetër — jo prezantimit vizual, por **përfaqësimit dhe shkëmbimit të të dhënave të strukturuara**.

### 13.1 Çka është XML-i?

- XML-i është një gjuhë **"markup"** e dizajnuar posaçërisht për dokumente që përmbajnë **informacion të strukturuar**.
- Shpesh përshkruhet si "ura" (bridge) që mundëson **shkëmbimin e të dhënave** në internet mes sistemesh dhe aplikacionesh heterogjene.
- XML-i është zhvilluar nga **W3C** dhe është një **standard i hapur publik**.
- XML-i definohet formalisht përmes katër specifikave plotësuese:
  - **XML** (vetë gjuha bazë);
  - **XLL** (Extensible Linking Language) — lidhjet mes dokumenteve;
  - **XSL** (Extensible Style Language) — stilizimi/transformimi i dokumenteve;
  - **XUA** (XML User Agent).
- XML-i bazohet historikisht mbi **SGML** (Standard Generalized Markup Language), duke ruajtur fuqinë e SGML-së por duke e thjeshtuar ndjeshëm sintaksën.

### 13.2 XML kundrejt HTML

Dallimet themelore mes HTML-së dhe XML-së janë:

| HTML | XML |
|---|---|
| Ka tagje **fikse**, të paravendosura | Ofron mundësi **të pakufizuar zgjerimi** të tagjeve — vetë zhvilluesi i përcakton |
| I orientuar nga **prezantimi** (si duket faqja) | I orientuar nga **përmbajtja** (çka përfaqësojnë të dhënat) |
| — | Ofron një **infrastrukturë standarde** të të dhënave |
| Nuk ka aftësi për **validim** të të dhënave | Mundëson forma të shumta dalje (output) dhe ka **rregulla strikte** sintakse |

### 13.3 Çka NUK është XML-i

Për të shmangur keqkuptime, është e rëndësishme të theksohet se XML-i:

- **nuk** është zëvendësim për HTML-në (megjithëse HTML-i mund të **gjenerohet** nga XML-i);
- **nuk** është format prezantimi (megjithëse XML-i mund të **shndërrohet** në një format të tillë, p.sh. me XSL);
- **nuk** është gjuhë programimi (megjithëse mund të përdoret praktikisht brenda çdo gjuhe programimi);
- **nuk** është protokoll transferimi rrjeti (megjithëse mund të **transferohet** nëpër rrjet, p.sh. brenda HTTP-së);
- **nuk** është bazë të dhënash (megjithëse mund të përdoret si mjet për **ruajtjen** e të dhënave).

### 13.4 Përse XML-i thjeshton punën

- **Thjeshton shpërndarjen/transportin e të dhënave**: sistemet kompjuterike dhe bazat e të dhënave shpesh ruajnë informacionin në formate jokompatibile mes tyre; zhvilluesit që kanë nevojë të shkëmbejnë të dhëna mes sistemeve jokompatibile në internet mund të përdorin XML-in si format të ndërmjetëm universal.
- **Thjeshton përditësimin/upgrade-in e platformave**: kalimi në hardware ose softuer të ri është zakonisht një shpenzim i madh kohe, dhe gjatë konvertimeve të tilla numri i madh i të dhënave jokompatibile rrezikon të humbasë — XML-i, duke ruajtur të dhënat në formë tekstuale (të thjeshtë dhe pavarur nga platforma), e lehtëson ndjeshëm procesin e migrimit drejt sistemeve të reja, duke evituar humbjen e të dhënave.

### 13.5 Pema XML (XML Tree)

Dokumentet XML kanë gjithmonë strukturë në formë **peme** (hierarkike), duke filluar nga një **element rrënjë (root)** e deri te **degët** (elementet fëmijë).

Shembulli klasik i një mesazhi:

```xml
<mesazhi>
  <to>Altin</to>
  <from>Ardi</from>
  <heading>Njoftim</heading>
  <body>Mos harro per takimin</body>
</mesazhi>
```

Këtu, `<mesazhi>` është elementi **root** (rrënjë), ndërsa `<to>`, `<from>`, `<heading>` dhe `<body>` janë elemente **child** (fëmijë) të elementit rrënjë. Struktura e përgjithshme e një peme XML pason skemën:

```xml
<root>
  <child>
    <subchild>.....</subchild>
  </child>
</root>
```

Kjo strukturë hierarkike mund të thellohet sipas nevojës — p.sh. një element "Kompania" mund të përmbajë një ose disa elemente "Punetori", secili prej të cilëve përmban nën-elemente si "Emri", "Mbiemri", "Tel", "Email" dhe "Adresa" — ku vetë "Adresa" mund të jetë sërish një nënpemë me elementet "Shteti", "Qyteti" dhe "Zip". Kjo natyrë rekursive e strukturës i lejon XML-it të modelojë të dhëna arbitrarisht komplekse.

XML-i mund të përdoret gjithashtu për të modeluar edhe **lidhje kryq-referenciale** (jo vetëm hierarki të pastra) — p.sh. te modeli "LIBRAT" ku çdo `<libra>` ka një `id` unik, ndërsa çdo `<artikulli>` përmban një atribut `ref` që tregon (referencon) te id-ja e librit përkatës, duke krijuar kështu marrëdhënie ndërmjet dy nyjeve të ndryshme të pemës, të ngjashme me çelësat e jashtëm (foreign keys) në bazat relacionale të të dhënave.

### 13.6 Sintaksa e XML-it

Rregullat themelore sintaksore të XML-it janë strikte, ndryshe nga HTML-ja:

- çdo element XML duhet patjetër të ketë **tagun e hapjes dhe tagun e mbylljes**;
- tagjet janë **të ndjeshme ndaj shkronjave të mëdha/vogla (case-sensitive)** — tagu i hapjes dhe ai i mbylljes duhet të përputhen saktësisht;
- vlerat e **atributeve** duhet detyrimisht të jenë brenda **thonjëzave** (p.sh. `data="20/05/2014"`, jo `data=20/05/2014`).

**Referencat e entiteteve**: disa karaktere kanë domethënie speciale në XML (p.sh. `<` shënon fillimin e një tagu), prandaj nëse duam ta përdorim vetë karakterin `<` brenda tekstit, duhet ta zëvendësojmë me referencën e tij të entitetit — p.sh. `&lt;` në vend të `<`:

```
E gabuar: <shenim> piket < 60 </shenim>
E saktë:  <shenim> piket &lt; 60 </shenim>
```

### 13.7 Elementet dhe atributet në XML

Një **element XML** është gjithçka nga tagu i fillimit deri te tagu i fundit. Një element mund të përmbajë: elemente të tjerë (fëmijë), tekst, atribute, ose kombinim i të gjitha këtyre.

Për shembull, në një `<libraria>` që përmban disa `<libri>`, secili `<libri>` ka një **atribut** `kategoria` (p.sh. `kategoria="ROMANE"`), ndërsa `<titulli>`, `<autori>`, `<viti>` dhe `<cmimi>` janë elemente fëmijë që mbartin **përmbajtje teksti**.

**Atributet kundrejt elementeve** — e njëjta e dhënë mund të modelohet në dy mënyra:

```xml
<!-- Gjinia si atribut -->
<personi gjinia="mashkull">
  <emri>Andi</emri>
  <mbiemri>Shala</mbiemri>
</personi>

<!-- Gjinia si element -->
<personi>
  <gjinia>mashkull</gjinia>
  <emri>Andi</emri>
  <mbiemri>Shala</mbiemri>
</personi>
```

Zgjedhja mes atributit dhe elementit fëmijë është shpesh çështje konvencioni dizajni: atributet zakonisht përdoren për "metadata" të thjeshta, ndërsa elementet për të dhëna kryesore ose të strukturuara më tej.

### 13.8 Rregullat e emërtimit në XML

Kur emërtojmë elemente XML, duhet t'u përmbahemi këtyre rregullave:

- emrat mund të përmbajnë shkronja, numra dhe disa karaktere të tjera;
- emrat **nuk** mund të fillojnë me numër apo shenjë pikësimi;
- emrat **nuk** mund të fillojnë me fjalën "xml" (në asnjë kombinim shkronjash të mëdha/vogla — p.sh. `xml`, `Xml`, `XML`);
- emrat **nuk** mund të përmbajnë hapësira.

### 13.9 Namespaces në XML

Meqë emrat e elementeve në XML **definohen lirisht nga zhvilluesi**, shpesh lind **konflikt emrash** kur dokumente të ndryshme XML (të krijuar për aplikacione të ndryshme) përzihen së bashku. Shembulli klasik: një element `<table>` mund të nënkuptojë një "tabelë HTML" (me rreshta `<tr>` dhe qeliza `<td>`) në një kontekst, ndërsa në një kontekst tjetër i njëjti tag `<table>` mund të përfaqësojë një "tavolinë mobilje" me dimensione (`<gjerësia>`, `<gjatësia>`).

Zgjidhja standarde për këtë konflikt është përdorimi i **parashtresave (prefix)**, të njohura si **namespaces**:

```xml
<!-- Namespace "h" për HTML -->
<h:table>
  <h:tr>
    <h:td>Libri</h:td>
    <h:td>Laptopi</h:td>
  </h:tr>
</h:table>

<!-- Namespace "f" për mobilje -->
<f:table>
  <f:emri>Foto</f:emri>
  <f:gjerësia>80</f:gjerësia>
  <f:gjatësia>120</f:gjatësia>
</f:table>
```

Me anë të prefikseve `h:` dhe `f:`, dy elemente `<table>` me kuptime krejt të ndryshme dallohen qartazi dhe nuk bien më ndesh me njëri-tjetrin.

### 13.10 DTD (Document Type Definition)

**DTD-ja** përcakton **strukturën, elementet dhe atributet** e lejuara për një dokument XML — pra funksionon si "skema" ose "gramatikë" që dokumenti duhet ta respektojë. Një aplikacion mund të përdorë një DTD standard për të verifikuar nëse të dhënat e pranuara nga jashtë janë **valide**.

DTD-ja mund të deklarohet:

- **brenda (internal)** vetë dokumentit XML, ose
- **jashtë (external)**, në një fajll të veçantë `.dtd`, i referuar nga dokumenti.

**Deklarimi i elementeve:**

```
<!ELEMENT emri-elementit kategoria>
<!ELEMENT emri-elementit (permbajtja-e-elementit)>
```

Për elemente të zbrazëta (pa përmbajtje, si `<br>`):

```
<!ELEMENT emri-elementit EMPTY>
<!ELEMENT br EMPTY>
```

**Deklarimi i atributeve:**

```
<!ATTLIST emri-elementit emri-atributit tipi-atributit vlera-e-parazgjedhur>
```

**Deklarimi i entiteteve** (të ngjashme me "konstante" tekstuale të ripërdorshme):

```
<!-- Entitet i brendshëm -->
<!ENTITY emri-entitetit "vlera-e-entitetit">

<!-- Entitet i jashtëm -->
<!ENTITY emri-entitetit SYSTEM "URI/URL">
```

**"Blloqet themelore" të DTD-së:**

- **PCDATA (Parsed Character Data)** — karaktere teksti brenda tagjeve, të cilat **DO të analizohen (parsohen)** nga parser-i — pra parser-i i kontrollon këto për entitete dhe markup dhe **nuk duhet** të përmbajnë drejtpërdrejt karakteret `<`, `>` ose `&` pa i "escape-uar" (siç u tregua më sipër me `&lt;`).
- **CDATA (Character Data)** — tekst që **NUK** do të analizohet fare nga parser-i, pra trajtohet si tekst i pastër literal.

#### Shembull i një DTD-je të brendshme (internal)

```xml
<?xml version="1.0"?>
<!DOCTYPE note [
<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
]>
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend</body>
</note>
```

#### Shembull i një DTD-je të jashtme (external)

```xml
<?xml version="1.0"?>
<!DOCTYPE note SYSTEM "note.dtd">
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
```

Ku fajlli i veçantë `note.dtd` përmban:

```
<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
```

### 13.11 XML Parser dhe validimi

**XML Parser** është një librari ose paketë softuerike që vepron si ndërmjetësues mes aplikacioneve klient dhe vetë dokumenteve XML. Ai kontrollon nëse dokumenti ka **formatin e duhur** dhe, kur ka DTD ose skemë të lidhur, e **validon** dokumentin kundrejt asaj skeme. Shumica e shfletuesve moderne kanë të integruar XML parser-a; qëllimi themelor i parser-it është ta transformojë dokumentin XML në një strukturë/kod të lexueshëm dhe të përpunueshëm nga programi.

**Validimi** është procesi ku kontrollohet nëse një dokument XML përputhet me elementet, atributet dhe strukturën e definuar në DTD-në përkatëse. Ekzistojnë dy nivele të kontrollit gjatë këtij procesi:

1. **"Well-formed" (i mirëformuar)** — dokumenti respekton rregullat bazë sintaksore të XML-it (tagje të mbyllura siç duhet, nested në mënyrë të saktë, atribute me thonjëza etj.), pavarësisht nëse ka apo jo DTD.
2. **"Valid" (i vlefshëm)** — përveç se është "well-formed", dokumenti gjithashtu përputhet plotësisht me strukturën e specifikuar në DTD-në (ose skemën) e tij.

### 13.12 Gjuhët e kërkimit mbi XML: XML-QL dhe XQuery

Meqë XML-i modelon të dhëna të strukturuara, kanë lindur gjuhë të dedikuara pyetjesh (query languages) për të nxjerrë informacion prej dokumenteve XML, në mënyrë të ngjashme me SQL-in për bazat relacionale.

**Shembull XML-QL 1** — kërkim i të gjithë autorëve të librave të botuar nga "Morgan Kaufmann":

```
where <book>
        <publisher><name>Morgan Kaufmann</name></publisher>
        <title>$T</title>
        <author>$A</author>
      </book> in "www.a.b.c/bib.xml"
construct <result>$A</result>
```

**Shembull XML-QL 2** — kërkim i librarive që shesin "The Java Programming Language" nën 25$:

```
where <store>
        <name>$N</name>
        <book>
          <title>The Java Programming Language</title>
          <price>$P</price>
        </book>
      </store> in "www.store/bib.xml"
      $P < 25
construct <result>$N</result>
```

**Shembuj XQuery** — XQuery është gjuha standarde moderne e pyetjeve mbi XML (shumë e ngjashme funksionalisht me SQL-in, por e përshtatur për strukturën hierarkike të XML-it):

```
(: gjej artikujt që lidhen me Web nga Dan Suciu, viti 2014 :)
<results> {
FOR $a IN document("literature.xml")//article
   FOR $n IN $a//author, $t IN $a/title
   WHERE $a/@year = "2014"
     AND contains($n, "Suciu") AND contains($t, "Web")
   RETURN <result> $n $t </result> } </results>
```

```
(: gjej artikuj bashkë-autorë me autorë që kanë shkruar bashkërisht një libër pas 2010 :)
<results> {
FOR $a IN document("literature.xml")//article
   FOR $a1 IN $a//author, $a2 IN $a//author
   WHERE SOME $b IN document("literature.xml")//book SATISFIES
     $b//author = $a1 AND $b//author = $a2 AND $b/@year > "2010"
   RETURN <result> $a1 $a2 <wrote> $a </wrote> </result> } </results>
```

### 13.13 Konvertimi i bazave relacionale në XML

XML-i shpesh përdoret si format "eksporti" për të dhëna që origjinalisht ruhen në një **bazë të dhënash relacionale**. Për shembull, nëse kemi tabelat relacionale:

- `Store(sid, name, phone)`
- `Book(bid, title, authors)`
- `StoreBook(sid, bid, price, stock)`

këto mund të "eksportohen" dhe grupohen sipas dyqanit (store) në një dokument XML me strukturë hierarkike të tipit:

```xml
<store>
  <name>...</name>
  <phone>...</phone>
  <book>
    <title>...</title>
    <authors>...</authors>
    <price>...</price>
  </book>
  <book>...</book>
  ...
</store>
```

Ky proces ilustron mirë ndryshimin themelor konceptual mes modelit **relacional** (tabela të sheshta, të lidhura me çelësa) dhe modelit **XML** (pemë hierarkike, e cila mund të "ngjeshë" të dhëna nga disa tabela relacionale në një strukturë të vetme, të ndërfutur — nested).

---

## 14. Ueb Shërbimet (Web Services)

**Shërbimet Web** janë teknologji e shpërndarë që realizon ndërveprimin dhe bashkëpunimin mes ofruesve të shërbimit dhe klientëve nëpër rrjet. Karakteristikat kryesore të tyre:

- janë njësi relativisht të vogla kodi, secila me një numër të caktuar, të mirëpërcaktuar detyrash;
- përdorin protokolle komunikuese të bazuara në **XML**;
- janë **të pavarura** nga sistemi operativ dhe nga gjuha e programimit e përdorur (interoperabilitet);
- mundësojnë që aplikacione të ndryshme (të shkruara në teknologji të ndryshme) të **shkëmbejnë të dhëna** mes tyre pa probleme.

Tre komponentët klasikë të arkitekturës së shërbimeve Web janë:

- **SOAP (Simple Object Access Protocol)** — protokolli i bazuar në XML për shkëmbimin e mesazheve mes klientit dhe shërbimit;
- **WSDL (Web Services Description Language)** — gjuha që përshkruan çfarë ofron shërbimi dhe si thirret;
- **UDDI (Universal Description, Discovery and Integration)** — regjistri qendror ku shërbimet publikohen dhe zbulohen nga klientët e mundshëm.

Shërbimet Web ilustrojnë praktikisht pikërisht ato veti të sistemeve të shpërndara që u diskutuan në pjesën e parë të kapitullit: hapja (standarde publike si XML/SOAP/WSDL), heterogjeniteti (pavarësia nga platforma/gjuha), dhe ndarja e burimeve nëpër rrjet.

---

## 15. Pyetje dhe raste studimi për diskutim

Kapitulli mbyllet me një sërë rastesh studimi e pyetjesh diskutimi, që lidhin konceptet teorike me situata praktike. Më poshtë përmblidhen ato me vlerë më të madhe konceptuale.

### 15.1 Arkitektura klient-server e aplikacioneve të Internetit

Aplikacione si Web-i, email-i ose WhatsApp bazohen të gjitha në modelin klient-server: klienti (shfletuesi, aplikacioni mobil) dërgon kërkesa, ndërsa serveri i përgjigjet duke ofruar burimin ose shërbimin e kërkuar. Në aplikacionet e mesazherisë moderne (p.sh. WhatsApp), arkitektura shpesh kombinon një server qendror për ruajtjen e llogarive/statuseve me mekanizma për dërgim efikas mesazhesh mes klientëve (push notifications, ruajtje e përkohshme e mesazheve në server derisa marrësi të jetë online).

### 15.2 Sinkronizimi i orëve — Algoritmi i Cristian-it dhe NTP

Ky është një nga shembujt numerikë më të rëndësishëm të kapitullit, sepse ilustron konkretisht sfidën e "orëve të pasinkronizuara" të përmendur më sipër.

**(a) Sinkronizimi në një LAN, pa burim të jashtëm kohe** — kjo mund të bëhet përmes shkëmbimit të mesazheve:

1. Zgjidhet një kompjuter si **referencë (master)**.
2. Kompjuteri tjetër (**slave**) i dërgon masterit një kërkesë (mesazhi `mr`) për kohën aktuale, në momentin `t`.
3. Masteri përgjigjet me kohën e vet (mesazhi `mt`).
4. Slave-i llogarit **kohën e plotë të udhëtimit vajtje-ardhje (round-trip time, Tround)**, që nevojitet nga dërgimi i `mr` deri te marrja e `mt`.
5. Slave-i e cakton orën e vet në **t + Tround/2** — pra supozon që gjysma e kohës së udhëtimit ka shkuar drejt masterit, dhe gjysma tjetër ka ardhur mbrapsht.

Kjo njihet si **algoritmi i Cristian-it (Cristian's Algorithm)**. Procedura është subjekt pasaktësie për shkak të vonesave të ndryshueshme në përpunimin e mesazheve nga sistemet operative të dy kompjuterëve.

**Shembulli numerik nga slajdet:** Në një LAN, `Tround` zakonisht është **1–10 ms**. Gjatë kësaj kohe, një orë me shkallë devijimi (drift) prej **10⁻⁶** (pra një pjesë në milion), ndryshon më së shumti rreth **10⁻⁵ milisekonda** — pra gabimi i shtuar nga vetë drift-i i orës gjatë intervalit të matjes është shumë i vogël krahasuar me vetë vonesën e rrjetit.

**(b) Faktorët që kufizojnë saktësinë** e kësaj procedure:

- vonesa e ndryshueshme e rrjetit (latency jo konstante);
- **asimetria** e vonesave (koha e shkuar dhe koha e ardhur mund të mos jenë të barabarta);
- ngarkesa e procesorit në secilën makinë, që shkakton vonesë shtesë në përgjigje;
- kongjestioni (mbingarkesa) e rrjetit;
- **drift-i** i orëve fizike, që devijojnë natyrshëm me kalimin e kohës;
- humbje ose ridërgim paketash.

Kur duam saktësi më rigoroze matematikisht, mund të llogaritet edhe një **interval besueshmërie**: nëse `min` është koha minimale e mundshme e transmetimit, atëherë momenti kur serveri `S` e vendos kohën `mt` mund të jetë sa më herët `min` pas dërgimit të `mr`, ose sa më vonë `min` para se `mt` të mbërrijë te `p`. Kjo do të thotë se koha reale e `S` në momentin e dërgimit të `mt` bie diku në intervalin **[t + min, t + Tround − min]**. Gjerësia e këtij intervali është **Tround − 2·min**, dhe rrjedhimisht **saktësia** e procedurës shprehet si **±(Tround/2 − min)**.

**(c) Sinkronizimi në shkallë të gjerë (Internet)** — për një numër shumë të madh kompjuterësh, përdoret **NTP (Network Time Protocol)**:

- NTP organizon serverët e kohës në një **hierarki me nivele (strata)**;
- klientët sinkronizohen me një ose më shumë servera kohe;
- protokolli përdor **mesatarizim statistikor dhe filtrim** për të rritur saktësinë dhe besueshmërinë e matjeve;
- lejon sinkronizim edhe në prani të vonesave të rrjetit që ndryshojnë (jo konstante).

Për sisteme që kërkojnë saktësi ekstremisht të lartë (p.sh. tregtimi financiar me shpejtësi të lartë, rrjete telekomi), përdoret **PTP (Precision Time Protocol)**, i cili arrin saktësi shumë më të mirë se NTP (deri në nivel mikrosekondash apo edhe më të vogël).

### 15.3 Lojëra online multiplayer — qasja e serverit të vetëm autoritativ

Në lojërat online multiplayer me shumë përdorues, një qasje e zakonshme dizajni është përdorimi i një **serveri të vetëm autoritativ** që mban gjendjen "të vërtetë" të lojës, ndërsa klientët dërgojnë vetëm inpute (jo gjendjen e tyre).

**Avantazhet:**

- **konsistencë e plotë** — nuk ka kontradikta mes kopjeve të ndryshme të gjendjes së lojës;
- **zbatim i drejtë i rregullave**, sepse gjithçka verifikohet nga një burim i vetëm i së vërtetës;
- **mbrojtje kundër mashtrimeve (cheating)**, sepse klientët nuk kanë autoritet mbi gjendjen e lojës.

**Disavantazhet:**

- kufizime në **shkallëzim** — një server i vetëm ka kapacitet të kufizuar;
- **ngarkesë e lartë** mbi atë server të vetëm;
- **vonesë (latencë)** e ndjeshme për lojtarët gjeografikisht të largët nga serveri;
- rrezik si **pikë e vetme dështimi**.

**Zgjidhje praktike** për këto probleme: ruhet autoriteti qendror i serverit, por bota e lojës **shpërndahet** në shard-e ose zona të veçanta; përditësimet kufizohen përmes "menaxhimit të interesit" (interest management — dërgohen te klienti vetëm ndryshimet relevante për të); dhe përdoret **parashikim në anën e klientit (client-side prediction)** me korrigjim të mëvonshëm nga serveri, për të fshehur latencën nga këndvështrimi i lojtarit.

### 15.4 Konkurrenca dhe problemi i "lost update" — shembulli i BLOB-it

Kur disa klientë ekzekutojnë kërkesa **njëkohësisht** mbi të njëjtin burim të përbashkët (p.sh. një objekt BLOB — Binary Large Object, i ruajtur i shpërndarë në disa servera për shkallëzim dhe performancë), lind pyetja: a duhet t'i lejojmë këto ekzekutime të ndodhin konkurrentisht?

**Argumente për ekzekutimin konkurrent:**

- **throughput** (kapacitet përpunimi) dhe **performancë** më të larta, sepse serveri shfrytëzon më mirë CPU-në dhe I/O-në (veçanërisht kur disa kërkesa bllokohen pas diskut/rrjetit);
- **latencë më e ulët** për klientët — nuk presin në radhë njëri pas tjetrit;
- **shkallëzim më i mirë**, p.sh. me anë të threads, kod asinkron ose event loop;
- konkurrenca përputhet natyrshëm me vetë natyrën e sistemeve të shpërndara, ku kërkesat mbërrijnë paralelisht dhe serializimi i plotë shpesh bëhet "bottleneck" (fyt shishe).

**Argumente kundër (rreziqet):**

- **race conditions** — dy klientë mund ta ndryshojnë të njëjtin burim njëkohësisht, duke prodhuar rezultat të gabuar;
- **"përzierje" (interleaving)** e operacioneve — operacione që duken "atomike" logjikisht shpesh implementohen në disa hapa (lexo → modifiko → shkruaj), dhe hapat e klientëve të ndryshëm mund të "ndërthuren";
- **lost update** — një përditësim humbet plotësisht sepse një përditësim tjetër e mbishkruan;
- **lexime "të ndyra" ose të pjesshme** — një klient mund të lexojë të dhëna ndërsa një klient tjetër ende po i shkruan (gjendje e paqëndrueshme);
- rritje e **kompleksitetit** — nevojiten locks, transaksione, versionim, retry-logjikë, shmangie deadlock-esh etj.

**Shembulli klasik i "race condition"**: Threadi i klientit A lexon vlerën e variablës X; threadi i klientit B lexon po ashtu vlerën e njëjtë X; A e shton 1 X-it dhe e ruan; B e zbret 1 X-it dhe e ruan. Nëse të dy nisin nga e njëjta vlerë e lexuar (pa sinkronizim), rezultati përfundimtar del **X = X − 1**, në vend të X-it origjinal (i pandryshuar, sepse +1 dhe −1 duhej të anulonin njëri-tjetrin). Nëse X do të përfaqësonte p.sh. balancin e një llogarie bankare ku A bën kredit dhe B bën debit, rezultati do të ishte thjesht **i gabuar**. Zgjidhja standarde për këtë lloj problemi janë teknikat klasike të sinkronizimit, si **semaforët**, që përdoren gjerësisht në sistemet operative dhe në sistemet e shpërndara për të siguruar që operacionet mbi një burim të përbashkët të mbeten **konsistente**.

**Shembull konkret i "interleaving"-ut me operacione append**: supozojmë se serveri (jo-atomikisht) e implementon operacionin "shto në fund" (append) përmes hapave: (1) lexo gjatësinë aktuale, (2) llogarit pozicionin (offset) ku do të shkruhet, (3) shkruaj të dhënat në atë pozicion, (4) përditëso metadata (gjatësinë e re). Nëse klienti A dhe klienti B thërrasin `append` njëkohësisht:

1. A lexon gjatësinë = 100; B lexon po ashtu gjatësinë = 100;
2. A shkruan `dataA` në offset 100;
3. B shkruan `dataB` gjithashtu në offset 100 — duke **mbishkruar** atë çka sapo shkroi A;
4. A përditëson gjatësinë në 120;
5. B përditëson gjatësinë në 130 (mbishkruan vlerën e A-së).

Rezultati: BLOB-i përfundon me vetëm `dataB` (ose një përzierje të korruptuar), ndërsa metadata (gjatësia e regjistruar) nuk përputhet më me përmbajtjen reale — pra kemi njëkohësisht **lost update** dhe **korruptim të të dhënave**. Ky shembull ilustron pse operacionet konkurrente mbi burime të përbashkëta kërkojnë gjithmonë mekanizma eksplicitë sinkronizimi (p.sh. locks, operacione atomike compare-and-swap, ose transaksione).

### 15.5 Cloud computing kundrejt client-server computing

**Cloud computing** është praktika e ruajtjes dhe qasjes së të dhënave/programeve nëpërmjet internetit, në vend të ruajtjes lokale në hard drive-in e vetë kompjuterit të përdoruesit; brenda cloud-it mund të hostohen praktikisht çdo lloj aplikacioni klient-server.

Dallimi kryesor: në **client-server computing** klasike, serveri zakonisht është **lokal** — në pronësi dhe operim të vetë punëdhënësit, i qasshëm brenda një rrjeti privat, i përdorur ekskluzivisht nga punonjësit e organizatës. Në **cloud computing**, serveri i qasemi përmes internetit dhe ai zakonisht është në pronësi të ndërmarrjeve të mëdha (p.sh. Google) ose ofruesve specializuar (startup-e që ofrojnë ruajtje/llogaritje si shërbim).

Cloud computing ka fituar popullaritet të madh falë avantazheve si: **fuqi e lartë llogaritëse** në dispozicion sipas kërkesës, **kosto e ulët** e shërbimeve (paguan vetëm për çka përdor), **performancë e lartë**, **shkallëzueshmëri** e menjëhershme, si dhe **qasje dhe disponueshmëri** e lartë nga kudo.

### 15.6 Ndarja e burimeve dhe HTML/URL/HTTP si teknologji bazë

**Ndarja e burimeve** (resource sharing) nënkupton procesin ku burimet vihen në dispozicion për t'u përdorur nga një host te tjetri nëpër rrjet — e cila lejon shumë përdorues t'u qasen njëkohësisht të njëjtave të dhëna, dhe përfaqëson faktorin kyç që ka nxitur zhvillimin e vetë sistemeve të shpërndara. Web-i (faqet Web) është shembulli klasik i shpërndarjes/ndarjes së burimeve, ku serveri (kompjuteri ose programi që menaxhon qasjen te burimi) shërben kërkesat e klientëve (kompjuterë ose programe, zakonisht shfletues, që gjenerojnë kërkesat).

Vlerësim i shkurtër i avantazheve/disavantazheve të secilës teknologji themelore:

- **HTML**: e lehtë për t'u lexuar nga çdo shfletues, kodim i thjeshtë me shabllone të gatshme — por krijon vetëm faqe **statike**, ka pothuajse **asnjë siguri të integruar**, dhe gabimet mund të jenë të kushtueshme (jo strikte).
- **URL**: referencë e thjeshtë dhe e lehtë për lokacionin e një burimi — por rrezikon **ridrejtim të lehtë** drejt faqesh mashtruese, ose të referojë burime të zhvendosura/fshira ("broken links").
- **HTTP**: protokoll i thjeshtë për implementim, i përshtatshëm për shumë lloje transferimesh, dhe relativisht i lehtë për memorie/CPU (falë numrit jo shumë të lartë të lidhjeve njëkohëse) — por është "verbose" (përdor shumë fjalë/tekst), jo shumë efikas për sasi të vogla të dhënash, jo optimal për pajisje mobile, dhe nuk ofron nga vetja **shkëmbime të besueshme** (rikuperim gabimesh etj.).

Përfundimisht, URL-ja dhe HTTP-ja mund të shërbejnë si teknologji bazë për modelin klient-server, por gjithmonë duke pasur parasysh kufizimet e tyre dhe duke kërkuar zgjidhje shtesë optimizuese sipas rastit.

### 15.7 Heterogjeniteti — pesë aspektet

Kur një server (p.sh. i shkruar në C++) ofron një objekt BLOB të qasshëm nga klientë të shkruar në gjuhë të tjera (p.sh. Java), mbi harduer potencialisht të ndryshëm, por të gjithë të lidhur nëpërmjet internetit, ndeshemi me problemin e **heterogjenitetit** — llojllojshmëria dhe ndryshimi që aplikohet ndaj:

1. **Rrjetave** — Interneti përbëhet nga lloje të ndryshme rrjetesh, por dallimet mes tyre **fshihen** falë protokolleve të përbashkëta komunikimi (p.sh. TCP/IP) që përdorin të gjitha makinat e lidhura.
2. **Harduerit** — makinat e ndryshme mund të kenë formate të ndryshme të përfaqësimit të të dhënave (p.sh. "endianness"); prandaj duhet definuar një **standard i përbashkët** për çdo tip të dhëne që shkëmbehet.
3. **Sistemit operativ** — sistemet operative nuk ofrojnë të njëjtin API për protokollet e internetit (p.sh. thirrjet për shkëmbim mesazhesh ndryshojnë mes UNIX dhe Windows); zgjidhja është një **shtresë abstraksioni** (p.sh. në nivel Java/C++) që përkthen operacionin e përbashkët në thirrjen specifike të sistemit operativ ku ekzekutohet programi.
4. **Gjuhës programuese** — gjuhë të ndryshme (këtu C++ dhe Java) kanë përfaqësime të ndryshme për struktura si stringjet, vektorët, rekordet; prandaj duhet një **standard i përbashkët** për çdo lloj strukture të dhënash që shkëmbehet, si dhe një mekanizëm **përkthimi** (marshalling/unmarshalling) drejt/nga gjuha specifike.
5. **Implementimit nga zhvillues të ndryshëm** — programe të shkruara nga zhvillues të ndryshëm nuk mund të komunikojnë mes vete nëse nuk përdorin standarde të njëjta; kjo kërkon **dakordësi dhe dokumentim** të hapur të standardeve të përdorura.

### 15.8 Hapja (openness) kundrejt heterogjenitetit

Kur duam të shtojmë një shërbim të ri (p.sh. objektin BLOB) në një sistem të hapur ekzistues, të qasshëm nga një shumëllojshmëri programesh klienti, standardet e nevojshme (të diskutuara te heterogjeniteti më lart) tashmë duhet të jenë në fuqi për sistemin: një grup i përbashkët protokollesh komunikimi, një standard për përfaqësimin e artikujve të të dhënave, një standard për operacionet e kalimit të mesazheve/thirrjeve, dhe një standard i pavarur nga gjuha për përfaqësimin e strukturave të të dhënave.

Megjithëse **hapja** dhe **heterogjeniteti** janë koncepte të lidhura ngushtë, ato përmbushin nevoja **të ndryshme**:

- **Hapja** ka të bëjë me aftësinë e sistemit për të shtuar dhe përdorur shërbime të reja përmes **ndërfaqeve dhe standardeve të publikuara**, pa ndikuar te klientët ekzistues — fokusi është te **zgjerueshmëria dhe ndërveprueshmëria logjike** e shërbimeve. Publikimi i standardeve lejon që pjesë të ndryshme të sistemit të implementohen nga shitës të ndryshëm dhe të bashkëpunojnë pa probleme.
- **Heterogjeniteti** ka të bëjë me mbështetjen e klientëve që ekzekutohen mbi platforma, sisteme operative dhe gjuhë **të ndryshme** — fokusi është te **kapërcimi i dallimeve teknike** mes mjediseve të ndryshme.

Pra, ndërfaqja e objektit BLOB duhet **publikuar** në mënyrë të tillë që, kur të shtohet në sistem, klientët — si ata ekzistues, edhe ata të rinj (të ndryshëm teknikisht) — të mund t'i qasen njëlloj.

### 15.9 Siguria për funksionet e mbrojtura

Nëse funksionet e një objekti (p.sh. BLOB) ndahen në **publike** (të qasshme nga të gjithë) dhe **të mbrojtura** (të qasshme vetëm nga përdorues të zgjedhur), sigurimi që vetëm përdoruesit e autorizuar t'u qasen funksioneve të mbrojtura kërkon adresimin e disa problemeve:

- **definimi i identitetit të përdoruesit** — identiteti i përfshirë në kërkesë duhet kontrolluar kundrejt listës së përdoruesve të autorizuar të qasen në atë funksion të mbrojtur;
- **vërtetësia e identitetit (autentikimi)** — sigurimi që identiteti i deklaruar vjen vërtet nga përdoruesi i pretenduar dhe jo nga dikush që "gënjen" (impersonation);
- **ndërhyrja në kërkesë** — parandalimi që përdorues të tjerë të ripërsërisin ("replay") kërkesat legjitime të dikujt tjetër, ose t'i keqpërdorin ato.

Problem shtesë: informacioni i nxjerrë nga një funksion i mbrojtur duhet **udhëtuar në mënyrë të sigurt** — pra duhet i **enkriptuar**, në mënyrë që të mbrohet nga ndërhyrjet (përgjimi) e përdoruesve të paautorizuar gjatë transmetimit.

### 15.10 Emërtimi (naming) i burimeve në shkallë të gjerë

Kur një shërbim (p.sh. "INFO") menaxhon një numër potencialisht shumë të madh burimesh, secili i qasshëm nga kudo në internet përmes një çelësi/emri, projektimi i skemës së emërtimit ndikon direkt në performancën e sistemit. Parimet kyçe të një dizajni të mirë:

- algoritmet e emërtimit duhet të jenë **të decentralizuara**, për të shmangur "pengesat" (bottlenecks) e performancës;
- strukturat **hierarkike** të emërtimit shkallëzohen zakonisht më mirë se strukturat lineare, prandaj zgjidhja e preferuar është një **skemë hierarkike**;
- burimet mund të **ndahen mes disa serverëve** (p.sh. emrat që fillojnë me "A" në serverin 1, ata me "B" në serverin 2, e kështu me radhë) — pra decentralizim, i cili mund të ketë edhe më shumë se një nivel ndarjeje;
- i njëjti server **nuk** duhet të përfshihet domosdoshmërisht në kërkimin e çdo emri — kjo shmang "fyt shishen" (bottleneck) e një pike të vetme.

Si krahasim, një zgjidhje thjesht **e centralizuar** do të përdorte vetëm një server "root" të vetëm, që mban një bazë të dhënash qendrore të vendndodhjes, e cila harton (mapon) çdo emër te serveri specifik ku ndodhet informacioni — një qasje që, siç e diskutuam edhe te "keqkuptimet e zakonshme" më sipër, mund të funksionojë mirë nëse centralizimi është vetëm **logjik**, por rrezikon fyt-shishe performance nëse çdo kërkim duhet detyrimisht të kalojë nëpër atë nyje të vetme.

---

## Përmbledhje

Ky kapitull hodhi themelet konceptuale të lëndës "Sistemet e Shpërndara", duke ndjekur një linjë logjike nga definimi abstrakt deri te zbatimet praktike:

- Një **sistem i shpërndarë** është një koleksion kompjuterësh të pavarur që i shfaqet përdoruesit si një sistem i vetëm koherent, i ndërtuar mbi procese të shumta, komunikim mes tyre, hapësira adresash të ndara dhe një qëllim të përbashkët.
- Dallimi mes **centralizimit, decentralizimit dhe shpërndarjes** nuk është gjithmonë i mprehtë; shumë "keqkuptime" të zakonshme (si "centralizimi = pikë e vetme dështimi = keq gjithmonë") duhen vlerësuar kritikisht — shembulli i DNS-it e tregon këtë qartë.
- Sistemet e shpërndara kuptohen më mirë përmes tetë **këndvështrimesh** plotësuese: arkitektura, proceset, komunikimi, koordinimi, emërtimi, konsistenca/replikimi, toleranca ndaj dështimeve dhe siguria.
- Katër janë **qëllimet kryesore të projektimit**: ndarja e burimeve, transparenca, hapja dhe shkallëzueshmëria — ku **transparenca** vetë ndahet në tetë nëntipe (qasje, lokacion, konkurrencë, replikim, prishje, mobilitet, performancë, shkallëzim).
- Sistemet e shpërndara përballen me sfida themelore që nuk ekzistojnë në llogaritjen jo të shpërndarë: njohuri vetëm lokale, orë të pasinkronizuara, mungesë hapësire adresash të përbashkët, dhe probleme klasike si zgjedhja e udhëheqësit, përjashtimi i ndërsjellë, sinkronizimi i kohës, fotografimi i gjendjes së shpërndarë dhe menaxhimi i replikave.
- **World Wide Web-i** shërben si rast studimi kryesor: tre shtyllat e tij (HTML, URL/URI, HTTP) ilustrojnë praktikisht si arrihet ndarja e burimeve dhe transparenca e lokacionit në një sistem real, masiv e të shpërndarë.
- **XML-i** plotëson pamjen si standardi kryesor për përfaqësimin dhe shkëmbimin e të dhënave të strukturuara mes sistemeve heterogjene, me mekanizma si DTD-ja për validim strukturor dhe namespace-t për shmangien e konflikteve të emrave.
- **Shërbimet Web** (SOAP, WSDL, UDDI) tregojnë si këto ide (XML, hapje, ndërveprueshmëri) përkthehen në infrastrukturë reale për komunikim mes aplikacioneve.
- Rastet e diskutimit në fund të kapitullit lidhin teorinë me praktikën: llogaritja numerike e saktësisë së sinkronizimit të orëve (algoritmi i Cristian-it, ±(Tround/2 − min)), problemet e konkurrencës (race conditions, lost update), dhe pesë aspektet e heterogjenitetit që duhen tejkaluar për ndërveprueshmëri të plotë mes klientëve dhe serverëve heterogjenë.

### Pyetje për vetë-kontroll

1. Shpjegoni dallimin mes centralizimit **logjik** dhe atij **fizik**, duke përdorur shembullin e DNS-it.
2. Renditni dhe përshkruani shkurtimisht tetë llojet e transparencës në sistemet e shpërndara. Cila prej tyre, sipas jush, është më e vështirë të arrihet plotësisht dhe përse?
3. Pse thuhet se "algoritmi i Cristian-it" jep vetëm një përafrim, jo një sinkronizim të përsosur të orëve? Çka do të ndodhte me saktësinë nëse `Tround` do të ishte më i madh (p.sh. në një lidhje satelitore, jo LAN)?
4. Në çfarë kushtesh ekzekutimi **konkurrent** i kërkesave mbi një burim të përbashkët (si BLOB-i) është i dëshirueshëm, dhe çfarë mekanizmash mund të përdoren për të shmangur problemet e "lost update"?
5. Shpjegoni ndryshimin mes **hapjes** dhe **heterogjenitetit** si dy koncepte të lidhura, por jo identike, në kontekstin e shtimit të një shërbimi të ri në një sistem ekzistues të shpërndarë.
6. Krahasoni XML-në me HTML-në: në çka ndryshojnë nga qëllimi i tyre themelor, dhe pse XML-i "nuk është" zëvendësim i HTML-së, edhe pse mund të gjenerojë HTML?
