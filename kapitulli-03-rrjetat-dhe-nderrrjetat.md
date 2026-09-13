# Kapitulli 3 — Rrjetat dhe Ndërrrjetat në Sistemet e Shpërndara

## Hyrje

Një sistem i shpërndarë nuk mund të ekzistojë pa një rrjet që lidh pjesët e tij. Kompjuterët, serverët dhe pajisjet që marrin pjesë në një sistem të shpërndarë janë fizikisht të ndarë — mund të ndodhen në të njëjtën zyrë ose në kontinente të ndryshme — por për përdoruesin duhet të duken sikur punojnë si një e tërë e vetme, koherente. Ky "iluzion i njësisë" mundësohet nga rrjeti kompjuterik: infrastruktura që lejon shkëmbimin e të dhënave dhe koordinimin ndërmjet nyjeve (nodes).

Ky kapitull shqyrton dy koncepte themelore:

- **Rrjeti (network)** — një grup pajisjesh (kompjuterë, serverë, pajisje mobile) që lidhen mes tyre për të shkëmbyer të dhëna dhe burime brenda një zone të caktuar.
- **Ndërrrjeti (internetwork)** — një bashkim i shumë rrjeteve të ndryshme, të lidhura ndërmjet tyre përmes pajisjeve si routerët, në mënyrë që të sillen si një rrjet i vetëm, më i madh.

Pa rrjetat dhe ndërrrjetat, nuk do të ishte e mundur të flisnim fare për "sisteme të shpërndara" — do të kishim thjesht kompjuterë të izoluar.

## Roli i rrjeteve dhe ndërrrjetave në sistemet e shpërndara

### Llojet themelore të rrjeteve sipas shtrirjes gjeografike

Rrjetat klasifikohen zakonisht sipas madhësisë së zonës që mbulojnë:

| Lloji i rrjetit | Emërtimi i plotë | Shtrirja tipike | Shembull |
|---|---|---|---|
| **LAN** | Local Area Network (rrjet lokal) | Një ndërtesë, zyrë ose fakultet | Rrjeti kompjuterik i një salle laboratori |
| **MAN** | Metropolitan Area Network (rrjet metropolitan) | Një qytet ose zonë urbane | Rrjeti që lidh disa ndërtesa universitare nëpër qytet |
| **WAN** | Wide Area Network (rrjet i gjerë) | Rajone, vende, kontinente | Interneti, rrjeti i një kompanie shumëkombëshe |

Këto tre nivele nuk janë thjesht kategori teorike — ato pasqyrojnë kompromise reale midis distancës, shpejtësisë dhe besueshmërisë, siç do ta shohim më poshtë kur diskutojmë performancën.

### Nga rrjeti tek ndërrrjeti

Kur disa rrjete të veçanta (p.sh. LAN-i i një fakulteti, LAN-i i një ndërmarrjeje, dhe rrjete të tjera lokale kudo në botë) lidhen mes tyre përmes **routerëve**, formohet një **ndërrrjet (internetwork)**. Shembulli më i qartë dhe më i njohur i një ndërrrjeti është vetë **Interneti** — një rrjet global i përbërë nga miliona rrjete më të vogla, secili me teknologjinë, administrimin dhe pronësinë e vet, por të gjitha të lidhura sipas një grupi të përbashkët protokollesh (familja TCP/IP).

Pika kyçe këtu është se një ndërrrjet nuk kërkon që të gjitha rrjetet përbërëse të përdorin të njëjtën teknologji fizike apo lidhjeje (Ethernet, WiFi, fibra optike etj.) — routerët dhe protokollet e përbashkëta e bëjnë të mundur që rrjete heterogjene të komunikojnë sikur të ishin një sistem i vetëm.

### Si i shërbejnë rrjetet sistemeve të shpërndara

Në një sistem të shpërndarë, shumë kompjuterë (nyje) punojnë së bashku si një sistem i vetëm logjik. Rrjeti është "shtylla kurrizore" që e bën këtë koordinim të mundur, duke ofruar:

- **Komunikim ndërmjet nyjeve** — shkëmbimin e mesazheve dhe kërkesave (requests) ndërmjet proceseve që gjenden në makina të ndryshme.
- **Shpërndarje të të dhënave** — kopjimin, sinkronizimin ose ndarjen (partitioning) e të dhënave nëpër nyje të ndryshme, që mund të jenë gjeografikisht larg njëra-tjetrës.
- **Balancim të ngarkesës (load balancing)** — shpërndarjen e kërkesave ndërmjet disa serverëve, në mënyrë që asnjë nyje e vetme të mos mbingarkohet.
- **Sinkronizim të proceseve** — koordinimin në kohë ndërmjet proceseve që ekzekutohen paralelisht në nyje të ndryshme (p.sh. për të ruajtur rregull, konsistencë të të dhënave apo për të shmangur konfliktet).

Ndërrrjetat, nga ana tjetër, e çojnë këtë koordinim përtej kufijve të një organizate ose vendi të vetëm. Rëndësia e tyre qëndron në faktin se:

- Lejojnë ndërtimin e sistemeve të shpërndara në **shkallë globale**, jo vetëm brenda një zyre a kampusi.
- Lidhin rrjete me **teknologji të ndryshme** (p.sh. rrjete me kabllo dhe rrjete pa tela, ose rrjete me protokolle të vjetra dhe të reja) duke i bërë ato të bashkëveprojnë.
- Mundësojnë **komunikim ndër-platformë dhe ndër-organizata**, gjë thelbësore për aplikacionet moderne.

**Shembull praktik:** Aplikacione si Google apo Facebook kanë serverët e tyre të shpërndarë në qendra të dhënash (data centers) në vende të ndryshme të botës. Kur një përdorues hap faqen, kërkesa e tij udhëton nëpër Internet (ndërrrjetin global) deri tek serveri më i afërt ose më i përshtatshëm, dhe përgjigja kthehet po nëpër të njëjtën infrastrukturë. Pra, sistemet e shpërndara funksionojnë falë kombinimit të **rrjeteve lokale** (brenda qendrës së të dhënave, ku shpejtësia është kritike) dhe **ndërrrjeteve globale** (Interneti, që lidh qendrat e të dhënave me përdoruesit anembanë botës).

## Performanca e rrjeteve dhe ndërrrjetave

### Faktorët që përcaktojnë performancën

Performanca e një rrjeti tregon se sa mirë ai është në gjendje të transmetojë të dhëna në mënyrë **të shpejtë, efikase dhe të besueshme** ndërmjet nyjeve që bashkëpunojnë brenda një sistemi të shpërndarë. Tre faktorët kryesorë që e përcaktojnë këtë performancë janë:

1. **Shtrirja (Range)** — distanca fizike maksimale që mund të mbulojë rrjeti pa humbje të konsiderueshme sinjali apo pa nevojën për pajisje ndërmjetësuese shtesë.
2. **Bandwidth (gjerësia e brezit)** — shpejtësia me të cilën mund të transferohen të dhënat, zakonisht e matur në Mbps (megabit/sekondë) ose Gbps.
3. **Vonesa (Latency)** — koha që i duhet një pakete të dhënash për të udhëtuar nga burimi tek destinacioni; sa më e vogël, aq më "reagues" (responsive) është komunikimi.

Në përgjithësi ekziston një kompromis natyror: rrjetet me shtrirje më të vogël (LAN) tentojnë të kenë shpejtësi më të lartë dhe vonesë më të ulët, ndërsa rrjetet me shtrirje të madhe (WAN) sakrifikojnë vonesën dhe në një farë mase shpejtësinë, në këmbim të mbulimit gjeografik shumë më të gjerë:

- **LAN** — shpejtësi e lartë, distancë e vogël.
- **MAN** — shpejtësi dhe distancë mesatare.
- **WAN** — distancë e madhe, por vonesë më e lartë.
- **Wireless (pa tel)** — ofron fleksibilitet (lëvizshmëri, instalim i thjeshtë), por zakonisht është më pak stabil se rrjetet me kabllo, për shkak të ndërhyrjeve, humbjes së sinjalit dhe ndryshueshmërisë së mjedisit.

### Rrjetat me kabllo (Wired)

Tabela e mëposhtme përmbledh vlerat tipike të shtrirjes, bandwidth-it dhe vonesës për llojet kryesore të rrjeteve me kabllo, sipas teknologjisë përkatëse:

| Lloji i rrjetit | Teknologjia | Shtrirja | Bandwidth | Vonesa |
|---|---|---|---|---|
| **LAN** | Ethernet | 1–2 km | 10–10.000 Mbps | 1–10 ms |
| **MAN** | ATM | 2–50 km | 1–600 Mbps | ~10 ms |
| **WAN** | IP routing | Globale | 0,01–600 Mbps | 100–500 ms |
| **Ndërrrjeti (Internet)** | — | Globale | 0,5–600 Mbps | 100–500 ms |

Vihet re se **LAN-i** ofron shpejtësinë më të lartë relative dhe vonesën më të ulët, sepse distanca fizike që duhet të përshkojë sinjali është e vogël dhe numri i pajisjeve ndërmjetësuese (switch-e, routerë) është i kufizuar. **WAN-i** dhe **Interneti**, përkundrazi, mbulojnë distanca globale, gjë që rrit në mënyrë të pashmangshme vonesën (sinjali duhet të kalojë nëpër shumë routerë dhe linke të ndërmjetme, ndonjëherë duke përshkuar mijëra kilometra fibre optike ose lidhje satelitore).

### Rrjetat pa tela (Wireless)

Rrjetat pa tela klasifikohen po sipas shtrirjes, por me emërtime paralele që fillojnë me "W" (Wireless):

| Lloji i rrjetit | Teknologjia | Shtrirja | Bandwidth | Vonesa |
|---|---|---|---|---|
| **WPAN** (rrjet personal) | Bluetooth | 10–30 m | 0,5–2 Mbps | 5–20 ms |
| **WLAN** (rrjet lokal) | WiFi | 0,15–1,5 km | 11–108 Mbps | 5–20 ms |
| **WMAN** (rrjet metropolitan) | WiMAX | 5–50 km | 1,5–20 Mbps | 5–20 ms |
| **WWAN** (rrjet i gjerë) | 3G | Qeliza: 1–5 km | 0,348–14,4 Mbps | 100–500 ms |

Krahasuar me homologët e tyre me kabllo, rrjetet pa tela ofrojnë të njëjtën hierarki logjike (personal → lokal → metropolitan → i gjerë), por me disa dallime të rëndësishme: **WPAN** (p.sh. Bluetooth) shërben për lidhje shumë afër (p.sh. midis telefonit dhe kufjeve), **WLAN** (WiFi) mbulon një ndërtesë ose shtëpi, **WMAN** (WiMAX) mund të mbulojë një qytet të tërë pa nevojën për kabllo fizike deri te çdo shtëpi, ndërsa **WWAN** (rrjetet celulare si 3G) lejojnë lëvizshmëri të plotë në një zonë shumë të gjerë, por me vonesë të krahasueshme me WAN-in, sepse sinjali kalon nëpër infrastrukturën e operatorit celular deri te rrjeti kryesor.

## Shtresimi konceptual i produkteve softuerike në rrjete dhe ndërrrjete

### Çfarë është shtresimi (layering)

**Shtresimi konceptual** nënkupton organizimin e sistemeve softuerike në **shtresa (layers)**, ku secila shtresë ka një rol të mirëpërcaktuar dhe komunikon me shtresat e tjera përmes **ndërfaqeve (interfaces)** standarde. Në thelb, shtresimi është një model organizimi që ndan funksionalitetin e një sistemi të shpërndarë në nivele të ndryshme, me qëllim që të thjeshtojë zhvillimin, mirëmbajtjen dhe komunikimin ndërmjet komponentëve.

Ideja kryesore është që çdo shtresë **t'i fshehë detajet e brendshme** shtresave më sipër, duke i ofruar asaj vetëm një ndërfaqe të thjeshtë dhe të qëndrueshme. Kështu, një ndryshim në mënyrën se si funksionon një shtresë e caktuar (p.sh. kalimi nga një teknologji rrjeti në tjetrën) nuk detyron ndryshimin e shtresave të tjera, për sa kohë ndërfaqja midis tyre mbetet e njëjtë.

### Qëllimet e shtresimit

Shtresimi ndiqet sepse sjell përfitime konkrete në projektimin e sistemeve komplekse si ato të shpërndara:

- **Modularitet** — ndarje e qartë e funksioneve, secila e izoluar në shtresën e vet.
- **Lehtësi në zhvillim dhe mirëmbajtje** — programuesit mund të punojnë në një shtresë pa pasur nevojë të kuptojnë çdo detaj të shtresave të tjera.
- **Ripërdorim i komponentëve** — një shtresë e njëjtë (p.sh. shtresa e rrjetit) mund të përdoret nga shumë aplikacione të ndryshme.
- **Pavarësi midis shtresave** — ndryshimet në një shtresë nuk kërkojnë rishkrimin e shtresave fqinje, për sa kohë ndërfaqja respektohet.
- **Shkallëzim më i lehtë (scalability)** — sistemi mund të rritet ose përshtatet më lehtë kur përgjegjësitë janë të ndara qartë.

### Shtresat kryesore në sistemet e shpërndara (nga poshtë lart)

Në kontekstin e sistemeve të shpërndara, softueri organizohet zakonisht në katër shtresa konceptuale kryesore:

1. **Shtresa e rrjetit (Network Layer)**
   - Merret me transmetimin fizik dhe logjik të të dhënave.
   - Bazohet në protokolle si TCP/IP dhe algoritme routing-u.
   - Siguron lidhjen bazë ndërmjet nyjeve.

2. **Shtresa e komunikimit / middleware**
   - Ndërmjetëson komunikimin midis aplikacioneve që gjenden në makina të ndryshme.
   - Ofron shërbime si **RPC (Remote Procedure Call)** dhe **mesazheria (messaging)**.
   - Roli i saj kryesor është që të **fshehë kompleksitetin e rrjetit** nga zhvilluesi i aplikacionit — programuesi thërret një funksion "sikur" të ishte lokal, ndërkohë që middleware-i kujdeset për transmetimin real nëpër rrjet.

3. **Shtresa e sistemit (Operating System / Platform)**
   - Menaxhon burimet themelore: CPU, memorie, procese.
   - Ofron bazën mbi të cilën ndërtohen dhe ekzekutohen aplikacionet e shpërndara.

4. **Shtresa e aplikacionit (Application Layer)**
   - Përmban aplikacionet konkrete që përdor drejtpërdrejt përdoruesi.
   - Shembuj: aplikacione web, sisteme cloud, shërbime online.

Kjo hierarki tregon qartë se sa më lart të shkojmë, aq më afër jemi me përdoruesin dhe logjikën e biznesit; sa më poshtë, aq më afër jemi me transmetimin fizik të biteve nëpër media.

## Enkapsulimi në protokolet e shtresuara

### Koncepti i enkapsulimit

**Enkapsulimi (encapsulation)** është procesi përmes të cilit çdo shtresë e rrjetit i shton të dhënave që vijnë nga shtresa mbi të, informacionin e vet të kontrollit, të njohur si **header** (dhe ndonjëherë edhe "trailer" në fund). Në mënyrë figurative, të dhënat "mbështillen" shtresë pas shtrese gjatë procesit të dërgimit në rrjet — çdo shtresë shton "letrën" e vet rreth atyre që merr nga shtresa më sipër, pa i ndryshuar përmbajtjen origjinale.

Ky mekanizëm mundëson:

- **Komunikim të standardizuar** midis sistemeve të ndryshme, sepse çdo shtresë ndjek një format të përbashkët headeri.
- **Modularitet** — çdo shtresë funksionon në mënyrë të pavarur, pa pasur nevojë të "dijë" se çfarë ndodh brenda shtresave të tjera.
- **Siguri dhe kontroll më të mirë** të të dhënave, sepse çdo shtresë mund të zbatojë kontrollet e veta (p.sh. kontroll gabimesh, enkriptim).
- **Ndërveprim ndër-platformë (interoperability)** — sisteme heterogjene mund të komunikojnë sepse respektojnë të njëjtat formate headeri, pavarësisht se çfarë harduerësh apo sistemesh operative kanë nën kapuç.

### Shembull praktik: hapja e një faqeje web

Kur një përdorues hap një faqe web, ndodh zinxhiri i mëposhtëm i enkapsulimit:

1. Aplikacioni (shfletuesi) dërgon një **kërkesë HTTP**.
2. **TCP** i shton kësaj kërkese portet (burimi dhe destinacioni), duke formuar një **segment**.
3. **IP** i shton adresat IP të burimit dhe destinacionit, duke formuar një **paketë**.
4. **Ethernet** (shtresa e lidhjes së të dhënave) i shton adresat **MAC**, duke formuar një **frame**.

Të gjitha këto shtresa, së bashku, formojnë paketën përfundimtare që udhëton fizikisht nëpër rrjet.

### Procesi hap pas hapi i enkapsulimit dhe dekapsulimit

Kur një mesazh dërgohet në një sistem të shpërndarë, secila shtresë kryen një veprim specifik:

| Shtresa | Veprimi | Rezultati |
|---|---|---|
| **Shtresa e aplikacionit** | Krijon të dhënat (p.sh. kërkesë HTTP) | Të dhëna (data) |
| **Shtresa e transportit** | Shton header TCP/UDP | Segment |
| **Shtresa e rrjetit** | Shton adresat IP | Paketë (packet) |
| **Shtresa e lidhjes (Data Link)** | Shton adresat MAC | Frame |
| **Shtresa fizike** | Dërgon bitët në medium | Sinjal fizik (bite) |

Procesi i kundërt quhet **dekapsulim (decapsulation)**: kur mesazhi arrin te marrësi, çdo shtresë heq header-in që i përket asaj, duke ia dorëzuar pjesën tjetër të "paketuar" shtresës mbi vete, derisa të dhënat e pastra arrijnë deri te aplikacioni në destinacion. Në këtë mënyrë, procesi i enkapsulimit/dekapsulimit është plotësisht simetrik ndërmjet dërguesit dhe marrësit.

## Modeli OSI (Open Systems Interconnection)

### Struktura me 7 shtresa

**Modeli OSI** është një strukturë konceptuale me **7 nivele (shtresa)**, që përshkruan mënyrën se si të dhënat transmetohen dhe përpunohen në rrjet. Qëllimi i tij është të ndajë funksionet e komunikimit në shtresa të veçanta, duke mundësuar **standardizim** dhe **ndërveprim** midis sistemeve dhe prodhuesve të ndryshëm.

Shtresat e modelit OSI, nga poshtë lart, janë:

1. **Fizike (Physical)** — transmeton bitët (0 dhe 1) drejtpërdrejt nëpër medium (kabllo bakri, fibër optike, valë radio).
2. **Lidhja e të dhënave (Data Link)** — organizon bitët në **frame** dhe përdor adresat **MAC** për komunikim brenda të njëjtit segment fizik; kujdeset edhe për zbulimin (dhe ndonjëherë korrigjimin) e gabimeve.
3. **Rrjeti (Network)** — përgjegjëse për **adresimin logjik dhe routing-un**; protokolli kryesor këtu është **IP**.
4. **Transporti (Transport)** — siguron **komunikim të besueshëm** (ose jo) ndërmjet aplikacioneve fundore; protokollet **TCP** (i besueshëm) dhe **UDP** (i shpejtë, pa garanci) veprojnë në këtë shtresë.
5. **Seanca (Session)** — menaxhon hapjen, mbajtjen dhe mbylljen e lidhjeve (sesioneve) ndërmjet dy aplikacioneve.
6. **Prezantimi (Presentation)** — merret me formatin e të dhënave, konvertimin e tyre (p.sh. kodimin e karaktereve) dhe enkriptimin/dekriptimin.
7. **Aplikacioni (Application)** — shtresa më e lartë, ku ndodh ndërveprimi direkt me përdoruesin, përmes protokolleve si **HTTP** dhe **FTP**.

Vlen të theksohet se modeli OSI është kryesisht një **model referimi teorik**, i vlefshëm për të kuptuar dhe klasifikuar funksionet e komunikimit, ndërsa në praktikë, rrjetet reale (dhe në veçanti Interneti) mbështeten më shpesh te modeli më i thjeshtuar **TCP/IP** (i diskutuar më poshtë), i cili grupon disa nga këto shtresa së bashku.

### Shtrirja e aplikimeve të shpërndara mbi modelin OSI

Aplikacionet e shpërndara nuk përdorin domosdoshmërisht të gjitha 7 shtresat në mënyrë eksplicite — shumica e logjikës së aplikacionit dhe middleware-it "ulet" mbi shtresën e transportit (TCP/UDP), duke përdorur shërbimet e saj për të realizuar komunikim të besueshëm ose të shpejtë, pa iu dashur të merren vetë me detajet e adresimit fizik apo transmetimit të biteve. Shtresat më të ulëta (fizike, lidhje të dhënash, rrjet) mbeten "të padukshme" për zhvilluesin e aplikacionit, ashtu si parashikon vetë parimi i shtresimit dhe i enkapsulimit të diskutuar më sipër.

### Shtresat e rrjetave të ndërlidhura (Internetwork Layers)

Kur disa rrjete lidhen për të formuar një ndërrrjet, funksionimi mbështetet po në po të njëjtin parim shtresimi, por me theks të veçantë te **shtresa e rrjetit**, e cila është ajo që lejon **routing-un ndërmjet rrjeteve të ndryshme**. Routerët veprojnë pikërisht në këtë shtresë: ata lexojnë adresat IP të paketave dhe vendosin nëpër cilin link duhet të përcillet secila paketë, në mënyrë që të arrijë te destinacioni i saj përmes potencialisht shumë rrjeteve të ndërmjetme.

## Rrugëtimi (Routing) në WAN

### Pse nevojitet routing

Në një rrjet të gjerë (WAN) apo në Internet, nuk ekziston një lidhje fizike direkte ndërmjet çdo çifti nyjesh — do të ishte teknikisht e pamundur dhe ekonomikisht joracionale. Në vend të kësaj, paketat udhëtojnë nga një router në tjetrin, secili prej të cilëve merr një **vendim lokal** se ku duhet ta dërgojë më tej paketën, bazuar në një **tabelë routing-u**. Qëllimi përfundimtar është që paketa të arrijë te destinacioni i saj duke ndjekur rrugën më të mirë të mundshme (zakonisht sipas kostos, numrit të "hopave", ose vonesës).

### Algoritmi RIP (Routing Information Protocol)

Një nga algoritmet klasike të routing-ut, i përdorur si shembull mësimor, është **RIP**. Ai bazohet në parimin e **vektorit të distancës (distance-vector)**: çdo router mban një tabelë routing-u lokale (Tl) me distancat (kostot) e njohura drejt destinacioneve të ndryshme, dhe periodikisht e shkëmben këtë tabelë me fqinjët e tij të drejtpërdrejtë.

Logjika e algoritmit mund të përmblidhet si më poshtë:

- **Dërgimi:** Çdo `t` sekonda (ose kur tabela lokale `Tl` ndryshon), routeri e dërgon tabelën e tij `Tl` në çdo link dalës që funksionon (pa defekt).
- **Pranimi:** Kur një tabelë routing-u `Tr` pranohet në linkun `n`, routeri e përpunon atë si vijon:
  - Për çdo rresht `Rr` në `Tr`:
    - Nëse ai rresht lidhet përmes linkut `n`, kostoja e tij rritet me 1 (`Rr.kosto = Rr.kosto + 1`) dhe linku i tij caktohet si `n`.
    - Nëse destinacioni i `Rr` nuk gjendet ende në tabelën lokale `Tl`, shtohet si një rresht i ri në `Tl`.
    - Përndryshe (nëse destinacioni ekziston tashmë në `Tl` si rreshti `Rl`), krahasohen dy kushte:
      - Nëse kostoja e re (`Rr.kosto`) është më e vogël se kostoja ekzistuese (`Rl.kosto`) — do të thotë se nyja e largët ka gjetur një rrugë më të mirë — atëherë `Rl` zëvendësohet me `Rr`.
      - Ose nëse `Rl.link` është vetë `n` — do të thotë se informacioni po vjen nga i njëjti fqinj prej të cilit e kishim mësuar më parë këtë rrugë, pra është burimi "më i besueshëm" për këtë destinacion — atëherë përsëri `Rl` përditësohet me `Rr`.

Në thelb, RIP mëson gradualisht rrugët më të mira drejt çdo destinacioni duke përhapur (propagate) informacionin e distancave ndërmjet routerëve fqinjë, derisa e gjithë rrjeti "konvergon" në një gjendje ku çdo router njeh rrugën optimale (ose të paktën një rrugë të vlefshme) drejt çdo destinacioni tjetër.

### Rrjeti i një kampusi universitar

Si shembull praktik i një ndërrrjeti të vogël e real, mund të marrim rrjetin e një kampusi universitar. Një rrjet i tillë zakonisht organizohet hierarkikisht: çdo ndërtesë (fakultet, bibliotekë, administratë) ka LAN-in e vet lokal (p.sh. bazuar në Ethernet ose WiFi), i cili lidhet përmes switch-eve dhe routerëve në një **backbone** qendror të kampusit; ky backbone, nga ana e tij, lidhet me Internetin përmes një ose disa **gateway-eve** (portave dalëse) dhe routerëve kufitarë. Kjo strukturë hierarkike (nyje fundore → LAN lokal → backbone i kampusit → Internet) është tipike për shumicën e organizatave të mëdha, dhe ilustron në praktikë si funksionon kalimi nga një rrjet i vetëm LAN drejt një ndërrrjeti më të gjerë.

## Protokolli IP dhe evolucioni drejt IPv6

### Struktura e headerit IPv6

Ashtu si IPv4, edhe **IPv6** përdor një header standard që përmban informacionin e nevojshëm për drejtimin (routing) e paketës, por me një format të thjeshtuar dhe të zgjeruar për të përballuar nevojat e rrjeteve moderne. Ndryshimet kryesore krahasuar me IPv4 janë:

- **Hapësira e adresave** rritet drastikisht: nga 32 bit (IPv4, ~4,3 miliardë adresa) në **128 bit** (IPv6), duke ofruar praktikisht një numër të pashtershëm adresash — i nevojshëm për shkak të rritjes eksponenciale të pajisjeve të lidhura në Internet (telefona, sensorë IoT, pajisje "smart" etj.).
- Header-i i IPv6 është më i thjeshtë dhe me gjatësi fikse, gjë që përshpejton përpunimin nga routerët (disa fusha opsionale të IPv4 zëvendësohen me "extension headers" të veçanta, të përdorura vetëm kur nevojiten).

### Tunneling për migrimin nga IPv4 në IPv6

Meqenëse jo të gjitha rrjetet e botës u përditësuan menjëherë nga IPv4 në IPv6, u desh një mekanizëm që të lejonte **bashkëjetesën (coexistence)** e të dy protokolleve gjatë periudhës kalimtare. Ky mekanizëm quhet **tunneling**.

**Tunneling për migrimin e IPv6** është teknika që lejon transmetimin e paketave IPv6 përmes rrjeteve që mbështesin ende vetëm IPv4, duke i **enkapsuluar (mbështjellë)** paketat IPv6 brenda paketave IPv4. Kjo lehtëson migrimin gradual drejt IPv6, pa detyruar ndryshimin e menjëhershëm të gjithë infrastrukturës ekzistuese.

Procesi funksionon si vijon:

1. Një aplikacion krijon një paketë IPv6.
2. Kjo paketë enkapsulohet brenda një pakete IPv4.
3. Paketa e "mbështjellë" dërgohet përmes rrjetit IPv4, duke vepruar si një lloj "tuneli virtual".
4. Në destinacion, paketa dekapsulohet dhe të dhënat origjinale IPv6 rikthehen në formën e tyre fillestare.

Skematikisht: `IPv6 → (enkapsulim) → IPv4 → (transport nëpër rrjetin IPv4) → dekapsulim → IPv6`.

Arsyet pse nevojitet tunneling gjatë kalimit nga IPv4 në IPv6 janë kryesisht dy:

- Jo të gjitha rrjetet aktuale mbështesin ende drejtpërdrejt IPv6.
- Nevojitet domosdoshmërisht një mënyrë për **bashkëjetesë (coexistence)** midis dy protokolleve, pa detyruar një ndërprerje të papritur të shërbimeve ekzistuese.

### Llojet kryesore të tunneling-ut

| Lloji | Karakteristika kryesore |
|---|---|
| **6to4** | Tunneling automatik; përdor vetë adresën IPv4 për të gjeneruar një adresë IPv6 përkatëse |
| **ISATAP** (Intra-Site Automatic Tunnel Addressing Protocol) | Përdoret zakonisht brenda një organizate të vetme, për komunikim intern |
| **Teredo** | Lejon lidhshmëri IPv6 edhe pas një pajisjeje **NAT** — e dobishme sidomos në rrjete shtëpiake |
| **Manual Tunneling** | Konfigurohet manualisht nga administratori i rrjetit, kur kërkohet kontroll i plotë mbi konfigurimin |

Tunneling-u, në kontekstin e sistemeve të shpërndara, mundëson lidhjen midis dy rrjeteve IPv6 përmes një rrjeti IPv4 ndërmjetës, si dhe një migrim gradual pa qenë nevoja që e gjithë infrastruktura të ndryshojë menjëherë. Gjithashtu ai lejon:

- **Komunikim ndërmjet nyjeve heterogjene**, që përdorin versione të ndryshme të IP-së (IPv4 dhe IPv6 njëkohësisht).
- **Integrim të sistemeve të vjetra (legacy) me sisteme të reja**, pa pasur nevojë t'i zëvendësosh menjëherë ato të vjetrat.
- **Vazhdimësi të shërbimeve** pa ndërprerje gjatë procesit gradual të migrimit.

Megjithatë, tunneling-u ka edhe **kufizime** që duhen mbajtur parasysh:

- Mund të **rrisë vonesën (latency)**, sepse çdo paketë kalon nëpër një hap shtesë enkapsulimi/dekapsulimi.
- Mund të krijojë **sfida sigurie** shtesë (p.sh. mundësinë e anashkalimit të firewall-eve që nuk e njohin trafikun e tunelizuar).
- Sjell një **konfigurim më kompleks** të infrastrukturës së rrjetit.

## Modeli TCP/IP në sistemet e shpërndara

### Katër shtresat e modelit TCP/IP

Ndërsa modeli OSI ka 7 shtresa teorike, në praktikë Interneti dhe shumica e sistemeve reale funksionojnë sipas modelit më të thjeshtuar **TCP/IP**, i cili grupon funksionet e komunikimit në vetëm **4 shtresa**:

1. **Shtresa e Aplikacionit (Application Layer)**
2. **Shtresa e Transportit (Transport Layer)**
3. **Shtresa e Internetit (Internet Layer)**
4. **Shtresa e Aksesit në Rrjet (Network Access / Link Layer)**

Këto katër shtresa bashkëpunojnë për të realizuar transmetimin e të dhënave në rrjet dhe në sistemet e shpërndara, duke mundësuar komunikim ndërmjet pajisjeve të ndryshme përmes Internetit. Le t'i shohim një nga një.

### 1. Shtresa e Aplikacionit

Kjo shtresë ndërvepron direkt me përdoruesin (ose me aplikacionin fundor) dhe ofron shërbime konkrete përmes protokolleve specifike, si:

- **HTTP** — për shfletimin e faqeve web.
- **FTP** — për transferimin e skedarëve.
- **SMTP** — për dërgimin e postës elektronike (email).

Në kontekstin e sistemeve të shpërndara, kjo shtresë realizon vetë **logjikën e aplikacionit** — pra atë çka aplikacioni "dëshiron të bëjë" (p.sh. të kërkojë një faqe, të ngarkojë një skedar, etj.).

### 2. Shtresa e Transportit

Shtresa e transportit siguron komunikim ndërmjet **proceseve** (jo vetëm ndërmjet makinave). Dy protokollet kryesore këtu janë:

- **TCP (Transmission Control Protocol)** — i besueshëm, ofron kontroll gabimesh dhe garanton renditjen e saktë të të dhënave të dërguara; i përshtatshëm kur korrektësia e plotë e të dhënave është prioritet (p.sh. transferim skedarësh, faqe web).
- **UDP (User Datagram Protocol)** — më i shpejtë, por pa garanci dorëzimi apo renditjeje; i përshtatshëm për aplikacione ku shpejtësia dhe kohë-reagimi (p.sh. transmetim video/audio në kohë reale, lojëra online) janë më të rëndësishme se garancia e plotë e dorëzimit.

Në sistemet e shpërndara, kjo shtresë menaxhon lidhjet dhe siguron dërgimin korrekt (ose sa më efikas, sipas rastit) të të dhënave ndërmjet proceseve që bashkëpunojnë.

### 3. Shtresa e Internetit

Kjo shtresë merret me **adresimin** dhe **routing-un** e paketave. Protokolli kryesor këtu është **IP** (në të dyja variantet e tij, **IPv4** dhe **IPv6**), i cili siguron dërgimin e paketave nga burimi deri te destinacioni, pavarësisht se sa router të ndërmjetëm duhet të kalohen.

### 4. Shtresa e Aksesit në Rrjet (Network Access / Link Layer)

Kjo shtresë përfaqëson lidhjen fizike dhe logjike direkte me mediumin e rrjetit. Përfshin teknologji si:

- **Ethernet** (për rrjete me kabllo).
- **WiFi** (për rrjete pa tela).

Roli i saj është të konvertojë paketat në sinjale fizike që mund të transmetohen nëpër mediumin konkret (kabllo bakri, fibër optike, valë radio).

### Si funksionojnë së bashku katër shtresat

Kur një aplikacion dërgon të dhëna, procesi ndjek këtë sekuencë logjike:

1. **Aplikacioni** krijon mesazhin që dëshiron të dërgojë.
2. **Transporti** e ndan mesazhin në segmente (nëse është e nevojshme) dhe e kontrollon (p.sh. me TCP).
3. **Interneti** i shton adresimin (IP) dhe e dërgon paketën drejt destinacionit.
4. **Link Layer** e transmeton fizikisht paketën si sinjal elektrik, optik ose radio.

### Rëndësia e modelit TCP/IP në sistemet e shpërndara

Modeli TCP/IP është themeli praktik mbi të cilin funksionon pothuajse çdo sistem i shpërndarë modern, sepse:

- Mundëson **komunikim global** përmes Internetit.
- Mbështet **shkëmbimin e të dhënave** ndërmjet nyjeve, pavarësisht distancës apo teknologjisë së tyre lokale.
- Siguron **modularitet dhe fleksibilitet**, falë ndarjes së qartë në shtresa (i njëjti parim i shtresimit të diskutuar më herët).
- Shërben si **bazë** për arkitekturat moderne të tilla si cloud computing, aplikacione web dhe mikroshërbime (microservices).

### Enkapsulimi konkret: TCP mbi Ethernet

Si rast konkret i enkapsulimit të diskutuar më herët, kur një aplikacion dërgon të dhëna përmes TCP-së mbi një rrjet Ethernet, të dhënat "mbështillen" në disa shtresa protokollesh (TCP/IP dhe Ethernet), duke shtuar informacione kontrolli (headers) në çdo nivel. Kjo mundëson transmetim të saktë dhe të besueshëm në rrjet — çdo shtresë e di saktësisht se çfarë duhet të bëjë me pjesën e vet të headerit, pa u ndikuar nga përmbajtja aktuale e të dhënave brenda.

### Pikëpamja e programuesit për TCP/IP dhe sistemet e shpërndara

Nga këndvështrimi i një zhvilluesi softueri që ndërton një aplikacion të shpërndarë, shumica e detajeve të rrjetit (adresimi IP, routing-u, transmetimi fizik) janë të fshehura pas ndërfaqeve standarde (soketa/sockets, API-të e rrjetit). Programuesi zakonisht punon vetëm në nivelin e shtresës së transportit e sipër (p.sh. hap një lidhje TCP, dërgon dhe pret të dhëna), ndërkohë që sistemi operativ dhe pajisjet e rrjetit kujdesen automatikisht për enkapsulimin, routing-un dhe dorëzimin fizik. Ky abstragim është pikërisht ai që e bën shtresimin kaq të vlefshëm në praktikë: zhvilluesi mund të ndërtojë aplikacione komplekse të shpërndara pa qenë nevoja të njohë çdo detaj të infrastrukturës nënshtresore.

## Struktura e adresave IP dhe paketa IP

### Adresimi në Internet

Çdo pajisje e lidhur në Internet identifikohet nga një **adresë IP** unike (ose e paktën unike brenda kontekstit të saj të rrjetit, duke marrë parasysh edhe teknika si NAT). Kjo adresë ndahet zakonisht në fusha të caktuara bitesh, të cilat tregojnë, ndër të tjera, rrjetin dhe pajisjen specifike brenda atij rrjeti. Adresat IPv4 shkruhen tradicionalisht në **paraqitje decimale me pika (dotted decimal)** — katër numra nga 0 deri në 255, të ndarë me pika (p.sh. `192.168.1.1`), secili numër duke përfaqësuar 8 bit (1 bajt) të adresës 32-bitëshe. IPv6, siç u përmend, përdor adresa 128-bitëshe, të shkruara zakonisht në formë heksadecimale të ndarë me dy pika (p.sh. `2001:0db8::1`).

### Përbërja e paketës IP

**Paketa IP** është njësia themelore e transmetimit në shtresën e Internetit dhe përmban të dhëna që vijnë nga shtresat më të larta. Ajo përbëhet nga dy pjesë kryesore:

**1. Header-i (Koka)** — përmban informacione kontrolli të domosdoshme për drejtimin dhe përpunimin e paketës, si:

- Adresa IP e burimit dhe e destinacionit.
- Versioni i protokollit (IPv4 ose IPv6).
- Gjatësia e paketës.
- Informacion mbi fragmentimin (nëse paketa është ndarë në copa më të vogla për t'iu përshtatur mediumit të transmetimit).
- **TTL (Time To Live)** — një numër që zvogëlohet me çdo hop (kalim nëpër router) dhe që, kur arrin në zero, shkakton hedhjen (discard) e paketës — mekanizëm mbrojtës kundër "unazave" (loops) të pafundme në routing.
- Protokolli i shtresës së transportit që bartet brenda (TCP, UDP, etj.).

**2. Payload (Ngarkesa)** — përmban të dhënat reale që po transportohen, zakonisht një **segment TCP** ose një **datagram UDP**, i cili nga ana e vet përmban të dhënat aktuale të aplikacionit.

### Roli i paketës IP në sistemet e shpërndara

Paketa IP është njësia bazë që:

- Mundëson komunikimin ndërmjet nyjeve që ndodhen në rrjete të ndryshme.
- Siguron adresim unik për çdo pajisje pjesëmarrëse.
- Mbështet routing-un dhe dërgimin e të dhënave edhe në distanca shumë të mëdha.
- Lidh shtresën e transportit (ku "jetojnë" TCP dhe UDP) me shtresën fizike të rrjetit, duke bërë kështu të mundur zbatimin praktik të gjithë modelit të shtresuar të komunikimit të diskutuar më sipër.

## Rrjete shtëpiake dhe NAT

Një rast shumë i zakonshëm në praktikë është **rrjeti shtëpiak tipik**, i cili bazohet në teknikën **NAT (Network Address Translation)**. Në një rrjet të tillë, ruteri shtëpiak (router) merr nga ofruesi i internetit (ISP) **një adresë IP publike të vetme**, ndërsa të gjitha pajisjet brenda shtëpisë (kompjutera, telefona, televizorë "smart") marrin **adresa IP private** (p.sh. në gamën `192.168.x.x`), të vlefshme vetëm brenda rrjetit lokal.

Kur një pajisje brenda shtëpisë dërgon një kërkesë drejt Internetit, ruteri **përkthen (translate)** adresën private të burimit në adresën publike të vet, mban shënim (në një tabelë të brendshme) se cila kërkesë i përkiste cilës pajisje, dhe kur përgjigja kthehet nga Interneti, e drejton atë sërish tek pajisja e duhur brenda rrjetit lokal. Kjo teknikë:

- Lejon shumë pajisje ta ndajnë të njëjtën adresë IP publike, duke kursyer hapësirën e kufizuar të adresave IPv4.
- Ofron një nivel shtesë "mbrojtjeje" implicite, sepse pajisjet brenda rrjetit privat nuk janë të kapshme drejtpërdrejt nga jashtë pa u nisur kërkesa fillimisht nga brenda.
- Është pikërisht një nga arsyet pse teknika si **Teredo** (e diskutuar te tunneling-u i IPv6) janë të nevojshme — për të mundësuar lidhshmëri IPv6 edhe kur pajisja gjendet pas një NAT-i.

## Mekanizmi i rrugëtimit MobileIP

Në sistemet e shpërndara moderne, pajisjet (veçanërisht ato mobile) mund të lëvizin nga një rrjet në tjetrin gjatë kohës që janë ende aktive (p.sh. një telefon që kalon nga WiFi-i i shtëpisë tek rrjeti celular). Problemi që lind këtu është se adresa IP tradicionalisht identifikon **edhe pajisjen, edhe pozicionin e saj** në rrjet — pra, nëse pajisja ndryshon rrjetin, në parim duhet të ndryshojë edhe adresa e saj IP, gjë që do të ndërpriste çdo lidhje ekzistuese.

**MobileIP** është mekanizmi që zgjidh këtë problem, duke lejuar që një pajisje mobile të ruajë të njëjtën adresë IP "shtëpiake" (home address) pavarësisht se ku lëviz fizikisht. Në terma të përgjithshëm, ky mekanizëm funksionon përmes:

- Një **agjenti shtëpiak (home agent)**, që gjendet në rrjetin "origjinal" të pajisjes dhe që mban shënim ku ndodhet aktualisht pajisja.
- Një **agjenti të huaj (foreign agent)**, në rrjetin ku pajisja gjendet për momentin, i cili i siguron pajisjes një adresë të përkohshme ("care-of address").
- Trafiku i destinuar për adresën shtëpiake të pajisjes **tunelohet** (po, sërish përdoret ideja e tunneling-ut!) nga agjenti shtëpiak drejt vendndodhjes së re, aktuale, të pajisjes.

Kështu, aplikacionet dhe lidhjet ekzistuese mund të vazhdojnë të funksionojnë pa u ndërprerë, edhe kur pajisja fizike ndryshon rrjetin nga i cili lidhet.

## Firewall (Muri i zjarrtë)

**Firewall (muri i zjarrtë)** është një pajisje ose komponent softueri i vendosur në kufirin ndërmjet një rrjeti të brendshëm (të besuar) dhe rrjeteve të jashtme (si Interneti), me qëllim kontrollin e trafikut hyrës dhe dalës sipas rregullave të paracaktuara sigurie. Në kontekstin e sistemeve të shpërndara dhe ndërrrjeteve, firewall-et janë thelbësorë sepse:

- Filtrojnë trafikun në bazë të kritereve si adresa IP, porta, protokolli (TCP/UDP) apo drejtimi i lidhjes.
- Mund të vendosen në konfigurime të ndryshme, p.sh. si **firewall kufitar (border/perimeter)** në hyrje të një organizate, ose në kombinim me një **zonë të demilitarizuar (DMZ)**, ku vendosen serverët që duhet të jenë të arritshëm pjesërisht nga jashtë (p.sh. serverët web publikë), pa i ekspozuar drejtpërdrejt rrjetet e brendshme më të ndjeshme.
- Ndërveprojnë me teknikat e diskutuara më sipër (si tunneling-u) në mënyra që kërkojnë konfigurim të kujdesshëm — trafiku i tunelizuar mund, në disa raste, të "fshihet" nga rregullat standarde të firewall-it, duke krijuar sfida shtesë sigurie, siç u përmend edhe te kufizimet e tunneling-ut.

Firewall-et janë pra një shembull konkret se si konceptet e shtresimit dhe adresimit të diskutuara në këtë kapitull (adresa IP, porte, protokolle transporti) përdoren në praktikë për të zbatuar politika sigurie mbi trafikun që udhëton nëpër rrjete dhe ndërrrjete.

## Standardet IEEE 802 për rrjetat

Shumë nga teknologjitë e diskutuara më sipër (Ethernet, WiFi, Bluetooth etj.) standardizohen nga familja e standardeve **IEEE 802**, e cila përcakton në mënyrë të detajuar si funksionojnë shtresat fizike dhe të lidhjes së të dhënave për lloje të ndryshme rrjetesh:

| IEEE Nr. | Emri | Titulli | Referenca |
|---|---|---|---|
| 802.3 | Ethernet | CSMA/CD Networks (Ethernet) | [IEEE 1985a] |
| 802.4 | — | Token Bus Networks | [IEEE 1985b] |
| 802.5 | — | Token Ring Networks | [IEEE 1985c] |
| 802.6 | — | Metropolitan Area Networks | [IEEE 1994] |
| 802.11 | WiFi | Wireless Local Area Networks | [IEEE 1999] |
| 802.15.1 | Bluetooth | Wireless Personal Area Networks | [IEEE 2002] |
| 802.15.4 | ZigBee | Wireless Sensor Networks | [IEEE 2003] |
| 802.16 | WiMAX | Wireless Metropolitan Area Networks | [IEEE 2004a] |

Vlen të vihet re se disa nga këto standarde (si Token Bus dhe Token Ring) janë kryesisht historike sot — Ethernet (802.3) dhe WiFi (802.11) dominojnë praktikisht të gjitha rrjetet LAN moderne — por njohja e tyre ndihmon të kuptohet evoluimi i teknologjive të rrjeteve dhe pse standardizimi (IEEE) ka qenë kaq i rëndësishëm për të lejuar bashkëveprimin ndërmjet pajisjeve të prodhuesve të ndryshëm.

### Rangjet dhe shpejtësitë e Ethernet-it

Brenda vetë familjes Ethernet (802.3), kanë ekzistuar disa varietete me shpejtësi dhe distanca të ndryshme maksimale segmenti, në varësi të mediumit fizik të përdorur:

| | 10Base5 | 10BaseT | 100BaseT | 1000BaseT |
|---|---|---|---|---|
| **Shpejtësia e të dhënave** | 10 Mbps | 10 Mbps | 100 Mbps | 1000 Mbps |
| **Twisted wire (UTP)** | 100 m | 100 m | 100 m | 25 m |
| **Coaxial cable (STP)** | 500 m | 500 m | 500 m | 25 m |
| **Fibër shumë-modale (Multi-mode)** | 2000 m | 2000 m | 500 m | 500 m |
| **Fibër njëmodale (Mono-mode)** | 25.000 m | 25.000 m | 20.000 m | 2.000 m |

Nga kjo tabelë vërehet një tendencë e qartë: sa më e lartë shpejtësia e transmetimit (nga 10 Mbps deri në 1000 Mbps), aq më e vogël bëhet distanca maksimale e mbuluar nga i njëjti medium fizik — kjo ndodh sepse sinjalet me frekuencë më të lartë (të nevojshme për shpejtësi më të mëdha) degradohen më shpejt gjatë udhëtimit dhe janë më të ndjeshme ndaj zhurmës (noise) dhe interferencave. Vihet re gjithashtu se **fibra optike** (sidomos ajo njëmodale) ofron distanca shumë më të mëdha se kablloja bakri (twisted wire ose coaxial), për të njëjtën shpejtësi transmetimi — kjo është arsyeja pse fibra optike përdoret gjerësisht si medium për backbone-e dhe lidhje WAN/MAN, ndërsa kablloja bakri mbetet e mjaftueshme dhe më ekonomike për lidhjet e fundit (last-mile) brenda një LAN-i.

## Përmbledhje

Në këtë kapitull u shqyrtuan konceptet themelore që bëjnë të mundur funksionimin e sistemeve të shpërndara përmes rrjeteve dhe ndërrrjeteve:

- **Rrjetat** (LAN, MAN, WAN) lidhin pajisje brenda zonave të ndryshme gjeografike, ndërsa **ndërrrjetat** (si Interneti) lidhin shumë rrjete të ndryshme përmes routerëve, duke i bërë ato të funksionojnë si një e tërë e vetme.
- Performanca e rrjeteve varet nga **shtrirja, bandwidth-i dhe vonesa**, dhe ekziston gjithmonë një kompromis ndërmjet këtyre faktorëve — sa më e madhe shtrirja, aq më e lartë tenton të jetë vonesa.
- **Shtresimi konceptual** (network, middleware, sistem operativ, aplikacion) e ndan funksionalitetin e sistemit në nivele të pavarura, duke sjellë modularitet, ripërdorim dhe lehtësi mirëmbajtjeje.
- **Enkapsulimi** është mekanizmi konkret përmes të cilit çdo shtresë shton header-in e vet mbi të dhënat, duke krijuar zinxhirin: të dhëna → segment → paketë → frame → bite; **dekapsulimi** e kryen procesin e kundërt në marrës.
- **Modeli OSI** (7 shtresa) është modeli konceptual referues, ndërsa **modeli TCP/IP** (4 shtresa: aplikacion, transport, internet, akses në rrjet) është modeli praktik mbi të cilin funksionon Interneti real.
- **IPv6** zgjeron hapësirën e adresave përtej kufizimeve të IPv4, dhe teknikat e **tunneling-ut** (6to4, ISATAP, Teredo, manual) lejojnë bashkëjetesën dhe migrimin gradual ndërmjet IPv4 dhe IPv6.
- **Paketa IP** përbëhet nga header (informacion kontrolli) dhe payload (të dhënat reale), dhe është njësia bazë e transmetimit në shtresën e Internetit.
- Teknika si **NAT** (në rrjete shtëpiake), **MobileIP** (për lëvizshmërinë e pajisjeve) dhe **firewall-et** (për siguri) janë zbatime konkrete të koncepteve të adresimit dhe routing-ut në situata praktike të përditshme.
- Standardet **IEEE 802** (Ethernet, WiFi, Bluetooth, WiMAX etj.) standardizojnë shtresat fizike dhe të lidhjes së rrjeteve, duke lejuar bashkëveprimin ndërmjet pajisjeve të prodhuesve të ndryshëm; brenda vetë Ethernet-it, shpejtësia më e lartë vjen zakonisht në këmbim të distancës maksimale më të vogël të segmentit.

## Pyetje për vetëkontroll

1. Cili është dallimi thelbësor ndërmjet një **rrjeti (network)** dhe një **ndërrrjeti (internetwork)**? Jepni një shembull konkret për secilin.
2. Krahasoni LAN, MAN dhe WAN në aspektin e shtrirjes, bandwidth-it dhe vonesës. Pse WAN-i ka zakonisht vonesë më të lartë se LAN-i?
3. Shpjegoni konceptin e **shtresimit konceptual** dhe listoni të paktën tre përfitime që sjell ai në zhvillimin e sistemeve të shpërndara.
4. Përshkruani procesin e **enkapsulimit** duke përdorur shembullin e dërgimit të një kërkese HTTP, nga shtresa e aplikacionit deri te shtresa fizike. Si quhet procesi i kundërt dhe ku ndodh ai?
5. Cilat janë 7 shtresat e modelit OSI? Si ndryshon ky model nga modeli 4-shtresor TCP/IP?
6. Shpjegoni algoritmin RIP: si i përditëson një router tabelën e tij të routing-ut kur pranon informacion nga një fqinj?
7. Çfarë është **tunneling-u** në kontekstin e migrimit IPv4→IPv6, dhe cilat janë llojet kryesore të tij? Përmendni edhe një kufizim të tij.
8. Cilat janë dy pjesët kryesore të një pakete IP, dhe çfarë informacioni përmban secila prej tyre?
9. Si funksionon **NAT** në një rrjet shtëpiak tipik, dhe pse është i nevojshëm?
10. Përse është e nevojshme **MobileIP** dhe si e ruan ai vazhdimësinë e lidhjes kur një pajisje ndryshon rrjetin?
11. Duke u bazuar te tabela e Ethernet-it, shpjegoni pse rritja e shpejtësisë së transmetimit shoqërohet zakonisht me uljen e distancës maksimale të mbuluar nga i njëjti medium fizik.
