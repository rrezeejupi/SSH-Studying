# Kapitulli 10 — Koha dhe Gjendja Globale në Sistemet e Shpërndara

## Hyrje

Koha është njëkohësisht një çështje praktike dhe një koncept teorik thelbësor për të kuptuar se si shpalosen (evoluojnë) ekzekutimet në sistemet e shpërndara. Në një kompjuter të vetëm, gjithçka ndodh sipas një rendi të vetëm, të diktuar nga një orë e vetme e procesorit — kështu që "para" dhe "pas" janë koncepte të qarta. Në një sistem të shpërndarë kjo thjeshtësi zhduket:

- Çdo kompjuter (nyje) ka **orën e vet fizike**, të pavarur nga orët e nyjeve të tjera.
- Orët fizike **devijojnë** (drejtojnë me shpejtësi pak të ndryshme nga njëra-tjetra), dhe për këtë arsye **nuk mund të sinkronizohen në mënyrë perfekte**.
- E vetmja mënyrë që proceset të komunikojnë dhe të shkëmbejnë informacion është përmes **mesazheve mbi rrjet**, dhe vonesa e këtyre mesazheve është **e paparashikueshme**.

Këto fakte kanë dy pasoja madhore, të cilat përbëjnë edhe strukturën e këtij kapitulli:

1. **Nuk mund të kemi një orë globale të përbashkët** — prandaj studiojmë algoritme për **sinkronizimin e orëve fizike** (Cristian, NTP) dhe alternativën e tyre, **kohën logjike** (orët e Lamportit), e cila rendit ngjarjet pa u mbështetur në kohën reale.
2. **Nuk mund të dimë me siguri "gjendjen globale" të sistemit në një moment të caktuar kohor** — prandaj studiojmë si mund të kapim (regjistrojmë) një **gjendje globale të qëndrueshme (konsistente)** e sistemit, edhe pse mungon koha globale, përmes **algoritmit snapshot të Chandy dhe Lamport**.

Arsyet pse koha është kaq e rëndësishme në sisteme të shpërndara:

- **Së pari**, koha është një sasi që gjithmonë duam ta matim me saktësi: për të ditur në cilën kohë reale ndodhi një ngjarje e caktuar në një kompjuter të caktuar, është e nevojshme që ora e tij lokale të sinkronizohet me një burim autoritativ e të jashtëm kohor.
- **Së dyti**, shumë algoritme që zgjidhin probleme klasike të shpërndarjes varen nga sinkronizimi i orës — p.sh. ruajtja e qëndrueshmërisë së të dhënave të shpërndara, kontrolli i origjinalitetit (freskisë) të një kërkese të dërguar te një server, dhe eliminimi i përpunimit të dyfishtë të kërkesave/përditësimeve të njëjta.

Sistemet e shpërndara janë subjekt i **pasigurisë kohore**: për shkak të vonesës së paparashikueshme të mesazheve në rrjet, **sinkronizimi absolut** i proceseve dihet të jetë **teorikisht i pamundur**. Kjo pamundësi rrjedh drejtpërdrejt nga vetë përkufizimi i një sistemi të shpërndarë, i cili sjell tri pasoja themelore:

- **Njëkohshmëria e komponentëve** (proceset ekzekutojnë paralelisht, në mënyrë të pavarur).
- **Mungesa e një ore globale të përbashkët**.
- **Dështimet e pavarura të komponentëve** (një nyje mund të dështojë pa i thënë të tjerave).

Motivimi kryesor për të ndërtuar dhe përdorur sisteme të shpërndara është **ndarja e burimeve** — term mjaft abstrakt që përfshin gjithçka, nga burimet hardware (disqe, printera) deri te entitetet e përcaktuara nga software (skedarë, baza të dhënash, objekte të të dhënave të çdo lloji).

### Struktura e kapitullit

Materiali ndahet natyrshëm në dy pjesë të mëdha:

**A. Koha globale**
1. Orët, ngjarjet dhe gjendjet e procesit
2. Sinkronizimi i orëve fizike (Cristian, NTP)
3. Koha logjike dhe orët logjike (Lamport)

**B. Gjendjet globale**
1. Gjendjet globale dhe prerjet e qëndrueshme
2. Algoritmi snapshot i Chandy dhe Lamport

---

## 1. Orët, ngjarjet dhe gjendjet e procesit

Për të arsyetuar në mënyrë formale rreth kohës dhe gjendjes në një sistem të shpërndarë, na duhet një model matematikor i thjeshtë por i saktë i asaj çka ndodh brenda çdo procesi.

### 1.1 Elementet e modelit

| Simbol | Kuptimi |
|---|---|
| ℘ | Koleksioni i **N** proceseve $p_i,\ i = 1, 2, \dots, N$, që përbëjnë sistemin e shpërndarë. |
| $s_i$ | **Gjendja** e procesit $p_i$ — gjithçka që përcakton "situatën" e tij në një çast të caktuar (vlerat e variablave, përmbajtja e memories lokale, etj.). Gjendja ndryshon si rezultat i **veprimeve** që kryen $p_i$. |
| $e$ | Një **ngjarje** (*event*) — ndodhia e një veprimi të vetëm brenda një procesi (p.sh. dërgimi ose pranimi i një mesazhi, ose ndonjë veprim tjetër lokal që ndryshon gjendjen). |
| $\rightarrow_i$ | Relacioni **"ndodh para"** brenda procesit $p_i$: $e \rightarrow_i e'$ do të thotë se ngjarja $e$ ndodh para ngjarjes $e'$ *në procesin $p_i$*. Ky relacion përcakton **rendin total** të ngjarjeve brenda një procesi të vetëm (sepse brenda një procesi, ngjarjet ndodhin njëra pas tjetrës, në mënyrë sekuenciale). |
| $history(p_i) = h_i$ | **Historia** e procesit $p_i$: sekuenca e renditur e të gjitha ngjarjeve që ka përjetuar deri në një pikë, $h_i = \langle e_i^0, e_i^1, e_i^2, \dots \rangle$. |

**Pika kyçe:** brenda çdo procesi individual, ekziston një rend total i qartë i ngjarjeve (secili proces "e di" rendin e veprimeve të veta). Problemi shfaqet kur duam të krahasojmë ngjarje **ndërmjet proceseve të ndryshme** — atëherë nuk kemi më një orë të përbashkët referimi.

### 1.2 Orët fizike, animi (drift) dhe zhvendosja (skew)

- Çdo kompjuter posedon një **orë fizike hardware** (zakonisht bazuar në një oshilator kristali).
- Këto orë hardware nuk janë asnjëherë perfekte: ato **animojnë** (*clock drift*) — d.m.th. shpejtësia me të cilën "tik-takojnë" ndryshon lehtë nga shpejtësia nominale/e vërtetë e kohës, dhe kjo shpejtësi mund të ndryshojë me kalimin e kohës (p.sh. për shkak të temperaturës).
- Për pasojë, dy orë që ishin njëkohësisht të sinkronizuara në kohën $t_0$ do të **largohen gradualisht** njëra nga tjetra — kjo diferencë quhet **zhvendosje e orës** (*clock skew*).
- Për të matur kohën reale në mënyrë të njëtrajtshme në mbarë botën, përdoret **Koha Universale e Koordinuar (UTC — Coordinated Universal Time)**, e transmetuar nga burime autoritative (p.sh. sinjale radio, satelitë GPS) dhe e përdorur si referencë absolute kur kompjuterët sinkronizohen me "botën e jashtme".
- Sfida themelore ilustrohet skematikisht si vijon: shumë kompjuterë, secili me orën e vet lokale, të lidhur me njëri-tjetrin përmes një rrjeti — dhe orët e tyre, të lëna të pakontrolluara, **animojnë** larg njëra-tjetrës me kalimin e kohës.

Kjo është arsyeja pse na duhen **algoritme sinkronizimi**: mekanizma që periodikisht rregullojnë orët lokale në mënyrë që të mbeten afër njëra-tjetrës (ose afër një burimi autoritativ), përkundër drejtimit të tyre jo-perfekt.

---

## 2. Sinkronizimi i orëve fizike

Kur flasim për sinkronizimin e orëve, dallojmë dy lloje:

- **Sinkronizimi i jashtëm** (*external synchronization*): orët lokale të proceseve sinkronizohen me një burim kohor të jashtëm dhe autoritativ (p.sh. UTC). Të gjithë proceset synojnë të afrohen sa më shumë me këtë kohë "objektive".
- **Sinkronizimi i brendshëm** (*internal synchronization*): proceset sinkronizojnë orët e tyre **njëra me tjetrën**, pa u kujdesur domosdoshmërisht nëse janë afër ndonjë kohe absolute të jashtme — mjafton që të jenë afër njëra-tjetrës (konsistente mes vete).

### 2.1 Sinkronizimi në një sistem sinkron

Në një sistem sinkron (ku dihen kufij të njohur mbi vonesat), sinkronizimi bazohet në matjen e kohës së shkuar deri sa mbërrin një mesazh:

- Nëse një mesazh dërgohet në kohën $t$, dhe koha e transmetimit të mesazhit ndërmjet dy proceseve është $T_{trans}$, atëherë koha e mbërritjes pritet të jetë $t + T_{trans}$.
- Meqë vonesa e transmetimit nuk është fikse, por ndryshon brenda një intervali të njohur $[min, max]$, pasiguria e vlerësimit të kohës shprehet me diferencën:
$$u = (max - min)$$
- Kjo pasiguri mund të reduktohet duke ekzekutuar matje të përsëritura dhe duke marrë parasysh N mostra, duke e sjellë pasigurinë e mundshme deri në $u \cdot (1 - 1/N)$ — pra sa më shumë matje kryejmë, aq më e ngushtë (më e saktë) bëhet kufizimi i pasigurisë.

Ky është themeli konceptual mbi të cilin ndërtohet **metoda e Cristian**.

---

## 3. Metoda e Cristian për sinkronizimin e orëve

Metoda e Cristian është një teknikë klasike e **sinkronizimit të jashtëm**, ku një proces $p$ (klient) sinkronizon orën e vet me një **server kohor $S$** që ka akses në një burim kohor të saktë (p.sh. i lidhur me një marrës UTC).

### 3.1 Ideja

Procesi $p$ nuk mund thjesht të pyesë serverin "sa është ora?" dhe të përdorë përgjigjen drejtpërdrejt, sepse mesazhi i përgjigjes vetë ha kohë për të udhëtuar nëpër rrjet — dhe kjo kohë udhëtimi është e panjohur paraprakisht. Zgjidhja e Cristian-it është ta **vlerësojë** këtë vonesë duke matur kohën e plotë të vajtje-ardhjes (*round-trip time*, RTT).

### 3.2 Hapat e algoritmit

1. Procesi $p$ shënon kohën lokale $T_0$ (sipas orës së vet) dhe i dërgon serverit $S$ një kërkesë ("sa është ora tani?").
2. Serveri $S$ pranon kërkesën, lexon kohën e vet aktuale të saktë $t$, dhe ia kthen $p$-së si përgjigje.
3. Procesi $p$ pranon përgjigjen në kohën lokale $T_1$ (përsëri sipas orës së vet, para se ta rregullojë).
4. $p$ llogarit kohën e plotë të vajtje-ardhjes: $RTT = T_1 - T_0$.
5. Duke supozuar se rrjeti është simetrik (koha e shkuarjes ≈ koha e ardhjes, secila afërsisht $RTT/2$), $p$ vlerëson se koha e serverit $t$ ishte e vlefshme rreth $RTT/2$ kohë pas dërgimit, prandaj rregullon orën e vet në:
$$T_{ri} = t + \frac{RTT}{2}$$
6. Procesi $p$ e vendos orën e vet lokale në $T_{ri}$ (ose e rregullon gradualisht drejt saj, në vend që ta "kërcejë" papritur, për të shmangur hopa të papritur në kohë që mund të prishin renditjen e ngjarjeve lokale).

### 3.3 Pasiguria

Vlerësimi ka gjithsesi një **margjinë gabimi**, sepse RTT nuk ndahet domosdoshmërisht në mënyrë të barabartë ndërmjet dërgimit dhe kthimit (mund të ketë asimetri në vonesat e rrjetit apo në kohën e përpunimit të serverit). Pasiguria mund të kufizohet si:
$$\pm \frac{(RTT - 2 \cdot min\_trans\_time)}{2}$$
ku $min\_trans\_time$ është vonesa minimale e njohur e transmetimit. Sa më i vogël RTT-ja (pra sa më afër dhe më i shpejtë serveri), aq më e saktë sinkronizimi.

**Kufizimet e metodës së Cristian:**
- Bazohet mbi **një server të vetëm** kohor — nëse ai server dështon, sinkronizimi ndalon (pikë e vetme dështimi).
- Nuk mbron kundër gabimeve keqdashëse ose defekteve në vetë serverin (nëse serveri jep një kohë të gabuar, klientët e besojnë atë pa e verifikuar kryq me burime të tjera).

Këto kufizime motivojnë protokollin më të fuqishëm dhe të shkallëzueshëm — **NTP**.

---

## 4. Protokolli i kohës së rrjetit (NTP)

**NTP (Network Time Protocol)** është protokolli standard i përdorur gjerësisht në internet për sinkronizimin e orëve. Ndryshe nga metoda e thjeshtë e Cristian-it (një klient — një server), NTP është projektuar për shkallëzueshmëri të gjerë, tolerancë ndaj dështimeve, dhe saktësi të lartë edhe në praninë e vonesave të ndryshueshme të rrjetit.

### 4.1 Objektivat e NTP-së

- T'u mundësojë klientëve në internet **sinkronizim të jashtëm** të saktë me UTC.
- Të sigurojë një shërbim **të besueshëm**, që mund të përballojë humbjen e lidhjeve dhe redundancën e shumë serverëve.
- Të mundësojë **sinkronizim të shpeshtë** për të kundërshtuar animin e orëve.
- Të mbrojë kundër ndërhyrjeve (dëmtimit apo sulmeve) mbi shërbimin kohor.

### 4.2 Struktura hierarkike (strata)

NTP organizon serverët kohorë në një **hierarki shtresash (strata)**, në formë peme/subneti:

- **Strata 1** (niveli më i lartë): serverë të lidhur drejtpërdrejt me një burim kohor shumë të saktë e autoritativ (p.sh. orë atomike, marrës GPS/UTC).
- **Strata 2**: serverë që sinkronizohen me serverët e stratës 1.
- **Strata 3** dhe kështu me radhë: serverë që sinkronizohen me shtresën menjëherë mbi to.

Kjo strukturë hierarkike (e ilustruar si nënrrjet sinkronizimi me nivele të renditura 1 → 2 → 2 → 3 → 3 → 3) lejon që ngarkesa e sinkronizimit të shpërndahet: serverët e nivelit të lartë u shërbejnë shumë serverëve të nivelit më të ulët, në vend që të gjithë klientët të mbështeten te i njëjti server qendror — duke shmangur kështu pengesën (bottleneck) dhe pikën e vetme të dështimit të metodës Cristian.

### 4.3 Mënyrat e sinkronizimit në NTP

NTP mbështet tri mënyra operimi ndërmjet një çifti serverësh:

1. **Mënyra multicast**: një server i shtresës më të lartë transmeton kohën periodikisht te të gjithë serverët në një rrjet lokal — e përshtatshme për rrjete me shpejtësi të lartë dhe kërkesa saktësie mesatare.
2. **Mënyra procedure-call (kërkesë/përgjigje)**: ngjashëm me metodën e Cristian-it — një server i bën kërkesë tjetrit dhe vlerëson kohën duke përdorur RTT-në, duke ofruar saktësi më të lartë.
3. **Mënyra simetrike**: përdoret ndërmjet serverëve në shtresat më të larta (ku kërkohet saktësi maksimale); dy serverë shkëmbejnë mesazhe që mbajnë kohën e katër ngjarjeve të fundit (dërgim/pranim reciprok), duke lejuar një vlerësim shumë të saktë të diferencës së orës dhe të vonesës.

### 4.4 Parimi themelor i llogaritjes

Në secilën prej mënyrave, çdo mesazh që shkëmbehet mban vulën kohore (timestamp) të dërgimit dhe pranimit. Duke krahasuar këto vula kohore ndërmjet dy anëve, secili server mund të vlerësojë:
- **Zhvendosjen (offset)** ndërmjet orës së vet dhe orës së partnerit, dhe
- **Vonesën (delay)** e rrugës së mesazhit,

në një mënyrë konceptualisht të ngjashme me RTT/2 të metodës Cristian, por të përpunuar statistikisht mbi shumë mostra për saktësi më të lartë dhe për të filtruar mostrat me vonesë të lartë ose të paqëndrueshme (jitter).

NTP përdor gjithashtu algoritme statistikore për të zbuluar dhe hedhur poshtë orë "të gabuara" (që devijojnë tepër nga shumica), duke e bërë sistemin **tolerant ndaj gabimeve** të serverëve individualë.

---

## 5. Koha logjike dhe orët logjike

Sinkronizimi i orëve fizike (Cristian, NTP) na afron sa më shumë me kohën reale, por **kurrë në mënyrë perfekte** — gjithmonë mbetet një pasiguri. Për shumë probleme në sistemet e shpërndara, ajo çka na intereson realisht **nuk është koha e saktë e orës**, por **rendi shkak-pasojë (causal order)** i ngjarjeve: "a ndodhi ngjarja A para ngjarjes B, apo janë të pavarura (njëkohshme)?"

Këtu hyn koncepti i **kohës logjike**, i prezantuar nga Leslie Lamport, i cili e zëvendëson kohën fizike me një numërim (orë logjike) që kap thjesht **rendin e mundshëm shkakësor** të ngjarjeve.

### 5.1 Relacioni "ndodh-para" (happens-before), HB→

Relacioni **ndodh-para** ($\rightarrow$), i shënuar edhe si relacioni i Lamportit, përkufizohet nga tri rregulla:

- **HB1** — Nëse ekziston një proces $p_i$ i tillë që $e \rightarrow_i e'$ (d.m.th. $e$ ndodh para $e'$ *brenda të njëjtit proces*), atëherë $e \rightarrow e'$.
 (Rendi lokal brenda një procesi ruhet gjithmonë në rendin global shkakësor.)
- **HB2** — Për çdo mesazh $m$: $dërgo(m) \rightarrow prano(m)$.
 (Dërgimi i një mesazhi ndodh gjithmonë para pranimit të tij — shkaku para pasojës.)
- **HB3** (tranzitiviteti) — Nëse $e \rightarrow e'$ dhe $e' \rightarrow e''$, atëherë $e \rightarrow e''$.

Nëse asnjëra nga këto rregulla nuk lidh dy ngjarje $e$ dhe $e'$ (pra as $e \rightarrow e'$, as $e' \rightarrow e$), atëherë ato quhen **njëkohshme (concurrent)**, $e \parallel e'$ — nuk ka mënyrë të përcaktohet cila "ndodhi para" tjetrës, sepse nuk ka asnjë zinxhir shkakësor (as të drejtpërdrejtë, as përmes mesazheve) mes tyre.

**Shembulli klasik me tri procese** $p_1, p_2, p_3$ (siç ilustrohet edhe në material): $p_1$ përjeton ngjarjet $a$ pastaj $b$, dhe dërgon mesazhin $m_1$ te $p_2$; $p_2$ përjeton ngjarjen $c$; $p_3$ përjeton $e$, më vonë $p_2$ dërgon mesazhin $m_2$ te $p_3$, që përjeton ngjarjen $f$. Nga rregullat HB1–HB3 mund të nxjerrim, p.sh., se $a \rightarrow b$ (HB1, brenda $p_1$), $b \rightarrow c$ (nëse $m_1$ dërgohet pas $b$ dhe pranohet si $c$, sipas HB2), dhe $e \rightarrow f$ nëse rrjedh nga zinxhiri i duhur — ndërsa ngjarje si $a$ dhe $e$, që nuk kanë asnjë zinxhir shkakësor mes tyre, janë **njëkohshme**.

### 5.2 Orët logjike të Lamportit

Për ta **kapur numerikisht** relacionin "ndodh-para", Lamport propozoi një mekanizëm të thjeshtë: çdo proces $p_i$ mban një **numërues logjik** $L_i$ (fillimisht 0), i cili përditësohet sipas dy rregullave:

1. **Para çdo ngjarjeje** (lokale, ose dërgim mesazhi), procesi $p_i$ rrit orën e vet logjike:
$$L_i \leftarrow L_i + 1$$
2. **Kur dërgon një mesazh**, $p_i$ e bashkëngjit vlerën aktuale të $L_i$ te mesazhi (si vulë kohore logjike).
3. **Kur pranon një mesazh** me vulë kohore $t_m$, procesi pranues $p_j$ e rregullon orën e vet si:
$$L_j \leftarrow \max(L_j,\ t_m) + 1$$

Kjo garanton vetinë themelore: **nëse $e \rightarrow e'$ (sipas relacionit ndodh-para), atëherë $L(e) < L(e')$.** (Kujdes: e kundërta nuk vlen domosdoshmërisht — dy vlera $L(e) < L(e')$ nuk garantojnë detyrimisht $e \rightarrow e'$, sepse ngjarje njëkohshme mund të marrin gjithsesi vlera të ndryshme numerike logjike.)

**Shembull i thjeshtë:** Nëse $p_1$ ka orën logjike 3 kur dërgon një mesazh te $p_2$, dhe $p_2$ ka orën e vet logjike në 2 kur e pranon, atëherë $p_2$ e rregullon orën e vet në $\max(2, 3) + 1 = 4$. Kështu, edhe pa asnjë njohuri të kohës fizike reale, sistemi ruan rendin shkakësor: çdo ngjarje "pasardhëse shkakësore" merr gjithmonë një numër logjik më të madh se "paraardhësja" e saj.

**Rëndësia praktike:** orët logjike (dhe zgjerimi i tyre, orët e vektorëve, që mund të dallojnë saktësisht njëkohshmërinë nga varësia shkakësore) janë vegla themelore për shumë algoritme të shpërndara: renditje e ngjarjeve, kontroll konkurence, snapshot-e të qëndrueshme (siç do të shohim më poshtë), replikim, e shumë të tjera — pa u mbështetur fare në orët fizike.

---

## 6. Gjendjet globale

### 6.1 Pse na duhet një "gjendje globale"?

Shpesh kemi nevojë të dimë **çfarë ndodh njëkohësisht** në mbarë sistemin — jo vetëm brenda një procesi të vetëm. P.sh., dëshirojmë të përgjigjemi pyetjeve si: "A ka mbetur ndonjë mesazh 'në ajër' (i padërguar akoma në destinacion)?", "A janë të gjitha burimet e alokuara në mënyrë të sigurt (pa bllokim të ndërsjellë)?", ose "A ka përfunduar llogaritja në mbarë sistemin?" Këto janë probleme klasike që kërkojnë njohuri të **gjendjes globale**, dhe ilustrohen mirë përmes tri shembujve klasikë:

**a) Mbledhja e mbeturinave (*garbage collection*) e shpërndarë**
 Procesi $p_1$ mban një referencë ndaj një objekti; kjo referencë i dërgohet përmes një mesazhi procesit $p_2$, i cili tashmë krijon referencën e vet ndaj po atij objekti. Nëse $p_1$ e fshin referencën e vet **para** se $p_2$ ta pranojë mesazhin, sistemi rrezikon të konkludojë gabimisht se objekti nuk ka më referenca (dhe ta trajtojë si "mbeturinë" për t'u fshirë), ndërkohë që në fakt objekti është ende në përdorim nga $p_2$. Për të vendosur saktë nëse një objekt është vërtet i papërdorur, duhet parë **gjendja e kombinuar** e të dy proceseve njëkohësisht.

**b) Zbulimi i bllokimit të ndërsjellë (*deadlock detection*)**
 $p_1$ pret një burim që mbahet nga $p_2$, dhe njëkohësisht $p_2$ pret një burim që mbahet nga $p_1$ (situatë "prit-për" reciproke). Vetëm duke parë gjendjen e të dy proceseve **njëkohësisht** mund të zbulohet cikli i pritjes reciproke; secili proces, i parë veç e veç, nuk e "sheh" problemin.

**c) Zbulimi i përfundimit (*termination detection*)**
 Proceset kalojnë ndërmjet gjendjes **aktive** dhe **pasive**; një proces pasiv aktivizohet vetëm kur pranon një mesazh. Sistemi si tërësi konsiderohet i "përfunduar" vetëm kur **të gjitha** proceset janë pasive **dhe** nuk ka asnjë mesazh ende "në fluturim" nëpër kanale. Përsëri, kjo kërkon një pamje të gjendjes së **mbarë** sistemit, jo vetëm të një procesi.

### 6.2 Përkufizimi formal i gjendjes globale

Për të formalizuar këto koncepte:

- $h_i^k = \langle e_i^0, e_i^1, \dots, e_i^k \rangle$ — historia e procesit $p_i$ deri (dhe duke përfshirë) ngjarjen e $k$-të.
- $s_i^k$ — **gjendja** e procesit $p_i$ menjëherë **pas** ngjarjes $k$ (pra rezultati i zbatimit të asaj ngjarjeje mbi gjendjen paraardhëse).
- **Historia globale**: bashkimi i historive të të gjitha proceseve, $H = h_0 \cup h_1 \cup \dots \cup h_{N-1}$.
- **Gjendja globale**: një tufë (tuple) e gjendjeve momentale të secilit proces, $S = (s_1, s_2, \dots, s_N)$.
- **Prerja (cut)**: një nën-bashkësi e historisë globale, e marrë duke prerë historinë e secilit proces në një pikë të caktuar (jo domosdoshmërisht të njëjtën ngjarje-numër për të gjithë), $C = h_1^{c1} \cup h_2^{c2} \cup \dots \cup h_N^{cN}$, ku $c_i$ tregon deri te cila ngjarje shkon prerja në procesin $p_i$.
- Ekzekutimi i sistemit mund të shihet si një **sekuencë gjendjesh globale** që pasojnë njëra-tjetrën: $S_0 \rightarrow S_1 \rightarrow S_2 \rightarrow \dots$, ku çdo kalim shkaktohet nga ndodhia e një (ose disa) ngjarjesh.

### 6.3 Prerje të qëndrueshme (konsistente) kundrejt atyre të paqëndrueshme

Jo çdo "prerje" e mundshme e historive të proceseve përfaqëson një gjendje globale që **mund të ketë ndodhur realisht** në ndonjë çast të vërtetë kohor. Kjo është dallimi qendror:

- Një **prerje e qëndrueshme (consistent cut)** është një prerje që **respekton relacionin ndodh-para**: nëse ndonjë ngjarje pranimi $e'$ (p.sh. pranim i një mesazhi) përfshihet në prerje, atëherë detyrimisht duhet të përfshihet edhe ngjarja korresponduese e dërgimit $e$ e atij mesazhi (sepse $dërgo \rightarrow prano$, sipas HB2). Me fjalë të tjera: **prerja nuk mund të përmbajë pranimin e një mesazhi pa përmbajtur edhe dërgimin e tij**.
- Një **prerje e paqëndrueshme (inconsistent cut)** e shkel këtë kusht: përfshin pranimin e një mesazhi, por **jo** dërgimin përkatës — pra sikur mesazhi të ishte pranuar "para" se të dërgohej, gjë absurde dhe fizikisht e pamundur.

Kjo ilustrohet qartë me shembullin: procesi $p_1$ përjeton ngjarjet $e_1^1, e_1^2, e_1^3$; procesi $p_2$ përjeton $e_2^0, e_2^1, e_2^2$. Mesazhi $m_1$ dërgohet nga $p_1$ dhe pranohet nga $p_2$; mesazhi $m_2$ dërgohet nga $p_2$ dhe pranohet nga $p_1$ më vonë. Nëse vizatojmë një "prerje" (vijë vertikale përafërsisht) që kalon **pas** pranimit të $m_2$ në $p_1$ por **para** dërgimit të $m_2$ në $p_2$, kjo prerje do të ishte e paqëndrueshme — sepse do të përmbante pasojën ($m_2$ i pranuar) pa përfshirë shkakun (dërgimin e $m_2$). Nëse, në të kundërt, prerja i "kap" të dy anët në rend të drejtë shkakësor (të gjithë dërgimet para pranimeve përkatëse), ajo është **e qëndrueshme**.

**Pse ka rëndësi?** Vetëm gjendjet globale që korrespondojnë me prerje **të qëndrueshme** kanë kuptim fizik real — vetëm ato mund të kenë ekzistuar vërtet gjatë ekzekutimit. Çdo algoritëm që synon të "kapë" (snapshot) gjendjen globale të një sistemi të shpërndarë duhet të garantojë se prerja e prodhuar është e qëndrueshme, përndryshe rezultati është i pakuptimtë ose çorientues (p.sh. mund të "shohë" një mesazh të pranuar por të mos ketë kurrë "parë" dërgimin e tij).

---

## 7. Algoritmi snapshot i Chandy dhe Lamport

Tani që dimë çfarë do të thotë një **gjendje globale e qëndrueshme**, mbetet pyetja praktike: si mund të **llogaritim (kapim) në mënyrë algoritmike** një gjendje të tillë, në kohë reale, ndërkohë që sistemi vazhdon të ekzekutojë, pa e ndalur atë, dhe pa pasur orë globale në dispozicion? Kjo është pikërisht ajo çka zgjidh **algoritmi snapshot i Chandy–Lamport** (1985), një ndër algoritmet më elegante e themelore të sistemeve të shpërndara.

### 7.1 Supozimet e modelit

- Sistemi përbëhet nga N procese, të lidhura ndërmjet tyre me **kanale komunikimi të drejtuara (unidirectional), pikë-për-pikë**, që dërgojnë mesazhe në **rend FIFO** (mesazhet mbërrijnë në të njëjtin rend në të cilin janë dërguar) dhe **nuk humbin** mesazhe.
- Çdo çift procesesh që komunikojnë ka të paktën një kanal të drejtpërdrejtë ndërmjet tyre (ose ekziston një rrugë komunikimi e njohur).
- Çdo proces mund të inicojë snapshot-in në çdo kohë, në mënyrë të pavarur.

### 7.2 Ideja themelore: mesazhet shënues (markers)

Algoritmi funksionon duke përhapur nëpër sistem mesazhe të veçanta të quajtura **shënues (markers)**, të cilët "ndajnë" rrjedhën e mesazheve të zakonshme mbi çdo kanal në "para snapshot-it" dhe "pas snapshot-it" — në mënyrë të ngjashme me atë se si një shënues fizik do të ndante letrat e postuara para një çasti nga ato të postuara pas atij çasti. Kjo garanton që prerja e rezultuar të jetë **e qëndrueshme**.

Algoritmi përcaktohet nga **dy rregulla**, të cilat zbatohen nga çdo proces $p_i$: **rregulli i marrjes (pranimit) së shënuesit** dhe **rregulli i dërgimit të shënuesit**.

### 7.3 Rregulli i marrjes së shënuesit (për procesin $p_i$)

Kur $p_i$ pranon një mesazh shënues mbi një kanal hyrës $c$:

```
Me marrjen e një mesazhi shënues te pᵢ mbi kanalin c:
    if (pᵢ nuk ka regjistruar ende gjendjen e vet)
        // ky është shënuesi i parë që pᵢ e sheh — snapshot-i po fillon "për të"
        1. regjistron gjendjen e vet (të procesit) TANI;
        2. regjistron gjendjen e kanalit c si BASHKËSI E ZBRAZËT (∅);
        3. fillon të regjistrojë çdo mesazh që mbërrin mbi kanalet e tjera HYRËSE
           (derisa të pranojë shënues edhe mbi ato kanale);
    else
        // pᵢ e ka regjistruar tashmë gjendjen e vet (shënues i mëparshëm e ka "zgjuar")
        4. pᵢ regjistron gjendjen e kanalit c si bashkësinë e MESAZHEVE
           që ka pranuar mbi c, që nga momenti kur regjistroi gjendjen e vet
           (e deri tani, kur mbërriti shënuesi mbi c — pra këto janë mesazhet
           "para shënuesit" mbi këtë kanal specifik, të cilat ende nuk ishin
           llogaritur në gjendjen e regjistruar të procesit);
    end if
```

Në thelb: **shënuesi i parë** që sheh një proces e "zgjon" atë për t'u përfshirë në snapshot dhe shënon fillimin e regjistrimit të mesazheve në kanalet e tjera hyrëse; **çdo shënues i mëpasshëm** mbi kanale të tjera mbyll (finalizon) regjistrimin e gjendjes së atij kanali specifik — pra mesazhet e regjistruara ndërmjet momentit kur $p_i$ regjistroi gjendjen e vet dhe momentit kur mbërriti shënuesi mbi atë kanal, përbëjnë gjendjen e regjistruar të atij kanali.

### 7.4 Rregulli i dërgimit të shënuesit (për procesin $p_i$)

```
Pasi pᵢ ka regjistruar gjendjen e vet (qoftë sepse e inicoi vetë
snapshot-in, qoftë sepse sapo pranoi shënuesin e parë):
    për SECILIN kanal DALËS c të pᵢ:
        pᵢ dërgon një mesazh shënues mbi c,
        PARA se të dërgojë ndonjë mesazh tjetër (të zakonshëm) mbi c.
```

Domethënë, menjëherë pas regjistrimit të gjendjes së vet, $p_i$ duhet t'ua përcjellë shënuesin **të gjitha** kanaleve të veta dalëse, dhe këtë e bën **para** çdo mesazhi tjetër — kjo garanton se shënuesi "ndan" saktë mesazhet "para snapshot-it" nga ato "pas snapshot-it" mbi çdo kanal, duke ruajtur qëndrueshmërinë (rendin FIFO e mban këtë ndarje të vlefshme).

### 7.5 Ekzekutimi i plotë i algoritmit, hap pas hapi

1. **Inicimi**: një proces (ose disa procese, njëkohësisht e në mënyrë të pavarur) vendos të nisë snapshot-in. Ai regjistron menjëherë gjendjen e vet lokale dhe zbaton **rregullin e dërgimit** — u dërgon shënues të gjitha kanaleve të veta dalëse.
2. **Përhapja**: çdo proces tjetër që pranon një shënues për herë të parë, zbaton **rregullin e marrjes** (rasti "if"): regjistron gjendjen e vet lokale (menjëherë, ashtu siç është në atë çast), shënon kanalin nga i cili erdhi shënuesi si bosh (∅), fillon të regjistrojë mesazhet hyrëse mbi kanalet e tjera të paregjistruara akoma, dhe pastaj zbaton edhe vetë **rregullin e dërgimit** — përcjell shënues te të gjitha kanalet e veta dalëse.
3. **Mbyllja e kanaleve**: kur një proces që tashmë e ka regjistruar gjendjen e vet, pranon një shënues të dytë (ose të mëtejshëm) mbi një kanal tjetër, zbaton rastin "else": mbyll regjistrimin e gjendjes së atij kanali si bashkësinë e mesazheve që kanë mbërritur mbi të që nga regjistrimi i gjendjes së procesit e deri te mbërritja e atij shënuesi.
4. **Përfundimi**: algoritmi përfundon (nga këndvështrimi lokal) për procesin $p_i$ kur ai ka pranuar nga saktësisht një shënues mbi **secilin** kanal të vet hyrës (dhe kështu ka regjistruar gjendjen e të gjitha kanaleve hyrëse). Kur të gjithë proceset kanë përfunduar, gjendjet e regjistruara individuale — gjendjet e proceseve **plus** gjendjet (mesazhet "në fluturim") e kanaleve — bashkohen (tipikisht dërgohen te një proces koordinues) për të formuar **snapshot-in global**.

### 7.6 Pse rezultati është një gjendje globale e qëndrueshme

Rezultati përfundimtar përbëhet nga:
- **Gjendja e regjistruar e secilit proces** (momentale, e marrë saktësisht kur pranoi shënuesin e parë ose kur inicoi vetë snapshot-in), dhe
- **Gjendja e regjistruar e secilit kanal** (bashkësia e mesazheve "në tranzit" mbi atë kanal në momentin logjik të snapshot-it — pra mesazhe që ishin dërguar para snapshot-it mbi kanalin përkatës, por ende nuk ishin pranuar nga procesi marrës kur ai regjistroi gjendjen e vet).

Falë faljes së rregullit "shënuesi dërgohet para çdo mesazhi tjetër" mbi çdo kanal (kombinuar me vetinë FIFO), garantohet që: **çdo mesazh që $p_i$ dërgon pas regjistrimit të gjendjes së vet, mbërrin te marrësi pas shënuesit**, pra pas që marrësi ka regjistruar (ose do të regjistrojë) gjendjen e vet. Kjo do të thotë se snapshot-i **kurrë nuk "sheh" pasojën (pranimin) e një mesazhi pa "parë" edhe shkakun (dërgimin) e tij** — që është pikërisht kushti i **prerjes së qëndrueshme** të përkufizuar më sipër (seksioni 6.3). Në këtë mënyrë algoritmi i Chandy–Lamport prodhon gjithmonë një gjendje globale që **mund të ketë ndodhur realisht**, edhe pse në praktikë ajo mund të mos përkojë me asnjë çast të vetëm real të kohës fizike (proceset regjistrohen në çaste të ndryshme lokale) — mjafton që të jetë **konsistente logjikisht**.

### 7.7 Përdorimi praktik

Snapshot-et e Chandy–Lamport shfrytëzohen për probleme si ato të seksionit 6.1: zbulim bllokimi të ndërsjellë të shpërndarë, zbulim përfundimi (termination detection), checkpointing/rikuperim nga dështime, mbledhje e shpërndarë e mbeturinave, dhe verifikim vetish globale (p.sh. balanca totale e parasë në një sistem bankar të shpërndarë mbetet e njëjtë).

---

## Përmbledhje

- Në sisteme të shpërndara **nuk ekziston një orë globale e përbashkët**; orët fizike lokale animojnë (drift) dhe zhvendosen (skew) njëra prej tjetrës, dhe vonesat e rrjetit janë të paparashikueshme — për këtë arsye **sinkronizimi absolut i proceseve është teorikisht i pamundur**.
- **Metoda e Cristian** sinkronizon orën e një klienti me një server kohor autoritativ, duke matur kohën e vajtje-ardhjes (RTT) dhe duke vlerësuar orën e serverit si $t + RTT/2$; kufizimi kryesor është varësia nga një server i vetëm.
- **NTP** e përgjithëson këtë ide në një **hierarki shtresash (strata)** serverësh kohorë, duke ofruar shkallëzueshmëri, redundancë dhe tolerancë ndaj gabimeve, përmes mënyrave multicast, procedure-call dhe simetrike.
- **Koha logjike** (orët e Lamportit) e zëvendëson kohën fizike me numra logjikë që respektojnë relacionin **ndodh-para** ($\rightarrow$, i përcaktuar nga rregullat HB1–HB3): brendashkakësisht, çdo pasojë merr numër logjik më të madh se shkaku i vet, edhe pa u mbështetur në kohën reale.
- Një **gjendje globale** është tufa e gjendjeve momentale të të gjitha proceseve; jo çdo "prerje" e historive individuale të proceseve është e kuptimshme — vetëm **prerjet e qëndrueshme (konsistente)** (ato që nuk përfshijnë pranimin e një mesazhi pa dërgimin përkatës) korrespondojnë me gjendje që mund të kenë ndodhur realisht.
- **Algoritmi snapshot i Chandy–Lamport** llogarit një gjendje globale të qëndrueshme pa ndalur ekzekutimin dhe pa orë globale, duke përhapur **mesazhe shënues** sipas dy rregullave: rregulli i marrjes (regjistro gjendjen e vet në shënuesin e parë; regjistro gjendjen e kanaleve si mesazhet e ardhura ndërmjet regjistrimit të gjendjes dhe mbërritjes së shënuesit përkatës) dhe rregulli i dërgimit (dërgo shënues mbi të gjitha kanalet dalëse, para çdo mesazhi tjetër, menjëherë pas regjistrimit të gjendjes).

## Pyetje kontrolluese

1. Pse sinkronizimi absolut i orëve në një sistem të shpërndarë konsiderohet teorikisht i pamundur?
2. Përshkruani hapat e metodës së Cristian-it dhe shpjegoni pse rezultati $t + RTT/2$ mbetet vetëm një vlerësim, jo një vlerë e saktë.
3. Cilat janë përparësitë e NTP-së ndaj metodës së thjeshtë të Cristian-it, dhe si e realizon NTP shkallëzueshmërinë përmes strukturës së saj hierarkike (strata)?
4. Shpjegoni relacionin "ndodh-para" (happens-before) përmes tri rregullave HB1–HB3, dhe jepni një shembull ku dy ngjarje janë njëkohshme (concurrent).
5. Si përditësohet ora logjike e Lamportit kur një proces (a) kryen një ngjarje lokale, (b) dërgon një mesazh, (c) pranon një mesazh?
6. Çfarë dallon një **prerje të qëndrueshme** nga një **prerje e paqëndrueshme** e historisë globale të një sistemi të shpërndarë? Jepni një shembull konkret të secilës.
7. Shpjegoni me fjalët tuaja rregullin e dërgimit dhe rregullin e marrjes së shënuesit në algoritmin e Chandy–Lamport, dhe tregoni pse zbatimi i tyre garanton që gjendja globale e prodhuar është gjithmonë e qëndrueshme.
8. Për cilat probleme praktike (jepni së paku tre) mund të përdoret algoritmi snapshot i Chandy–Lamport?
