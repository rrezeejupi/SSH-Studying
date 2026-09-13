# Kapitulli 7 — Objektet dhe Komponentet e Shpërndara

## Hyrje

Në kapitujt paraardhës u trajtuan modelet klasike të komunikimit në sistemet e shpërndara: thirrjet e procedurave në largësi (RPC) dhe komunikimi i bazuar në mesazhe. Ky kapitull shtron një hap më tej — si mund të organizohet një sistem i shpërndarë duke përdorur **objekte** dhe **komponentë** që "jetojnë" në kompjuterë të ndryshëm, por që bashkëveprojnë sikur të ishin lokalë.

Do të trajtohen dy koncepte kryesore, të lidhura ngushtë me njëra-tjetrën:

1. **Objektet e shpërndara (Distributed Objects)** — zgjerim i programimit të orientuar në objekte (OOP) në një mjedis të shpërndarë, i ilustruar përmes CORBA dhe gjuhës IDL.
2. **Komponentet e shpërndara (Distributed Components)** — një hap evolutiv mbi objektet, që i trajton varësitë mes pjesëve të softuerit në mënyrë eksplicite, i ilustruar përmes modelit **Fractal**.

---

## Pjesa I — Objektet e Shpërndara

### 1. Çka janë objektet e shpërndara?

Një **objekt i shpërndarë** është një objekt (në kuptimin e programimit të orientuar në objekte — pra një entitet që enkapsulon **gjendje** dhe **sjellje**/metoda) të cilit mund t'i qasemi nga një proces tjetër, zakonisht në një kompjuter tjetër në rrjet, njësoj sikur ai objekt të gjendej lokalisht.

Ideja themelore:

- Çdo objekt ka një **ndërfaqe** (interface) që përcakton se cilat metoda mund të thirren nga jashtë, pa e zbuluar implementimin e brendshëm.
- Klientët nuk komunikojnë me objektin real, por me një **referencë në largësi (remote reference)** — një lloj "dorëzë" që tregon ku ndodhet objekti dhe se si mund të kontaktohet.
- Kur klienti thërret një metodë mbi referencën, thirrja "udhëton" transparentisht nëpër rrjet deri te objekti real dhe kthehet me rezultatin — pikërisht ashtu siç funksionon RPC, por e ngritur në nivelin e objekteve (prandaj shpesh quhet **RMI — Remote Method Invocation**).

**Përse përdoren objektet e shpërndara?**

- **Enkapsulim**: gjendja e brendshme e objektit mbrohet; klienti sheh vetëm ndërfaqen publike.
- **Ripërdorshmëri dhe modularitet**: objektet mund të zhvillohen, testohen dhe zëvendësohen në mënyrë të pavarur.
- **Heterogjenitet**: një klient i shkruar në një gjuhë programimi (p.sh. Java) mund të thërrasë një objekt të implementuar në një gjuhë tjetër (p.sh. C++), sepse ndërfaqja përshkruhet në një gjuhë neutrale (IDL) dhe jo në kodin burimor të një gjuhe të caktuar.
- **Transparencë e vendndodhjes**: klienti nuk ka nevojë të dijë nëse objekti gjendet në të njëjtin proces, në të njëjtën makinë apo në anën tjetër të botës.

*(Figura 8.1 e origjinalit — "Objektet e Shpërndara" — ilustron pikërisht këtë skemë: një **klient** që mban një **referencë në largësi (remote object reference)** drejt një **objekti server**, dhe një metodë e thirrur nga klienti që ekzekutohet në objektin real në anën tjetër të rrjetit, me rezultatin që kthehet mbrapsht si përgjigje.)*

### 2. Referenca në largësi (remote object reference)

Një referencë në largësi është analoge me një pointer/referencë të zakonshme brenda një programi, por funksionon nëpër kufij të proceseve dhe makinave. Ajo tipikisht përmban:

- Adresën e rrjetit (host + port) të serverit ku "jeton" objekti;
- Një identifikues unik të objektit brenda atij serveri;
- Informacion mbi ndërfaqen që objekti e implementon (për t'u siguruar që klienti mund ta thërrasë saktë).

Referencat në largësi mund të **kalohen si parametra** te metoda të tjera (edhe në largësi), gjë që lejon ndërtimin e grafeve komplekse objektesh të shpërndara — p.sh. një metodë mund të kthejë një sekuencë referencash drejt objekteve të tjera (siç do të shihet te shembulli `ShapeList` më poshtë).

### 3. IDL — Gjuha e Përshkrimit të Ndërfaqeve (Interface Definition Language)

Meqë klienti dhe serveri mund të jenë shkruar në gjuhë programimi të ndryshme, nevojitet një mënyrë **neutrale ndaj gjuhës** për të përshkruar se çfarë metodash ofron një objekt i shpërndarë, cilët parametra pranon dhe çfarë kthen. Kjo është roli i **IDL**.

Karakteristikat kryesore të IDL:

- Përshkruan vetëm **ndërfaqen** e objektit (emrat e metodave, tipet e parametrave, tipet e kthimit, përjashtimet e mundshme) — **jo** implementimin.
- Nga një përshkrim IDL, një **kompajler IDL** (p.sh. `idlj` për Java në ekosistemin CORBA) gjeneron automatikisht:
  - **Stub-in e klientit** (proxy) — kodin që klienti e thërret lokalisht, i cili në sfond e paketon (marshalls) thirrjen dhe e dërgon në rrjet;
  - **Skeleton-in e serverit** — kodin që pranon thirrjen e ardhur nga rrjeti, e shpaketon dhe e thërret metodën reale te objekti.
- Kjo teknikë quhet **gjenerim stub/skeleton nga IDL** dhe eliminon nevojën që programuesi të shkruajë me dorë kodin e komunikimit në rrjet.

### 4. Shembulli themelor: Ndërfaqet `Shape` dhe `ShapeList`

Deka përdor një shembull klasik (edhe në literaturën standarde të CORBA) — një **tabelë e bardhë e ndarë (shared whiteboard)** ku disa përdorues shtojnë forma gjeometrike (drejtkëndësha etj.) që të gjithë klientët i shohin njësoj. Kjo kërkon një grup objektesh të shpërndara që përfaqësojnë format dhe listën e formave.

#### 4.1 Struktura `Rectangle`

```idl
struct Rectangle {
    long width;
    long height;
    long x;
    long y;
};
```

Kjo është një strukturë e thjeshtë IDL (record) që përfaqëson një drejtkëndësh nëpërmjet gjerësisë, lartësisë dhe koordinatave `x, y` të pozicionit të tij. Struktura kalon **si vlerë (by value)** mes klientit dhe serverit — pra kur transmetohet nëpër rrjet, e gjithë përmbajtja e saj kopjohet.

#### 4.2 Struktura `GraphicalObject`

```idl
struct GraphicalObject {
    string type;
    Rectangle enclosing;
    boolean isFilled;
};
```

Kjo strukturë përfaqëson **gjendjen e plotë** të një objekti grafik në tabelën e ndarë:

- `type` — lloji i formës (p.sh. "circle", "rectangle", "line");
- `enclosing` — drejtkëndëshi rrethues (bounding box) i formës, i përfaqësuar me strukturën `Rectangle` të mësipërme;
- `isFilled` — nëse forma duhet vizatuar e mbushur (true) apo vetëm kontura (false).

Vini re se `GraphicalObject` **përmban** një `Rectangle` — kjo tregon se strukturat IDL mund të ndërtohen njëra mbi tjetrën, njësoj si `struct`-et e ngërthyera në gjuhë si C apo Java.

#### 4.3 Ndërfaqja `Shape`

```idl
interface Shape {
    long getVersion();
    GraphicalObject getAllState();  // kthen gjendjen e GraphicalObject
};
```

`Shape` është ndërfaqja e një objekti të shpërndarë që përfaqëson **një formë të vetme** në tabelë. Ka dy metoda:

- `getVersion()` — kthen numrin e versionit aktual të formës. Kjo lejon klientët të zbulojnë nëse gjendja e tyre lokale (e cache-uar) e formës është e vjetëruar, pa pasur nevojë të transferojnë çdo herë gjithë gjendjen.
- `getAllState()` — kthen **gjendjen e plotë** të formës, si një `GraphicalObject`. Në thirrje në largësi, kjo strukturë kopjohet dhe dërgohet te klienti (pass-by-value), ndryshe nga vetë objekti `Shape`, i cili mbetet në server dhe klienti mban vetëm referencën në largësi drejt tij.

Kjo dallon qartë dy koncepte: **objektet** (si `Shape`) qarkullojnë gjithmonë si **referenca në largësi**, ndërsa **strukturat e të dhënave** (si `GraphicalObject`, `Rectangle`) qarkullojnë **si vlera**.

#### 4.4 Ndërfaqja `ShapeList`

```idl
typedef sequence<Shape, 100> All;

interface ShapeList {
    exception FullException { };
    Shape newShape(in GraphicalObject g) raises (FullException);
    All allShapes();  // kthen sekuencën e referencave në objektet remote (në largësi)
    long getVersion();
};
```

`ShapeList` është ndërfaqja e objektit "menaxher" të tabelës së ndarë — ai mban listën e të gjitha formave aktuale dhe u lejon klientëve t'i shtojnë e t'i marrin ato:

- `typedef sequence<Shape, 100> All;` — përcakton tipin `All` si një **sekuencë (varg me gjatësi të ndryshueshme, e lidhur/bounded deri në 100 elemente)** referencash `Shape`. Kjo tregon një veti të rëndësishme: **një sekuencë objektesh të shpërndara** mund të kalohet si rezultat i vetëm — çdo element i saj është vetë një referencë e pavarur në largësi.
- `exception FullException { };` — një përjashtim i definuar në IDL, që hidhet kur tabela ka arritur kapacitetin maksimal.
- `newShape(in GraphicalObject g) raises (FullException)` — krijon një formë të re në server (duke marrë gjendjen fillestare `g` si parametër **hyrës — `in`**, të kaluar si vlerë) dhe kthen një **referencë të re në largësi** drejt objektit `Shape` sapo krijuar. Nëse tabela është plot, hidhet `FullException`.
- `allShapes()` — kthen `All`, pra sekuencën me referenca në largësi drejt **të gjitha** formave aktualisht në tabelë. Vini re komentin origjinal: "kthen sekuencën e referencës së objektit remote (në largë[si])" — pra klienti merr një listë referencash, jo kopje të gjendjes; për të marrë gjendjen e secilës formë, klienti duhet të thërrasë më pas `getAllState()` mbi secilën referencë individualisht.
- `getVersion()` — numri i versionit të vetë listës (p.sh. rritet sa herë shtohet një formë e re), i dobishëm për cache-im dhe sinkronizim në anën e klientit.

**Përmbledhje e modelit hyrje/dalje të parametrave në IDL:**

| Kualifikues | Kuptimi |
|---|---|
| `in` | Parametri kalohet nga klienti te serveri (hyrës) |
| `out` | Parametri kthehet nga serveri te klienti (dalës) |
| `inout` | Parametri shkon në të dyja drejtimet |

#### 4.5 Grupimi në modul

```idl
module Tabela {
    struct Katrori { ... };
    struct ObjektiGrafik { ... };
    interface Shape { ... };
    typedef sequence<Shape, 100> All;
    interface ShapeList { ... };
};
```

Fjala kyçe `module` në IDL shërben për të **grupuar** definicionet e lidhura (struktura, ndërfaqe, tipe) nën një emërhapësirë (namespace) të përbashkët — analoge me `package` në Java ose `namespace` në C++. Kjo shmang përplasjet e emrave kur kombinohen ndërfaqe IDL nga burime të ndryshme.

### 5. Tipet e konstruktuara në IDL

IDL ofron një grup tipesh të ndërtuara (constructed types) për të modeluar struktura komplekse të dhënash, në mënyrë neutrale ndaj gjuhës së programimit:

| Tipi | Shembull | Përdorimi |
|---|---|---|
| **sequence** | `typedef sequence<Shape, 100> All;` ose `typedef sequence<Shape> All;` | Përcakton një varg me gjatësi të ndryshueshme (dinamik) të elementeve të specifikuar. Sekuencat mund të jenë **të lidhura (bounded)** — me kufi maksimal të gjatësisë, si `100` më sipër — ose **të palidhura (unbounded)**. |
| **string** | `string emri;` ose `typedef string<8> SmallString;` | Përcakton një varg karakteresh, tradicionalisht i ndarë me karakter `null`. Ngjashëm me sekuencat, edhe stringjet mund të jenë të lidhur (me gjatësi maksimale) ose të palidhur. |
| **array** | `typedef octet uniqueId[12];`, `typedef GraphicalObject GO[10][8];` | Përcakton një varg me **gjatësi fikse** (madje edhe shumë-dimensional) të elementeve të specifikuar në IDL — ndryshe nga `sequence`, gjatësia e `array`-it nuk ndryshon në kohë ekzekutimi. |
| **record (struct)** | `struct GraphicalObject { string type; Rectangle enclosing; boolean isFilled; };` | Përcakton një tip rekord që grupon një bashkësi fushash (entitetesh) heterogjene. Struktura kalohen gjithmonë **si vlerë** në argumente dhe rezultate. |
| **enumerated** | `enum Rand (Exp, Number, Name);` | Lidh emrin e tipit me një bashkësi të vogël vlerash të numrave të plotë (integer), analoge me `enum` në gjuhë të tjera programimi. |
| **union** | `union Exp switch (Rand) { case Exp: string vote; case Number: long n; case Name: string s; };` | "Unioni disjunkt" IDL lejon që një bashkësi tipesh të ndryshme të kalohet si i njëjti argument. "Koka" e union-it parametrizohet nëpërmjet një `enum` (këtu `Rand`) që specifikon se cili anëtar (member) është aktualisht në përdorim. |

Këto tipe të konstruktuara janë blloqet themelore me të cilat u ndërtuan `Rectangle`, `GraphicalObject`, `Shape` dhe `ShapeList` më sipër.

### 6. Arkitektura CORBA

**CORBA (Common Object Request Broker Architecture)**, e standardizuar nga OMG (Object Management Group), është ndoshta implementimi më i njohur historikisht i objekteve të shpërndara, dhe shërben si shembulli kryesor mbi të cilin ndërtohet ky kapitull.

#### 6.1 Komponentet themelore të arkitekturës CORBA

Sipas figurës përkatëse të dekut ("Komponentet themelore të arkitekturës CORBA"), një sistem CORBA përbëhet nga elementet vijuese, të organizuara në anën e klientit dhe në anën e serverit, me **ORB (Object Request Broker)** në mes si "magjistralja" e komunikimit:

**Në anën e klientit:**
- **Programi klient** — kodi aplikativ që dëshiron të thërrasë metoda në objekte të shpërndara.
- **Klient proxy (stub)** — kodi i gjeneruar nga IDL që i jep klientit një ndërfaqe lokale identike me ndërfaqen e objektit të largët; e paketon thirrjen (marshalling) dhe ia dorëzon ORB-it.
- **Invokim dinamik (Dynamic Invocation Interface)** — një rrugë alternative ndaj stub-it statik, që lejon klientin të ndërtojë dhe të thërrasë kërkesa në kohë ekzekutimi, pa ditur paraprakisht (në kohë kompajlimi) ndërfaqen e saktë të objektit.

**Në anën e serverit:**
- **Objekt skeleton (server skeleton)** — kodi i gjeneruar nga IDL që pranon kërkesën e ardhur nga ORB, e shpaketon (unmarshalling) dhe thërret metodën e vërtetë te implementimi (servant) i objektit.
- **Skeleton dinamik (Dynamic Skeleton Interface)** — analoge me invokimin dinamik në anën e klientit, lejon serverin të trajtojë kërkesa për ndërfaqe që nuk njihen paraprakisht në kohë kompajlimi.
- **Adapteri i objektit (Object Adapter)** — komponenti që menaxhon ciklin jetësor të objekteve servuese (aktivizim/deaktivizim, gjenerim identifikuesish, etj.) dhe i dorëzon kërkesat te implementimet konkrete.
- **Repozitori i implementimit (Implementation Repository)** — mban informacion se ku (në cilin proces/makinë) gjenden implementimet e objekteve dhe si mund të aktivizohen.
- **Repozitori i ndërfaqeve (Interface Repository)** — mban përshkrimet IDL të ndërfaqeve në formë të lexueshme në kohë ekzekutimi, i nevojshëm për invokimin/skeletonin dinamik.

**Në mes:**
- **ORB (Object Request Broker) — "core"** — infrastruktura qendrore që bart **kërkesën (Request)** nga klienti te serveri dhe **përgjigjen (Reply)** mbrapsht, duke fshehur tërësisht detajet e rrjetit, vendndodhjes dhe transportit. ORB-i i klientit dhe ORB-i i serverit bashkëveprojnë mbi rrjet për ta realizuar këtë transparencë.

Ky model demonstron parimin themelor: klienti "beson" se po thërret një metodë lokale mbi objektin `A`, ndërsa në fakt thirrja kalon nëpër proxy → ORB → rrjet → ORB → skeleton → implementimin real të `A` në server, dhe përgjigja kthehet po nëpër të njëjtën rrugë në drejtim të kundërt.

#### 6.2 Shërbimet CORBA (CORBA Services)

Përtej mekanizmit bazë të thirrjes në largësi, specifikimi CORBA përcakton një grup **shërbimesh standarde** që i vënë në dispozicion çdo aplikacioni të ndërtuar mbi CORBA, si p.sh.:

- **Naming Service** — shërbim emërtimi, që lejon regjistrimin dhe kërkimin e objekteve sipas emrit (siç shihet në shembujt e kodit Java më poshtë, ku serveri regjistron `ShapeList` me emër, dhe klienti e kërkon po me atë emër).
- **Trading Service** — kërkim objektesh sipas veçorive/aftësive, jo vetëm sipas emrit.
- **Event/Notification Service** — komunikim asinkron i bazuar në ngjarje mes objekteve.
- **Transaction Service** — mbështetje për transaksione të shpërndara mbi objekte.
- **Security Service** — autentikim dhe autorizim mbi thirrjet në objekte të shpërndara.
- **Persistence Service** — ruajtja e gjendjes së objekteve në memorie të qëndrueshme.

Këto shërbime e bëjnë CORBA-n jo thjesht një mekanizëm RMI, por një **platformë të plotë** për ndërtimin e sistemeve të shpërndara mbi objekte.

### 7. Nga IDL te kodi: Java dhe CORBA

Deka ilustron, hap pas hapi, rrugën nga përshkrimi IDL i `ShapeList` deri te një sistem funksional klient-server në Java, duke përdorur kompajlerin `idlj`.

#### 7.1 Ndërfaqet Java të gjeneruara nga `idlj`

```java
public interface ShapeListOperations {
    Shape newShape(GraphicalObject g) throws ShapeListPackage.FullException;
    Shape[] allShapes();
    int getVersion();
}

public interface ShapeList extends ShapeListOperations,
        org.omg.CORBA.Object, org.omg.CORBA.portable.IDLEntity { }
```

Kompajleri `idlj` e përkthen automatikisht ndërfaqen IDL `ShapeList` në ndërfaqe Java:

- `ShapeListOperations` përmban vetëm **metodat e biznesit** (ato që erdhën drejtpërdrejt nga IDL) — `newShape`, `allShapes`, `getVersion`.
- `ShapeList` e zgjeron atë, duke shtuar edhe ndërfaqet e nevojshme të infrastrukturës CORBA (`org.omg.CORBA.Object` — identiteti bazë i çdo objekti CORBA, dhe `IDLEntity` — marker për tipet e gjeneruara nga IDL).

Kjo ndarje lejon që i njëjti kontratë biznesi (`ShapeListOperations`) të përdoret njëkohësisht nga ndërfaqja e klientit dhe nga klasat servuese në server.

#### 7.2 Implementimi i serverit — `ShapeListServant`

```java
import org.omg.CORBA.*;
import org.omg.PortableServer.POA;

class ShapeListServant extends ShapeListPOA {
    private POA theRootpoa;
    private Shape theList[];
    private int version;
    private static int n = 0;

    public ShapeListServant(POA rootpoa) {
        theRootpoa = rootpoa;
        // inicializohen variablat e tjera të instancës
    }

    public Shape newShape(GraphicalObject g)
            throws ShapeListPackage.FullException {
        version++;
        Shape s = null;
        ShapeServant shapeRef = new ShapeServant(g, version);
        try {
            org.omg.CORBA.Object ref =
                theRootpoa.servant_to_reference(shapeRef);
            s = ShapeHelper.narrow(ref);
        } catch (Exception e) {}
        if (n >= 100) throw new ShapeListPackage.FullException();
        theList[n++] = s;
        return s;
    }

    public Shape[] allShapes() { ... }
    public int getVersion() { ... }
}
```

`ShapeListServant` është **implementimi konkret** (servanti) i ndërfaqes `ShapeList` në anën e serverit — ai zgjeron klasën abstrakte `ShapeListPOA`, e gjeneruar automatikisht nga `idlj`, që trajton detajet e shpaketimit të thirrjeve CORBA.

Në metodën `newShape`:
1. Rritet numri i versionit dhe krijohet një servant i ri `ShapeServant` për formën e re, duke i kaluar gjendjen `g` dhe versionin.
2. Servanti aktivizohet dhe kthehet si **referencë CORBA** përmes `theRootpoa.servant_to_reference(...)`, e ndjekur nga `ShapeHelper.narrow(...)` — një hap tipik në CORBA për ta "ngushtuar" (narrow) referencën gjenerike `org.omg.CORBA.Object` në tipin specifik `Shape`.
3. Nëse tabela e formave është plot (`n >= 100`), hidhet përjashtimi i definuar në IDL, `FullException`.
4. Forma e re shtohet në listë dhe kthehet referenca e saj te klienti (thirrësi).

#### 7.3 Nisja e serverit — `ShapeListServer`

```java
public class ShapeListServer {
    public static void main(String args[]) {
        try {
            ORB orb = ORB.init(args, null);
            POA rootpoa = POAHelper.narrow(
                orb.resolve_initial_references("RootPOA"));
            rootpoa.the_POAManager().activate();
            ShapeListServant SLSRef = new ShapeListServant(rootpoa);
            org.omg.CORBA.Object ref = rootpoa.servant_to_reference(SLSRef);
            ShapeList SLRef = ShapeListHelper.narrow(ref);

            org.omg.CORBA.Object objRef =
                orb.resolve_initial_references("NameService");
            NamingContext ncRef = NamingContextHelper.narrow(objRef);
            NameComponent nc = new NameComponent("ShapeList", "");
            NameComponent path[] = { nc };
            ncRef.rebind(path, SLRef);

            orb.run();
        } catch (Exception e) { ... }
    }
}
```

Ky program nis serverin CORBA hap pas hapi:

1. Inicializohet **ORB**-i lokal.
2. Merret dhe aktivizohet **POA (Portable Object Adapter)** — adapteri i objektit që menaxhon ciklin jetësor të servantëve.
3. Krijohet servanti `ShapeListServant` dhe kthehet si referencë CORBA `ShapeList`.
4. Lidhet me **Naming Service**-in e CORBA-s: merret konteksti i emërtimit (`NamingContext`), krijohet një komponent emri `"ShapeList"`, dhe objekti **regjistrohet (bind/rebind)** nën atë emër — kështu që klientët e tjerë ta gjejnë duke kërkuar thjesht emrin `"ShapeList"`, pa ditur paraprakisht adresën e saktë të rrjetit.
5. `orb.run()` e nis ciklin e ORB-it, duke e mbajtur serverin aktiv dhe në pritje të kërkesave hyrëse.

#### 7.4 Klienti — `ShapeListClient`

```java
public class ShapeListClient {
    public static void main(String args[]) {
        try {
            ORB orb = ORB.init(args, null);
            org.omg.CORBA.Object objRef =
                orb.resolve_initial_references("NameService");
            NamingContext ncRef = NamingContextHelper.narrow(objRef);
            NameComponent nc = new NameComponent("ShapeList", "");
            NameComponent path[] = { nc };
            ShapeList shapeListRef =
                ShapeListHelper.narrow(ncRef.resolve(path));

            Shape[] sList = shapeListRef.allShapes();
            GraphicalObject g = sList[0].getAllState();
        } catch (org.omg.CORBA.SystemException e) { ... }
    }
}
```

Klienti ndjek një rrugë simetrike: inicializon ORB-in, kontakton **Naming Service**-in, **kërkon** (`resolve`) objektin e regjistruar me emrin `"ShapeList"`, dhe merr mbrapsht një referencë lokale (`shapeListRef`) drejt objektit real në largësi. Prej këtu, klienti thërret `allShapes()` sikur të ishte një metodë lokale e zakonshme — merr një varg referencash `Shape[]`, dhe mbi elementin e parë thërret `getAllState()` për ta marrë gjendjen e plotë të asaj forme (si strukturë `GraphicalObject`, e kaluar si vlerë).

Ky shembull i plotë ilustron qartë **transparencën e vendndodhjes**: as kodi i serverit, as ai i klientit nuk përmbajnë logjikë eksplicite rrjeti (socket-e, serializim manual) — gjithçka trajtohet nga infrastruktura CORBA e gjeneruar nga IDL.

---

## Pjesa II — Komponentet e Shpërndara

### 8. Nga objektet te komponentet: pse nevojitet një hap më tej?

Objektet e shpërndara zgjidhin problemin e komunikimit dhe të enkapsulimit, por lënë hapur një çështje tjetër, kritike për sisteme të mëdha, afatgjata dhe që evoluojnë me kalimin e kohës: **varësitë (dependencies)** mes pjesëve të ndryshme të softuerit shpesh nuk shprehen në mënyrë eksplicite.

Kur një objekt A e thërret objektin B, kjo varësi shpesh gërshetohet thellë brenda kodit të A-së (p.sh. si një referencë e krijuar direkt brenda metodave të A), dhe bëhet e vështirë:

- të zëvendësohet B me një implementim tjetër pa modifikuar A-në;
- të kuptohet, thjesht duke parë nga jashtë, se prej cilëve objekte tjerë varet A-ja;
- të ripërdoret A-ja në një kontekst tjetër pa "tërhequr" pas vetes gjithë varësitë e saj të fshehura.

Një **komponent softuerik** trajton këtë problem duke bërë eksplicite:

1. **Cilat ndërfaqe ofron** komponenti (ndërfaqet "provided" — çka mund t'i kërkohet atij nga jashtë), dhe
2. **Cilat ndërfaqe kërkon** komponenti nga mjedisi për të funksionuar (ndërfaqet "required" — çka i nevojitet atij nga jashtë).

Këto informacione janë të dukshme nga jashtë komponentit, zakonisht të përshkruara shprehimisht (p.sh. në një skedar konfigurimi ose në metadata), dhe komponenti **zëvendësohet** si i tërë, jo modifikohet pjesë-pjesë. Kjo e bën komponentin njësinë natyrale për **vendosje (deployment)**, **konfigurim** dhe **kompozim** të sistemeve komplekse të shpërndara.

**Përfitimet kryesore të modelit të komponentëve krahasuar me objektet e thjeshta:**

- **Varësi eksplicite** — lehtëson analizën, konfigurimin dhe zëvendësimin e pjesëve të sistemit.
- **Kompozim (composition)** — komponentët mund të kombinohen për të ndërtuar komponentë më të mëdhenj (komponentë "kompozitë"), duke krijuar hierarki.
- **Ripërdorshmëri e vërtetë** — një komponent mund të vendoset (deploy) në kontekste dhe kontejnerë të ndryshëm pa modifikime në kodin e tij.
- **Menaxhim i ciklit jetësor** — mjedisi ekzekutues (kontejneri) mund të krijojë, konfigurojë, lidhë dhe shkatërrojë komponentët në mënyrë të njësuar dhe të automatizuar.

### 9. Kontejnerët dhe serverët e aplikacionit

Në praktikën industriale (siç ilustrohet edhe te shembujt përkatës të dekut mbi arkitekturën e softuerit, strukturën e kontejnerit, serverët e aplikacionit, dhe EJB — Enterprise JavaBeans), komponentët e shpërndarë zakonisht nuk ekzekutohen "të zhveshur", por brenda një **kontejneri (container)**, i cili u ofron atyre shërbime infrastrukturore pa qenë nevoja që komponenti vetë t'i implementojë:

- **Menaxhimi i transaksioneve** — kontejneri mund të fillojë, të konfirmojë (commit) apo të rikthejë (rollback) automatikisht transaksione rreth thirrjeve në metoda të komponentit, sipas **atributeve të transaksionit** të deklaruara (p.sh. "kërkohet transaksion", "krijo transaksion të ri", "pa transaksion").
- **Menaxhimi i sigurisë** — autentikim dhe autorizim i transparentë ndaj thirrjeve.
- **Konteksti i thirrjes (invocation context)** — kontejneri mban gjatë ekzekutimit informacion kontekstual (identiteti i thirrësit, transaksioni aktual, etj.) të disponueshëm për komponentin.
- **Menaxhimi i ciklit jetësor** — krijimi, aktivizimi, pasivizimi dhe fshirja e instancave të komponentëve, shpesh me **pool-e** instancash për efikasitet.
- **Lidhja (binding) e varësive** — kontejneri "injekton" te komponenti referencat drejt shërbimeve apo komponentëve të tjerë që ai kërkon, bazuar në përshkrimin e varësive të tij.

Ky model — komponent + kontejner që i ofron shërbime infrastrukturore transparente — është parimi themelor mbi të cilin ndërtohen platforma si Enterprise JavaBeans (EJB), dhe i shtron themelet konceptuale për modele më të përgjithshme komponentësh, si **Fractal**, të cilin e trajtojmë më poshtë.

### 10. Modeli i komponentëve Fractal

**Fractal** është një model komponentësh softuerikë i përgjithshëm, i pavarur nga një gjuhë e vetme programimi, i projektuar për të mbështetur ndërtimin e sistemeve komplekse, të shpërndara dhe të konfigurueshme dinamikisht, përmes kompozimit **rekursiv** (fraktal) të komponentëve — pra një komponent mund të përbëhet nga komponentë të tjerë, të cilët nga ana e tyre mund të përbëhen sërish nga komponentë të tjerë, e kështu me radhë.

#### 10.1 Parimet themelore

- **Uniformitet**: çdo komponent, qoftë i thjeshtë apo kompleks, trajtohet nëpërmjet të njëjtit grup ndërfaqesh standarde të kontrollit (të quajtura *controller interfaces*), pavarësisht nga përmbajtja e tij e brendshme.
- **Kompozim rekursiv**: një komponent **kompozit (composite)** mund të përmbajë brenda vetes një ose më shumë komponentë të tjerë (nën-komponentë), duke krijuar një **hierarki pemësore** komponentësh — prej ku vjen edhe emri "Fractal" (vetë-ngjashmëria strukturore në çdo nivel të hierarkisë).
- **Ndarja e ndërfaqeve funksionale nga ato të kontrollit**: një komponent Fractal ofron:
  - **Ndërfaqe funksionale (business interfaces)** — ato që realizojnë funksionalitetin aktual të aplikacionit (analoge me metodat e biznesit të `Shape`/`ShapeList` më sipër);
  - **Ndërfaqe kontrolli (controller interfaces)** — ndërfaqe standarde, të përbashkëta për të gjithë komponentët Fractal, që lejojnë inspektimin dhe menaxhimin e vetë strukturës e ciklit jetësor të komponentit nga jashtë (nga mjedisi ekzekutues ose nga vegla administrimi).

#### 10.2 Struktura e një komponenti Fractal

Një komponent Fractal tipik përbëhet konceptualisht nga dy pjesë:

1. **Membrana (membrane)** — pjesa e jashtme, e cila ekspozon ndërfaqet e kontrollit dhe menaxhon kufirin e komponentit ndaj botës së jashtme (kush mund të hyjë/dalë, si lidhen varësitë, si inspektohet përmbajtja).
2. **Përmbajtja (content)** — pjesa e brendshme, e cila mund të jetë:
   - **komponent primitiv (primitive component)** — përmban drejtpërdrejt kod ekzekutues (implementim konkret, "gjethe" e hierarkisë, pa nën-komponentë); ose
   - **komponent kompozit (composite component)** — përmban një ose më shumë nën-komponentë të tjerë (primitivë ose sërish kompozitë), të lidhur mes tyre përmes ndërfaqeve funksionale.

Kjo strukturë e dyfishtë (membranë + përmbajtje) është pikërisht ajo që lejon trajtimin uniform: nga jashtë, një komponent kompozit duket dhe sillet njësoj si një komponent primitiv — dallimi (çfarë ka brenda) mbetet i fshehur pas membranës.

#### 10.3 Ndërfaqet `Component` dhe `ContentController`

Fractal përcakton një grup ndërfaqesh standarde kontrolli që çdo komponent (ose të paktën ata që i mbështesin veçoritë përkatëse) i implementon. Dy prej më themelorëve, që theksohen edhe në dekun burimor, janë:

**Ndërfaqja `Component`**

Kjo është ndërfaqja **bazë**, e pranishme te çdo komponent Fractal, pavarësisht nëse është primitiv apo kompozit. Nëpërmjet saj, mjedisi ekzekutues (ose një vegël administrimi) mund:

- të zbulojë ndërfaqet funksionale që komponenti i ekspozon (si ato të ofruara, ashtu edhe ato të kërkuara);
- të marrë një referencë drejt ndonjë prej ndërfaqeve të kontrollit shtesë që komponenti i mbështet (si `ContentController` më poshtë), nëpërmjet një mekanizmi tipik "zbulimi i ndërfaqeve" (introspeksion).

Në thelb, `Component` është "pika hyrëse" universale për të bashkëvepruar me çdo komponent në nivel meta (kontrolli/administrimi), pa pasur nevojë të dihet paraprakisht nëse ai është primitiv apo kompozit.

**Ndërfaqja `ContentController`**

Kjo ndërfaqe kontrolli i takon vetëm **komponentëve kompozitë** dhe menaxhon **përmbajtjen** e tyre — pra listën e nën-komponentëve që ai përmban. Nëpërmjet saj mund të kryhen operacione si:

- **shtimi** i një nën-komponenti të ri brenda komponentit kompozit;
- **heqja** e një nën-komponenti ekzistues;
- **marrja e listës** së të gjithë nën-komponentëve aktualë të përmbajtur brenda komponentit.

Kjo ndërfaqe është ajo që e realizon praktikisht **kompozimin rekursiv**: një komponent kompozit "ndërtohet" dinamikisht duke i shtuar nën-komponentë përmes `ContentController`, dhe meqë çdo nën-komponent respekton po të njëjtën ndërfaqe `Component`, i njëjti mekanizëm mund të përsëritet në thellësi të pakufizuar — kështu formohet hierarkia "fraktale".

*(Figura përkatëse e dekut, "Ndërfaqet Component dhe ContentController në Fractal", ilustron pikërisht këtë raport: një komponent kompozit që, nëpërmjet membranës së tij (që ekspozon `Component` dhe `ContentController`), përmban dhe menaxhon një ose më shumë nën-komponentë të lidhur me ndërfaqe funksionale ofrim/kërkim mes tyre.)*

#### 10.4 Komponentë primitivë kundrejt komponentëve kompozitë — përmbledhje

| Veçori | Komponent primitiv | Komponent kompozit |
|---|---|---|
| Përmbajtja | Kod ekzekutues konkret | Një ose më shumë nën-komponentë |
| Roli i `ContentController` | Zakonisht nuk aplikohet (nuk ka çka të menaxhojë) | I domosdoshëm — menaxhon nën-komponentët |
| Roli i `Component` | I pranishëm — zbulim ndërfaqesh | I pranishëm — zbulim ndërfaqesh |
| Pozicioni në hierarki | "Gjethe" (leaf) e pemës së komponentëve | Nyje e brendshme (internal node) e pemës |
| Shembull konceptual | Një shërbim i vetëm, i thjeshtë (p.sh. llogaritje) | Një nënsistem i tërë, i përbërë nga disa shërbime të lidhura |

#### 10.5 Shembull konfigurimi (koncept)

Dekut përfshin edhe një figurë me një "shembull konfigurimi komponentësh në Fractal", e cila tregon tipikisht se si disa komponentë primitivë (p.sh. `Klienti`, `Serveri`) lidhen mes tyre përmes ndërfaqeve funksionale (një ndërfaqe e kërkuar e njërit lidhet me ndërfaqen e ofruar të tjetrit), të gjithë të përmbajtur brenda një komponenti kompozit "aplikacioni", i cili nga jashtë duket si një njësi e vetme, e menaxhueshme dhe e vendosshme (deployable) në tërësi. Ky lloj konfigurimi zakonisht përshkruhet në mënyrë deklarative (p.sh. në XML ose ADL — Architecture Description Language), duke e ndarë përshkrimin e arkitekturës nga kodi i vetë komponentëve.

---

## Përmbledhje

- **Objektet e shpërndara** zgjerojnë programimin e orientuar në objekte në mjedise të shpërndara: klienti bashkëvepron me një objekt në largësi nëpërmjet një **referencë në largësi**, dhe thirrjet e metodave "udhëtojnë" transparentisht nëpër rrjet (RMI).
- **IDL (Interface Definition Language)** përshkruan ndërfaqet e objekteve në mënyrë neutrale ndaj gjuhës së programimit, duke lejuar gjenerimin automatik të **stub-it të klientit** dhe **skeletonit të serverit**. IDL ofron tipe të konstruktuara si `struct`, `sequence`, `string`, `array`, `enum` dhe `union`.
- Shembulli `Shape`/`ShapeList` ilustron: struktura të dhënash (`Rectangle`, `GraphicalObject`) që kalojnë **si vlerë**, ndërfaqe objektesh (`Shape`, `ShapeList`) që kalojnë **si referencë në largësi**, sekuenca referencash objektesh (`All`), dhe përjashtime IDL (`FullException`).
- **CORBA** është një arkitekturë standarde për objekte të shpërndara, e ndërtuar rreth **ORB**-it si ndërmjetës komunikimi mes proxy-t të klientit dhe skeletonit të serverit, e pasuruar me shërbime standarde (emërtimi, transaksionet, siguria, etj.). Kompajleri `idlj` gjeneron automatikisht kodin Java (ndërfaqe, servantë, klasa ndihmëse) nga një përshkrim IDL.
- **Komponentet e shpërndara** e zgjerojnë modelin e objekteve duke i bërë **eksplicite varësitë** mes pjesëve të softuerit (çfarë ofrojnë dhe çfarë kërkojnë), duke e bërë komponentin njësinë natyrale për vendosje, konfigurim dhe zëvendësim, tipikisht brenda **kontejnerëve** që ofrojnë shërbime infrastrukturore (transaksione, siguri, cikël jetësor).
- **Fractal** është një model komponentësh të përgjithshëm, i bazuar në **kompozim rekursiv**: çdo komponent ka një membranë me ndërfaqe kontrolli standarde (si `Component`, për zbulim ndërfaqesh, dhe `ContentController`, për menaxhimin e nën-komponentëve) dhe një përmbajtje që mund të jetë **primitive** (kod konkret) ose **kompozite** (nën-komponentë të tjerë) — duke krijuar hierarki "fraktale" komponentësh.

## Pyetje kontrolluese

1. Çfarë është një **referencë në largësi** dhe si ndryshon ajo nga një pointer/referencë e zakonshme brenda një programi?
2. Përse nevojitet IDL në një sistem si CORBA, në vend që klienti dhe serveri të komunikonin drejtpërdrejt në gjuhën e tyre të programimit?
3. Në ndërfaqen `ShapeList`, pse metoda `newShape` merr `GraphicalObject` si parametër **hyrës të kaluar si vlerë**, ndërsa kthen një `Shape` si **referencë në largësi**? Çfarë dallimi konceptual ilustron kjo?
4. Cili është roli i `typedef sequence<Shape, 100> All;` dhe si e shfrytëzon atë metoda `allShapes()`?
5. Rendit rolet e katër elementeve kryesore të arkitekturës CORBA: klient proxy, ORB, objekt skeleton, adapter i objektit.
6. Në kodin Java të `ShapeListServer`, çfarë roli luan **Naming Service**-i dhe pse është i domosdoshëm që klienti të gjejë objektin `ShapeList`?
7. Cili është dallimi thelbësor mes një **objekti** të thjeshtë të shpërndarë dhe një **komponenti** softuerik, në aspektin e varësive?
8. Shpjego dallimin mes një **komponenti primitiv** dhe një **komponenti kompozit** në modelin Fractal.
9. Çfarë funksioni kryen ndërfaqja `ContentController` në Fractal, dhe pse ajo lidhet ngushtë me natyrën "rekursive/fraktale" të modelit?
10. Si ndryshon roli i ndërfaqes `Component` nga ai i `ContentController` në Fractal?
