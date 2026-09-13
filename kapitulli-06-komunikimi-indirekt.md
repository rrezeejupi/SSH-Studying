# Kapitulli 6 — Komunikimi Indirekt në Sistemet e Shpërndara (Indirect Communication)

## Hyrje: Përmbajtja e kapitullit

Ky kapitull trajton një nga temat qendrore të sistemeve të shpërndara: si mund të komunikojnë proceset pa pasur nevojë të njohin njëri-tjetrin drejtpërdrejt. Do të mbulojmë, sipas rendit të ligjëratës:

1. Konceptin e komunikimit indirekt dhe dallimin nga komunikimi i drejtpërdrejtë.
2. Lidhjen (coupling) dhe moslidhjen (decoupling) në hapësirë dhe në kohë.
3. Komunikimin në grup (group communication): grupe të hapura/të mbyllura, të mbivendosura/jo-mbivendosura.
4. Broadcast kundrejt multicast dhe renditjen e mesazheve.
5. Veglat (toolkits) për komunikim indirekt: JGroups, Akka, Spread.
6. Sistemet sinkrone dhe asinkrone, si dhe menaxhimin e anëtarësisë së grupit.
7. Arkitekturën publiko-abono (publish/subscribe): rrjeta e brokerave, rrugëtimi i bazuar në filtrim, shembuj realë (JMS).
8. Sistemet me mesazhe radhazi (message queues) dhe AMQP.
9. Abstraktimin e memories së ndarë: hapësira e qifteve (tuple space) dhe JavaSpaces.
10. Komunikimin e bazuar në ngjarje (event-based communication).
11. Një përmbledhje krahasuese e të gjitha stileve të komunikimit indirekt.

---

## 1. Çka është komunikimi indirekt?

Në komunikimin **e drejtpërdrejtë (direct communication)** — për shembull një thirrje RPC ose një socket TCP — dërguesi (sender) dhe marrësi (receiver) duhet:
- të jenë **të dy aktivë njëkohësisht**, dhe
- **të njohin njëri-tjetrin** (adresën, portin, identitetin).

Në **komunikimin indirekt (indirect communication)**, dërguesi dhe marrësi **nuk kanë qasje të drejtpërdrejtë** te njëri-tjetri. Në vend të kësaj, mesazhet kalojnë përmes një **ndërmjetësi** (middleware, broker, grup, radhë, hapësirë e përbashkët). Kjo do të thotë që dërguesi as nuk e di domosdoshmërisht kush do ta marrë mesazhin, as nuk ka nevojë marrësi të jetë online në momentin e dërgimit.

### Teknikat kryesore të komunikimit indirekt

Kapitulli identifikon pesë teknika themelore, të cilat do t'i zhvillojmë secilën në vijim:

| # | Teknika | Ideja kryesore | Shembuj |
|---|---------|-----------------|---------|
| 1 | **Komunikimi në grup** (group communication) | Dërguesi i drejtohet një grupi, jo një procesi të vetëm | Multicast/broadcast, JGroups |
| 2 | **Publiko/Abono** (publish/subscribe) | Publikuesi lëshon mesazhe mbi një temë (topic); vetëm abonentët e marrin | MQTT, Kafka topics |
| 3 | **Mesazhe radhazi** (message queue) | Mesazhi vendoset në radhë dhe lexohet më vonë nga marrësi | RabbitMQ, Kafka, Amazon SQS |
| 4 | **Memoria e ndarë** (shared data space) | Proceset lexojnë/shkruajnë në një hapësirë të përbashkët të dhënash | Tuple spaces, distributed caches |
| 5 | **Komunikimi i bazuar në ngjarje** (event-based) | Një komponent gjeneron ngjarje, të tjerët reagojnë | Event brokers, Kafka, RabbitMQ |

### Përparësitë e komunikimit indirekt

- **Më pak varësi mes komponentëve** — një komponent nuk "njeh" strukturën e brendshme të tjetrit.
- **Fleksibilitet dhe shkallëzueshmëri më e madhe** — komponentët mund të shtohen, hiqen ose zëvendësohen pa prishur sistemin.
- **Marrësi nuk duhet të jetë aktiv** në momentin kur dërgohet mesazhi (moslidhje në kohë).

### Mangësitë e komunikimit indirekt

- **Vonesa më e madhe** — mesazhi kalon nëpërmjet një apo më shumë ndërmjetësve.
- **Renditja e mesazheve** është më e vështirë për t'u garantuar.
- **Kompleksitet shtesë** në ruajtjen, përsëritjen (retry) ose humbjen e mundshme të mesazheve.

> **Ideja kryesore:** komunikimi indirekt e bën sistemin e shpërndarë më të pavarur (loosely coupled), më elastik dhe më të lehtë për t'u zgjeruar — me çmimin e vonesës shtesë dhe kompleksitetit të menaxhimit të gjendjes.

---

## 2. Lidhja e hapësirës dhe kohës (space–time coupling)

Një mënyrë e dobishme për të krahasuar stilet e ndryshme të komunikimit indirekt është përmes konceptit të **lidhjes hapësirë-kohë**: sa varet një komponent nga një tjetër përsa i përket vendndodhjes dhe kohës së veprimit.

### Lidhja në hapësirë (Space Coupling)

Ka të bëjë me **adresimin** — a e di dërguesi identitetin e saktë të marrësit?

- **Lidhje e fortë (tightly coupled):** dërguesi e njeh saktësisht marrësin (IP, endpoint, ID). Shembull tipik: thirrja RPC direkte.
- **Lidhje e dobët (loosely coupled):** dërguesi nuk e njeh marrësin specifik, por i drejtohet një ndërmjetësi (topic, queue). Shembull tipik: publish/subscribe.

### Lidhja në kohë (Time Coupling)

Ka të bëjë me **sinkronizimin kohor** — a duhet dërguesi dhe marrësi të jenë aktivë njëkohësisht?

- **Lidhje e fortë:** të dyja palët duhet të jenë aktive në të njëjtën kohë (p.sh. komunikimi sinkron request/response).
- **Lidhje e dobët:** dërguesi dhe marrësi nuk kanë nevojë të jenë aktivë njëkohësisht (p.sh. message queues).

### Moslidhja në hapësirë (Space Decoupling)

Kur ka moslidhje në hapësirë:
- Dërguesi **nuk e di kush** do ta marrë mesazhin, marrësi **nuk e di nga kush** erdhi.
- Komunikimi kalon përmes një ndërmjetësi (queue, topic, broker).

Kjo sjell fleksibilitet të konsiderueshëm në praktikë:
- **Zëvendësim** i pjesëmarrësve — një shërbim mund të hiqet dhe të vendoset një tjetër pa ndikuar palën tjetër.
- **Përditësim** — ndryshon versioni i një komponenti pa e prishur sistemin e tërë.
- **Përsëritje (replication)** — mund të shtohet më shumë se një marrës për të njëjtin mesazh (p.sh. për tolerancë ndaj gabimeve).
- **Migrim** — një komponent mund të kalojë në server tjetër pa ndikuar dërguesin.

Në thelb: sistemi nuk "prishet" nëse ndryshon një komponent i vetëm. Motoja e kësaj moslidhjeje: **"Nuk e di me kë po flas."**

### Moslidhja në kohë (Time Decoupling)

Kur ka moslidhje në kohë:
- Dërguesi dhe marrësi **nuk kanë nevojë të jenë aktivë njëkohësisht**.
- Dërguesi mund të dërgojë mesazhin sot; marrësi mund ta lexojë më vonë, madje edhe një ditë tjetër.

Në praktikë kjo nënkupton:
- Nëse marrësi është offline, **mesazhi ruhet** (p.sh. në një radhë).
- Marrësi e merr mesazhin sapo të jetë gati.
- Sistemi vazhdon të funksionojë edhe kur ka ndërprerje të njërës palë.

Kjo është veçanërisht e rëndësishme në mjedise jo-të-qëndrueshme (internet, cloud, mobile), ku shërbimet mund të rrëzohen (crash), pajisjet lidhen dhe shkëputen shpesh, dhe rrjeti nuk është gjithnjë i qëndrueshëm. Moslidhja në kohë bën që sistemi **të mos ndalet**, **të mos humbasë mesazhe** dhe të jetë më rezistent ndaj dështimeve. Motoja: **"Nuk ka rëndësi kur po flet."**

> Kombinimi i moslidhjes në hapësirë dhe në kohë është pikërisht ajo që e dallon komunikimin indirekt nga ai i drejtpërdrejtë, dhe është baza mbi të cilën ndërtohen të gjitha teknikat e mbetura të këtij kapitulli.

---

## 3. Komunikimi në grup (Group Communication)

**Komunikimi në grup** është mënyra kur një proces dërgon një mesazh jo te një proces i vetëm, por te një **grup procesesh** që trajtohen si një njësi logjike e vetme.

### Çka është një grup?

Një grup është një koleksion procesesh/shërbimesh që:
- marrin të njëjtat mesazhe,
- bashkëpunojnë për një detyrë të përbashkët,
- mund të shtohen ose hiqen dinamikisht gjatë ekzekutimit (anëtarësi dinamike).

### Si funksionon në krahasim me komunikimin një-me-një

Në vend të skemës klasike:

```
Procesi A ➜ Procesi B
```

kemi:

```
Procesi A ➜ Grupi X ➜ (B, C, D, …)
```

Dërguesi nuk merret individualisht me secilin marrës — sistemi (middleware-i i grupit) kujdeset automatikisht për shpërndarjen e mesazhit te të gjithë anëtarët.

Llojet kryesore të komunikimit në grup janë: **multicast**, **broadcast** dhe **publish/subscribe**. Në rastin e publish/subscribe, dërguesi "publikon" një informacion dhe vetëm ata që janë të abonuar e marrin mesazhin. Ky është zakonisht komunikim indirekt me **moslidhje në hapësirë** (dërguesi nuk i njeh të gjithë anëtarët), dhe shpesh edhe **moslidhje në kohë** (p.sh. kur përdoren queue-t ose pub/sub).

### Karakteristikat kryesore të komunikimit në grup

- **Garancitë e dorëzimit (delivery guarantees):** a arrin mesazhi te të gjithë anëtarët?
- **Renditja (ordering):** a e marrin të gjithë anëtarët mesazhet në të njëjtën radhë?
- **Menaxhimi i anëtarësisë (membership management):** si shtohen/largohen anëtarët nga grupi?

### Përparësitë

- Shkallëzim më i lehtë (scale-out) — shtohen instanca të reja pa ndryshuar dërguesin.
- Replikim dhe tolerancë ndaj gabimeve — humbja e një anëtari nuk e ndalon sistemin.
- Shpërndarje efikase e të dhënave te shumë marrës njëkohësisht.

### Sfidat

- Ruajtja e renditjes së mesazheve kur ka shumë dërgues dhe marrës.
- Menaxhimi i dështimeve të anëtarëve (failure detection dhe rikuperimi).
- Sinkronizimi i gjendjes së grupit (kush është anëtar aktualisht).

### Komunikimi në grup si abstraksion

Komunikimi në grup është një **abstraksion mbi komunikimin shumëpjesësh** (multi-party), i cili mund të implementohet mbi protokolle si IP multicast, ose mbi rrjete të tjera ekuivalente. Kjo shtresë abstraksioni ofron funksionalitete shtesë të rëndësishme që vetë rrjeti (p.sh. IP) nuk i ofron:

- Menaxhimi i anëtarëve të grupit.
- Zbulimi i dështimeve (failure detection).
- Sigurimi i besueshmërisë së komunikimit.
- Garantimi i shpërndarjes së mesazheve (delivery guarantees).

Shërbimet e ndryshme për komunikim në grup dallojnë njëra nga tjetra sipas disa dimensioneve kryesore, të cilat i trajtojmë në vijim: grupet e hapura kundrejt të mbyllurave, grupet e mbivendosura kundrejt jo-mbivendosurave, dhe sistemet sinkrone kundrejt asinkrone.

---

## 4. Grupet e mbyllura dhe grupet e hapura

Ky është dimensioni i parë sipas të cilit klasifikohen shërbimet e komunikimit në grup, dhe ka të bëjë me **kush lejohet të dërgojë** mesazhe në grup.

- **Grupi i mbyllur:** vetëm anëtarët e vetë grupit mund të dërgojnë dhe të pranojnë mesazhe brenda tij. Një proces që është anëtar i një grupi të mbyllur, e merr çdo mesazh që dërgohet në atë grup (nga proceset e tjera anëtare).
- **Grupi i hapur:** çdo proces — qofshin ata anëtarë apo jo — mund të dërgojë mesazhe në grup nga jashtë.

**Kur përdoret secili lloj?**

- Grupet **e mbyllura** janë të dobishme, për shembull, kur anëtarët duhet të bashkëdërgojnë (bashkëpunojnë duke shkëmbyer) mesazhe mes tyre, dhe vetëm ata anëtarë duhet t'i shohin ato mesazhe (p.sh. procese llogaritëse që koordinohen mes tyre dhe rezultatet e ndërmjetme nuk duhen ekspozuar jashtë).
- Grupet **e hapura** janë të dobishme, për shembull, për dërgimin e ngjarjeve (events) te grupe procesesh të interesuara, edhe pse vetë burimi i ngjarjes nuk është pjesë e atij grupi (p.sh. një sensor që publikon ngjarje te një grup dëgjuesish, pa qenë vetë anëtar i grupit).

---

## 5. Grupet e mbivendosura dhe grupet jo-mbivendosura

Ky është dimensioni i dytë, dhe ka të bëjë me **anëtarësinë e njëkohshme në disa grupe**.

- **Grupet e mbivendosura (overlapping groups):** subjektet (procese apo objekte) mund të jenë anëtare të **disa grupeve njëkohësisht**. Për shembull, një proces mund të jetë pjesë e grupit "Serverë Databaze" dhe njëkohësisht pjesë e grupit "Nyje Rajoni-Evropë".
- **Grupet jo-mbivendosura (non-overlapping groups):** grupe ku anëtarësitë **nuk përputhen** — çdo proces i takon **më së shumti** një grupi të vetëm.

Vlen të theksohet se në sistemet e botës reale (cloud, microservices, sisteme të mëdha të shpërndara), është **realiste të pritet që anëtarësia e grupeve të përputhet** — pra grupet e mbivendosura janë rasti më i zakonshëm në praktikë, jo përjashtimi. Kjo e ndërlikon menaxhimin e anëtarësisë dhe renditjen e mesazheve, sepse një mesazh mund të duhet të trajtohet në kontekstin e disa grupeve njëherësh.

---

## 6. Broadcast kundrejt Multicast

Dallimi ndërmjet broadcast dhe multicast qëndron te **çfarë di sistemi rreth marrësve**:

| Karakteristikë | Broadcast | Multicast |
|---|---|---|
| Kush mban gjurmë të dëgjuesve? | **Askush** — mesazhi shkon te të gjithë | **Sistemi** mban shënim të saktë kush duhet ta marrë mesazhin |
| Adresimi | Të gjithë brenda domenit (p.sh. rrjetit) | Vetëm grupi specifik i regjistruar |
| Shembull | Transmetim radio; IP Broadcast (p.sh. `192.168.1.255`) | IP-multicast (p.sh. `239.1.1.1`) |

- Në një **shërbim Broadcast**, mesazhi shpërndahet te të gjithë pjesëmarrësit e mundshëm brenda një domeni (p.sh. rrjeti lokal), pa kufizuar marrësit dhe pa mbajtur gjurmë se kush po "dëgjon" në të vërtetë.
- Në një **shërbim Multicast**, dërguesi i dërgon mesazhin një **grupi specifik**, dhe vetë sistemi (protokolli i multicast-it) mban shënime se cilat nyje/procese duhet ta marrin mesazhin, në mënyrë që të mos shpërndahet tek të gjithë pa nevojë.

**Karakteristikë e rëndësishme:** IP-multicast konsiderohet i **jo-besueshëm** (best-effort) — nuk garanton dorëzim, nuk mban gjurmët e anëtarësisë në mënyrë të fortë, dhe as **nuk garanton rendin** e mesazheve kur ka disa dërgues njëkohësisht (multiple senders). Kjo është arsyeja pse mbi IP-multicast ndërtohen shtresa shtesë middleware (si JGroups, Spread) që shtojnë besueshmëri dhe renditje.

---

## 7. Renditja e mesazheve (Ordering of Messages)

Kur shumë procese dërgojnë mesazhe drejt një grupi, mund të lindin situata ku procese të ndryshme i marrin mesazhet në **rend të ndryshëm**. Për shumë aplikacione kjo është e papranueshme (p.sh. dy komanda kontradiktore që arrijnë në rend të ndryshëm në nyje të ndryshme). Prandaj përcaktohen disa **paradigma renditjeje**:

### Renditja FIFO (First-In-First-Out)

Të gjitha mesazhet e dërguara nga **i njëjti dërgues** merren nga çdo marrës **sipas radhës së dërgimit**. Pra, nëse procesi P dërgon m1 e pastaj m2, çdo marrës do t'i shohë ato në po atë rend: m1 para m2. Kjo NUK thotë asgjë për renditjen relative mes mesazheve të dërguesve të ndryshëm.

### Rendi shkakor (Causal Ordering)

Nëse një mesazh **m2** dërgohet si **pasojë** e një mesazhi **m1** (d.m.th. procesi ka parë/marrë m1 dhe më pas, si reagim, dërgon m2), atëherë **të gjithë anëtarët e grupit** duhet ta shohin m1 **para** m2 — pavarësisht nga cili proces i dërgon ato. Kjo kap lidhjen shkak-pasojë (causality), jo thjesht renditjen kronologjike të një dërguesi të vetëm.

> **Vërejtje e rëndësishme:** rendi shkakor **nuk nënkupton domosdo FIFO**. Një proces mund të dërgojë m1 dhe më pas m2, por vetë ai proces ende **nuk e ka parë (marrë)** mesazhin e vet m1 kur dërgon m2 — pra FIFO garanton renditje sipas dërguesit, ndërsa causal ordering garanton renditje sipas varësisë shkakësore, dhe këto dy koncepte janë të ndryshme edhe pse shpesh ngatërrohen.

### Porosia totale (Total Ordering)

**Të gjithë anëtarët** e grupit i shohin **të gjitha** mesazhet në **të njëjtin rend absolut** — pavarësisht nga cili dërgues i ka dërguar dhe pavarësisht nga relacionet shkakësore. Kjo është garancia më e fortë, dhe zakonisht më e shtrenjta për t'u implementuar (kërkon koordinim shtesë, si p.sh. një sekuencues qendror ose algoritëm konsensusi).

**Përmbledhje e forcës së garancive** (nga më e dobëta te më e forta):

```
Pa renditje  <  FIFO  <  Rend shkakor  <  Porosi totale
```

Sa më e fortë garancia, aq më i madh kostoja e performancës dhe komunikimit shtesë i nevojshëm për ta ruajtur atë. Zgjedhja e paradigmës varet nga kërkesat e aplikacionit — p.sh. një sistem bashkëpunimi në kohë reale (si redaktim i përbashkët dokumentesh) shpesh kërkon rend shkakor, ndërsa një sistem replikimi konsistent të gjendjes kërkon porosi totale.

---

## 8. Veglat (Toolkits) për implementimin e komunikimit në grup

Për të mos implementuar nga zeroja renditjen, besueshmërinë dhe menaxhimin e anëtarësisë, ekzistojnë biblioteka/toolkits të gatshme mbi të cilat ndërtohen sistemet e shpërndara reale.

### JGroups

- Ofron një **API shumë të thjeshtë** për dërgim/pranim mesazhesh në grup — kodi mbetet i njëjtë pavarësisht se cili protokoll-stack (protocol stack) përdoret nën kapuç (p.sh. UDP multicast, TCP).
- Për të dërguar/pranuar mesazhe, aplikacioni krijon një **kanal** (`JChannel`). Besueshmëria e kanalit specifikohet përmes një konfigurimi XML, i cili përcakton stack-un e protokollit që do të përdoret (p.sh. UDP, fragmentimi, kontrolli i fluksit, zbulimi i dështimeve).

Shembull i thjeshtuar në Java:

```java
JChannel channel = new JChannel("/home/bela/udp.xml");
channel.setReceiver(new ReceiverAdapter() {
    public void receive(Message msg) {
        System.out.println("received msg from " + msg.getSrc()
                            + ": " + msg.getObject());
    }
});
channel.connect("MyCluster");
channel.send(new ObjectMessage(null, "Përshëndetje"));
```

Ky kod: (1) krijon një kanal të konfiguruar përmes XML-it, (2) regjistron një "receiver" që reagon kur mbërrin një mesazh, (3) i bashkohet grupit `"MyCluster"`, dhe (4) dërgon një mesazh të thjeshtë te i gjithë grupi.

### Akka Toolkit

- Akka është një **toolkit me burim të hapur (open source)** që thjeshtëson ndërtimin e aplikacioneve **konkurrente dhe të shpërndara** mbi JVM.
- Mbështet modele të shumta programimi për konkurencën, por thekson veçanërisht **modelin e aktorëve (actor-based concurrency)**, i frymëzuar nga gjuha Erlang. Në këtë model, çdo "aktor" është njësi e pavarur ekzekutimi që komunikon vetëm përmes shkëmbimit mesazhesh (jo memorie të përbashkët), gjë që e bën natyrshëm një formë komunikimi indirekt dhe të lidhur dobët.
- Ka lidhje gjuhësore (bindings) si për **Java** ashtu edhe për **Scala**. Vetë Akka është shkruar në Scala; për shkak të popullaritetit të saj, aktorët e vetë bibliotekës standarde të Scala-s janë zhvlerësuar (deprecated) në favor të Akka.

### Spread Toolkit

- Spread është gjithashtu një toolkit **open source**, i menduar për aplikacione të shpërndara që kërkojnë **besueshmëri të lartë**, **performancë të lartë**, dhe komunikim të fuqishëm mes grupeve të ndryshme anëtarësh.
- Është krijuar posaçërisht për të fshehur (abstraktuar) kompleksitetin e rrjeteve **asinkrone**, duke i lejuar zhvilluesit të ndërtojnë aplikacione të shpërndara të besueshme dhe të shkallëzueshme pa u marrë vetë me këto probleme të nivelit të ulët.
- Arkitektura: një **bibliotekë klienti** me të cilën lidhen aplikacionet e përdoruesve, një **"binary daemon"** që funksionon në çdo kompjuter pjesë e grupit të procesorëve, dhe programe shtesë shërbimi/demonstrimi.

**Karakteristika/përfitime kryesore të ofruara nga Spread:**
- Mesazhe të besueshme dhe të shkallëzueshme gjatë komunikimit në grup.
- API e fuqishme që thjeshtëson ndërtimin e arkitekturave të shpërndara.
- E lehtë për t'u përdorur, vendosur (deploy) dhe mirëmbajtur.
- Shumë e shkallëzuar — nga një rrjet lokal deri te rrjete komplekse me sipërfaqe të gjerë (WAN).
- Mbështet mijëra grupe me anëtarësi të ndryshme njëkohësisht.
- Siguron besueshmëri të mesazheve edhe në prani të dështimeve të makinerive, përplasjeve (crash) të proceseve, ndarjeve të rrjetit (network partitions) dhe rikuperimeve/bashkimeve pas tyre.
- Ofron një gamë opsionesh për besueshmëri, renditje dhe garanci stabiliteti të mesazheve.
- Thekson qëndrueshmërinë dhe performancën e lartë.
- Përdor algoritme **plotësisht të shpërndara**, pa asnjë pikë qendrore dështimi (single point of failure).

---

## 9. Sistemet sinkrone dhe asinkrone

Dallimi sinkron/asinkron ndikon drejtpërdrejt te dizajni i algoritmeve themelore për komunikim shumëpjesësh (multi-party):

- **Komunikimi sinkron** kërkon që dërguesi dhe marrësi(t) të merren parasysh njëkohësisht në të njëjtin ambient kohor — ekzistojnë kufizime kohore mbi arritjen e mesazheve. Ky dallim ka ndikim të rëndësishëm në algoritme: p.sh., disa algoritme supozojnë se grupet janë **të mbyllura**. I njëjti efekt mund të arrihet edhe në një grup **të hapur** duke e "simuluar" mbylljen — përzgjidhet një anëtar i grupit dhe i dërgohet atij një mesazh një-me-një, të cilin ai pastaj e bën multicast brenda grupit të vet.
- **Komunikimi asinkron:** dërguesi dërgon një mesazh dhe pastaj **vazhdon menjëherë** (pa bllokuar/pritur), pa pasur nevojë të "takohet në kohë" me marrësin për të komunikuar. Kjo është themeli i moslidhjes në kohë të diskutuar më sipër.

---

## 10. Roli i menaxhimit të anëtarëve të grupit (Group Membership Management)

Për të mundësuar komunikim të besueshëm në grup, sistemi ka nevojë për një **shërbim të menaxhimit të anëtarësisë** (group membership service), i cili merret me:

- **Mbajtjen e një pamjeje (view)** aktuale të kush është anëtar i grupit në çdo moment.
- **Njoftimin e ndryshimeve** të anëtarësisë (kur një anëtar hyn, del, apo dështon) tek të gjithë anëtarët e tjerë, në mënyrë të koordinuar (shpesh nëpërmjet "view-synchronous communication" — çdo anëtar sheh të njëjtën sekuencë pamjesh).
- **Zbulimin e dështimeve (failure detection)** — dallimin mes një procesi që është ngadalësuar dhe një procesi që në të vërtetë ka rrëzuar/dështuar.
- **Koordinimin e rikuperimit** pas ndarjeve të rrjetit (network partitions) ose bashkimit të nën-grupeve.

Ky shërbim është kritik sepse pa të, algoritmet e renditjes së mesazheve (FIFO, shkakor, total) dhe garancitë e dorëzimit nuk do të ishin të zbatueshme në mënyrë të besueshme — cilido algoritëm renditjeje duhet të dijë saktësisht **kush** është pjesë e grupit në një moment të caktuar për ta vendosur mesazhin brenda kontekstit të duhur.

**Shembull ilustrues: Sistemi i dhomës së marrëveshjes (Dealing room system).** Ky është një shembull klasik nga literatura e sistemeve të shpërndara (tregti financiare në kohë reale), ku tregtarë (dealers) të ndryshëm në një rrjet lokal duhet të marrin çmime dhe përditësime tregu në kohë reale dhe në të njëjtin rend, ndërsa dështimi ose ndërprerja e një stacioni pune nuk duhet ta ndalë funksionimin e sistemit për të tjerët. Ky shembull tregon nevojën praktike për komunikim në grup me menaxhim anëtarësie dhe renditje mesazhesh të fortë (shpesh total order), pikërisht sepse të gjithë tregtarët duhet të shohin çmimet në të njëjtin rend për të shmangur vendimet kontradiktore.

---

## 11. Paradigma Publiko-Abono (Publish/Subscribe)

### Konceptet bazë

- **Publishers (Publikuesit):** gjenerojnë të dhëna për ngjarje dhe i publikojnë ato.
- **Subscribers (Abonentët):** shprehin pajtimet (subscriptions) e tyre — çfarë lloj ngjarjesh i interesojnë — dhe më pas përpunojnë ngjarjet që u vijnë.

Publikuesit dhe abonentët janë **plotësisht të lidhur dobët në hapësirë**: publikuesi nuk e di kush janë abonentët, dhe abonentët nuk e dinë kush publikoi ngjarjen — vetëm dinë "temën" ose kriterin që i interesojnë.

### Implementimi: nga qendror te i shpërndarë

- **Implementimi më i thjeshtë:** një **server qendror** që mban gjurmët e të gjitha abonimeve (subscriptions) dhe përcjell çdo ngjarje të publikuar tek abonentët përkatës. E lehtë për t'u implementuar, por vuan nga:
  - **Disponueshmëria** e kufizuar — nëse serveri bie, gjithçka ndalon. Kjo adresohet duke përdorur **të paktën dy servera** (redundancë).
  - **Shkallëzueshmëria** e kufizuar — një server i vetëm mbytet nën ngarkesë të madhe.
- **Zgjidhja për shkallëzueshmëri:** përdoret një **rrjet i shpërndarë ndërmjetësish (broker network)** të ngjarjeve. Klientët (publikues dhe abonentë) lidhen me brokerin më të afërt, dhe brokerat mes tyre formojnë një **rrjet të mbivendosur (overlay network)** që di të drejtojë (route) ngjarjet nga publikuesi tek abonentët e duhur — pyetja qendrore që lind këtu është: *duke pasur parasysh një rrjet të ndërmjetësve, si i shpërndajmë në mënyrë efikase ngjarjet nga publikuesi te abonentët?* — kjo trajtohet nga algoritmet e rrugëtimit të bazuar në filtrim (shih më poshtë).

### Sistemet reale Pub/Sub

Pub/sub shpesh është pjesë e një **platforme më të gjerë mesazhesh**:
- **JMS — Java Messaging Service** (Shërbimi i Mesazheve Java)
- **ZeroMQ**
- **Redis** (kanalet pub/sub)
- **Kafka** (topics)

ose ofrohet si **shërbim i ndarë (i pavarur, cloud-based)**:
- **Google Cloud Pub/Sub**

Ekzistojnë edhe **standarde** të pranuara gjerësisht për pub/sub:
- **OMG Data Distribution Service (DDS)**
- **Atom** — web feeds (RSS), ku klientët bëjnë "sondazh" (polling) periodik për të kontrolluar azhurnime, në vend që t'u njoftohet aktivisht (push).

### Rrjeta e brokerave-agjentëve (network of brokers)

Në një arkitekturë të shkallëzueshme pub/sub, publikuesit dhe abonentët nuk lidhen me një server qendror, por me **brokerin më të afërt gjeografikisht/logjikisht**. Brokerat lidhen mes tyre duke formuar një **topologji rrjeti** (shpesh pemë ose graf i lidhur), dhe bashkëpunojnë për:

1. **Përhapjen e abonimeve** — kur një klient abonohet në një temë te broker-i i tij, ky abonim përhapet (propagohet) tek brokerat fqinjë, në mënyrë që të dinë ku duhet të drejtojnë ngjarjet përkatëse.
2. **Rrugëtimin e ngjarjeve** — kur mbërrin një ngjarje e publikuar, çdo broker e krahason atë me tabelën e tij të abonimeve dhe e përcjell vetëm nëpër degët e rrjetit që çojnë drejt abonentëve të interesuar — jo drejt të gjithëve.

Kjo arkitekturë e shpërndarë e bën sistemin shumë më të shkallëzueshëm dhe elastik se një zgjidhje me server qendror.

### Arkitektura e sistemit publiko-abono

Në përgjithësi, arkitektura e një sistemi pub/sub përbëhet nga tri shtresa logjike:

1. **Klientët** — publikuesit dhe abonentët, të cilët komunikojnë vetëm me brokerin/ndërmjetësin e tyre lokal, jo drejtpërdrejt me njëri-tjetrin.
2. **Rrjeti i brokerave** — shtresa e ndërmjetme (middleware) që mban abonimet dhe drejton ngjarjet përmes rrjetit të mbivendosur (overlay).
3. **Kanali i shpërndarjes** — mekanizmi konkret (p.sh. TCP, multicast) përmes të cilit brokerat komunikojnë mes tyre dhe me klientët.

Kjo ndarje realizon plotësisht moslidhjen në hapësirë (asnjëri palë nuk e njeh tjetrën) dhe, kur kombinohet me ruajtje të përkohshme të ngjarjeve, edhe moslidhje pjesore në kohë.

### Rrugëtimi i bazuar në filtrim (Filtering-based Routing)

Për drejtimin efikas të ngjarjeve nëpër rrjetin e brokerave përdoret një algoritëm i bazuar në **filtrim** — çdo broker mban një tabelë rrugëtimi (routing table) të ndërtuar nga abonimet e marra prej fqinjëve, dhe e përdor këtë tabelë për të vendosur ku ta përcjellë çdo ngjarje të re. Në pseudokod (siç paraqitet në ligjëratë):

```text
upon receive publish(event e) from node x
    matchlist := match(e, subscriptions)     // gjej abonentët lokalë të përputhur
    send notify(e) to matchlist               // njofto abonentët lokalë
    fwdlist := match(e, routing)               // gjej brokerat fqinjë ku duhet përcjellë
    send publish(e) to fwdlist - x             // përcille tek fqinjët (përveç x, prej nga erdhi)

upon receive subscribe(subscription s) from node x
    if x is client then
        add x to subscriptions                 // x është klient direkt — regjistroje lokalisht
    else
        add(x, s) to routing                    // x është broker fqinj — regjistro rrugën drejt tij
    send subscribe(s) to neighbours - x         // përhape abonimin tek fqinjët e tjerë
```

**Shpjegim i logjikës:**
- Kur mbërrin një **ngjarje e publikuar** nga nyja `x`, brokeri: (a) e krahason ngjarjen me abonimet lokale të klientëve dhe u dërgon njoftim atyre që përputhen, dhe (b) e krahason gjithashtu me tabelën e rrugëtimit për të vendosur te cilët fqinjë (broker të tjerë) duhet ta përcjellë ngjarjen — duke e shmangur dërgimin mbrapsht te nyja `x` prej nga erdhi (parandalon ciklet/dublikimin).
- Kur mbërrin një **kërkesë abonimi**, brokeri kontrollon nëse burimi është një klient i drejtpërdrejtë (rast në të cilin abonimin e ruan lokalisht) apo një broker tjetër (rast në të cilin regjistron një hyrje rrugëtimi që tregon "përmes këtij fqinji arrihen abonentët me këtë abonim"), dhe më pas **përhap** abonimin edhe tek fqinjët e vet (përveç atij prej nga erdhi), për ta shtrirë njohurinë e abonimit nëpër tërë rrjetin e brokerave.

Kjo qasje quhet **content/filter-based routing** — për dallim nga qasja më e thjeshtë **topic-based routing**, ku ngjarjet klasifikohen sipas "temave" paracaktuara (si kanale të emërtuara), qasja e bazuar në filtrim lejon abonime më të sofistikuara — bazuar në përmbajtjen aktuale të ngjarjes (p.sh. "të gjitha ngjarjet ku fusha `temperatura > 30`"), jo vetëm sipas emrit të temës.

**Shembuj sistemesh publiko-abono** të njohura që ndërtohen mbi këto parime përfshijnë sisteme si SIENA, Gryphon, Hermes, dhe sistemet moderne të industrisë (Kafka, RabbitMQ me exchange të tipit topic/headers, Google Cloud Pub/Sub), të cilat ndryshojnë kryesisht në mënyrën si e implementojnë rrugëtimin (topic-based kundrejt content-based) dhe topologjinë e rrjetit të brokerave.

### Modeli i programimit i ofruar nga JMS

**JMS (Java Message Service)** është API-ja standarde e Java-s për sisteme me mesazhe, që mbështet të dyja modelet: **publish/subscribe** (nëpërmjet `Topic`) dhe **point-to-point/queue** (nëpërmjet `Queue`). Modeli i programimit i JMS për pub/sub përfshin hapat tipikë:

1. Kërkim (lookup) i një `TopicConnectionFactory` përmes JNDI (Java Naming and Directory Interface).
2. Kërkim i vetë `Topic`-ut (temës) me emër specifik.
3. Krijimi i një `TopicConnection` dhe pastaj një `TopicSession`.
4. Nga sesioni, krijohet ose një **publikues** (`TopicPublisher`) ose një **abonent** (`TopicSubscriber`).

#### Shembull: Publikuesi (`FireAlarmJMS`)

```java
import javax.jms.*;
import javax.naming.*;

public class FireAlarmJMS {
    public void raise() {
        try {
            Context ctx = new InitialContext();
            TopicConnectionFactory topicFactory =
                (TopicConnectionFactory) ctx.lookup("TopicConnectionFactory");
            Topic topic = (Topic) ctx.lookup("Alarms");
            TopicConnection topicConn = topicFactory.createTopicConnection();
            TopicSession topicSess =
                topicConn.createTopicSession(false, Session.AUTO_ACKNOWLEDGE);
            TopicPublisher topicPub = topicSess.createPublisher(topic);
            TextMessage msg = topicSess.createTextMessage();
            msg.setText("Fire!");
            topicPub.publish(msg);
        } catch (Exception e) {
            // trajtimi i gabimit
        }
    }
}
```

Ky kod: kërkon fabrikën e lidhjeve dhe temën "Alarms" përmes JNDI, krijon një lidhje dhe një sesion, krijon një publikues për atë temë, ndërton një mesazh tekst me përmbajtje `"Fire!"`, dhe e publikon atë — çdo abonent i temës "Alarms" do ta marrë.

#### Shembull: Abonenti (`FireAlarmConsumerJMS`)

```java
import javax.jms.*;
import javax.naming.*;

public class FireAlarmConsumerJMS {
    public String await() {
        try {
            Context ctx = new InitialContext();
            TopicConnectionFactory topicFactory =
                (TopicConnectionFactory) ctx.lookup("TopicConnectionFactory");
            Topic topic = (Topic) ctx.lookup("Alarms");
            TopicConnection topicConn = topicFactory.createTopicConnection();
            TopicSession topicSess =
                topicConn.createTopicSession(false, Session.AUTO_ACKNOWLEDGE);
            TopicSubscriber topicSub = topicSess.createSubscriber(topic);
            topicSub.start();
            TextMessage msg = (TextMessage) topicSub.receive();
            return msg.getText();
        } catch (Exception e) {
            return null;
        }
    }
}
```

Këtu abonenti krijon një `TopicSubscriber` mbi të njëjtën temë "Alarms", niset (`start()`), dhe thërret `receive()` — një thirrje që **bllokon** (pret) derisa të mbërrijë një mesazh i ri, pas së cilës kthen tekstin e tij (p.sh. `"Fire!"`).

> Vini re: publikuesi dhe abonenti **nuk njihen** me njëri-tjetrin — vetëm ndajnë emrin e temës "Alarms". Ky është shembulli klasik i moslidhjes në hapësirë të realizuar konkretisht në kod.

---

## 12. Sistemet me mesazhe radhazi (Message Queues)

Ndryshe nga pub/sub (ku mesazhi shpërndahet potencialisht te shumë abonentë), **message queue** (radha e mesazheve) është një model ku mesazhi zakonisht konsumohet nga **një** marrës (ose një nga një grup konsumatorësh konkurrues — competing consumers).

### Karakteristikat kryesore

- Një **queue/radhë** (normalisht me disiplinë **FIFO**) është një objekt i **pavarur nga proceset** — ekziston si entitet më vete në infrastrukturën e mesazheve.
- Proceset mund të kryejnë katër operacione themelore mbi një radhë:
  1. **Dërgojnë** mesazhe në radhë.
  2. **Pranojnë** (marrin) mesazhe nga radha.
  3. **Sondazhojnë (poll)** radhën — kontrollojnë periodikisht nëse ka mesazh të ri.
  4. **Njoftohen** (notify) nga radha — kur mbërrin mesazh i ri, sistemi u njofton pa pritur ata të pyesin.

- Sistemet me radhë mesazhesh konsiderohen **më të strukturuara dhe më të besueshme** në krahasim me sistemet pub/sub të thjeshta, sepse çdo mesazh zakonisht **ruhet me qëndrueshmëri (persisted)** derisa të konsumohet dhe konfirmohet (acknowledge), gjë që garanton se asnjë mesazh nuk humbet edhe nëse konsumatori është përkohësisht jashtë funksionimit.

### Sfida e gjetjes së radhëve

Radhët mund të funksionojnë (fizikisht) në secilën nyje të sistemit, por lind nevoja për një **mekanizëm gjetjeje (lookup/discovery)** për t'i gjetur ato kur dërgohet ose pranohet një mesazh:

- Një **server qendror** është zgjidhja më e thjeshtë, por **nuk lejon shkallëzim** të mirë (bëhet pengesë — bottleneck).
- Alternativë: një **lidhës (connector/broker)**, i ngjashëm konceptualisht me mekanizmin e "binder"-it në RPC, i cili merret me mbikëqyrjen dhe zbulimin e radhëve, duke lejuar shkallëzueshmëri më të mirë përmes shpërndarjes.

### Kornizat (frameworks) kryesore për message queuing

- **WebSphere MQ** by IBM
- **Java Messaging Service (JMS)** — mbështet edhe queue (point-to-point), jo vetëm topics
- **RabbitMQ**
- **ZeroMQ**
- **Apache Qpid**

### Standardi AMQP

**AMQP — Advanced Message Queuing Protocol** është standardi i hapur mbi të cilin ndërtohen shumë prej këtyre sistemeve, duke lejuar ndërveprim (interoperability) mes implementimeve të ndryshme të message queuing-ut, pavarësisht prodhuesit apo gjuhës.

### Një topologji e thjeshtë rrjete në WebSphere MQ

Në sisteme si WebSphere MQ, nyjet e ndryshme (Queue Managers) organizohen në një topologji rrjeti ku secila nyje menaxhon radhët e veta lokale, dhe radhët "të largëta" (remote queues) referojnë radhë që fizikisht gjenden në nyje të tjera — kështu mesazhet mund të udhëtojnë përmes disa hopeve (kanaleve mes queue-manager-ëve) derisa të mbërrijnë te radha përfundimtare, në mënyrë analoge me rrugëtimin e paketave në një rrjet të shpërndarë.

---

## 13. Abstraktimi i memories së ndarë në sistemet e shpërndara (Shared Memory Abstraction)

Deri tani stilet e diskutuara (grup, pub/sub, queue) bazohen te **kalimi i mesazheve** (message passing). Një paradigmë krejt tjetër është **memoria e ndarë e shpërndarë** — proceset "komunikojnë" jo duke dërguar mesazhe drejtpërdrejt njëri-tjetrit, por duke lexuar dhe shkruar në një **hapësirë të përbashkët të dhënash** (shared data space), të aksesueshme nga të gjithë pjesëmarrësit, pavarësisht nga vendndodhja fizike e tyre.

Kjo formë komunikimi realizon moslidhje edhe më të fortë në hapësirë dhe kohë: procesi që shkruan të dhëna në hapësirën e përbashkët as nuk e di kush do t'i lexojë, e as kur do t'i lexojë — të dhënat thjesht "qëndrojnë" në hapësirë derisa dikush t'i marrë.

### Hapësira e qifteve (Tuple Space Abstraction)

Realizimi klasik i memories së ndarë të shpërndarë quhet **hapësirë qiftesh (tuple space)**, e prezantuar fillimisht nga gjuha Linda. Të dhënat ruhen si **qifte (tuples)** — rreshta të thjeshtë me fusha vlerash (analoge me një rresht tabele) — dhe operacionet themelore mbi hapësirën e qifteve janë:

- **`write` (shkruaj):** vendos një qift të ri në hapësirë.
- **`read` (lexo):** lexon (kopjon) një qift që përputhet me një "shabllon" (template) të dhënë, pa e hequr atë nga hapësira — kështu të tjerë mund ta lexojnë sërish.
- **`take`/`merr` (fshij):** lexon **dhe njëkohësisht heq** qiftin nga hapësira — vetëm një proces mund ta "marrë" me sukses një qift specifik (operacion atomik).

Kërkimi për një qift bëhet nëpërmjet **përputhjes së shabllonit (template matching)** — jo nëpërmjet një adrese apo çelësi të saktë, çka i jep kësaj qasjeje shumë fleksibilitet.

### Replikimi dhe operacionet mbi hapësirën e qifteve

Për besueshmëri, hapësira e qifteve zakonisht **përsëritet (replikohet)** nëpër disa nyje ("pamje" — view — e anëtarëve). Protokollet për operacionet themelore funksionojnë kështu:

**Operacioni `write` (shkrim):**
1. Sajti kërkues bën **multicast** të kërkesës për shkrim tek të gjithë anëtarët e pamjes.
2. Me marrjen e kërkesës, çdo anëtar e fut qiftin në replikën e vet lokale dhe njofton se e ka kryer veprimin.
3. Hapi përsëritet derisa të pranohen **të gjitha** njoftimet e konfirmimit.

**Operacioni `read` (lexim):**
1. Sajti kërkues shpërndan kërkesën për lexim te të gjithë anëtarët e pamjes.
2. Çdo anëtar që e ka qiftin e përputhur ia kthen atë kërkuesit.
3. Kërkuesi merr **qiftin e parë** të gjetur si rezultat (duke injoruar kopjet e tjera identike që vijnë nga anëtarë të tjerë).
4. Hapi përsëritet derisa të merret **të paktën një** përgjigje.

**Operacioni `take`/`merr` (fshirje) — dy faza:**

*Faza 1 — Zgjedhja e qiftit për fshirje:*
1. Sajti kërkues shpërndan kërkesën `take` te të gjithë anëtarët e pamjes.
2. Çdo replikë kërkon një **dryn (lock)** mbi bashkësinë e qifteve të përputhura; nëse dryni nuk mund të merret, kërkesa tërhiqet (retry më vonë).
3. Anëtarët që kanë marrë me sukses drynin, përgjigjen duke kthyer bashkësinë e qifteve përputhëse.
4. Hapi përsëritet derisa të gjitha sajtet të kenë pranuar/përgjigjur dhe ndërveprimi (bashkësia e përbashkët e rezultateve) të mos jetë bosh.
5. Një qift specifik zgjidhet **në mënyrë të rastësishme** nga bashkësia e ndërveprimit si rezultat përfundimtar.
6. Nëse vetëm një **pakicë** (minoritet) e anëtarëve kanë pranuar kërkesën me sukses, atyre u kërkohet të lëshojnë drynin, dhe Faza 1 përsëritet nga fillimi (për të shmangur mospërputhje mes replikave).

*Faza 2 — Fshirja e qiftit të zgjedhur:*
1. Sajti kërkues shpërndan kërkesën `remove` te të gjithë anëtarët, duke specifikuar saktësisht cilin qift ta fshijnë.
2. Çdo anëtar e fshin qiftin nga replika e vet, dërgon njoftim konfirmimi, dhe e liron drynin.
3. Hapi përsëritet derisa të pranohen të gjitha njoftimet.

Kjo protokoll me dy faza dhe drynë siguron **atomicitet** të operacionit `take` — vetëm një proces mund ta "marrë me sukses" të njëjtin qift, edhe pse hapësira është e replikuar nëpër shumë nyje.

### JavaSpaces API

**JavaSpaces** është një implementim konkret në Java i konceptit të hapësirës së qifteve (pjesë e ekosistemit Jini). Përdor klasa Java (të quajtura **entries**) në vend të qifteve gjenerike.

#### Shembull: përcaktimi i entry-t (`AlarmTupleJS`)

```java
import net.jini.core.entry.*;

public class AlarmTupleJS implements Entry {
    public String alarmType;

    public AlarmTupleJS() { }  // konstruktor pa argumente — i domosdoshëm për Entry

    public AlarmTupleJS(String alarmType) {
        this.alarmType = alarmType;
    }
}
```

#### Shembull: shkrimi në hapësirë (`FireAlarmJS`)

```java
import net.jini.space.JavaSpace;

public class FireAlarmJS {
    public void raise() {
        try {
            JavaSpace space = SpaceAccessor.findSpace("AlarmSpace");
            AlarmTupleJS tuple = new AlarmTupleJS("Fire!");
            space.write(tuple, null, 60 * 60 * 1000);  // ruhet 1 orë (lease)
        } catch (Exception e) {
            // trajtimi i gabimit
        }
    }
}
```

Vini re parametrin e tretë të `write()` — një **kohëzgjatje qiraje (lease)** prej 60*60*1000 milisekondash (1 orë): qiftet në JavaSpaces kanë jetëgjatësi të kufizuar dhe hiqen automatikisht kur skadon qiraja, nëse nuk rinovohen.

#### Shembull: leximi nga hapësira (`FireAlarmConsumerJS`)

```java
import net.jini.space.JavaSpace;

public class FireAlarmConsumerJS {
    public String await() {
        try {
            JavaSpace space = SpaceAccessor.findSpace();
            AlarmTupleJS template = new AlarmTupleJS("Fire!");
            AlarmTupleJS recvd =
                (AlarmTupleJS) space.read(template, null, Long.MAX_VALUE);
            return recvd.alarmType;
        } catch (Exception e) {
            return null;
        }
    }
}
```

Konsumatori ndërton një **shabllon** (`template`) me fushën `alarmType = "Fire!"`, dhe thërret `read()` me kohë pritjeje praktikisht të pakufizuar (`Long.MAX_VALUE`) — kjo do të bllokojë deri sa hapësira të përmbajë një entry të përputhur, pas së cilës e kthen atë (pa e fshirë, ngase u përdor `read`, jo `take`).

> Krahaso këtë me shembujt e mëparshëm të JMS: në rastin e tuple space, "komunikimi" ndodh përmes një hapësire të përbashkët të dhënash me kërkim sipas modelit (pattern matching), jo përmes një kanali eksplicit mesazhesh si te pub/sub.

---

## 14. Komunikimi i bazuar në ngjarje (Event-based Communication)

**Komunikimi i bazuar në ngjarje** është një model shumë i përdorur në sistemet moderne të shpërndara, ku komponentët komunikojnë duke **dërguar dhe marrë ngjarje (events)** në vend që të thërrasin drejtpërdrejt njëri-tjetrin (si në thirrjen e metodave/RPC).

### Çka është një ngjarje (event)?

Një **event** është një ndryshim i gjendjes ose një veprim që ndodh diku në sistem. Shembuj konkretë:
- *"Përdoruesi u regjistrua"*
- *"Pagesa u krye"*
- *"Porosia u dërgua"*

### Rolet në modelin event-based

- **Producer (Prodhuesi):** krijon dhe dërgon ngjarje.
- **Event Broker (Ndërmjetësi i ngjarjeve):** shpërndan ngjarjet nga prodhuesit te konsumatorët — praktikisht i njëjti rol si broker-i në pub/sub (shembuj: Kafka, RabbitMQ).
- **Consumer (Konsumatori):** merr dhe përpunon ngjarjet.

### Karakteristika kryesore

Komponentët **nuk kanë nevojë të dinë për njëri-tjetrin** — komunikimi është plotësisht i lidhur dobët (**loosely coupled**), pikërisht siç e diskutuam në seksionin 2. Kjo e bën event-based communication praktikisht një zbatim praktik i të gjitha parimeve të komunikimit indirekt të mbuluara në këtë kapitull.

**Shembull ilustrues:** një ngjarje `"UserRegistered"` (Përdoruesi u regjistrua) mund të konsumohet njëkohësisht dhe në mënyrë të pavarur nga disa sisteme të ndryshme:
- sistemi i email-it (dërgon email mirëseardhjeje),
- sistemi i marketingut (regjistron përdoruesin e ri për fushata),
- sistemi i analizës (numëron regjistrimet e reja për statistika).

Asnjë prej këtyre konsumatorëve nuk ndikon apo bllokon tjetrin, dhe prodhuesi i ngjarjes (sistemi i regjistrimit) as nuk i njeh e as nuk pret për ta.

### Ku përdoret komunikimi i bazuar në ngjarje?

- **Mikroshërbime (Microservices)** — komunikim asinkron mes shërbimeve të pavarura.
- **Sisteme me volum të madh të dhënash** (big data pipelines).
- **Aplikacione në kohë reale** (real-time apps).
- **Sisteme IoT** (Internet of Things) — sensorë që gjenerojnë ngjarje vazhdimisht.

---

## 15. Përmbledhje e stileve të komunikimit indirekt

Tabela e mëposhtme përmbledh e krahason katër stilet kryesore të komunikimit indirekt të trajtuara në këtë kapitull, sipas dimensioneve kryesore të diskutuara gjatë gjithë ligjëratës.

| Dimensioni | Komunikimi në grup (Group communication) | Publiko/Abono (Publish/Subscribe) | Mesazhe radhazi (Message Queue) | Memoria e ndarë (Shared/Tuple Space) |
|---|---|---|---|---|
| **Moslidhje në hapësirë** | Pjesore — dërguesi i drejtohet grupit, jo anëtarëve individualë, por shpesh e njeh identitetin e grupit | E plotë — publikuesi dhe abonenti nuk njihen fare, komunikojnë përmes temës/brokerit | E plotë — dërguesi njeh vetëm radhën, jo marrësin final | E plotë — proceset ndajnë vetëm hapësirën, jo identitetin e njëri-tjetrit |
| **Moslidhje në kohë** | Zakonisht jo (kërkohet marrësi aktiv/anëtar në kohën e dërgimit), përveç nëse kombinohet me queue | Pjesore/e plotë, sipas implementimit (nëse brokeri ruan mesazhet) | E plotë — mesazhi ruhet derisa marrësi ta lexojë | E plotë — të dhënat qëndrojnë në hapësirë derisa lexohen/skadojnë |
| **Numri i marrësve** | Shumë (të gjithë anëtarët e grupit) | Shumë (të gjithë abonentët e përputhur) | Zakonisht një (ose një nga një grup konsumatorësh konkurrues) | Një ose disa, sipas modelit të kërkimit (read/take) |
| **Modeli i adresimit** | Sipas grupit (group ID) | Sipas temës/përmbajtjes (topic/content filter) | Sipas emrit të radhës (queue name) | Sipas shabllonit të përputhjes (template matching) |
| **Renditja tipike** | FIFO, shkakore ose totale, sipas nevojës | Zakonisht FIFO për temë të vetme; rrallë totale nëpër tema | FIFO brenda radhës | Nuk aplikohet drejtpërdrejt — varet nga koha e shkrimit/leximit |
| **Shembuj** | JGroups, Spread, Akka (actor clustering) | JMS Topics, Kafka topics, MQTT, Google Pub/Sub | RabbitMQ, JMS Queues, Amazon SQS, WebSphere MQ | JavaSpaces, Linda tuple space |
| **Rasti tipik i përdorimit** | Replikim gjendjeje, koordinim klasterësh, njoftime real-time (dealing room) | Shpërndarje ngjarjesh te shumë konsumatorë të interesuar | Përpunim i punëve/detyrave në mënyrë të besueshme dhe të renditur | Bashkëpunim i lirshëm përmes të dhënave të përbashkëta, pa kanal eksplicit |

---

## Përmbledhje

- **Komunikimi indirekt** lejon proceset të shkëmbejnë mesazhe pa pasur qasje të drejtpërdrejtë njëri-me-tjetrin, duke kaluar përmes një ndërmjetësi (grup, broker, radhë, hapësirë e përbashkët).
- Themeli konceptual i të gjitha teknikave është **moslidhja në hapësirë dhe në kohë** — sa më shumë komponentët të mos e njohin njëri-tjetrin (hapësirë) dhe të mos kenë nevojë të jenë aktivë njëkohësisht (kohë), aq më fleksibël, i shkallëzueshëm dhe rezistent ndaj dështimeve bëhet sistemi — me kosto në vonesë dhe kompleksitet renditjeje.
- **Komunikimi në grup** i drejtohet një bashkësie procesesh si njësi e vetme, dhe klasifikohet sipas: grupeve **të hapura/të mbyllura** (kush mund të dërgojë), grupeve **të mbivendosura/jo-mbivendosura** (a mund të jetë një proces anëtar i disa grupeve), dhe sistemeve **sinkrone/asinkrone**.
- **Broadcast** i dërgon mesazhin të gjithëve pa mbajtur gjurmë marrësish; **multicast** i dërgon mesazhin një grupi specifik të njohur nga sistemi (IP-multicast nuk garanton besueshmëri apo renditje).
- **Renditja e mesazheve** ka tri nivele kryesore: **FIFO** (radhitje sipas dërguesit), **shkakore** (respekton varësinë shkak-pasojë) dhe **totale** (të gjithë e shohin të njëjtin rend absolut) — secila me kosto rritëse.
- Vegla si **JGroups**, **Akka** dhe **Spread** ofrojnë infrastrukturë të gatshme për komunikim të besueshëm në grup, me menaxhim anëtarësie dhe garanci renditjeje.
- **Publish/subscribe** ndan rolet në publikues dhe abonentë, të lidhur vetëm nëpërmjet temës/filtrit; për shkallëzueshmëri përdoret një **rrjet i shpërndarë brokerash** me **rrugëtim të bazuar në filtrim** — çdo broker përcjell ngjarjet vetëm nëpër degët që çojnë drejt abonentëve të interesuar.
- **Message queues** (radhët e mesazheve) ofrojnë komunikim më të strukturuar dhe të besueshëm se pub/sub i thjeshtë, tipikisht një-për-një, mbi standardin **AMQP**.
- **Hapësira e qifteve (tuple space)** dhe **JavaSpaces** realizojnë një formë krejtësisht të ndryshme komunikimi — përmes leximit/shkrimit në një memorie të përbashkët, me operacione `write`, `read` dhe `take`, replikuara për besueshmëri.
- **Komunikimi i bazuar në ngjarje** përgjithëson të gjitha këto ide në modelin producer–broker–consumer, thelbësor për mikroshërbimet, sistemet IoT dhe aplikacionet në kohë reale.

---

## Pyetje për vetë-kontroll

1. Cili është dallimi themelor mes komunikimit të drejtpërdrejtë dhe atij indirekt, dhe cilat janë dy dimensionet e "lidhjes" (coupling) që i dallojnë stilet e ndryshme të komunikimit indirekt?
2. Shpjegoni dallimin mes grupeve të hapura dhe të mbyllura, duke dhënë nga një shembull praktik për secilën.
3. A mund një proces të jetë anëtar i disa grupeve njëkohësisht në grupet e mbivendosura? Pse në sistemet reale kjo është rregull, jo përjashtim?
4. Cili është dallimi mes broadcast dhe multicast, dhe pse IP-multicast konsiderohet "jo i besueshëm"?
5. Jepni një shembull konkret ku renditja FIFO nuk mjafton, por nevojitet renditje shkakore. A nënkupton renditja shkakore automatikisht FIFO? Arsyetoni.
6. Përse nevojitet një rrjet i shpërndarë brokerash në vend të një serveri qendror në arkitekturën publish/subscribe? Cilat janë të metat e serverit qendror?
7. Në pseudokodin e rrugëtimit të bazuar në filtrim, çfarë ndodh kur një broker merr një kërkesë `subscribe` nga një klient kundrejt nga një broker tjetër?
8. Krahasoni sistemet me mesazhe radhazi (message queues) me sistemet publish/subscribe: cili nga të dy ofron zakonisht më shumë besueshmëri dhe pse?
9. Përshkruani operacionet `write`, `read` dhe `take` në një hapësirë qiftesh, dhe pse `take` kërkon një protokoll me dry (lock) kur hapësira është e replikuar.
10. Si e realizon komunikimi i bazuar në ngjarje (event-based communication) parimin e moslidhjes (decoupling), dhe në cilat lloje sistemesh moderne përdoret më shpesh?
