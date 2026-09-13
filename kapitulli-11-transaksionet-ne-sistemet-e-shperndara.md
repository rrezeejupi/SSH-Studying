# Kapitulli 11 — Transaksionet në Sistemet e Shpërndara (Distributed Transactions)

## Hyrje në transaksionet e shpërndara

Në sistemet e shpërndara, shumë klientë mund t'i qasen njëkohësisht të njëjtave të dhëna që ndodhen në një apo më shumë serverë. Kjo qasje e njëkohshme sjell rrezikun që rezultatet e operacioneve të përzihen mes veti, të humbasin, ose baza e të dhënave të mbetet në një gjendje të pasaktë nëse diçka dështon në mes të rrugës (p.sh. rrjeti bie, serveri "rrëzohet", etj.). Për ta zgjidhur këtë problem përdoret koncepti i **transaksionit**.

> **Transaksioni** përcakton një sekuencë operacionesh në server, të cilën serveri e garanton se do të jetë **atomike** — domethënë do të kryhet tërësisht ose fare — edhe në prani të klientëve të shumtë që punojnë njëkohësisht dhe edhe në rast të dështimeve të mundshme të serverit.

Qëllimi themelor i transaksioneve është të sigurojë që **të gjitha objektet e menaxhuara nga një server të mbeten në një gjendje të qëndrueshme (konsistente)**, pavarësisht se sa transaksione të shumta i qasen njëkohësisht atyre objekteve.

Një **transaksion i shpërndarë** është një transaksion i bazës së të dhënave që përfshin dy ose më shumë rrjeta host (server) — pra operacionet e transaksionit nuk kryhen vetëm në një makinë, por shtrihen përtej kufijve të një serveri të vetëm. Bazat e të dhënave janë resursi më i zakonshëm mbi të cilin kryhen transaksione, dhe shpesh një transaksion i vetëm prek disa baza të dhënash njëkohësisht (p.sh. një transaksion bankar që tërheq para nga një degë dhe depoziton në një tjetër).

## Transaksionet e shpërndara dhe operacionet atomike

Transaksionet e shpërndara i referohen transaksioneve **flat (të rrafshëta)** ose **nested (të mbivendosura)** që i qasen objekteve të menaxhuara nga serverë të shumëllojshëm (të ndryshëm).

> "Një transaksion klienti bëhet i shpërndarë nëse i inicon operacionet e tij në disa serverë të ndryshëm."

**Çka është një operacion atomik?**

Operacionet që janë të lira nga ndërhyrja e operacioneve paralele, të cilat janë duke u kryer njëkohësisht në thread-e (rrjedha ekzekutimi) të tjera, quhen **operacione atomike**. Pra, gjatë kryerjes së një operacioni atomik, asnjë operacion tjetër i njëkohshëm nuk mund ta ndërpresë apo ta shohë gjendjen e tij të ndërmjetme.

**Si arrihen operacionet atomike?**

- Përdorimi i metodave të **sinkronizuara** (synchronized) në Java.
- **Mutex** (mutual exclusion) — një mekanizëm i përbashkët i qasjes së përjashtuar, që lejon vetëm një thread/proces në një kohë të hyjë në seksionin kritik të kodit.

## Transaksionet e shpërndara të rrafshëta (flat)

Në një transaksion **të rrafshët (flat)**, klienti bën kërkesa drejt më shumë se një serveri, por gjithmonë **në mënyrë sekuenciale**:

- Klienti i një transaksioni të tillë **e kompleton (përfundon) secilën kërkesë para se të vazhdojë me tjetrën**.
- Si rrjedhojë, transaksioni i qaset objekteve të serverëve njëri pas tjetrit, jo paralelisht.

Kjo do të thotë se e gjithë sekuenca operacionesh brenda transaksionit flat trajtohet si një njësi e vetme, pa hierarki apo nëntransaksione të veçanta — thjesht një varg kërkesash njëra pas tjetrës.

## Transaksionet e shpërndara të mbivendosura (nested)

Për dallim nga transaksionet flat, në një **transaksion të mbivendosur (nested)**:

- Transaksioni i shkallës më të lartë mund të **hapë nën-transaksione** ose degë transaksionesh të nivelit më të ulët.
- Për secilin nën-transaksion të tillë, mund të hapet përsëri një shkallë tjetër me nivel edhe më të thellë.

Kjo krijon një strukturë **pemë (hierarki)** të transaksioneve, ku çdo degë mund të ekzekutohet potencialisht në mënyrë të pavarur (dhe madje paralele), duke rritur shkallën e konkurrencës brenda vetë transaksionit.

### Shembulli i transaksioneve bankare nested

Një klient hap një transaksion rrënjë `T` dhe brenda tij hap katër nën-transaksione:

```
T = openTransaction
    openSubTransaction  T1  →  A.withdraw(10)
    openSubTransaction  T2  →  B.withdraw(20)
    openSubTransaction  T3  →  C.deposit(10)
    openSubTransaction  T4  →  D.deposit(20)
closeTransaction
```

Këtu, `T1`, `T2`, `T3` dhe `T4` janë nën-transaksione të pavarura nga njëri-tjetri (bijë të drejtpërdrejtë të `T`), secili duke kryer nga një operacion mbi llogari të ndryshme (X, Y, Z...). Meqenëse janë nën-transaksione të pavarura, ato mund të ekzekutohen paralelisht, gjë që rrit efikasitetin.

### Shembulli i transaksioneve bankare të shpërndara

Kur nën-transaksionet shtrihen nëpër degë (branch) të ndryshme të serverëve (p.sh. BranchX, BranchY, BranchZ), secili server **bashkohet (join)** si pjesëmarrës (participant) në transaksionin global:

```
T = openTransaction
    a.withdraw(4);     → BranchX, participant A
    c.deposit(4);       → BranchZ, participant C
    b.withdraw(3);     → BranchY, participant B
    d.deposit(3);       → BranchZ, participant D
closeTransaction
```

Çdo degë (BranchX, BranchY, BranchZ) bëhet pjesëmarrëse (participant) e transaksionit të shpërndarë përmes operacionit `join`, dhe koordinimi ndërmjet tyre siguron që `closeTransaction` të prodhojë efekt atomik në të gjitha degët njëkohësisht.

## Transaksioni Debit/Kredit

Transaksioni debit/kredit konsiderohet **shembulli më i njohur i transaksioneve flat**, dhe është unik në dy aspekte — thjeshtësia e tij strukturore dhe përdorimi i gjerë praktik në sistemet bankare.

**Definimi i transaksionit debit/kredit:**

1. Duke pasur një bazë të dhënash me madhësi të mirëpërcaktuar, transaksioni merr një mesazh nga terminali që kërkon të **debitojë** ose **kreditojë** një shumë të caktuar në një llogari.
2. Transaksioni **modifikon shumën** në llogari sipas kërkesës, dhe në fund **inserton në bazën e të dhënave një rresht të ri** që përmban të gjithë parametrat e transaksionit, duke përfshirë edhe datën dhe kohën e kryerjes.
3. **Balanci (gjendja) i ri i llogarisë** kthehet nga programi i aplikacionit dhe i paraqitet shfrytëzuesit përmes një mesazhi.

Ekzistojnë dy variante të këtij transaksioni:

- **Transaksioni bazë** — nuk përmban funksionin `Rollback`; nëse diçka shkon keq, nuk ka mënyrë të kthehet prapa te gjendja fillestare.
- **Transaksioni me funksionin Rollback** — lejon që, në rast dështimi apo gabimi, ndryshimet e bëra të anulohen dhe llogaria të kthehet në gjendjen para transaksionit, duke ruajtur kështu vetinë e atomicitetit.

## Transaksionet nested (të ndërthurura)

Transaksionet nested ofrojnë një **organizim ndryshe nga transaksionet sekuenciale** — ato krijojnë një **hierarki të pjesëve të punës**.

**Shembulli i udhëtimit me aeroplan:** Në vend që të merret një "pikë ruajtjeje" (checkpoint) pas çdo pjese të udhëtimit (p.sh. rezervimi i fluturimit, rezervimi i hotelit, rezervimi i makinës), secili prej këtyre hapave bëhet **veprim i pavarur** (nën-transaksion), i cili mund të përfundojë ose të dështojë pa e detyruar patjetër dështimin e gjithë udhëtimit — ose, nëse dështon, mund të trajtohet vetëm dega përkatëse e prekur.

## Definimi i strukturës së ndërthurjes

Struktura e një transaksioni nested paraqitet si një **pemë transaksionesh**, ku:

- Nën-pemët mund të jenë vetë transaksione **nested ose flat**.
- **Transaksionet gjethe** (leaf) janë gjithmonë flat — ato nuk kanë më nën-transaksione nën vete.
- **Transaksioni rrënjë** (root) quhet niveli më i lartë i hierarkisë; të gjitha të tjerat quhen **nën-transaksione**.
- Një nën-transaksion mund të **përfundojë (commit)** ose të **anulohet (abort)**.
- **Anulimi i një nën-transaksioni shkakton anulimin e të gjitha nën-transaksioneve të tij** (të gjitha degëve nën të, rekurzivisht).

## Tre rregullat e ndërthurjes

Funksionimi korrekt i transaksioneve nested rregullohet nga tre parime themelore:

1. **Commit rule (rregulli i kryerjes)**
   Rezultatet e një nën-transaksioni, pas `commit`-it të tij, bëhen të qasshme **vetëm për transaksionin prind** — por ato **nuk marrin efekt tërësisht (final)** derisa të përfundojnë (commit-ohen) **të gjitha nën-transaksionet e tjera** të hierarkisë deri te rrënja.

2. **Rollback rule (rregulli i kthimit prapa)**
   Nëse një nën-transaksion i cilitdo niveli **anulohet**, atëherë **të gjitha nën-transaksionet e tij anulohen po ashtu**, pavarësisht nga pozicioni i tyre në hierarkinë e ndërthurjes dhe pavarësisht nëse ato kishin përfunduar (commit-uar) tashmë lokalisht. Ky rregull aplikohet **rekurzivisht** teposhtë nëpër tërë hierarkinë e ndërthurjes.

3. **Visibility rule (rregulli i dukshmërisë)**
   Të gjitha ndryshimet e bëra nga një nën-transaksion bëhen të dukshme te transaksioni prind **vetëm pasi ai nën-transaksion të kryejë veprimin (commit)**. Për më tepër, ndryshimet e bëra nga një nën-transaksion **nuk janë të dukshme te "fqinjët" e tij** (nën-transaksionet e tjera në të njëjtin nivel), në rast të ekzekutimit paralel.

## Kontrolli i konkurrencës në transaksionet nested

Për ta shfrytëzuar potencialin e plotë të tyre (paralelizmin), transaksionet nested mbështeten fuqishëm në **kontrollin e konkurrencës (concurrency control)**.

**Çka është kontrolli i konkurrencës?** Kur shumë përdorues i qasen njëkohësisht bazës së të dhënave, operacionet e tyre mbi të dhëna duhet të koordinohen në mënyrë që:

- të parandalohen rezultatet e gabuara, dhe
- të ruhet konsistenca e të dhënave.

Qëllimi është të krijohet **iluzioni** te secili përdorues se ai po i qaset një baze të dhënash të dedikuar vetëm për të, edhe pse në realitet baza ndahet mes shumë përdoruesve njëkohësisht.

### Përfitimet e kontrollit të konkurrencës te transaksionet nested

- **Paralelizimi i transaksioneve** — sa më i madh (kompleks) të jetë një transaksion, aq më shumë paralelizëm mund të ekzistojë brenda ekzekutimit të tij (nën-transaksionet e pavarura mund të punojnë njëkohësisht).
- **Kontrolli i rekuperimit (recovery) ndërmjet transaksioneve** — një nën-transaksion i papërfunduar mund të abortohet dhe të kthehet prapa, **pa shkaktuar efekte te transaksionet e tjera**.
- **Struktura eksplicite e kontrollit** — hierarkia e qartë e nën-transaksioneve e bën më të lehtë menaxhimin dhe monitorimin e ekzekutimit.
- **Modulariteti i sistemit** — nën-transaksionet lehtësojnë një kompozim të thjeshtë dhe të sigurt të një programi kompleks nga pjesë më të vogla.
- **Shpërndarja e implementimit** — mënyra si shpërndahen të dhënat dhe procesimi mes serverëve ka ndikim të madh në eficiencën e përgjithshme të sistemit.

## Përdorimi i transaksioneve të ndërthurura

Përdorimi praktik i transaksioneve nested lidhet ngushtë me parimin e **modularitetit**:

- Një modul i dizajnuar mirë prodhon efekte **vetëm përmes interface-it** të tij.
- Nëse nuk përdoren variabla globale, **nuk do të ketë efekte anësore** — kjo nënkupton që, edhe kur një modul dështon, ai **nuk do të korruptojë** ndonjë strukturë të dhënash që përdoret jashtë tij.
- **Analogji me bazat e të dhënave SQL:** një `UPDATE` brenda një transaksioni mund të paraprihet nga një `INSERT` (i cili mund të dështojë) — nëse `INSERT`-i dështon, vetëm ai pjesë anulohet, pa prishur pjesën tjetër të transaksionit.

**Kombinimi me pika ruajtjeje (savepoints):** Transaksionet nested mund të kombinohen me pika ruajtjeje, ashtu që fillimi i çdo nën-transaksioni të krijojë automatikisht një pikë ruajtjeje (p.sh. S1, S2, S3...). Nëse, për shembull, dështon nën-transaksioni T3, sistemi mund të **kthehet vetëm në pikën e ruajtjes S2**, duke mos i humbur ndryshimet e vlefshme të bëra më parë (te T1 dhe T2) — pra nuk është nevoja të anulohet gjithçka nga fillimi.

## Vetitë ACID

Vetitë **ACID** janë një grup vetish që **garantojnë besueshmërinë** e transaksioneve të bazës së të dhënave. Emri është akronim i katër vetive:

### Atomiciteti (A = Atomicity)

Transaksionet duhet të ndjekin rregullin **"të gjitha ose asgjë"** (all-or-nothing): ose ndodhin **të gjitha** ndryshimet e bëra nga një transaksion, ose **nuk ndodh asnjëra**. Nuk lejohet gjendje e ndërmjetme ku vetëm një pjesë e ndryshimeve është kryer — kjo mbron nga korruptimi i të dhënave në rast dështimi në mes të ekzekutimit.

### Konsistenca (C = Consistency)

Transaksionet gjithnjë operojnë mbi një **pamje konsistente** me bazën e të dhënave dhe **e lënë bazën e të dhënave në një gjendje konsistente** pas përfundimit — d.m.th. çdo rregull, kufizim (constraint) apo relacion logjik mes të dhënave respektohet para dhe pas transaksionit.

### Izolimi (I = Isolation)

Izolimi jep **iluzionin që çdo transaksion ekzekutohet vetëm**, edhe pse në realitet mund të ketë shumë transaksione që ekzekutohen njëkohësisht. Efektet e njërit transaksion nuk duhet të "ndotin" apo të ndikohen nga gjendjet e ndërmjetme (jo-finale) të një transaksioni tjetër që ende nuk ka kryer commit.

### Qëndrueshmëria (D = Durability)

Kjo veti tregon që, **në momentin kur një transaksion kryhet (commit-ohet)**, efektet e tij janë **të garantuara të qëndrojnë** edhe në rast të dështimit pasues (p.sh. rrëzim i serverit, ndërprerje e energjisë). Të dhënat e commit-uara ruhen përherë (zakonisht në disk/log), pavarësisht ngjarjeve të mëvonshme.

### Vështirësitë praktike të ACID në sisteme afatgjata

Aplikacionet moderne me dizajn kompleks kompjuterik shpesh janë me **afat të gjatë ekzekutimi**, dhe ruajtja e vetive tradicionale ACID në transaksione të tilla kërkon **mbyllje (locking) të resurseve për periudha të gjata kohore** — gjë që mund të dëmtojë performancën dhe konkurrencën. Për këtë arsye, disa sisteme përdorin **përgjithësime** të vetive ACID, duke u liruar pjesërisht nga disa kufizime, dhe duke i ripërkufizuar konceptet si më poshtë:

- **Kthimi (Recovery)** — aftësia për ta çuar bazën e të dhënave në një gjendje e cila konsiderohet korrekte në rast dështimi.
- **Pajtueshmëria (Consistency)** — korrektësia e gjendjes së bazës së të dhënave që prodhohet nga një transaksion i kryer.
- **Shikueshmëria (Visibility)** — korrektësia e gjendjes së bazës së të dhënave që prodhohet nga një transaksion i kryer (lidhet me se kur dhe kujt i bëhen të dukshme rezultatet).
- **Përhershmëria (Permanence)** — aftësia e transaksionit për t'i ruajtur përgjithmonë rezultatet e tij në bazën e të dhënave.

## Implementimi i vetive ACID

### Atomiciteti

Implementohet zakonisht përmes:

- **Hapësirës punuese private (private workspace)** — çdo transaksion punon mbi një kopje/hapësirë private të të dhënave, dhe ndryshimet zbatohen mbi bazën e të dhënave reale vetëm pas commit-it.
- **Metodës Whiteahead** (write-ahead — regjistrimi i ndryshimeve para se ato të zbatohen realisht, në mënyrë që të mund të kthehen prapa nëse nevojitet).

### Konsistenca — Serializimi

Konsistenca sigurohet përmes metodës së quajtur **serializim (serializability)**: transaksionet punojnë në mënyrë korrekte nëse rezultati i tyre është **i njëjtë sikur të ishin ekzekutuar në seri**, njëri pas tjetrit, edhe nëse në realitet ekzekutohen njëkohësisht.

Duke supozuar se operacionet mbi bazën e të dhënave janë vetëm **lexo/shkruaj (read/write)** — ku *lexo* merr të dhëna nga baza dhe *shkruaj* modifikon të dhëna në bazë — ekzistojnë tri rregulla themelore të renditjes së konfliktit ndërmjet dy transaksioneve:

| Nr. | Transaksioni 1 | Transaksioni 2 | Rregulli |
|---|---|---|---|
| 1 | Shkruaj | Lexoj | Transaksioni 1 nuk duhet të shkruajë në një objekt që është duke u lexuar nga Transaksioni 2, nëse Transaksioni 1 ndodh më vonë se Transaksioni 2. Kjo kërkon që Transaksioni 1 të jetë më vonë se maksimumi i kohës së leximit të objektit. |
| 2 | Shkruaj | Shkruaj | Transaksioni 1 nuk duhet të shkruajë në një objekt që është duke u shkruar nga Transaksioni 2, nëse Transaksioni 1 ndodh më vonë se Transaksioni 2. Kjo kërkon që Transaksioni 1 të jetë më vonë se maksimumi i kohës së kryerjes së operacionit mbi objektin. |
| 3 | Lexoj | Shkruaj | Transaksioni 1 nuk duhet të lexojë një objekt që është duke u shkruar nga Transaksioni 2, nëse Transaksioni 1 ndodh më vonë se Transaksioni 2. Kjo kërkon që Transaksioni 1 të jetë më vonë se maksimumi i kohës së kryerjes së operacionit mbi objektin. |

Këto rregulla sigurojnë që renditja e operacioneve konfliktuale (lexo/shkruaj mbi të njëjtin objekt) të mos e prishë iluzionin e ekzekutimit serik.

### Izolimi

Izolimi implementohet zakonisht përmes teknikave të **bllokimit (locking)**:

- **Faza e mbylljes (2-Phase Locking — 2PL)** — çdo transaksion kalon nëpër një fazë ku fiton (merr) bllokime dhe një fazë ku i liron ato, pa marrë bllokime të reja pasi ka filluar t'i lirojë.
- **Faza e mbylljes strikte (Strict 2-Phase Locking)** — variant më i rreptë, ku të gjitha bllokimet mbahen deri në momentin e commit-it/abort-it (nuk lirohen gradualisht), duke parandaluar që transaksione të tjera të lexojnë të dhëna "të papërfunduara" (dirty reads).

### Qëndrueshmëria (Durability)

Implementimi i qëndrueshmërisë bëhet zakonisht duke **shkruar të gjitha transaksionet në një dokument (log/regjistër)**, në mënyrë që sistemi të mund të përballojë dështimet. Kur një transaksion përjeton dështim, ose ndodhin dështime harduerike, **sistemi i menaxhimit të bazës së të dhënave (DBMS)** e rishikon atë dokument (log) për të kthyer prapa (ose ripërsëritur) ndryshimet e bëra nga transaksioni, duke rikuperuar gjendjen e saktë.

## Dështimi i transaksioneve dhe rimëkëmbja

Ekzistojnë **dy lloje dështimesh**:

- **Dështime të rastit** (të rastësishme, të përkohshme).
- **Dështime permanente**.

**Procedura e rimëkëmbjes nga dështimet** përbëhet nga tri hapa:

1. **Detektimi i gabimeve** — vërehet se ka ndodhur një gabim.
2. **Kufizimi i dëmtimeve** — izolohet efekti i gabimit që të mos përhapet më tej.
3. **Rimëkëmbja e gabimeve** — sistemi kthehet nga gjendja e gabuar në një gjendje valide, d.m.th. eliminohet vetë gabimi.

**Rimëkëmbja e gabimeve** nënkupton kthimin nga një gjendje e gabuar në një gjendje valide. Ekzistojnë dy qasje kryesore:

- **Rimëkëmbja e gabimeve nga mbrapa (backward error recovery)** — sistemi kthehet në një gjendje të mëparshme të njohur si valide (p.sh. duke përdorur log-un e transaksioneve ose pikat e ruajtjes).
- **Rimëkëmbja e gabimeve nga para (forward error recovery)** — sistemi, nga gjendja me gabime, drejtohet **përpara** në një gjendje të re e cila duhet të jetë valide (pa u kthyer prapa).

## Kritikat ndaj vetive ACID

Pavarësisht rëndësisë së tyre, vetitë ACID nuk janë pa kufizime dhe janë subjekt kritikash:

- Ekzistojnë **interpretime të shumëllojshme** të vetive ACID, dhe ato **nuk janë të vërtetuara në mënyrë matematikore** në mënyrë strikte.
- Definimi i tyre do të ishte shumë më i përdorshëm sikur të realizoheshin me **saktësi matematikore**, pasi ekzistojnë shumë mospërputhje në definicionet bazë të përdorura nga sisteme të ndryshme.
- Në praktikë, mund të kemi transaksione që kanë vetinë e **atomicitetit**, por jo domosdoshmërisht edhe vetinë e plotë të **izolimit**.
  - Një zgjidhje e thjeshtë do të ishte që bazat e të dhënave të kërkonin që transaksionet gjithmonë të përdorin shkallën e **izolimit të serializueshëm (serializable isolation)**.
  - Megjithatë, një faktor kufizues këtu është **performanca**: izolimi i serializueshëm mund të kufizojë ndjeshëm shkallën e bashkëpunimit (konkurrencës) mes transaksioneve; teknika tradicionale si **"Protokolli i kryerjes me dy faza" (Two-Phase Commit)** janë më të shtrenjta krahasuar me thjesht përdorimin e bllokimeve (locks) në bazat e të dhënave.

Kjo është arsyeja pse shumë sisteme moderne të shpërndara (veçanërisht baza të dhënash NoSQL) shpesh ofrojnë vetëm forma të dobësuara (relaxed) të ACID-it (p.sh. konsistencë eventuale — eventual consistency), në këmbim të performancës dhe shkallëzueshmërisë (scalability) më të mira.

## Bllokimi (Locking) i resurseve

**Lock-et (bllokimet)** përdoren për të renditur transaksionet që duan t'i qasen të njëjtit objekt, duke garantuar që vetëm një transaksion në një kohë ta modifikojë (ose lexojë, në rastin e lock-eve ekskluzive) atë objekt.

Megjithatë, **përdorimi i lock-eve mund të shkaktojë deadlock**, veçanërisht kur ndodhin:

- **Minimizimi i resurseve në dispozicion** — pak resurse të lira për shumë transaksione konkurruese.
- **Alokimi i pakontrolluar i resurseve** — resurse që ndahen pa strategji të qartë renditjeje.
- **Cikli i varshmërisë për resurse** — kur transaksione presin njëra-tjetrën në mënyrë rrethore (ciklike).

**Transaksionet e ndërthurura (nested)** kanë veçorinë se **trashëgojnë bllokimet (lock-et)** nga paraardhësit e tyre — d.m.th. nëse transaksioni prind mban një lock, nën-transaksionet e tij e trashëgojnë atë akses.

## Deadlock në transaksionet e shpërndara

**Deadlock** është gjendja ku një grup transaksionesh **kërkojnë resurse** që janë tashmë në posedim të transaksioneve të tjera brenda të njëjtit grup, duke krijuar një pritje rrethore (cikël) pa dalje — asnjë prej tyre nuk mund të vazhdojë.

**Dy lloje deadlock-esh:**

- **Deadlock nga mungesa e resurseve** — kur resurset fizike/logjike janë të pamjaftueshme dhe transaksionet presin njëri-tjetrin ciklikisht për to.
- **Deadlock nga bllokada e komunikimit** — kur transaksione presin mesazhe nga njëri-tjetri, duke krijuar një pritje ciklike në nivel komunikimi.

**Trajtimi i deadlock-ve** bëhet përmes tri strategjive kryesore:

1. **Parandalimi i deadlock** (deadlock prevention).
2. **Anashkalimi i deadlock** (deadlock avoidance).
3. **Detektimi i deadlock** (deadlock detection).

### Metoda për parandalimin e deadlock-ve

Një qasje e përgjithshme është **renditja e resurseve** dhe kufizimi që transaksionet t'u qasen atyre vetëm në rend rritës — kështu shmanget mundësia e ciklit të pritjes. Përveç kësaj, ekzistojnë dy metoda specifike parandalimi, bazuar në kohën e fillimit të proceseve (timestamp):

- **Wait-die:**
  - Nëse një proces **i vjetër** kërkon një resurs që mbahet nga një proces **më i ri**, procesi i vjetër **duhet të presë**.
  - Nëse një proces **i ri** kërkon një resurs që mbahet nga një proces **i vjetër**, procesi i ri **do të përfundohet** (abortohet).

- **Wound-wait:**
  - Nëse një proces **i vjetër** kërkon një resurs që mbahet nga një proces **i ri**, procesi i vjetër **do ta "boshatisë" (preemptojë) procesin e ri** — i cili do të përfundohet dhe më vonë do të rifillojë e do të presë.
  - Nëse, ndërkaq, procesi **më i ri** kërkon një resurs nga procesi **i vjetër**, atëherë procesi i ri **do të vendoset në pritje**.

### Metoda për anashkalimin e deadlock-ve

Në qasjen e anashkalimit, një resurs i delegohet (i jepet) një procesi **vetëm nëse gjendja e sistemit në tërësi konsiderohet e sigurt** (safe state) — pra vetëm nëse ky alokim nuk çon drejt mundësisë së një deadlock-u në të ardhmen.

Kjo qasje **nuk është praktike** në sistemet e shpërndara, sepse:

- Çdo sajt (site) duhet të mbajë gjurmë të **gjendjes globale** të tërë sistemit, gjë që kërkon **kapacitet të madh dhe komunikim intensiv**.
- Procesi i kontrollimit të gjendjes globale duhet të jetë **i përjashtues** (ekskluziv, pra vetëm një kontroll në një kohë).
- Nëse disa sajte kontrollojnë gjendjen njëkohësisht, kjo mund të **ngarkojë rrjetin e komunikimit** dhe të cenojë vetë **saktësinë e vlerësimit global** të sigurisë.

### Metodat për detektimin e deadlock-ëve

Detektimi i deadlock-ut përfshin:

- Krijimin dhe mirëmbajtjen e **grafit "transaction_wait_for"** (grafi i pritjes ndërmjet transaksioneve).
- Kërkimin e **cikleve ekzistuese** brenda atij grafi — një cikël nënkupton deadlock.

Algoritmet e detektimit duhet të plotësojnë dy kushte themelore:

1. **Nuk duhet të kaloj (të lërë pa vërejtur) asnjë deadlock real.**
2. **Nuk duhet të raportojë deadlock të rremë** (false positive).

**Zgjidhja e deadlock-ut**, pasi është detektuar, përfshin zgjedhjen e një ose më shumë transaksioneve **për t'u zbrazur (abortuar)**, duke liruar kështu resurset e tyre në mënyrë që **cikli i pritjes të thyhet**.

### Përparësitë dhe mangësitë e algoritmeve detektuese

**Algoritmet e centralizuara**

- *Përparësi:* Të lehta për menaxhim — një pikë e vetme kontrolli e thjeshton logjikën.
- *Dobësi:* Krijojnë **një pikë të vetme të dështimit** (single point of failure); linjat komunikuese drejt sajtit të kontrollit mund të ngarkohen shumë, sepse ai sajt pranon informacion për gjendjen e të gjitha sajteve të tjera.

**Algoritmet e shpërndara**

- *Përparësi:* Procesi i detektimit të deadlock-ut fillon vetëm nëse dyshohet se një transaksion në pritje mund të jetë pjesë e një cikli deadlock — pra nuk kontrollohet vazhdimisht e tërë rrjeta.
- *Dobësi:* Zgjidhja e deadlock-ut bëhet më e vështirë, sepse i njëjti deadlock mund të detektohet njëkohësisht nga disa sajte të ndryshme (rrezik dyfishimi i veprimit korrigjues).

**Algoritmet hierarkike**

- *Përparësi:* Ofrojnë një zgjidhje optimale duke mos krijuar pikë të vetme dështimi; sajtet nuk preokupohen me detektimin e deadlock-eve në pjesë të sistemit me të cilat nuk kanë lidhje.

## Përmbledhje e vetive ACID

- **Atomiciteti** tregon se ose ndodhin të gjitha ndryshimet e bëra nga një transaksion, ose nuk ndodh asnjëra.
- **Konsistenca** tregon që transaksionet gjithnjë operojnë mbi një pamje konsistente me bazën e të dhënave dhe e lënë atë në gjendje konsistente.
- **Izolimi** jep iluzionin që çdo transaksion ekzekutohet vetëm, i pandikuar nga transaksionet e tjera njëkohshme.
- **Qëndrueshmëria** tregon që, sapo një transaksion kryhet (commit-ohet), efektet e tij garantohen të qëndrojnë edhe në rast dështimi pasues.
- Së bashku, vetitë **ACID garantojnë besueshmërinë** e transaksioneve të bazës së të dhënave.

## Ushtrime

Ushtrimet më poshtë praktikojnë konceptet e **kontrollit të konkurrencës me bllokim (locking)** dhe **deadlock-ut** në transaksione të shpërndara. Në të dyja skenarët, një server menaxhon një grup objektesh/resursesh `a1, a2, …, an` dhe u ofron klientëve dy operacione:

- `lexo(i)` (ose `read(i)`) — lexon vlerën e objektit `ai`.
- `shkruaj(i, vlera)` (ose `write(i, vlera)`) — ruan përmbajtjen `vlera` në objektin `ai`.

### Ushtrimi 1

Konsideroni transaksionet A, B dhe C si në tabelën e mëposhtme (koha rritet nga lart poshtë):

| Koha | A | B | C |
|---|---|---|---|
| 1 | filloTransaksioni | filloTransaksioni | filloTransaksioni |
| 2 | y = Lexo(j) | | |
| 3 | | x = Lexo(k) | |
| 4 | | Shkruaj(i, 55) | |
| 5 | | Shkruaj(j, 54) | Shkruaj(i, 98) |
| 6 | | Commit | |
| 7 | | | |
| 8 | x = Lexo(i) | | |
| 9 | Shkruaj(k, 23) | | |
| 10 | Commit | | Shkruaj(k, 52) |
| 11 | | | Commit |

**Pyetjet:**

1. A duhet procesi B të presë për ta siguruar bllokimin, për leximin `x = Lexo(k)`?
2. A duhet procesi C të presë për ta siguruar bllokimin, për shkrimin `Shkruaj(i, 98)`?
3. A duhet procesi A të presë për ta siguruar bllokimin, për leximin `x = Lexo(i)`?
4. A bëhet commit transaksioni A dhe C? Pse?
5. Nëse gjatë ekzekutimit të programit paraqitet gjendja e deadlock-ut, të tregohet një zgjidhje se si mund të largohemi nga ajo gjendje.

### Ushtrimi 2

Një server i menaxhon resurset `a1, a2, …, an` dhe u ofron përdoruesve dy operacione: `read(i)` — lexon përmbajtjen e resursit numër `i`, dhe `write(i, vlera)` — vendos përmbajtjen `vlera` në resursin numër `i`. Konsideroni transaksionet A, B, C dhe D si në tabelën e mëposhtme:

| Koha | Procesi A | Procesi B | Procesi C | Procesi D |
|---|---|---|---|---|
| 1 | openTransaction | openTransaction | openTransaction | openTransaction |
| 2 | y1 = read(j) | | | |
| 3 | | y2 = read(j) | | |
| 4 | | write(k, 54) | y3 = read(i) | |
| 5 | | write(j, 25) | write(i, 42) | y4 = read(k) |
| 6 | | | | write(k, 52) |
| 7 | | | | y5 = read(i) |
| 8 | | | | |
| 9 | | | write(j, 6) | |
| 10 | write(k, 28) | | commit | |
| 11 | read(k) | | | |
| 12 | write(j, 28) | | | |
| 13 | commit | | | write(k, 55) |
| 14 | | y2 = read(j) | | commit |
| 15 | | commit | | |

**Pyetjet:**

a) A duhet procesi B të presë për ta siguruar bllokimin, për leximin e resursit `j`?

b) A duhet procesi D të presë për ta siguruar bllokimin, për të lexuar resursin `k`?

c) Të tregohet koha kur procesi B arrin ta përfundojë transaksionin; nëse nuk arrin ta përfundojë, të tregohet arsyeja.

d) A duhet procesi A të presë për ta siguruar bllokimin, për të shkruar resursin `k`? Nëse po, të tregohet cilin proces duhet ta presë.

e) A arrijnë ta përfundojnë punën proceset A, C dhe D?

## Përmbledhje

Transaksionet e shpërndara zgjerojnë konceptin klasik të transaksionit të bazës së të dhënave përtej kufijve të një serveri të vetëm, duke garantuar sjellje atomike edhe kur klientë të shumtë e serverë të shumtë ndërveprojnë njëkohësisht. Transaksionet **flat** kryejnë kërkesa sekuenciale te serverë të ndryshëm, ndërsa transaksionet **nested** organizojnë punën si një hierarki (pemë) nën-transaksionesh të pavarura, të cilat mund të ekzekutohen paralelisht dhe të dështojnë/anulohen në mënyrë të izoluar, sipas tri rregullave themelore — **commit**, **rollback** dhe **visibility**. Kjo strukturë mundëson modularitet, rikuperim lokal dhe shkallëzueshmëri më të mirë.

Besueshmëria e çdo transaksioni — flat apo nested — mbështetet mbi katër vetitë **ACID**: **atomicitet**, **konsistencë**, **izolim** dhe **qëndrueshmëri**, të cilat implementohen përmes teknikave si hapësira punuese private, serializimi, bllokimi me dy faza (2PL) dhe regjistrimi (logging) i transaksioneve. Megjithatë, respektimi strikt i ACID-it mund të kufizojë performancën dhe konkurrencën në sisteme afatgjata dhe të shpërndara, prandaj shumë sisteme praktike përdorin versione të dobësuara të këtyre vetive.

Përdorimi i **bllokimeve (locks)** për renditjen e qasjes në resurse është themelor për kontrollin e konkurrencës, por sjell rrezikun e **deadlock-ut** — një gjendje pritjeje ciklike ndërmjet transaksioneve. Deadlock-u trajtohet përmes tri strategjive kryesore: **parandalimi** (p.sh. metodat wait-die dhe wound-wait), **anashkalimi** (jopraktik në shkallë të gjerë për shkak të kostos së monitorimit global) dhe **detektimi** (përmes grafit të pritjes transaction_wait_for), secila me kompromise të veta ndërmjet thjeshtësisë, tolerancës ndaj dështimeve dhe kostos së komunikimit — të ilustruara nga krahasimi i algoritmeve të centralizuara, të shpërndara dhe hierarkike.
