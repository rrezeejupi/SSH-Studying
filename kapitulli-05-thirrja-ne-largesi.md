# Kapitulli 5 — Thirrja në Largësi (Remote Invocation)

*Lënda: Sistemet e Shpërndara — Prof. Dr. Isak Shabani*

## Hyrje

Në sistemet e shpërndara, komponentët e ndryshëm të një aplikacioni (klienti dhe serveri) zakonisht ekzekutohen në procese ose madje në makina të ndryshme, të lidhura përmes rrjetit. Sfida kryesore është si t'i mundësojmë këta komponentë të komunikojnë dhe të bashkëveprojnë ashtu sikur të ndodheshin në të njëjtën hapësirë memorie. **Thirrja në largësi (Remote Invocation)** është tërësia e teknikave, protokolleve dhe mekanizmave që bëjnë të mundur që një program të thërrasë një procedurë ose metodë që ekzekutohet fizikisht në një kompjuter tjetër, duke fshehur nga programuesi kompleksitetin e komunikimit në rrjet.

Ky kapitull shqyrton shtresat softuerike (middleware) që mundësojnë këtë komunikim, protokollin bazë Request-Reply, protokollet e shkëmbimit të RPC-së, gjuhët e përkufizimit të ndërfaqeve (IDL), mekanizmin e Remote Procedure Call (RPC), konceptin e Remote Method Invocation (RMI) me shembuj konkretë në Java, si dhe teknologjitë moderne REST dhe gRPC (me Google Protocol Buffers).

---

## 1. Shtresat e Middleware-it

**Middleware** është një shtresë softuerike që vendoset midis sistemit operativ dhe aplikacioneve, dhe që ndërmjetëson komunikimin dhe integrimin midis aplikacioneve dhe sistemeve të ndryshme që gjenden në rrjet.

- Middleware ofron një nivel programimi **përtej** shkëmbimit të thjeshtë të proceseve dhe pasimit të mesazheve (message passing) të nivelit të ulët — pra e ngre abstraksionin nga "dërgo bajte përmes soketit" në "thirr një metodë në distancë".
- Shtresat e middleware-it bazohen në **protokolle** të mirëpërcaktuara dhe në **programimin e ndërfaqeve të aplikacioneve (API)**.
- Middleware luan rolin e **urës lidhëse** ndërmjet sistemit operativ, bazës së të dhënave dhe aplikacioneve, sidomos në mjedise të lidhura në rrjet.

### Përfitimet nga përdorimi i middleware-it

| Përfitimi | Shpjegimi |
|---|---|
| **Transparenca e lokacionit** | Objektet në largësi (remote) i duken klientit sikur ndodhen në të njëjtën makinë me të, edhe pse fizikisht janë diku tjetër në rrjet. |
| **Protokollet e komunikimit** | Klienti dhe serveri nuk kanë nevojë të dinë nëse middleware-i përdor UDP apo TCP si protokoll themelor; middleware-i mundëson shkëmbimin e të dhënave ndërmjet aplikacioneve dhe sistemeve të ndryshme. |
| **Harduer dhe Sistem Operativ** | Middleware-i fsheh dallimet në përfaqësimin e të dhënave (p.sh. renditja e bajteve — byte order) të shkaktuara nga harduer ose sisteme operative të ndryshme. |
| **Integrim më i mirë i sistemeve** | Lidh aplikacione të zhvilluara në platforma ose gjuhë programimi të ndryshme. |
| **Gjuhët programuese** | Lejon që programi i klientit dhe ai i serverit të shkruhen në gjuhë programimi të ndryshme (p.sh. klienti në Java, serveri në C++), për sa kohë respektojnë të njëjtin protokoll komunikimi. |

Në thelb, middleware-i e bën komunikimin në rrjet **transparent**: programuesi shkruan kod sikur po thërret funksione lokale, ndërsa middleware-i kujdeset për "sipërmarrjen e ndyrë" — paketimin e të dhënave, dërgimin në rrjet, pritjen e përgjigjes dhe trajtimin e gabimeve.

---

## 2. Struktura e Protokollit Request-Reply

Baza e çdo mekanizmi të thirrjes në largësi është një **model komunikimi Kërkesë–Përgjigje (Request–Reply)**: një palë (klienti) dërgon një kërkesë, dhe pala tjetër (serveri) përgjigjet. Ky model përdoret gjerësisht si në rrjete ashtu edhe në sisteme të shpërndara në përgjithësi (RPC, RMI, HTTP, API të ndryshme).

### 2.1 Metodat bazë të protokollit

Në nivelin më të ulët, protokolli Request-Reply zbatohet përmes disa metodave themelore:

```java
public byte[] kryejOperacionin(RemoteRef s, int operationId, byte[] arguments)
```
Kjo metodë dërgon mesazhin e kërkesës tek serveri në distancë dhe kthen përgjigjen si rezultat. Parametri `arguments` përfshin të dhëna që përcaktojnë:
- serverin në distancë që synohet,
- operacionin (metodën) që do të thirret, dhe
- argumentet aktuale të atij operacioni.

```java
public byte[] merrKerkesen()
```
Kjo metodë përdoret nga ana e **serverit**: e merr kërkesën e klientit përmes portit të serverit.

```java
public void dergoPergjigje(byte[] pergjigja, InetAddress klientHost, int klientPort)
```
Kjo metodë e dërgon mesazhin e përgjigjes tek klienti, duke përdorur adresën e tij IP (`klientHost`) dhe portin (`klientPort`) nga i cili erdhi kërkesa.

Këto tri metoda tregojnë simetrinë e protokollit: klienti "kryen operacionin" (dërgon + pret përgjigje), ndërsa serveri "merr kërkesën" dhe pastaj "dërgon përgjigjen".

### 2.2 Struktura e mesazhit Request (Kërkesë)

Një mesazh kërkese (request) zakonisht përmban këto fusha:

- **Client ID** — identifikon se kush po e dërgon kërkesën.
- **Request ID** — një identifikues unik për këtë kërkesë specifike (i domosdoshëm sepse mund të ketë shumë kërkesa paralele njëkohësisht).
- **Operacioni (Method/Operation)** — çfarë kërkohet të kryhet (p.sh. `read`, `write`, `GET`).
- **Parametrat** — të dhënat hyrëse që i nevojiten operacionit.
- **Timestamp** *(opsional)* — koha e dërgimit të kërkesës.
- **Header** *(opsional)* — metadata shtesë, si autentikim (auth), format i të dhënave, etj.

Në formë të përgjithshme:

```
Request = { ClientID, RequestID, Operation, Parameters, Timestamp }
```

### 2.3 Struktura e mesazhit Reply (Përgjigje)

Serveri kthen një përgjigje që përmban:

- **Request ID** — përdoret për ta lidhur përgjigjen me kërkesën përkatëse.
- **Statusi** — tregon nëse operacioni ishte i suksesshëm apo dështoi (p.sh. `OK`, `ERROR`).
- **Rezultati (Result/Data)** — të dhënat e kërkuara nga klienti.
- **Error** *(nëse ka ndodhur gabim)* — informacion shtesë për natyrën e gabimit.
- **Timestamp** *(opsional)*.

Në formë të përgjithshme:

```
Reply = { RequestID, Status, Result, Error, Timestamp }
```

### 2.4 Lidhja mes Request dhe Reply

Elementi kyç që bashkon një kërkesë me përgjigjen e saj është **Request ID**:

- Çdo `Request ID` është unik dhe shërben si çelës lidhës.
- Në parim, çdo **një Request** gjeneron **një Reply**.
- Meqë mund të ekzistojnë njëkohësisht shumë kërkesa paralele (nga i njëjti klient ose nga klientë të ndryshëm), identifikuesit e kërkesave janë të domosdoshëm që serveri dhe klienti të mos i ngatërrojnë përgjigjet me njëra-tjetrën.

**Shembull ilustrues** (p.sh. një kërkesë bankare):

```
Request:
{ ID: 25, Operation: "getBilanci", Llogaria: 123 }

Reply:
{ ID: 25, Status: "OK", Bilanci: 500€ }
```

Këtu `ID: 25` në përgjigje tregon qartë se kjo është përgjigja që i korrespondon pikërisht kërkesës me `ID: 25`.

---

## 3. Protokollet Shkëmbyese (Exchange) të RPC-së

Kur zbatohet mekanizmi i RPC-së mbi rrjet, ekzistojnë tri variante bazë protokolli, të njohura si protokolle **R, RR** dhe **RRA**, që përcaktojnë sa mesazhe shkëmbehen dhe çfarë niveli garancie ofrojnë:

- **R = Protokolli Request** — klienti dërgon vetëm kërkesën, pa pritur asnjë përgjigje eksplicite (përdoret kur rezultati nuk është i domosdoshëm, p.sh. operacione "fire-and-forget").
- **RR = Protokolli Request-Reply** — modeli klasik: klienti dërgon kërkesën dhe pret detyrimisht përgjigjen nga serveri. Ky është protokolli më i përdorur.
- **RRA = Protokolli Request-Reply-Acknowledge** — pas marrjes së përgjigjes, klienti dërgon një konfirmim shtesë (acknowledge) tek serveri, duke i thënë "e mora përgjigjen". Kjo shton një shkallë më të lartë sigurie që mesazhet nuk humbin, në kurriz të një trafiku shtesë në rrjet.

Zgjedhja e njërit prej këtyre tri protokolleve varet nga bilanci që kërkohet mes efikasitetit (më pak mesazhe) dhe besueshmërisë (më shumë garanci se komunikimi ka kaluar me sukses).

---

## 4. Mesazhi i Kërkesës dhe Përgjigjes në HTTP

**HTTP (HyperText Transfer Protocol)** është shembulli më i njohur dhe më i përdorur praktikisht i një protokolli Request-Reply. Çdo transaksion HTTP përbëhet nga një mesazh kërkese (HTTP Request) i dërguar nga klienti (p.sh. shfletuesi ose një aplikacion) dhe një mesazh përgjigjeje (HTTP Reply/Response) i kthyer nga serveri.

Struktura e përgjithshme përputhet plotësisht me modelin e mësipërm Request-Reply:

- **Kërkesa HTTP** përmban një metodë (p.sh. `GET`, `POST`, `PUT`, `DELETE`), një URL/resurs, header-a (metadata si tipi i përmbajtjes, autentikimi) dhe, opsionalisht, një trup (body) me të dhëna.
- **Përgjigja HTTP** përmban një kod statusi (p.sh. `200 OK`, `404 Not Found`, `500 Internal Server Error`), header-a, dhe një trup me rezultatin e kërkuar.

Kjo tregon se HTTP nuk është thjesht një protokoll për faqe interneti, por një realizim konkret dhe standard i modelit Request-Reply, çka e bën bazën edhe për arkitekturën REST dhe për shumë API-të moderne të internetit, siç do të shihet më poshtë.

---

## 5. Gjuhët e Përkufizimit të Ndërfaqes — IDL (Interface Definition Languages)

Që klienti dhe serveri (të cilët mund të jenë shkruar në gjuhë programimi të ndryshme) të kuptohen mes vete, nevojitet një përshkrim formal, i pavarur nga gjuha, i ndërfaqes që serveri e ofron. Ky përshkrim quhet **IDL**.

### 5.1 Çfarë është IDL

**IDL (Interface Definition Language)** është një specifikim formal që përkufizon një modul/objekt në largësi, duke përcaktuar:

- **Parametrat** e metodave,
- **Argumentet** (dhe drejtimin e tyre — hyrës `in`, dalës `out`),
- **Tipet** e të dhënave që përdoren.

IDL siguron një përkufizim të objekteve në largësi që mund të përdoret nga çdo gjuhë programimi — pra IDL nuk është vetë gjuhë ekzekutimi, por një "kontratë" e pavarur. Që të funksionojë mekanizmi kërkesë-përgjigje, ndërfaqet e klientit dhe serverit duhet të jenë në përputhje të plotë me një IDL të përbashkët.

### 5.2 Shembull me CORBA IDL

```idl
struct Person {
    string emri;    // Fusha që ruan emrin e personit (tip string)
    string vendi;   // Fusha që ruan vendin e personit (tip string)
    long viti;      // Fusha që ruan vitin (p.sh. viti i lindjes)
};                  // Fundi i deklarimit të strukturës Person

interface PersonList {                             // Deklarohet një ndërfaqe me emrin PersonList
    readonly attribute string emriListes;           // Atribut vetëm për lexim që ruan emrin e listës
    void shtoPersonin(in Person p);                 // Shton një person në listë (parametri hyn vetëm si input)
    void lexoPersonin(in string emri, out Person p);// Kërkon person sipas emrit dhe e kthen si rezultat në p
    long numri();                                   // Kthen numrin total të personave në listë
};                                                   // Fundi i deklarimit të ndërfaqes PersonList
```

**Shpjegim:** `struct Person` përkufizon një tip të dhëne të përbërë (emri, vendi, viti). Ndërfaqja `PersonList` përkufizon operacionet e mundshme mbi një listë personash: shtimin e një personi të ri (`shtoPersonin`, ku `in` do të thotë se `Person p` kalohet si parametër hyrës), kërkimin e një personi sipas emrit (`lexoPersonin`, ku `out` do të thotë se `Person p` kthehet si rezultat), dhe marrjen e numrit total të personave (`numri`). Kjo ndërfaqe mund të përdoret nga një klient i shkruar, për shembull, në C++ ose Java, pa e ditur në çfarë gjuhe është shkruar serveri — vetëm duke respektuar këtë kontratë IDL.

---

## 6. Thirrja në Distancë përmes Shërbimeve RPC

**RPC (Remote Procedure Call)** mundëson që një program të thërrasë një procedurë të vendosur në një kompjuter tjetër, sikur ajo procedurë të ishte lokale — duke fshehur nga programuesi paketimin e të dhënave dhe komunikimin në rrjet.

### 6.1 Hapat e ekzekutimit të një thirrjeje RPC

1. **Klienti thërret funksionin lokal** — programi klient thërret një funksion sikur të ishte lokal, pa e ditur se në fakt ekzekutimi ndodh në një server tjetër.
2. **Stub-i i klientit (client stub)** — stub-i e merr këtë thirrje dhe kryen **marshalling**, d.m.th. paketimin e parametrave në një format të përshtatshëm për transmetim në rrjet (p.sh. serializimin e tyre në bajte).
3. **Dërgimi i kërkesës në rrjet** — kërkesa e paketuar dërgohet përmes rrjetit drejt serverit, duke përdorur protokolle komunikimi (TCP/UDP).
4. **Stub-i i serverit (server stub)** — pasi mesazhi mbërrin, stub-i i serverit kryen **unmarshalling** (shpaketimin e të dhënave) dhe ia kalon parametrat procedurës reale që do të ekzekutohet.
5. **Ekzekutimi në server** — funksioni/procedura reale ekzekutohet në anën e serverit.
6. **Kthimi i përgjigjes** — rezultati i marrë paketohet (marshalling) nga serveri dhe dërgohet përsëri te klienti.
7. **Marrja e rezultatit nga klienti** — stub-i i klientit e shpaketon (unmarshalling) përgjigjen dhe ia dorëzon programit klient si rezultat "normal", sikur të kishte ardhur nga një thirrje lokale funksioni.

Ky proces tregon idenë qendrore të RPC-së: **transparenca** — programuesi klient nuk e sheh kompleksitetin e rrjetit; ai shkruan kod si për një thirrje funksioni të zakonshëm, ndërsa stub-et (klient dhe server) kujdesen për gjithë "magjinë" e komunikimit.

### 6.2 Semantikat e thirrjes në distancë (Fault Tolerance)

Meqenëse komunikimi në rrjet mund të dështojë (mesazhe që humbin, vonesa, dyfishim mesazhesh), RPC-ja duhet të vendosë çfarë **semantike thirrjeje** do të ofrojë ndaj gabimeve. Kjo varet nga tri masa tolerance ndaj gabimeve:

| Ritransmeto kërkesën? | Filtrimi i dyfishimeve? | Ri-ekzekutimi / retransmetimi | Semantika e thirrjes |
|---|---|---|---|
| Jo | Jo e aplikueshme | Jo e aplikueshme | **Ndoshta** (maybe) |
| Po | Jo | Ri-ekzekuto procedurën | **Së-paku-njëherë** (at-least-once) |
| Po | Po | Retransmeto vetëm përgjigjen | **Më-së-shumti-njëherë** (at-most-once) |

Shpjegim i tri semantikave:

- **"Ndoshta" (maybe):** nuk ka asnjë ritransmetim. Nëse mesazhi humbet, thirrja thjesht dështon në heshtje — procedura mund të mos jetë ekzekutuar fare, ose mund të jetë ekzekutuar dhe vetëm përgjigja u humb. Klienti nuk ka garanci.
- **"Së-paku-njëherë" (at-least-once):** klienti ritransmeton kërkesën nëse nuk merr përgjigje në kohë, por serveri nuk i filtron dyfishimet — kjo do të thotë se procedura mund të ekzekutohet **më shumë se një herë** (rrezik veçanërisht për operacione jo-idempotente, si "transfero 100€").
- **"Më-së-shumti-njëherë" (at-most-once):** kombinon ritransmetimin me filtrimin e mesazheve të dyfishuara (serveri i njeh kërkesat e përsëritura përmes Request ID-së) dhe, në vend që ta ri-ekzekutojë procedurën, thjesht **ritransmeton përgjigjen** e ruajtur më parë. Kjo garanton se procedura ekzekutohet **jo më shumë se një herë**, gjë që është zgjidhja më e sigurt për operacione që nuk duhet të përsëriten.

### 6.3 Roli i procedurave fundore (stub-eve) të klientit dhe serverit

Arkitektura e brendshme e një thirrjeje RPC përfshin këta komponentë kryesorë, të organizuar simetrikisht në të dyja anët:

```
Procesi Klient                                    Procesi Server
────────────────                                  ────────────────
programi klient                                   procedura e shërbimit
      │                                                    ▲
klient stub (procedura)  ──── Kërkesa ────►   dispatcher ──┘
      │                                            │
Moduli komunikues       ◄──── Përgjigjia ────  serveri stub (procedura)
                                                    │
                                            Moduli komunikues
```

- **Klient stub (procedura)**: paraqitet ndaj programit klient si një procedurë lokale e zakonshme; në realitet kryen marshalling të parametrave dhe ia kalon Modulit komunikues.
- **Moduli komunikues**: në të dyja anët, kujdeset për dërgimin/marrjen aktuale të mesazheve nëpër rrjet (implementon protokollin Request-Reply të përshkruar më sipër).
- **Dispatcher**: në anën e serverit, merr mesazhin e ardhur dhe vendos se cilës procedurë shërbimi i takon të ekzekutohet (bazuar në identifikuesin e operacionit).
- **Serveri stub (procedura)**: kryen unmarshalling të parametrave dhe thërret procedurën aktuale të shërbimit; pastaj paketon rezultatin për kthim.
- **Procedura e shërbimit**: kodi real që ekzekuton logjikën e kërkuar në server.

---

## 7. Ndërfaqja e Fajllave në Sun XDR (Shembull i RPC-së praktike)

**Sun XDR (eXternal Data Representation)** është një shembull historik i njohur i IDL-së dhe i RPC-së, i përdorur për protokollin NFS (Network File System). Më poshtë është një shembull i një ndërfaqeje për lexim/shkrim fajlla:

```c
const MAX = 1000;
typedef int FileIdentifier;
typedef int FilePointer;
typedef int Length;

struct Data {
    int length;
    char buffer[MAX];
};

struct writeargs {
    FileIdentifier f;
    FilePointer position;
    Data data;
};

struct readargs {
    FileIdentifier f;
    FilePointer position;
    Length length;
};

program FILEREADWRITE {
    version VERSION {
        void WRITE(writeargs) = 1;
        Data READ(readargs) = 2;
    } = 2;
} = 9999;
```

**Shpjegim:**
- `FileIdentifier`, `FilePointer` dhe `Length` janë alias (pseudonime) për tipin `int`, të përdorur për ta bërë kodin më të kuptueshëm semantikisht.
- `Data` përfaqëson një bllok të dhënash me gjatësi ndryshuese, të ruajtura në një buffer me madhësi maksimale `MAX`.
- `writeargs` grupon parametrat e nevojshëm për operacionin `WRITE`: identifikuesin e fajllit, pozicionin ku do të shkruhet, dhe vetë të dhënat.
- `readargs` grupon parametrat për operacionin `READ`: identifikuesin e fajllit, pozicionin dhe gjatësinë e të dhënave që kërkohen.
- Blloku `program FILEREADWRITE { version VERSION { ... } = 2; } = 9999;` përkufizon programin RPC me numër identifikues `9999` dhe versionin `2`, brenda të cilit deklarohen dy procedurat e largëta: `WRITE` (numër procedure `1`) dhe `READ` (numër procedure `2`). Këta numra identifikues përdoren nga dispatcher-i i serverit për të zgjedhur procedurën e duhur kur mbërrin një kërkesë.

Ky shembull tregon se si IDL përdoret në praktikë për të gjeneruar automatikisht stub-et e klientit dhe serverit për një shërbim konkret RPC.

---

## 8. Thirrjet Lokale kundrejt Thirrjeve në Largësi

Në një sistem të shpërndarë me objekte, disa thirrje metodash mbeten **lokale** (brenda të njëjtit proces), ndërsa disa të tjera bëhen **në largësi** (drejt një procesi/objekti tjetër në rrjet). Për shembull, nëse kemi objektet A, B, C, D, E, F të shpërndara në disa procese:

- Objekti **A** mund të bëjë një **thirrje në distancë** drejt objektit **B**.
- **B**, nga ana e vet, mund të bëjë një **thirrje lokale** drejt **C** (të njëjtit proces) dhe një **thirrje lokale** drejt **D**.
- **D** mund të bëjë përsëri një **thirrje në distancë** drejt **F**, ndërsa **F** komunikon lokalisht me **E**.

Ky model tregon se brenda një aplikacioni të shpërndarë, zinxhiri i thirrjeve kalon në mënyrë transparente midis kufijve lokalë dhe atyre në rrjet — dallimi mes një thirrjeje lokale dhe një thirrjeje në largësi është i padukshëm për programuesin, sepse middleware-i (proxy-të, stub-et) e menaxhon këtë ndryshim në sfond.

---

## 9. Objekti në Distancë dhe Ndërfaqja e tij

Një **objekt në largësi (remote object)** përbëhet konceptualisht nga dy pjesë:

1. **Ndërfaqja në largësi (remote interface)** — lista publike e metodave (p.sh. m1, m2, m3) që mund të thirren nga jashtë, nga një klient në një proces tjetër. Kjo është "fasada" e vetme e dukshme për botën e jashtme.
2. **Implementimi i brendshëm** — përmban të dhënat (state) e objektit dhe implementimin real të metodave (p.sh. m1–m6, ku disa metoda si m4, m5, m6 mund të jenë krejtësisht të brendshme/private dhe të padukshme nga jashtë).

Klientët e jashtëm mund të thërrasin **vetëm** metodat e ekspozuara në ndërfaqen në largësi; ata nuk kanë qasje të drejtpërdrejtë në të dhënat e brendshme apo në metodat private të objektit. Kjo respekton parimin themelor të **enkapsulimit**, i zgjeruar tani në shkallë rrjeti: objekti në largësi mbron gjendjen e tij të brendshme dhe lejon ndërveprim vetëm përmes kontratës publike të përcaktuar nga ndërfaqja.

---

## 10. Instancimi i Objekteve në Largësi

Është e rëndësishme të dallohet **klasa** nga **objekti**:
- **Klasa** është një përkufizim ose model që përshkruan strukturën dhe sjelljen e mundshme.
- **Objekti** është një instancë konkrete e asaj klase, që mban të dhëna specifike (gjendje).

Në sistemet e shpërndara, një objekt mund të ndodhet në një proces ose hapësirë tjetër nga ajo e programit që dëshiron ta thërrasë. Në raste të tilla, objekti në largësi mund të **mos ekzistojë ende** në momentin e kërkesës dhe duhet:

- të **krijohet (instancohet)** në atë moment, ose
- të **ngarkohet** nëse ekziston tashmë por është joaktiv, ose
- të fillohet një **proces i ri** që do ta përmbajë dhe menaxhojë atë objekt.

Për këtë arsye, sistemet me objekte në largësi duhet të ofrojnë mekanizma specifikë (si **aktivizimi/activation**) që mundësojnë krijimin automatik të objekteve edhe nëse ato nuk ekzistojnë ende në momentin kur bëhet kërkesa e klientit — pikë që do të rishfaqet më vonë te klasa `Activatable` e Java RMI.

---

## 11. Roli i Proxy, Dispatcher dhe Skeleton

Tri komponentë kyç realizojnë mekanizmin e thirrjes së metodave në largësi në anën e klientit dhe të serverit:

- **Proxy** — një element **lokal** (te klienti) që "përfaqëson" një objekt të largët. Klienti thërret metoda mbi proxy-n sikur ai të ishte vetë objekti real; proxy-ja fsheh faktin që thirrja në fakt shkon në rrjet.
- **Dispatcher (dispeçer)** — ndodhet në anën e **serverit**. Ai merr/pranon mesazhin e kërkuar nga Moduli i Komunikimit dhe përdor `methodID`-në (identifikuesin e metodës) të përfshirë në kërkesë për të zgjedhur/lokalizuar metodën e duhur brenda skeletonit.
- **Skeleton** — një element **në largësi** (te serveri) që pranon thirrjen e ardhur nga rrjeti (nga proxy-ja e klientit) dhe e lidh/kanalizon atë drejt objektit real të largët, duke thirrur metodën konkrete mbi të dhe duke kthyer rezultatin.

Skema logjike e rrjedhës: **Klienti → Proxy → (rrjeti) → Dispatcher → Skeleton → Objekti real i largët**, dhe përgjigja kthehet në drejtim të kundërt. Kjo ndarje rolesh është pikërisht ajo që mundëson **transparencën e thirrjes**: klienti "flet" vetëm me proxy-n, lokalisht, ndërsa proxy-ja, dispatcher-i dhe skeleton-i bashkëpunojnë për të përcjellë thirrjen deri te objekti real.

---

## 12. Metoda e Thirrjes në Largësi — Remote Method Invocation (RMI)

**RMI (Remote Method Invocation)** është përgjithësimi i konceptit të RPC-së në paradigmën e orientuar nga objekti.

Konceptet themelore:

- Objektet që mund të marrin/pranojnë kërkesa në largësi për shërbime quhen **"Remote Objects"** (objekte në largësi).
- Këto objekte duhet të kenë një mënyrë për t'u aksesuar përmes një **remote reference** (referencë në largësi) — një lloj "adrese" që identifikon objektin e largët në mënyrë unike në rrjet.
- Për të thirrur (invoke) një metodë, parametrat e saj duhet të përcaktohen përmes një **ndërfaqeje në largësi (remote interface)** — e njëjta ide si IDL-ja e diskutuar më sipër, por tani e integruar direkt në gjuhën e programimit.
- Të gjitha këto teknologji të kombinuara (remote objects, remote references, remote interfaces) quhen kolektivisht **Remote Method Invocation (RMI)**.

**RMI** është pra mekanizmi që mundëson që një objekt në një kompjuter (klient) të thërrasë metoda të një objekti që ndodhet në një kompjuter tjetër (server), sikur ato metoda të ishin lokale.

**Java RMI** është realizimi konkret i këtij koncepti, specifik për gjuhën Java. Ai bazohet në konceptin e objekteve të shpërndara, ku komunikimi realizohet përmes **stub-eve** (ndërmjetësve, ekuivalent me proxy-në) dhe **skeleton-eve**, që merren përkatësisht me dërgimin dhe marrjen e të dhënave nëpër rrjet — pikërisht ashtu siç u përshkrua te seksioni i Proxy/Dispatcher/Skeleton.

---

## 13. Klasa `Naming` e Java RMI Registry

`java.rmi.Naming` është një klasë ndihmëse në Java RMI që shërben si **ndërmjetës** për të regjistruar, gjetur dhe çregjistruar objekte në largësi brenda **RMI Registry** (një shërbim emrash që lidh emra simbolikë me referenca të objekteve remote, ngjashëm me një "libër telefonik" për objektet e largëta).

Metodat kryesore të klasës `Naming`:

| Metoda | Përshkrimi |
|---|---|
| `void rebind(String name, Remote obj)` | Regjistron/identifikon objektin në largësi me një emër të caktuar nga serveri. Nëse emri ekziston tashmë, e **zëvendëson** lidhjen ekzistuese. |
| `void bind(String name, Remote obj)` | Përdoret në mënyrë alternative nga serveri për të regjistruar objektin me emër, por **gjeneron gabim** nëse emri i është dhënë tashmë një reference tjetër. |
| `void unbind(String name, Remote obj)` | Heq (largon) lidhjen (binding) e një objekti nga regjistri. |
| `Remote lookup(String name)` | Përdoret nga **klientët** për të kërkuar objektin në largësi përmes emrit të tij; kthen referencën e objektit remote. |
| `String[] list()` | Kthen një varg (array) me emrat e të gjitha objekteve aktualisht të lidhura (bind) në regjistër. |

Këto pesë metoda formojnë kompletisht ciklin e jetës së një objekti remote në registry: regjistrimi (`bind`/`rebind`), kërkimi (`lookup`), heqja (`unbind`), dhe inventarizimi (`list`).

---

## 14. Klasat që Mbështesin Java RMI

Java RMI ndërton mbi një hierarki klasash/ndërfaqesh që përcaktojnë natyrën e objekteve në largësi:

```
                    ObjektiRemote (Remote — ndërfaqja bazë)
                            │
                    ServeriRemote (RemoteServer — klasa abstrakte bazë)
                       /              \
        E Aktivizueshme        ObjektiUnikastRemote
        (Activatable)           (UnicastRemoteObject)
                                        │
                              <klasa e shërbyesit>
```

- **`Remote`** është ndërfaqja markues (marker interface) bazë që çdo ndërfaqe remote duhet ta trashëgojë, për të treguar se metodat e saj mund të thirren nga largësia.
- **`RemoteServer`** është klasa abstrakte prej së cilës trashëgojnë të gjitha implementimet konkrete të objekteve serveri në largësi.
- **`Activatable`** përfaqëson objekte që **mund të aktivizohen** (instancohen) automatikisht "on-demand", pikërisht siç u diskutua te seksioni i instancimit të objekteve në largësi — objekti mund të mos jetë aktiv derisa vjen kërkesa e parë për të.
- **`UnicastRemoteObject`** është implementimi më i zakonshëm dhe më i thjeshtë: objekti krijohet dhe qëndron "gjallë" derisa procesi i serverit është aktiv, dhe çdo thirrje remote drejtohet tek pikërisht ky instancim unik (unicast) i objektit.
- Klasa e shërbyesit konkret (**servant**) e aplikacionit trashëgon zakonisht nga `UnicastRemoteObject`, siç do të shihet në shembujt e mëposhtëm.

---

## 15. Rast studimi: Ndërfaqet Remote `Shape` dhe `ShapeList` (Detyrë me Java RMI)

Ky rast studimi ndërton, hap pas hapi, një aplikacion tipik RMI: një server që menaxhon një listë objektesh grafike (forma), dhe një klient që i qaset kësaj liste nga distanca.

### 15.1 Kërkesa e detyrës

**(a)** Të shkruhen dy ndërfaqe të përdorura në Java RMI:
- **`Shape`** — përfaqëson një objekt individual grafik, me metoda për marrjen e versionit (`getVersion`) dhe marrjen e gjendjes së plotë të objektit (`getAllState`).
- **`ShapeList`** — përfaqëson një koleksion objektesh `Shape`, me metoda për krijimin e objekteve të reja (`newShape`), marrjen e listës së objekteve (`allShapes`) dhe kontrollimin e versionit (`getVersion`).

Të dyja ndërfaqet duhet të trashëgojnë `Remote`, çka i bën të përdorshme në RMI, dhe çdo metodë e tyre duhet të deklarojë `throws RemoteException` (sepse çdo thirrje në rrjet mund të dështojë për arsye komunikimi). Këto ndërfaqe formojnë **kontratën** mes klientit dhe serverit: serveri i implementon (si *servant*), ndërsa klienti i përdor për të thirrur metoda remote sikur të ishin lokale.

**(b)** Të krijohet klasa servant (implementimi i serverit), e cila menaxhon një listë objektesh `Shape` dhe trashëgon nga `UnicastRemoteObject` (që e bën objektin të aksesueshëm nga distanca). Përdoret një `Vector theList` për të ruajtur objektet e krijuara, dhe një variabël `version` për të gjurmuar ndryshimet.

**(c)** Të shkruhet një klient RMI që lidhet me serverin në një host të caktuar (p.sh. `bruno`), kërkon objektin remote `ShapeList` përmes RMI Registry (`Naming.lookup`), dhe thërret metodën `allShapes()` për të marrë të dhënat.

### 15.2 Ndërfaqet remote `Shape` dhe `ShapeList`

```java
import java.rmi.*;           // Importon të gjitha klasat e nevojshme për RMI (Remote Method Invocation)
import java.util.Vector;     // Importon klasën Vector për ruajtjen e koleksioneve të objekteve

public interface Shape extends Remote { // Ndërfaqja Shape trashëgon Remote — objektet e saj përdoren në distancë
  int getVersion() throws RemoteException;
    // Kthen versionin e objektit Shape; mund të hedhë përjashtim në rast gabimi komunikimi
  GraphicalObject getAllState() throws RemoteException;
    // Kthen gjendjen e plotë të objektit (GraphicalObject); mund të hedhë RemoteException
}

public interface ShapeList extends Remote { // Ndërfaqja ShapeList menaxhon një listë objektesh Shape në distancë
  Shape newShape(GraphicalObject g) throws RemoteException;
    // Krijon një objekt të ri Shape nga një GraphicalObject dhe e kthen atë (në distancë)
  Vector allShapes() throws RemoteException;
    // Kthen një koleksion (Vector) me të gjitha objektet Shape
  int getVersion() throws RemoteException;
    // Kthen versionin e listës së objekteve Shape
}
```

Vini re modelin që përsëritet gjithmonë te ndërfaqet remote në Java: **trashëgimi nga `Remote`** + **çdo metodë deklaron `throws RemoteException`**. Kjo e dyta është e domosdoshme sepse, ndryshe nga një thirrje lokale, një thirrje remote mund të dështojë për shkaqe krejt të jashtme për logjikën e programit (rrjeti bie, serveri nuk përgjigjet, etj.), dhe Java e detyron programuesin ta trajtojë këtë mundësi eksplicitisht.

### 15.3 Serveri — `ShapeListServer` (me metodën `main`)

```java
import java.rmi.*; // Importon klasat nga paketa java.rmi që nevojiten për komunikim RMI

public class ShapeListServer{
  public static void main(String args[]){
    System.setSecurityManager(new RMISecurityManager());
      // Vendos një SecurityManager për RMI (kontrollon sigurinë gjatë komunikimit në rrjet)
    try{
      ShapeList aShapeList = new ShapeListServant();
        // Krijon një objekt të klasës ShapeListServant — objekti remote që do të publikohet
      Naming.rebind("Shape List", aShapeList);
        // Regjistron objektin remote në RMI Registry me emrin "Shape List" (e zëvendëson nëse ekziston)
      System.out.println("ShapeList server ready");
    }
    catch(Exception e) {
      System.out.println("ShapeList server main " + e.getMessage());
        // Printon mesazhin e gabimit për diagnostikim
    }
  }
}
```

### 15.4 Servanti — `ShapeListServant` (implementon `ShapeList`)

```java
import java.rmi.*;
import java.rmi.server.UnicastRemoteObject; // Klasa që bën objektin të aksesueshëm përmes rrjetit
import java.util.Vector;

public class ShapeListServant extends UnicastRemoteObject implements ShapeList {
  // Trashëgon nga UnicastRemoteObject -> e bën objektin të aksesueshëm në RMI
  // Implementon ShapeList -> definon metodat që klientët mund t'i thërrasin

  private Vector theList;   // Lista (Vector) që ruan objektet Shape
  private int version;      // Versioni aktual (rritet sa herë shtohet një Shape i ri)

  public ShapeListServant() throws RemoteException {
    // Konstruktori duhet të hedhë RemoteException sepse është objekt remote
    // Zakonisht: theList = new Vector(); version = 0;
  }

  public Shape newShape(GraphicalObject g) throws RemoteException {
    version++;                                   // Rrit versionin sa herë krijohet një Shape i ri
    Shape s = new ShapeServant(g, version);       // Krijon një implementim konkret ShapeServant
    theList.addElement(s);                        // E shton në listë
    return s;                                      // Kthen objektin Shape te klienti thirrës
  }

  public Vector allShapes() throws RemoteException {
    return theList;  // Kthen të gjithë objektet Shape në listë
  }

  public int getVersion() throws RemoteException {
    return version;  // Kthen versionin aktual të listës
  }
}
```

### 15.5 Klienti — `ShapeListClient`

```java
import java.rmi.*;
import java.rmi.server.*;
import java.util.Vector;

public class ShapeListClient{
  public static void main(String args[]){
    System.setSecurityManager(new RMISecurityManager());
      // Vendos SecurityManager për kontroll sigurie gjatë komunikimit RMI

    ShapeList aShapeList = null; // Fillimisht null — ende s'është lidhur me serverin

    try{
      aShapeList = (ShapeList) Naming.lookup("//bruno/ShapeList");
        // Kërkon në RMI Registry objektin remote:
        // "bruno" = emri i hostit (serverit), "ShapeList" = emri me të cilin u regjistrua
        // Bëhet cast në tipin ShapeList që të mund të përdoret

      Vector sList = aShapeList.allShapes();
        // Thërret metodën remote allShapes() dhe merr listën e objekteve nga serveri
    } catch(RemoteException e) {
      System.out.println(e.getMessage()); // Gabime të komunikimit RMI
    } catch(Exception e) {
      System.out.println("Client: " + e.getMessage()); // Çdo gabim tjetër i përgjithshëm
    }
  }
}
```

**Vërejtje pedagogjike:** vini re rrjedhën e plotë — serveri regjistron objektin (`rebind`), klienti e kërkon me emrin e njëjtë (`lookup`), dhe pastaj thërret `allShapes()` mbi referencën e kthyer **sikur** të ishte një metodë lokale, ndonëse ekzekutimi real ndodh në procesin e serverit, në një makinë tjetër.

---

## 16. Rast studimi i dytë: `Naming` dhe Regjistrimi i një Shërbimi RMI (Detyrë_2)

Kjo detyrë tregon një zbatim minimal, hap-pas-hapi, të përdorimit të klasës `Naming` për të regjistruar, kërkuar dhe menaxhuar (unbind) një objekt remote në `localhost`.

### 16.1 Ndërfaqja remote — `MyService.java`

```java
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface MyService extends Remote {
  String pershendetje(String emri) throws RemoteException;
}
```

**Sqarim:** ky interfejs përcakton metodat që klienti mund t'i thërrasë nga larg. Trashëgon `Remote`, dhe metoda remote deklaron `throws RemoteException`.

### 16.2 Implementimi i shërbimit — `MyServiceImpl.java`

```java
import java.rmi.RemoteException;
import java.rmi.server.UnicastRemoteObject;

public class MyServiceImpl extends UnicastRemoteObject implements MyService {

  public MyServiceImpl() throws RemoteException {
    super();
  }

  @Override
  public String pershendetje(String emri) throws RemoteException {
    return "Pershendetje " + emri + ", kjo metode u thirr nga serveri RMI!";
  }
}
```

**Sqarim:** kjo klasë implementon `MyService`. Trashëgimi nga `UnicastRemoteObject` e bën objektin të aksesueshëm nga klientët përmes rrjetit.

### 16.3 Serveri — `RMIServer.java`

```java
import java.rmi.Naming;
import java.rmi.registry.LocateRegistry;

public class RMIServer {
  public static void main(String[] args) {
    try {
      LocateRegistry.createRegistry(1099);
        // Krijon RMI Registry në portin standard 1099

      MyService service = new MyServiceImpl();
        // Krijon objektin remote që do të publikohet në registry

      Naming.rebind("rmi://localhost/MyService", service);
        // Regjistron objektin remote me emrin "MyService"; nëse ekziston më parë, e zëvendëson

      System.out.println("Serveri RMI eshte startuar...");
      System.out.println("Objekti remote u regjistrua me emrin MyService.");

      String[] lista = Naming.list("rmi://localhost/");
        // Kthen listën e objekteve remote të regjistruara në registry
      System.out.println("Objektet e regjistruara ne RMI Registry:");
      for (String emri : lista) {
        System.out.println(emri);
      }
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
}
```

### 16.4 Klienti — `RMIClient.java`

```java
import java.rmi.Naming;

public class RMIClient {
  public static void main(String[] args) {
    try {
      MyService service = (MyService) Naming.lookup("rmi://localhost/MyService");
        // Kërkon objektin remote në RMI Registry dhe e kthen referencën e tij
        // Konvertimi (cast) në tipin MyService

      String pergjigja = service.pershendetje("Student");
        // Thërret metodën remote në server. Edhe pse duket si thirrje lokale,
        // ekzekutimi real ndodh në server

      System.out.println(pergjigja);
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
}
```

### 16.5 Largimi i objektit nga registry — `RMIUnbind.java`

```java
import java.rmi.Naming;

public class RMIUnbind {
  public static void main(String[] args) {
    try {
      Naming.unbind("rmi://localhost/MyService");
        // Largon objektin remote nga RMI Registry
      System.out.println("Objekti MyService u hoq nga RMI Registry.");
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
}
```

### 16.6 Përmbledhje e zgjidhjes

Klasa `Naming` në Java RMI përdoret për komunikim me RMI Registry: serveri regjistron objektin remote me `rebind`, klienti e gjen atë me `lookup`, ndërsa menaxhimi i mëtejshëm bëhet me `list` (inventarizim) dhe `unbind` (heqje). Kjo mundëson lidhjen ndërmjet klientit dhe serverit — qofshin ata në `localhost` apo në makina të ndryshme — dhe përdorimin e objekteve remote në aplikacione të shpërndara si të ishin objekte lokale.

---

## 17. Arkitektura REST

**REST (Representational State Transfer)** paraqet një **stil arkitekture** (jo një protokoll rigid) për dizajnimin e shërbimeve në ueb, i propozuar për të ofruar një mënyrë të thjeshtë dhe efikase për ndërtimin e sistemeve të shpërndara në internet.

Karakteristikat kryesore:

- Edhe pse REST nuk është i lidhur në mënyrë të ngurtë me një protokoll specifik, ai bazohet fuqishëm në parimet e **HTTP/1** — pikërisht modeli Request-Reply i diskutuar te seksioni 4.
- REST është bërë **standardi kryesor** për ndërtimin e API-ve në ueb, falë thjeshtësisë, fleksibilitetit dhe shkallëzueshmërisë (scalability) që ofron.
- Një nga avantazhet kryesore të REST-it është koncepti i **shtresëzimit (layered architecture)**: klienti nuk ka nevojë të dijë nëse komunikon drejtpërdrejt me serverin, apo përmes ndërmjetësve të ndryshëm.
- Kjo qasje e shtresëzuar mundëson shtimin e komponentëve ndërmjetës si **proxy, cache** ose **load balancer** midis klientit dhe serverit, të cilët ndihmojnë në optimizimin dhe përshpejtimin e komunikimit — pa qenë e nevojshme që klienti apo serveri të ndryshojnë sjelljen e tyre.

REST krahasohet natyrshëm me RPC/RMI: ndërsa RPC/RMI e paraqesin komunikimin si "thirrje metode/procedure", REST e paraqet si manipulim (GET/POST/PUT/DELETE) të **resurseve** të identifikuara përmes URL-ve, duke përdorur direkt semantikën e HTTP-së.

---

## 18. Korniza gRPC

**gRPC** është një teknologji moderne dhe efikase për ndërtimin e sistemeve të shpërndara, që ofron performancë të lartë përmes:

- përdorimit të protokollit **HTTP/2**, dhe
- **serializimit binar** të të dhënave.

Falë fleksibilitetit të tij në mënyrat e komunikimit (siç do të shihet më poshtë), gRPC është veçanërisht i përshtatshëm për aplikacione që kërkojnë komunikim të shpejtë dhe të besueshëm ndërmjet shërbimeve — p.sh. në arkitektura mikroshërbimesh (microservices).

### 18.1 Protokolli i Komunikimit gRPC

- **gRPC** është një protokoll komunikimi me burim të hapur (open-source), i zhvilluar nga **Google** dhe i publikuar për herë të parë në vitin **2015**.
- Ai u prezantua si **alternativë moderne** ndaj teknologjive më të vjetra, si **CORBA**, **XML-RPC** dhe **RESTful API-të** klasike, duke ofruar performancë më të lartë dhe komunikim më efikas mes aplikacioneve të shpërndara.

**Karakteristikat kryesore të gRPC:**

**a) Përdorimi i HTTP/2**, i cili sjell përmirësime të rëndësishme krahasuar me HTTP/1.1:
- **Eliminon "Head-of-Line Blocking" (HOL)** përmes **multipleksimit** — dërgimi paralel i disa kërkesave njëkohësisht mbi të njëjtën lidhje TCP, në vend që të presin njëra-tjetrën në radhë.
- Përdor mekanizmin **HPACK** për kompresimin e header-ave, duke zvogëluar ndjeshëm madhësinë e të dhënave të transmetuara.
- Mbështet **prioritizimin e kërkesave**, ku serveri i shpërndan bandwidth-in sipas rëndësisë së tyre, duke përmirësuar kohën e ngarkimit të aplikacioneve.
- Ofron funksionalitetin **Server Push**, që i mundëson serverit t'i dërgojë klientit të dhëna pa pritur domosdoshmërisht një kërkesë të re nga ai.

**b) Serializimi i të dhënave në format binar** — në vend të formateve tekstuale si JSON apo XML (të përdorura tipikisht në REST), gRPC transmeton të dhënat në **format binar**, i cili është ndjeshëm më i shpejtë dhe më efikas si për transmetim ashtu edhe për procesim.

**c) Kros-platformë** — gRPC funksionon në gjuhë programimi dhe platforma të ndryshme.

**d) Përdorimi i Google Protocol Buffers** — për definimin e strukturave të të dhënave dhe të shërbimeve (trajtohet në detaje te seksioni 20).

### 18.2 Llojet e Komunikimit në Kornizën gRPC

gRPC mbështet katër modele komunikimi ndërmjet klientit dhe serverit:

1. **Unary RPC** — modeli klasik: klienti dërgon **një** kërkesë dhe merr **një** përgjigje të vetme nga serveri, së bashku me statusin dhe metadata shtesë. (Ky është ekuivalenti direkt i një thirrjeje RPC/RMI tradicionale.)

2. **Client Streaming** — klienti dërgon **disa kërkesa radhazi** (një rrjedhë/stream mesazhesh) te serveri, ndërsa serveri kthen **vetëm një** përgjigje përfundimtare, pasi ka përpunuar të gjitha mesazhet e marra.
   - *Shembull:* analiza e log-eve (regjistrave) në një sistem kompjuterik, ku klienti dërgon vazhdimisht rreshta logu dhe serveri kthen një përmbledhje në fund.

3. **Server Streaming** — klienti dërgon **një kërkesë të vetme**, ndërsa serveri përgjigjet me një **varg (stream) mesazhesh**, që mund të vazhdojnë të vijnë me kohë.
   - *Shembull:* transmetimi i rezultateve të ndeshjeve sportive në kohë reale.

4. **Bidirectional Streaming** — si klienti ashtu edhe serveri dërgojnë secili nga një **varg mesazhesh**, në mënyrë të pavarur nga njëri-tjetri dhe njëkohësisht. Kjo mundëson komunikim të vazhdueshëm dhe simetrik në të dy drejtimet, ideal për aplikacione si bisedat në kohë reale ose lojërat online.

### 18.3 Zhvillimi i një Shërbimi gRPC me të Gjitha Llojet e Komunikimit

Në praktikë, një shërbim i vetëm gRPC mund të implementojë të katër llojet e komunikimit të përshkruara më sipër, sipas nevojave të ndryshme të aplikacionit. Për shembull, në rastin e një **platforme tregtare online (market online)**:

- **Unary RPC** mund të përdoret për **autentifikim** (kërkesë login → përgjigje token).
- **Client/Server Streaming** mund të përdoret për operacione **blerjeje** (p.sh. dërgimi i disa artikujve në shportë njëri pas tjetrit).
- **Server/Bidirectional Streaming** mund të përdoret për **përcjelljen e të dhënave në kohë reale**, si p.sh. ndryshimet e çmimeve ose statusi i porosisë.

Kjo tregon fleksibilitetin praktik të gRPC-së: i njëjti shërbim mund të kombinojë modele të ndryshme komunikimi sipas natyrës së secilit operacion.

---

## 19. Përdorimi i Google Protocol Buffers përmes gRPC/RPC

**Protocol Buffers (Protobuf)** është mekanizmi i Google-it për serializimin e strukturuar të të dhënave, i përdorur si "gjuhë përshkruese" (në rolin e IDL-së) për shërbimet gRPC. Elementet kryesore të përdorimit të tij janë:

- **Gjenerimi i librarive (code generation)** — nga një skedar `.proto` që përshkruan strukturën e mesazheve dhe shërbimeve, kompajleri i Protobuf gjeneron automatikisht kodin (stub-et e klientit dhe serverit, klasat e të dhënave) në gjuhën e zgjedhur të programimit — në mënyrë krejt analoge me gjenerimin e stub-eve nga IDL-ja e CORBA-s ose nga RPC-ja klasike.
- **Enkodimi i të dhënave** — të dhënat serializohen në një **format binar kompakt** dhe efikas, në vend të formateve tekstuale si XML apo JSON, çka redukton ndjeshëm madhësinë e mesazheve të transmetuara.
- **Shpejtësia, siguria dhe ndarja e paketave** — serializimi binar rrit shpejtësinë e transmetimit dhe procesimit; formati i mirëpërcaktuar (skema strikte e `.proto`) ndihmon në sigurinë e të dhënave (validim strikt i tipeve) dhe lehtëson ndarjen modulare të definicioneve (paketave) të mesazheve mes projekteve të ndryshme.
- **Strukturim më i pastër** — përkufizimi i qartë i mesazheve dhe shërbimeve në skedarë `.proto` sjell një organizim më të mirëmbajtur dhe më të lexueshëm të kontratës mes klientit dhe serverit, krahasuar me formate më "të lira" si JSON pa skemë.

Kombinimi **gRPC + Protocol Buffers** përfaqëson kështu evolucionin modern të ideve që kemi ndjekur gjatë gjithë këtij kapitulli: një IDL formal (skedari `.proto`), gjenerim automatik stub-esh (analog me proxy/skeleton), transport binar efikas mbi HTTP/2, dhe mbështetje për modele komunikimi më të pasura se thirrja e thjeshtë kërkesë-përgjigje (streaming në dy drejtime).

---

## Përmbledhje

Në këtë kapitull u trajtua tërësia e koncepteve që qëndrojnë pas thirrjes së metodave/procedurave në largësi në sistemet e shpërndara:

- **Middleware-i** është shtresa softuerike që fsheh kompleksitetin e rrjetit dhe të dallimeve mes platformave, duke ofruar transparencë vendndodhjeje, protokolli, harduerike dhe gjuhësore.
- Baza e çdo komunikimi në largësi është protokolli **Request-Reply**: një kërkesë e strukturuar (ClientID, RequestID, Operation, Parameters) dhe një përgjigje e lidhur me të përmes RequestID-së (Status, Result, Error). HTTP është realizimi më i njohur praktik i këtij modeli.
- Ekzistojnë tri variante protokolli për shkëmbimin RPC: **R, RR, RRA**, secili me shkallë të ndryshme garancie.
- **IDL** (si CORBA IDL apo Sun XDR) përkufizon ndërfaqet e shërbimeve pavarësisht nga gjuha e programimit, duke mundësuar bashkëveprim mes sistemeve heterogjene.
- **RPC** realizon thirrjen e një procedure të largët përmes stub-eve të klientit dhe serverit (marshalling/unmarshalling), dhe ofron semantika të ndryshme tolerance ndaj gabimeve: **maybe, at-least-once, at-most-once**.
- **RMI** e zgjeron RPC-në në botën e orientuar nga objekti, duke përdorur **proxy** (te klienti), **dispatcher** dhe **skeleton** (te serveri) për të drejtuar thirrjet drejt objektit real të largët.
- **Java RMI** ofron mekanizma konkretë (`Remote`, `UnicastRemoteObject`, `Activatable`, klasa `Naming` me `bind/rebind/lookup/unbind/list`) për ndërtimin praktik të aplikacioneve klient-server, siç u demonstrua përmes shembujve `Shape`/`ShapeList` dhe `MyService`.
- **REST** përfaqëson një stil arkitekture i bazuar në HTTP, i thjeshtë, i shkallëzueshëm dhe i shtresëzuar, që është bërë standard për API-të e uebit.
- **gRPC**, i zhvilluar nga Google, ofron performancë të lartë përmes HTTP/2 dhe serializimit binar me **Protocol Buffers**, dhe mbështet katër modele komunikimi: **Unary, Client Streaming, Server Streaming** dhe **Bidirectional Streaming**.
- **Google Protocol Buffers** funksionon si IDL modern: gjeneron automatikisht libraritë e klientit/serverit, kodifikon të dhënat në format binar kompakt, dhe siguron shpejtësi, siguri dhe strukturim të pastër të kontratave mes shërbimeve.

---

## Pyetje për vetë-vlerësim

1. Cilat janë katër përfitimet kryesore që ofron middleware-i në sistemet e shpërndara? Shpjegoni secilin me nga një shembull të shkurtër.
2. Cilat fusha përmban zakonisht një mesazh Request dhe cilat një mesazh Reply? Pse është i domosdoshëm `Request ID`?
3. Çfarë dallimi ka mes protokolleve shkëmbyese **R**, **RR** dhe **RRA** të RPC-së?
4. Përshkruani, hap pas hapi, se çfarë ndodh nga momenti kur klienti thërret një funksion "lokal" e deri sa merr rezultatin, në një thirrje RPC.
5. Krahasoni tri semantikat e thirrjes RPC (**maybe, at-least-once, at-most-once**) në aspektin e rrezikut të ekzekutimit të shumëfishtë të një operacioni.
6. Çfarë roli luan secili nga komponentët **Proxy**, **Dispatcher** dhe **Skeleton** në një thirrje RMI?
7. Pse çdo metodë e një ndërfaqeje `Remote` në Java duhet të deklarojë `throws RemoteException`?
8. Cili është dallimi funksional mes metodave `bind` dhe `rebind` të klasës `java.rmi.Naming`?
9. Në shembullin `ShapeListServant`, çfarë ndodh me variablën `version` sa herë thirret metoda `newShape`, dhe pse është e dobishme kjo?
10. Cilat janë katër modelet e komunikimit që mbështet gRPC, dhe jepni nga një shembull praktik për secilin.
11. Si e përmirëson HTTP/2 performancën e gRPC-së krahasuar me HTTP/1.1 (përmendni të paktën dy mekanizma konkretë)?
12. Çfarë avantazhesh sjell serializimi binar me Google Protocol Buffers krahasuar me formate tekstuale si JSON ose XML?
