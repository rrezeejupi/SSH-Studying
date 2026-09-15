# Pyetjet e Provimeve — Sistemet e Shpërndara (me Përgjigje)

Ky dokument mbledh **të gjitha pyetjet** e gjetura në tri provime të lëndës *"Sistemet e Shpërndara"* (Fakulteti i Inxhinierisë Elektrike dhe Kompjuterike, Universiteti i Prishtinës — Prof. Ass. Dr. Dhuratë Hyseni & MSc. Ass. Blend Arifaj, disa herë me Dalina Vranovci), së bashku me **përgjigjet e tyre**, të bazuara në 11 kapitujt e përgatitur më parë nga ligjëratat e lëndës.

Janë tri provime të ndryshme:

1. **Provimi 1** — dt. 19.12.2025, Provim, Grupi A (60 minuta) — 15 pyetje
2. **Provimi 2** — dt. 28.03.2025, Provim (60 minuta) — 10 pyetje (disa identike me Provimin 1)
3. **Provimi 3** — dt. 08.07.2025, Provim-IKS (75 minuta) — 16 pyetje

---

## Provimi 1 — dt. 19.12.2025, Grupi A

### Pyetja 1 [8 pikë]
**Shkruani 2–3 veçori të sistemeve të shpërndara *sinkrone* dhe *asinkrone*?**

**Përgjigje:**

Në një **sistem sinkron**:
- Çdo proces ka një kufi të njohur dhe të garantuar kohor për ekzekutimin e çdo hapi (nuk ekziston "kohë e panjohur" pritjeje).
- Çdo mesazh i dërguar mes proceseve arrin brenda një kufiri të njohur kohor (vonesë maksimale e njohur në rrjet).
- Çdo proces ka një orë lokale me shkallë të njohur devijimi nga koha reale, gjë që lejon sinkronizim të saktë të orëve fizike.
- Rrjedhojë: në sistemet sinkrone është e mundur të përdoret **timeout** për të dalluar me siguri një proces të ngadaltë nga një proces i dështuar (crash).

Në një **sistem asinkron**:
- Nuk ka kufi të njohur mbi kohën e ekzekutimit të një hapi procesi — një proces mund të "ngrijë" përkohësisht (p.sh. për shkak të garbage collection-it apo ngarkesës) pa u konsideruar i dështuar.
- Nuk ka kufi të njohur mbi vonesën e dërgimit të mesazheve — një mesazh mund të vonohet në mënyrë të pakufizuar.
- Nuk ka kufi mbi shpejtësinë e devijimit të orëve, prandaj sinkronizimi i saktë i kohës është i vështirë ose i pamundur të garantohet.
- Rrjedhojë: në sistemet asinkrone është e **pamundur** të dallohet me siguri absolute nëse një proces është i ngadaltë apo i dështuar (kjo është arsyeja pse shumica e sistemeve reale të internetit — p.sh. TCP/IP — konsiderohen praktikisht asinkrone).

Shumica e sistemeve reale të shpërndara (interneti, aplikacionet web) janë **asinkrone** në thelb, ndërsa sistemet kohë-reale (p.sh. kontrolli industrial) modelohen si **sinkrone**.

---

### Pyetja 2 [8 pikë]
**Përshkruani paradigmën e renditjes së mesazheve (*message queue*) dhe tregoni tri teknikat e marrjes së mesazheve.**

**Përgjigje:**

**Message queue** (radhë mesazhesh) është një formë e **komunikimit indirekt/asinkron**, ku prodhuesit (producer/sender) e mesazheve nuk komunikojnë direkt me konsumatorët (receiver), por i dërgojnë mesazhet në një **radhë të ndërmjetme** (të menaxhuar nga një server/broker mesazhesh). Kjo i **shkëput në kohë dhe hapësirë** dërguesin nga marrësi: dërguesi nuk ka nevojë ta dijë kush/kur do ta marrë mesazhin, dhe marrësi mund të mos jetë as i lidhur në momentin e dërgimit. Mesazhet zakonisht ruhen në radhë sipas rendit FIFO (ndonjëherë me përparësi/priority), dhe qëndrojnë atje derisa të merren (ose të skadojnë).

Tri teknikat kryesore të marrjes së mesazheve nga radha janë:

1. **Marrja bllokuese (blocking receive)** — procesi marrës thërret `receive()` dhe **bllokohet** (pushon ekzekutimin) derisa të mbërrijë një mesazh në radhë.
2. **Marrja jo-bllokuese/vote-lëshim (non-blocking / polling receive)** — procesi thërret `receive()` dhe kthehet menjëherë, qoftë me një mesazh (nëse ka) qoftë me tregues bosh; procesi duhet ta "pyesë" (poll) radhën periodikisht vetë.
3. **Njoftimi/callback i bazuar në ngjarje (notification-based / callback receive)** — procesi regjistron një funksion "callback" (dorezues të thirrjes), dhe sistemi/middleware-i e thërret automatikisht atë funksion kur mbërrin një mesazh i ri, pa e bllokuar procesin dhe pa kërkuar polling aktiv.

---

### Pyetja 3 [8 pikë]
**Përshkruaj dhe vizatoni vetitë bazike të transaksioneve *flat* dhe *nested*.**

**Përgjigje:**

Një **transaksion flat (i rrafshët)** kryen një sekuencë kërkesash (operacione) drejt një ose disa serverëve, njëra pas tjetrës, brenda kufijve `openTransaction`/`begin` … `commit`/`abort`, pa ndonjë ndarje të brendshme hierarkike:

```
openTransaction
   op1 (server A)
   op2 (server B)
   op3 (server A)
commit
```

Karakteristikat kryesore: (i) është një njësi *e vetme* atomike — ose kryhen të gjitha operacionet, ose asnjëra; (ii) nuk mund të kesh commit të pjesshëm — nëse dështon qoftë edhe një operacion, i gjithë transaksioni bëhet abort; (iii) është e thjeshtë për t'u implementuar, por pa paralelizëm të brendshëm (operacionet zakonisht kryhen sekuencialisht nga këndvështrimi i klientit).

Një **transaksion nested (i ndërthurur/hierarkik)** organizohet si një **pemë** nën-transaksionesh: një transaksion "prind" (top-level) mund të nisë disa **nën-transaksione** të pavarura, secili prej të cilëve mund të vazhdojë të nisë nën-transaksione të tjera, e kështu me radhë:

```
                Transaksioni Prind (T)
               /            \
           T1 (nën)         T2 (nën)
          /     \                \
        T11    T12               T21
```

Karakteristikat kryesore: (i) nën-transaksionet mund të ekzekutohen **paralelisht** dhe në serverë të ndryshëm, duke rritur konkurrencën; (ii) çdo nën-transaksion mund të bëjë **commit ose abort në mënyrë të pavarur**, pa e prishur automatikisht tërë pemën; (iii) commit-i i një nën-transaksioni është **i kushtëzuar**: bëhet "final" vetëm nëse edhe transaksioni prind (rrënja e pemës) bën commit në fund — kjo njihet ndryshe si **commit provizor**; (iv) nëse një nën-transaksion bën abort, vetëm efektet e tij (dhe të pasardhësve të tij në pemë) zhbëhen, pa prishur domosdoshmërisht nën-transaksionet "vëllazërore".

---

### Pyetja 4 [8 pikë]
**Përshkruani dhe krahasoni Fazën e mbylljes (Two-Phase Locking — 2PL) dhe Fazën e mbylljes strikte (Strict 2PL).**

**Përgjigje:**

**Two-Phase Locking (2PL)** është një protokoll i kontrollit të konkurrencës i ndarë në dy faza për çdo transaksion:

1. **Faza rritëse (growing phase):** transaksioni mund të **marrë** (acquire) bllokime (lock) të reja mbi objektet që i duhen, por **nuk mund të lëshojë** asnjë bllokim.
2. **Faza zvogëluese (shrinking phase):** transaksioni fillon të **lëshojë** bllokime, dhe nga ky moment **nuk mund të marrë** më bllokime të reja.

Pika kur transaksioni lëshon bllokimin e parë të tij shënon kalimin nga faza e parë në të dytën. Kjo garanton **serializueshmërinë** e planeve (schedule) të ekzekutimit — d.m.th. rezultati përfundimtar është ekuivalent me ndonjë ekzekutim sekuencial (jo të njëkohshëm) të transaksioneve.

**Problemi i 2PL "të thjeshtë":** meqë bllokimet lëshohen para se transaksioni të bëjë commit (gjatë fazës zvogëluese, e cila mund të fillojë përpara commit-it), të dhënat e shkruara nga një transaksion mund të bëhen të dukshme për transaksione të tjera **para** se transaksioni origjinal të konfirmohet (commit) — kjo mund të çojë në **leximin e të dhënave "të pista"** (dirty read) nëse transaksioni origjinal më vonë bën abort (rollback), duke lënë transaksionet e tjera me vlera që nuk kanë ekzistuar kurrë zyrtarisht.

**Strict Two-Phase Locking (Strict 2PL)** e zgjidh këtë problem duke kërkuar që **të gjitha bllokimet e shkrimit (write locks/exclusive locks) të mbahen deri në momentin e commit-it ose abort-it** të transaksionit (jo vetëm deri sa transaksioni të fillojë "fazën zvogëluese"). Kështu, faza e vetme e "lëshimit" ndodh **të gjitha njëherësh, në fund**, në momentin e commit/abort:

| Kriter | 2PL (i thjeshtë) | Strict 2PL |
|---|---|---|
| Kur mund të lëshohen bllokimet | Sapo të fillojë faza zvogëluese (mund të jetë para commit-it) | Vetëm në momentin e commit/abort |
| Rreziku i "dirty read" | Ekziston | Eliminohet |
| Mundësia për rollback në kaskadë (cascading aborts) | Ekziston | Eliminohet (askush s'ka lexuar të dhëna të pakonfirmuara) |
| Konkurrenca (paralelizmi) | Pak më e lartë | Pak më e ulët (bllokimet mbahen më gjatë) |
| Përdorimi praktik | Rrallë vetëm | Standard në shumicën e bazave të të dhënave dhe transaksioneve të shpërndara |

Në praktikë, pothuajse të gjithë sistemet reale të bazave të të dhënave përdorin **Strict 2PL** (ose variante edhe më të forta si Strict 2PL me "rigorous" locking), sepse eliminon leximet e pista dhe thjeshton rikuperimin (recovery) pas dështimeve.

---

### Pyetja 5 [8 pikë]
**Përshkruani mënyrën e funksionimit të arkitekturës REST për ueb shërbime.**

**Përgjigje:**

**REST (Representational State Transfer)** është një stil arkitekturor (jo protokoll) për ndërtimin e shërbimeve web mbi HTTP, i bazuar në disa parime themelore:

- **Resurset (resources)** janë entitetet kryesore të sistemit (p.sh. një përdorues, një porosi), secili i identifikuar në mënyrë unike nga një **URI** (p.sh. `/users/42`).
- **Ndërfaqe uniforme:** ndërveprimi me resurset bëhet përmes një grupi fiks foljesh/metodash standarde HTTP: **GET** (lexo/merr), **POST** (krijo), **PUT** (përditëso/zëvendëso plotësisht), **PATCH** (përditëso pjesërisht), **DELETE** (fshi).
- **Pa gjendje (stateless):** çdo kërkesë nga klienti drejt serverit duhet të përmbajë të gjithë informacionin e nevojshëm për ta kuptuar dhe përpunuar; serveri nuk mban "sesion" apo gjendje të klientit mes kërkesave — kjo e bën sistemin më të lehtë për shkallëzim (scalability).
- **Reprezentimi i resurseve:** të dhënat shkëmbehen zakonisht në format **JSON** (ose XML), të quajtura "reprezentime" (representations) të gjendjes së resursit — prej nga edhe emri "Representational State Transfer".
- **Cache-ueshmëria:** përgjigjet mund të shënohen si të "cache-ueshme" (cacheable) nga klienti, për të reduktuar ngarkesën në server dhe për të përmirësuar performancën.
- **Arkitekturë e shtresuar (layered system):** klienti nuk ka nevojë ta dijë nëse po komunikon direkt me serverin final apo përmes ndërmjetësve (proxy, load balancer, gateway).

**Procesi tipik i funksionimit:** klienti dërgon një kërkesë HTTP (p.sh. `GET /users/42`) → serveri identifikon resursin nga URI-ja, kryen operacionin e kërkuar, dhe kthen një përgjigje HTTP me kod statusi (p.sh. `200 OK`, `404 Not Found`) dhe trupin e përgjigjes (zakonisht JSON) që përfaqëson gjendjen e resursit.

Krahasuar me **SOAP**, REST është më i lehtë, më i shpejtë për t'u zbatuar, përdor drejtpërdrejt semantikën e HTTP-së, dhe është standardi mbizotërues për API-të moderne web dhe mikroshërbimet.

---

### Pyetja 6 [8 pikë]
**Cilat janë pesë teknikat e komunikimit indirekt në sistemet e shpërndara?**

**Përgjigje:**

Komunikimi indirekt i shkëput proceset komunikuese **në kohë** (nuk duhet të jenë të dyja aktive njëkohësisht) dhe/ose **në hapësirë** (dërguesi nuk duhet ta njohë identitetin e marrësit). Pesë teknikat kryesore janë:

1. **Komunikimi në grup (Group communication):** një mesazh dërgohet nga një dërgues te një **grup** procesesh njëherësh (multicast te anëtarët e grupit), në vend të një marrësi të vetëm.
2. **Publikimi-Pajtimi (Publish-Subscribe):** prodhuesit "publikojnë" ngjarje/mesazhe sipas një teme ose përmbajtjeje, ndërsa konsumatorët "pajtohen" për temat që i interesojnë; një **ndërmjetës (broker)** i rrugëton mesazhet nga publikuesit te pajtuesit e duhur, pa i njohur ata drejtpërdrejt njëri-tjetrin.
3. **Radhët e mesazheve (Message queues):** prodhuesit vendosin mesazhe në një radhë të menaxhuar nga një server i ndërmjetëm; konsumatorët i marrin mesazhet nga radha (shpesh në rend FIFO), të shkëputur në kohë nga prodhuesit.
4. **Hapësira e tuplave/objekteve të ndara (Tuple space / Shared/Distributed Objects space, p.sh. JavaSpaces, Linda):** proceset shkruajnë dhe lexojnë "tupla" (të dhëna të strukturuara) në një hapësirë të përbashkët, e aksesueshme nga të gjithë pjesëmarrësit, pa nevojën për adresim direkt.
5. **Sistemet e bazuara në ngjarje (Event-based/notification systems):** komponentët "shpallin" (raise) ngjarje kur ndodh diçka me interes, dhe komponentë të tjerë të regjistruar si "dëgjues" (listeners) njoftohen automatikisht — një formë e përgjithësuar e publish-subscribe, e përdorur shpesh brenda arkitekturave event-driven.

Të pesta këto teknika kanë të përbashkët faktin që **eliminojnë varësinë direkte (referencën eksplicite)** mes dërguesit dhe marrësit, duke rritur shkallëzueshmërinë dhe fleksibilitetin e sistemit.

---

### Pyetja 7 [3 pikë]
**Në një sistem të shpërndarë, ______ përdoret për të siguruar që të gjitha kopjet e një të dhëne të qëndrojnë të njëjta në të gjitha nyjet.**

a) Konsistenca  b) Replikimi  c) Particionimi  d) Sharding

**Përgjigje: a) Konsistenca.** *(Replikimi është akti i krijimit të kopjeve; konsistenca është vetia/mekanizmi që garanton se ato kopje mbeten identike/të përditësuara njëlloj në të gjitha nyjet.)*

---

### Pyetja 8 [3 pikë]
**Një deadlock ndodh kur:**

a. Një proces pret pafundësisht një burim që mbahet nga një proces tjetër
b. Të gjitha nyjet punojnë pa ndërprerje
c. Serveri dështon dhe klientët vazhdojnë punën
d. Të dhënat replikohen në mënyrë të saktë në të gjitha nyjet

**Përgjigje: a).** Më saktë, deadlock-u ndodh kur ekziston një **cikël pritjeje** mes dy ose më shumë proceseve/transaksioneve — secili pret (pafundësisht) një burim të mbajtur nga një proces tjetër brenda po atij cikli, kështu që asnjëri nuk mund të vazhdojë (shih shembujt e detajuar në zgjidhjen e Pyetjes 15 më poshtë).

---

### Pyetja 9 [3 pikë]
**Cili është përfitimi kryesor i përdorimit të mikroshërbimeve?**

a. Të gjitha shërbimet vendosen së bashku në një monolit
b. Shërbimet mund të zhvillohen, testohen dhe vendosen në mënyrë të pavarur
c. Menaxhimi bëhet i centralizuar dhe më i thjeshtë
d. Të gjitha burimet ruhen në një nyje të vetme

**Përgjigje: b).** Mikroshërbimet e ndajnë aplikacionin në shërbime të vogla, të pavarura, secili me përgjegjësinë e vet, çka lejon zhvillim, testim, vendosje (deployment) dhe shkallëzim **të pavarur** të secilit shërbim — në kontrast me arkitekturën monolitike, ku e gjithë aplikacioni vendoset si një njësi e vetme.

---

### Pyetja 10 [3 pikë]
**Cila veti është karakteristikë e një rrjeti *peer-to-peer*?**

a. Kontroll i centralizuar  b. Topologjia fikse e rrjetit  c. Role dhe përgjegjësi të barabarta midis nyjeve  d. Serverë të dedikuar për menaxhimin e burimeve

**Përgjigje: c).** Në një arkitekturë peer-to-peer (P2P), të gjitha nyjet (peers) kanë role **të barabarta** — secila mund të funksionojë njëkohësisht si klient dhe si server, pa pasur nevojë për një autoritet apo server qendror; kjo e dallon nga arkitektura klient-server, ku rolet janë të ndara qartë dhe kontrolli tenton të jetë i centralizuar.

---

### Pyetja 11 [3 pikë]
**Në kontekstin e sistemeve të shpërndara, çfarë është një "stub"?**

a. Një përfaqësues nga ana e shërbimeve për një objekt të largët
b. Një përfaqësues nga ana e klientit për një objekt të largët
c. Një mekanizëm për menaxhimin e transaksioneve të shpërndara
d. Një protokoll për tolerancën e gabimeve

**Përgjigje: b).** "Stub" (ose *proxy* nga ana e klientit) është një objekt lokal që "imiton" ndërfaqen e objektit të largët; klienti thërret metoda mbi stub-in sikur të ishte objekti real, ndërsa stub-i në të vërtetë **paketon (marshal)** thirrjen dhe argumentet dhe ia dërgon nëpër rrjet objektit të largët (nga ana tjetër, në server, ky rol simetrik luhet nga **skeleton/dispatcher**).

---

### Pyetja 12 [3 pikë]
**Cili mekanizëm përdoret për të shmangur konfliktet gjatë aksesit të njëkohshëm në të dhëna të shpërndara?**

a. Locking  b. Sharding  c. Indeksimi grupor  d. Indeksimi jo grupor

**Përgjigje: a) Locking.** Bllokimi (locking, p.sh. 2PL/Strict 2PL) është mekanizmi standard i kontrollit të konkurrencës që parandalon që dy ose më shumë transaksione të aksesojnë e modifikojnë të njëjtat të dhëna njëkohësisht në mënyrë konfliktuale.

---

### Pyetja 13 [3 pikë]
**Cila nga këto është një avantazh i replikimit të të dhënave?**

a. Rrit konsistencën dhe disponueshmërinë  b. Ul kohën e aksesit për të gjitha nyjet  c. Redukton ngarkesën në rrjet  d. Eliminon nevojën për monitorim

**Përgjigje: a).** *(Më saktë, replikimi rrit **disponueshmërinë** — nëse një kopje/server dështon, të dhënat mbeten të arritshme nga një kopje tjetër — dhe, nëse menaxhohet mirë, mund të mbështesë edhe konsistencën; opsionet b, c, d nuk janë avantazhe të drejtpërdrejta dhe universale të replikimit — replikimi në fakt **rrit** trafikun e rrjetit për sinkronizim dhe **kërkon** monitorim shtesë.)*

---

### Pyetja 14 [15 pikë]
**Konsideroni një sistem bankar të përbërë nga 'llogari', 'transaksione' dhe 'balancë'. Tregoni se cilat veprime (bllokimi, arritja e bllokimit, lëshimi i bllokimit) ndërmerren dhe çfarë lloji të bllokimeve janë vendosur për secilin nga veprimet e mëposhtme:**

1. Procesi A dëshiron të transferojë 2000 EUR nga llogaria X në llogarinë Y.
2. Ndërsa procesi A është duke kryer transferimin, procesi B kërkon të lexojë balancën e llogarisë Y.
3. Procesi C dëshiron të transferojë 500 EUR nga llogaria Y në llogarinë Z.
4. Procesi A përfundon punën e tij.
5. Procesi D dëshiron të bllokojë llogarinë Z për auditim.
6. Procesi B përfundon punën e tij.
7. Procesi B kërkon të lexojë balancën e llogarisë X.
8. Procesi C përfundon transferimin e tij.

**Përgjigje:**

Një transfer bankar duhet ta **debitojë** njërën llogari dhe ta **kreditojë** tjetrën si një njësi atomike, prandaj kërkon **bllokim shkrimi (write-lock, ekskluziv)** mbi të dyja llogaritë e përfshira; një lexim i thjeshtë i balancës kërkon vetëm **bllokim leximi (read-lock, i përbashkët)**.

| # | Veprimi | Bllokimi i kërkuar | A bllokohet (pret)? | Kur zgjidhet/vazhdon |
|---|---|---|---|---|
| 1 | A transferon nga X→Y | Shkrim (ekskluziv) mbi **X** dhe mbi **Y** | Jo — askush nuk i mban ende | A i merr të dyja bllokimet menjëherë |
| 2 | B lexon balancën e Y | Lexim mbi **Y** | **Po** — Y mbahet me shkrim nga A | Zgjidhet kur A të përfundojë (hapi 4) |
| 3 | C transferon nga Y→Z | Shkrim mbi **Y** (e më vonë mbi Z) | **Po** — Y ende mbahet me shkrim nga A | Mbetet në pritje deri sa Y të lirohet plotësisht (pas B, hapi 6) |
| 4 | A përfundon (commit) | **Liron** bllokimet mbi X dhe Y | — | X dhe Y bëhen të lira |
| 5 | D bllokon Z për auditim | Bllokim ekskluziv (auditimi) mbi **Z** | Jo — Z është ende e lirë (C ende nuk ka arritur ta kërkojë) | D e merr menjëherë |
| 6 | B përfundon (liron lock-un e leximit) | **Liron** lock-un mbi Y | — | Y bëhet i lirë; C (që priste që nga hapi 3) e merr tani bllokimin e shkrimit mbi Y |
| 7 | B lexon balancën e X | Lexim mbi **X** | Jo — X është e lirë që nga hapi 4 | B e merr menjëherë |
| 8 | C përfundon transferimin | C ka nevojë edhe për shkrim mbi **Z**, e cila mbahet nga D që nga hapi 5 | **Po** — C pret derisa D ta lirojë Z (të përfundojë auditimin) | Sapo D liron Z, C shkruan dhe bën commit |

**Përfundim:** veprimet që **shkaktojnë pritje (bllokohen)** janë hapi 2 (B pret A), hapi 3 (C pret A, pastaj B) dhe hapi 8 (C pret D); veprimet që **marrin bllokim menjëherë, pa pritur**, janë hapi 1, hapi 5 dhe hapi 7. Të gjitha operacionet e **transferit** (debit+kredit) përdorin **bllokime ekskluzive (shkrimi)**, ndërsa **leximet e balancës** përdorin vetëm **bllokime të përbashkëta (leximi)**. Nuk ka deadlock në këtë skenar (nuk formohet asnjë cikël pritjeje — çdo pritje zgjidhet gradualisht, njëra pas tjetrës).

---

### Pyetja 15 [16 pikë]
**Një server manaxhon objektet a1, a2, …, an dhe ofron dy operacione për klientët e tij: (1) lexo(i) dhe (2) shkruaj(i, vlera). Duke konsideruar transaksionet A, B, C:**

| Koha | A | B | C |
|---|---|---|---|
| 1 | filloTransaksioni | filloTransaksioni | filloTransaksioni |
| 2 | y=Lexo(j) | | |
| 3 | | x=Lexo(k) | |
| 4 | | Shkruaj(i, 55) | |
| 5 | | Shkruaj(j, 54) | Shkruaj(i, 98) |
| 6 | | Commit | |
| 8 | x=Lexo(i) | | |
| 9 | Shkruaj(k, 23) | | |
| 10 | Commit | | Shkruaj(k, 52) |
| 11 | | | Commit |

*(Kjo është saktësisht "Ushtrimi 1" i zgjidhur më parë në skedarin `kapitulli-11-zgjidhjet-e-ushtrimeve.md` — përgjigja e plotë riprodhohet këtu për ta pasur gjithçka në një dokument.)*

**1) A duhet B të presë për bllokimin, për `x=Lexo(k)`?** Jo — `k` është e lirë në kohën 3, B e merr menjëherë.

**2) A duhet C të presë për bllokimin, për `Shkruaj(i, 98)`?** Po — `i` mbahet me shkrim nga B që nga koha 4 (i pakryer), kështu C pret B.

**3) A duhet A të presë për bllokimin, për `x=Lexo(i)`?** Po — `i` ende mbahet nga B (i pazgjidhur, sepse edhe B vetë është duke pritur `j`).

**4) A bëhet commit A dhe C? Pse?** Këtu formohet **deadlock mes A dhe B**: B pret bllokimin e `j` (mbajtur nga A), ndërsa A pret bllokimin e `i` (mbajtur nga B) — cikël A→B→A. Sistemi duhet ta zbulojë këtë dhe të bëjë **abort B**. Pas abort-it të B-së: C e merr `i`-në, shkruan, shkruan edhe `k` (pa konflikt) dhe **bën commit me sukses**; më pas A e merr `i`-në (e liruar nga C), lexon, shkruan `k` dhe **bën commit me sukses** — pra **po, A dhe C përfundojnë me commit**, por vetëm pasi B të anulohet (rollback) për ta thyer deadlock-un.

**5) Zgjidhje për të dalë nga deadlock-u:** zbulimi i ciklit në grafin e pritjes (waits-for graph) dhe **abort/rollback** i njërit transaksion "viktimë" (këtu B), duke liruar bllokimet e tij dhe duke lejuar transaksionet e tjera të vazhdojnë; në mënyrë alternative mund të përdoret parandalimi paraprak me algoritme si **wait-die**/**wound-wait** bazuar në vulat kohore të transaksioneve.

---

## Provimi 2 — dt. 28.03.2025

*(Katër nga këto dhjetë pyetje janë identike me Provimin 1 — te ato raste jepet përgjigje e shkurtër me referencë.)*

### Pyetja 1 [8 pikë]
**Çka janë sistemet e shpërndara, përshkruaj së paku tri karakteristika që i veçojnë ato?**

**Përgjigje:**

Një **sistem i shpërndarë** është një koleksion kompjuterësh të pavarur (nyje) që i shfaqet përdoruesit si **një sistem i vetëm, koherent**, edhe pse përbëhet nga shumë komponentë fizikisht të ndarë që komunikojnë vetëm përmes shkëmbimit të mesazheve në rrjet.

Tri (e më shumë) karakteristika që e veçojnë:

1. **Konkurrenca (concurrency):** shumë procese në nyje të ndryshme ekzekutohen njëkohësisht, duke ndarë burime dhe duke koordinuar veprimet e tyre.
2. **Mungesa e një ore globale (no global clock):** proceset koordinohen vetëm përmes shkëmbimit të mesazheve, dhe nuk ekziston një referencë e vetme, e përbashkët e kohës — çdo nyje ka orën e vet, me devijime.
3. **Dështimet e pavarura (independent failures of components):** çdo komponent (nyje, lidhje rrjeti) mund të dështojë në mënyrë të pavarur nga të tjerët, dhe pjesa tjetër e sistemit duhet të vazhdojë të funksionojë (ose të degradojë me hijeshi).
4. *(shtesë)* **Transparenca** — sistemi përpiqet ta fshehë nga përdoruesi faktin që është i shpërndarë (transparencë vendndodhjeje, aksesi, dështimi, etj.).

---

### Pyetja 2 [8 pikë]
**Web Shërbimet përdorin një protokoll të bazuar në XML që quhet SOAP. Cilat janë avantazhet dhe disavantazhet e përdorimit të XML për thirrje në distancë?**

**Përgjigje:**

**Avantazhet e XML-së:**
- **Lexueshmëri njerëzore dhe vetë-përshkrues (self-describing):** të dhënat vijnë të shoqëruara nga etiketa (tags) kuptimplota, duke e bërë formatin të lexueshëm dhe të kuptueshëm pa dokumentacion shtesë.
- **Pavarësi nga platforma dhe gjuha programuese:** çdo gjuhë/sistem mund ta parsojë XML-në, duke e bërë ideale për ndërveprim (interoperability) mes sistemeve heterogjene.
- **Strukturë hierarkike fleksibël:** mund të përfaqësojë struktura komplekse, të ndërthurura (nested) të dhënash.
- **Validueshmëri:** mund të validohet kundrejt një skeme (DTD/XSD), duke garantuar korrektësinë strukturore të mesazheve.
- **Extensibilitet:** lehtë shtohen fusha të reja pa e prishur pajtueshmërinë mbrapa (backward compatibility).

**Disavantazhet e XML-së:**
- **Verbozitet i lartë:** etiketat e përsëritura e rrisin ndjeshëm madhësinë e mesazhit krahasuar me formate binare ose me JSON, duke rritur konsumin e bandwidth-it dhe kohën e transmetimit.
- **Kosto e lartë procesuese:** parsimi (analiza) i XML-së kërkon më shumë kohë CPU krahasuar me formatet binare të kompaktuara (p.sh. Protocol Buffers).
- **Kompleksitet shtesë:** ekosistemi SOAP/XML (WSDL, XSD, namespaces) shton kompleksitet zhvillimi krahasuar me alternativat më të lehta si REST+JSON.
- **Performancë më e ulët** në skenarë me shumë kërkesa/thirrje të shpeshta në distancë (high-throughput RPC), ku formatet binare (p.sh. Protocol Buffers/gRPC) janë dukshëm më efikase.

---

### Pyetja 3 [8 pikë]
**Çka janë Socket-et, përshkruaj disa veçori të tyre?**

**Përgjigje:**

Një **socket** është një **pikë fundore (endpoint)** e komunikimit dypalësh mes dy programeve që funksionojnë në rrjet (në të njëjtën makinë ose në makina të ndryshme). Socket-i lidh një proces me shtresën e transportit (TCP/UDP) të stack-ut të rrjetit, dhe identifikohet nga kombinimi **adresë IP + numër porte**.

Veçoritë kryesore:
- Çdo proces mund të krijojë **shumë socket-e**, secili i identifikuar nga porti i vet; komunikimi ndërmjet dy proceseve kërkon një socket në secilën anë.
- Mesazhet e dërguara përmes një socket-i **datagram (UDP)** transmetohen si njësi të pavarura, pa garanci dorëzimi apo rendi, por me vonesë (latencë) minimale.
- Mesazhet e dërguara përmes një socket-i **stream (TCP)** transmetohen si një **rrjedhë** e vazhdueshme bajtësh, me garanci dorëzimi, rendi të saktë dhe kontroll fluksi/gabimesh, por me pak më shumë "overhead".
- Portet **e njohura (well-known ports, 0–1023)** janë të rezervuara për shërbime standarde (p.sh. HTTP=80, HTTPS=443); portet e tjera mund të përdoren lirshëm nga aplikacionet.
- Një socket në anën e serverit zakonisht "dëgjon" (listen) në një port fiks për lidhje hyrëse, ndërsa klienti krijon socket-in e vet me port të caktuar dinamikisht nga sistemi operativ.

---

### Pyetja 4 [8 pikë]
**Përshkruaj dhe krahaso Fazën e mbylljes (2PL) dhe Fazën e mbylljes strikte (Strict 2PL)?**

**Përgjigje:** Identike me **Pyetjen 4 të Provimit 1** — shih përgjigjen e plotë atje (faza rritëse/zvogëluese e 2PL, problemi i "dirty read", dhe si e zgjidh atë Strict 2PL duke i mbajtur bllokimet e shkrimit deri në commit/abort).

---

### Pyetja 5 [8 pikë]
**Përshkruani konceptin e transparencës në një sistem të shpërndarë. Jepni së paku katër shembuj të llojeve të ndryshme të transparencës.**

**Përgjigje:**

**Transparenca** në një sistem të shpërndarë nënkupton **fshehjen** nga përdoruesi/aplikacioni të fakteve që lidhen me natyrën e shpërndarë të sistemit — komponentët duhet t'i shfaqen përdoruesit si një **sistem i vetëm i integruar**, pavarësisht ndarjes fizike, shumëfishimit apo dështimeve të mundshme të pjesëve përbërëse.

Llojet kryesore të transparencës (të paktën katër):

1. **Transparenca e aksesit (Access transparency):** të dhënat/resurset lokale dhe të largëta aksesohen me të njëjtat operacione, pavarësisht se ku ndodhen.
2. **Transparenca e vendndodhjes (Location transparency):** përdoruesit nuk e dinë (dhe nuk kanë nevojë ta dinë) vendndodhjen fizike të një resursi për ta aksesuar atë.
3. **Transparenca e konkurrencës (Concurrency transparency):** disa procese mund të operojnë njëkohësisht mbi resurse të përbashkëta pa ndërhyrje apo interferencë të dukshme mes tyre.
4. **Transparenca e riprodhimit/replikimit (Replication transparency):** ekzistenca e shumë kopjeve të një resursi është e fshehur — përdoruesi sheh vetëm një version "logjik" të të dhënës.
5. **Transparenca e dështimit (Failure transparency):** dështimet dhe rikuperimet e komponentëve fshihen nga përdoruesi, i cili vazhdon të përdorë sistemin pa u ndikuar dukshëm.
6. **Transparenca e lëvizjes/migrimit (Mobility transparency):** lëvizja e resurseve dhe objekteve brenda sistemit nuk ndikon në mënyrën si ndërveprohet me to.
7. **Transparenca e performancës (Performance transparency):** sistemi mund të ri-konfigurohet automatikisht për të përmirësuar performancën, pa ndikuar në ndërfaqen e përdoruesit.
8. **Transparenca e shkallëzimit (Scaling transparency):** sistemi mund të rritet në shkallë (më shumë përdorues/nyje) pa ndryshuar strukturën e tij logjike apo algoritmet e aplikacioneve.

---

### Pyetja 6 [8 pikë]
**Cilat janë pesë teknikat e komunikimit indirekt?** — Identike me **Pyetjen 6 të Provimit 1** (shih atje: komunikimi në grup, publish-subscribe, radhët e mesazheve, hapësira e tuplave, sistemet e bazuara në ngjarje).

---

### Pyetja 7 [3 pikë]
**______ është procesi i rrafshimit apo shkatërrimit të një strukture të të dhënave, dhe konvertimin e të dhënave për një përfaqësim të jashtëm.**

**Përgjigje: Marshalling (Serializimi/Marshalling i të dhënave).** Ky proces merr strukturat e të dhënave dhe objektet e memories lokale (me tregues/pointerë specifikë për proces) dhe i konverton në një **format linear, të pavarur nga platforma** (p.sh. bajte të serializuara, XML, JSON) të përshtatshëm për transmetim në rrjet; procesi i kundërt (rindërtimi i strukturës nga formati linear) quhet **unmarshalling/deserializim**.

---

### Pyetja 8 [3 pikë]
**Cila veti është karakteristikë e një rrjeti peer-to-peer?** — Identike me **Pyetjen 10 të Provimit 1** → **Përgjigje: c) Role dhe përgjegjësi të barabarta midis nyjeve.**

---

### Pyetja 9 [3 pikë]
**Cila nga vetitë është përfitim i përdorimit të mikroshërbimeve në një sistem të shpërndarë?**

a. Përmirësimi i arkitekturës monolitike  b. Korrigjimi dhe testimi më i lehtë  c. Menaxhimi i centralizuar i burimeve  d. Shërbime të shkëputura që mund të vendosen në mënyrë të pavarur

**Përgjigje: d)** *(edhe b) është pjesërisht e vërtetë si pasojë e d), por përfitimi themelor/strukturor i mikroshërbimeve është pikërisht se shërbimet janë të shkëputura dhe vendosen/zhvillohen në mënyrë të pavarur — nga kjo rrjedh edhe korrigjimi/testimi më i lehtë).*

---

### Pyetja 10 [3 pikë]
**Në kontekstin e sistemeve të shpërndara, çfarë është një "stub"?** — Identike me **Pyetjen 11 të Provimit 1** → **Përgjigje: b) Një përfaqësues nga ana e klientit për një objekt të largët.**

---

## Provimi 3 — Provim-IKS, dt. 08.07.2025 (75 minuta)

### Pyetja 1 [8 pikë]
**Cilat janë tri shërbimet kryesore që duhet t'i ofrojë middleware, komento secilën prej tyre?**

**Përgjigje:**

Middleware-i është shtresa softuerike që qëndron mes sistemit operativ/rrjetit dhe aplikacioneve të shpërndara, dhe ofron (të paktën) tri shërbime themelore:

1. **Fshehja e heterogjenitetit (interoperabiliteti):** middleware-i fsheh dallimet mes platformave harduerike, sistemeve operative, gjuhëve programuese dhe protokolleve të rrjetit të nyjeve pjesëmarrëse, duke lejuar që komponentë të ndryshëm të komunikojnë pa u shqetësuar për detajet e implementimit të njëri-tjetrit.
2. **Komunikimi/thirrja në largësi:** ofron mekanizma të nivelit të lartë për komunikim mes proceseve (p.sh. RPC, RMI, message-oriented middleware), duke i kursyer zhvilluesit nga puna direkte me socket-e dhe protokolle të nivelit të ulët.
3. **Shërbimet e përbashkëta (common services):** ofron shërbime standarde që përdoren shpesh nga aplikacionet e shpërndara — p.sh. **emërtimi (naming)**, **siguria (security)**, **transaksionet**, **kohëzgjatja (persistence)**, **koordinimi/sinkronizimi** — në mënyrë që çdo aplikacion të mos duhet t'i riimplementojë vetë.

*(Shtesë: middleware-i ofron gjithashtu transparencë — vendndodhjeje, aksesi, konkurrence, dështimi etj. — mbi sistemin e shpërndarë të nënshtruar.)*

---

### Pyetja 2 [8 pikë]
**Cilat janë tre llojet e dështimeve që mund të ndodhin te sistemet e shpërndara, komento secilën prej tyre?**

**Përgjigje:**

1. **Dështimet e rrëzimit (Crash failures):** një proces/server ndalon plotësisht së funksionuari (ndalet befasisht) dhe nuk kryen më asnjë veprim — supozohet se pasi ndalet, nuk rikthehet vetë (ose rikthimi trajtohet si një proces i ri).
2. **Dështimet e lëshimit (Omission failures):** një proces/kanal **dështon të kryejë** një veprim që duhej ta kryente — p.sh. *send-omission* (dërguesi nuk e dërgon mesazhin që duhej), *receive-omission* (marrësi nuk e merr mesazhin që i ishte dërguar), ose humbje e mesazhit brenda kanalit të komunikimit.
3. **Dështimet e kohës (Timing failures):** përgjigja/mesazhi mbërrin, por **jashtë kufirit kohor** të pritur (shumë vonë, ose ndonjëherë edhe shumë shpejt), duke shkelur garancitë kohore të një sistemi (kryesisht) sinkron.
4. *(shtesë, nëse kërkohet i katërti):* **Dështimet arbitrare/Bizantine (Byzantine/arbitrary failures):** komponenti sillet në mënyrë krejt të paparashikueshme — dërgon mesazhe të gabuara, kontradiktore apo keqdashëse — lloji më i vështirë për t'u trajtuar.

---

### Pyetja 3 [8 pikë]
**Si funksionon algoritmi i Berkeley-t për sinkronizimin e orëve dhe në çfarë raste është më i përshtatshëm se NTP?**

**Përgjigje:**

**Algoritmi i Berkeley-t** është një metodë e **sinkronizimit të brendshëm** të orëve (internal synchronization), ku **nuk supozohet** ekzistenca e një burimi të jashtëm të saktë të kohës (si p.sh. një orë atomike ose sinjal GPS/UTC), ndryshe nga NTP.

Hapat kryesorë:
1. Një makinë caktohet si **koordinatore/master** (mund të zgjidhet dinamikisht, p.sh. me një algoritëm zgjedhjeje/election, dhe të zëvendësohet nëse dështon).
2. Koordinatorja **pyet periodikisht** (poll) të gjitha makinat "skllave" (slaves) për orën e tyre lokale.
3. Makinat përgjigjen me kohën e tyre; koordinatorja e vlerëson **kohën e vajtje-ardhjes (round-trip time)** të secilës përgjigje për të vlerësuar/kompensuar vonesën e rrjetit.
4. Koordinatorja llogarit një **mesatare të rregulluar (fault-tolerant average)** të të gjitha orëve (duke përjashtuar vlerat "outlier" të orëve dukshëm të gabuara/të dëmtuara).
5. Në vend që t'u dërgojë makinave kohën absolute, koordinatorja i dërgon secilës makinë **rregullimin (offset-in)** që duhet t'i shtojë/zbresë orës së saj lokale, në mënyrë që të gjitha të konvergojnë drejt mesatares së përbashkët.

**Kur është më i përshtatshëm se NTP:** Algoritmi i Berkeley-t është më i përshtatshëm në **rrjete lokale (LAN) të izoluara**, ku **nuk ka qasje** në një burim të jashtëm të saktë e të besueshëm të kohës UTC (p.sh. server NTP publik, orë radio/GPS) — pra kur qëllimi është vetëm që makinat brenda grupit të kenë **kohë të njëjtë mes tyre** (konsistencë e brendshme), pa qenë e domosdoshme që ajo kohë të jetë e saktë kundrejt kohës reale absolute. NTP, në krahasim, kërkon lidhje me një hierarki serverësh (strata) që përfundimisht lidhen me një burim UTC të jashtëm, dhe është zgjedhja e preferuar kur nevojitet saktësi absolute kundrejt kohës botërore reale.

---

### Pyetja 4 [8 pikë]
**Përshkruani shkurtimisht tre mënyrat/modelet kryesore për përshkrimin e dizajnit të një sistemi të shpërndarë: modeli fizik, modeli arkitekturor dhe modeli fundamental.**

**Përgjigje:**

1. **Modeli Fizik (Physical Model):** përshkruan infrastrukturën konkrete, harduerike të sistemit — llojet, numrin dhe vendndodhjen e kompjuterëve/serverëve, lidhjet fizike të rrjetit mes tyre — pa u marrë ende me detajet e softuerit apo algoritmeve.
2. **Modeli Arkitekturor (Architectural Model):** përshkruan **organizimin logjik** të sistemit në terma të komponentëve softuerikë dhe marrëdhënieve mes tyre — p.sh. arkitektura klient-server, peer-to-peer, shtresore (layered), me shumë nivele (multi-tier), ose e bazuar në mikroshërbime — si dhe si komunikojnë këta komponentë (paradigmat komunikuese: RPC, RMI, message-passing etj.).
3. **Modeli Fundamental (Fundamental Model):** një abstragim më i thellë, i pavarur nga arkitektura specifike, që analizon **vetitë themelore** të çdo sistemi të shpërndarë përmes tri nën-modeleve: **Modeli i Ndërveprimit** (interaction model — kohëzgjatja e operacioneve, vonesat, sinkron vs. asinkron), **Modeli i Dështimit** (failure model — llojet e dështimeve që mund të ndodhin, si crash/omission/timing/arbitrare) dhe **Modeli i Sigurisë** (security model — kërcënimet ndaj konfidencialitetit, integritetit dhe disponueshmërisë). Ky model përdoret për të arsyetuar formalisht rreth korrektësisë së algoritmeve të shpërndara pavarësisht nga zbatimi konkret.

---

### Pyetja 5 [8 pikë]
**Përmend dhe shpjego të paktën katër lloje të transparencës në sistemet e shpërndara?** — Identike në thelb me **Pyetjen 5 të Provimit 2** (shih atje listën e plotë: aksesi, vendndodhja, konkurrenca, replikimi, dështimi, mobiliteti, performanca, shkallëzimi).

---

### Pyetja 6 [8 pikë]
**Cilat janë tre protokollet shkëmbyese (exchange) të RPC — Remote Procedure Call?**

**Përgjigje:**

1. **Protokolli R (Request):** klienti dërgon vetëm kërkesën (request) te serveri, pa pritur asnjë përgjigje apo konfirmim — i përshtatshëm kur nuk ka nevojë për vlerë kthimi (p.sh. thirrje "fire-and-forget").
2. **Protokolli RR (Request-Reply):** klienti dërgon kërkesën, serveri e përpunon dhe dërgon një **përgjigje (reply)**; vetë përgjigja shërben edhe si konfirmim (acknowledgement) implicit i marrjes së kërkesës — protokolli më i përdorur.
3. **Protokolli RRA (Request-Reply-Acknowledge reply):** si RR, por klienti dërgon shtesë një **konfirmim eksplicit (ACK)** për përgjigjen e marrë nga serveri — kjo i lejon serverit të lirojë me siguri burimet e rezervuara për atë kërkesë (p.sh. informacionin e ruajtur për ritransmetim), duke qenë i sigurt që klienti e ka marrë përgjigjen.

---

### Pyetja 7 [8 pikë]
**Cilat janë dy teknikat e thirrjes në distancë për komunikim në sistemet e shpërndara?**

**Përgjigje:**

1. **RPC (Remote Procedure Call — Thirrja e Procedurës në Largësi):** lejon që një program të thërrasë një **procedurë/funksion** që ekzekutohet në një proces tjetër (zakonisht në një makinë tjetër), duke e trajtuar thirrjen në largësi njësoj si një thirrje lokale funksioni — modeli **procedural**.
2. **RMI (Remote Method Invocation — Thirrja e Metodës në Largësi):** version i orientuar nga objektet i të njëjtit koncept — lejon që një klient të thërrasë **metoda** mbi një **objekt të largët**, duke ruajtur semantikën e orientimit në objekte (referenca për objekte të largëta, trashëgimia e ndërfaqeve, etj.) — përdoret p.sh. në Java RMI.

Në të dyja rastet, thirrja lokale "imitohet" nga një **proxy/stub** në anën e klientit dhe një **skeleton/dispatcher** në anën e serverit, që kujdesen për marshalling/unmarshalling dhe transportin real të mesazheve.

---

### Pyetja 8 [3 pikë]
**WSDL përdoret për të:**

a) Krijuar strukturë të dhënash në një bazë të dhënash  b) Përshkruar ndërfaqen dhe operacionet e një Web Service  c) Enkriptuar komunikimin ndërmjet serverëve  d) Validuar të dhënat hyrëse në një API

**Përgjigje: b).** WSDL (Web Services Description Language) është një dokument XML që përshkruan **çfarë operacionesh** ofron një shërbim web, **si thirren** ato (parametrat hyrës/dalës), dhe **ku/si** mund të arrihet shërbimi (endpoint, binding, protokolli i transportit).

---

### Pyetja 9 [3 pikë]
**Cila nga përgjigjet është e vërtetë për SOAP (Simple Object Access Protocol)?**

A) Zakonisht përdoret për mesazhe të thjeshta te transferit  B) Është i kufizuar në HTTP si protokolli i tij i komunikimit  C) Nuk është i përshtatshëm për ERP  D) Përdor XML për formatin e mesazhit dhe mund të funksionojë mbi çdo protokoll si HTTP, SMTP, etj.

**Përgjigje: D).** SOAP është i pavarur nga protokolli i transportit — mesazhet e tij (të formatuara në XML, me *envelope*, *header* dhe *body*) mund të dërgohen mbi HTTP, SMTP, TCP e protokolle të tjera; kjo fleksibilitet e bën të përshtatshëm edhe për sisteme ndërmarrjesh komplekse si ERP (në kontrast me pretendimin te C).

---

### Pyetja 10 [3 pikë]
**Cili është dallimi kryesor mes transaksioneve "flat" dhe "nested"?**

A) Flat mund të dështojnë, nested jo  B) Flat nuk mbështesin rollback, nested po  C) Nested përmbajnë transaksione të tjera brenda vetes  D) Flat kanë qëndrueshmëri më të lartë

**Përgjigje: C).** Siç shpjegohet te Pyetja 3 e Provimit 1, transaksionet nested organizohen si një **pemë nën-transaksionesh** brenda transaksionit prind, ndërsa transaksionet flat janë një sekuencë e vetme, e pastrukturuar hierarkikisht, operacionesh.

---

### Pyetja 11 [3 pikë]
**Cili komponent i middleware-it vepron si ndërmjetës për kërkesa dhe përgjigje?**

A) DNS Resolver  B) Message Queue  C) Object Request Broker (ORB)  D) RPC Monitor

**Përgjigje: C) Object Request Broker (ORB).** ORB-i (koncept qendror në arkitekturat e objekteve të shpërndara si CORBA) është komponenti i middleware-it që merr kërkesat e klientit drejtuar objekteve të largëta, i rrugëton te objekti/serveri i duhur, dhe e kthen përgjigjen mbrapsht te klienti, duke fshehur nga të dyja palët detajet e komunikimit në rrjet.

---

### Pyetja 12 [3 pikë]
**Cila nga këto teknika përdoret për kontrollin e konkurrencës në transaksione të shpërndara?**

A) Deadlock prevention  B) Timestamp ordering  *(opsionet e tjera nuk janë plotësisht të lexueshme në fotografinë origjinale — ka gjasa të vazhdojë me C) Two-phase locking (2PL) dhe D) Të gjitha të mësipërmet)*

**Përgjigje:** Të tria teknikat e listuara — **parandalimi/zbulimi i deadlock-ut**, **renditja me vula kohore (timestamp ordering)**, dhe **bllokimi me dy faza (2PL/Strict 2PL)** — janë teknika **të vlefshme dhe reale** të kontrollit të konkurrencës në transaksione të shpërndara; nëse opsioni i fundit (D) është "Të gjitha të mësipërmet", ai është përgjigja më e plotë dhe e saktë. Nëse pyetja kërkon vetëm njërën si "më e përdorura standardisht", përgjigja më e zakonshme në praktikë është **Two-Phase Locking (2PL)**.

---

### Pyetja 13 [3 pikë]
*(Fillimi i pyetjes u pre në fotografinë origjinale — dukeshin vetëm dy nga opsionet.)*
**Cilat janë llojet/modelet e konsistencës në sistemet e shpërndara (p.sh. në kontekst të replikimit të të dhënave)?**

c) Konsistencë kauzale (Causal consistency)  d) Konsistencë përfundimtare (Eventual consistency)

**Përgjigje (shpjegim i llojeve kryesore të konsistencës, meqë stema e plotë e pyetjes nuk ishte plotësisht e lexueshme):**

- **Konsistenca strikte/sekuenciale (Strong/Sequential consistency):** çdo lexim sheh domosdoshmërisht vlerën e shkrimit **më të fundit** të kryer kudo në sistem — garancia më e fortë, por më e shtrenjtë (rrit vonesën, ul disponueshmërinë).
- **Konsistenca e dobët (Weak consistency):** nuk garantohet që leximet të reflektojnë menjëherë shkrimet e fundit; lejohet vonesë e papërcaktuar përpara se ndryshimet të "shihen" nga të tjerët.
- **Konsistenca kauzale (Causal consistency):** operacionet e lidhura **shkak-pasojë** (causally related) shihen nga të gjitha nyjet në të njëjtin rend; operacionet e pavarura (konkurruese) mund të shihen në rend të ndryshëm nga nyje të ndryshme.
- **Konsistenca përfundimtare (Eventual consistency):** nëse nuk vijnë shkrime të reja, **të gjitha kopjet përfundimisht do të konvergojnë** drejt të njëjtës vlerë — nuk garanton kur saktësisht ndodh kjo, por garanton që ndodh "eventualisht" (e përdorur gjerësisht në sistemet NoSQL dhe të shkallës së gjerë, si p.sh. DNS, Amazon DynamoDB).

---

### Pyetja 14 [3 pikë]
**Cila nga përgjigjet është e vërtetë për SOAP (Simple Object Access Protocol)?** — Identike me **Pyetjen 9** më sipër → **Përgjigje: D).**

---

### Pyetja 15 [3 pikë]
**Cila nga opsionet e mëposhtme është më e përshtatshme për komunikim ndërmjet mikroshërbimeve që kërkojnë performancë të lartë dhe transmetim efikas binar?**

A) REST  B) gRPC  C) WebSockets  D) GraphQL

**Përgjigje: B) gRPC.** gRPC ndërton mbi **HTTP/2** dhe përdor **Protocol Buffers** për serializim binar shumë kompakt dhe të shpejtë (në krahasim me XML/JSON tekstual të REST-it), duke ofruar gjithashtu mbështetje të drejtpërdrejtë për *streaming* dypalësh — prandaj është zgjedhja standarde për komunikim me performancë të lartë mes mikroshërbimeve.

---

### Pyetja 16 [20 pikë]
**Një server i menaxhon resurset a1, a2, …, an dhe ofron dy operacione: read(i) dhe write(i, vlera). Konsideroni transaksionet A, B, C dhe D:**

| Koha | A | B | C | D |
|---|---|---|---|---|
| 1 | openTransaction | openTransaction | openTransaction | openTransaction |
| 2 | y1=read(j) | | | |
| 3 | | y2=read(j) | | |
| 4 | | write(k,54) | y3=read(i) | |
| 5 | | write(j,25) | write(i,42) | y4=read(k) |
| 6 | | | | write(k,52) |
| 7 | | | | y5=read(i) |
| 9 | | | write(j,6) | |
| 10 | write(k,28) | | commit | |
| 11 | read(k) | | | |
| 12 | write(j,28) | | | |
| 13 | commit | | | write(k,55) |
| 14 | | y2=read(j) | | commit |
| 15 | | commit | | |

*(Kjo është saktësisht "Ushtrimi 2" i zgjidhur më parë në `kapitulli-11-zgjidhjet-e-ushtrimeve.md` — riprodhuar këtu i plotë.)*

**a) A duhet B të presë për bllokimin, për të lexuar `j`?** Jo — në kohën 3, `j` mbahet vetëm me lexim nga A; bllokimet e leximit janë të përbashkëta (shared), kështu B e merr menjëherë.

**b) A duhet D të presë për bllokimin, për të lexuar `k`?** Po — `k` mbahet me shkrim (ekskluziv) nga B që nga koha 4; D pret B.

**c) Në cilën kohë B arrin ta përfundojë transaksionin; nëse jo, pse?** **B nuk e përfundon kurrë vetvetiu.** Që nga koha 5, B pret bllokimin e shkrimit mbi `j` (mbajtur nga A, i pakonfirmuar), ndërsa vetë A, që nga koha 10, pret bllokimin e shkrimit mbi `k` (mbajtur nga B). Kjo është **deadlock i vërtetë mes A dhe B** (cikli A→B→A) — asnjëri s'mund të vazhdojë pa ndërhyrjen e sistemit (zbulim + abort i njërit).

**d) A duhet A të presë për bllokimin, për të shkruar `k`? Cilin proces?** Po — A pret **B**, sepse B ende mban bllokimin ekskluziv mbi `k` (i pakonfirmuar që nga koha 4).

**e) A arrijnë t'i përfundojnë punën A, C dhe D?** **Jo, asnjëri**, përderisa deadlock-u A–B mbetet i pazgjidhur: **C** pret `j`-në e mbajtur nga A *dhe* B (të dy të ngërthyer në deadlock); **D** pret `k`-në e mbajtur nga B (i cili nuk e liron kurrë, pasi është vetë në deadlock); **A** është vetë pjesë e ciklit. Vetëm pasi sistemi ta zbulojë deadlock-un dhe të bëjë **abort** njërin nga A ose B (zakonisht B), transaksionet e mbetura mund të vazhdojnë e të përfundojnë njëri pas tjetrit.

---

## Përmbledhje e përgjithshme

Shumica e pyetjeve në këto tri provime mbulojnë temat e Kapitujve **2** (modelet, transparenca), **4** (socket-e, IPC), **5** (RPC/RMI, protokollet e shkëmbimit), **6** (komunikimi indirekt), **8** (SOAP/WSDL/REST/gRPC), **10** (sinkronizimi i orëve — Cristian, NTP, Berkeley), dhe veçanërisht **11** (transaksionet, ACID, 2PL/Strict 2PL, deadlock) — të cilat janë trajtuar në detaje të plota në skedarët `kapitulli-XX-*.md` të përgatitur më parë. Për praktikë shtesë, skenarët e bllokimit/deadlock-ut (Pyetja 15 e Provimit 1 dhe Pyetja 16 e Provimit 3) janë ekzaktësisht të njëjtat ushtrime të zgjidhura në `kapitulli-11-zgjidhjet-e-ushtrimeve.md`.
