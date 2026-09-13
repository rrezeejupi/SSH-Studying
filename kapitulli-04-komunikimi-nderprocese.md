# Kapitulli 4 — Komunikimi Ndërprocese në Sistemet e Shpërndara

*Lënda: Sistemet e Shpërndara — Prof. Dr. Isak Shabani*

## Hyrje

Në çdo sistem të shpërndarë, proceset që përbëjnë aplikacionin (klientë, serverë, shërbime të ndryshme) shpesh ekzekutohen në kompjuterë të ndryshëm, të lidhur përmes rrjetit. Që këto procese të bashkëpunojnë, atyre u duhet një mekanizëm për të shkëmbyer të dhëna — ky mekanizëm quhet **Komunikim Ndërprocese** (*Inter-Process Communication — IPC*). Ky kapitull shqyrton bazat teorike dhe praktike të IPC-së: nga shtresat e middleware-it, te programimi konkret me socket-e në Java (UDP dhe TCP), serializimi i të dhënave, referencat për objekte në distancë, dhe komunikimi multicast.

Struktura e kapitullit ndjek përmbajtjen e paraqitur në leksion:

1. Shtresat e Middleware-it
2. Komunikimi ndërmjet proceseve në sistemet e shpërndara
3. Operacionet me API (komunikim i orientuar në lidhje, HTTP)
4. Krahasimi i Datagram dhe Stream Socket-ve
5. UDP klienti dhe UDP serveri
6. TCP klienti dhe TCP serveri
7. Forma e serializuar e IPC në Java
8. Referenca për një objekt në distancë
9. Komunikimet Multicast

---

## 1. Shtresat e Middleware-it

**Middleware** është shtresa softuerike që qëndron mes sistemit operativ (dhe rrjetit) nga njëra anë dhe aplikacioneve të shpërndara nga ana tjetër. Qëllimi i saj është t'ua fshehë aplikacioneve kompleksitetin e komunikimit të drejtpërdrejtë në rrjet dhe t'u ofrojë atyre abstraksione të nivelit më të lartë (p.sh. thirrje procedurash në distancë, objekte të shpërndara, mesazhe). Brenda kësaj shtrese ekzistojnë disa koncepte themelore:

- **Socket-i** — është një **pikë fundore** (*endpoint*) që lidh komunikimin e dyanshëm midis dy programeve që funksionojnë në rrjet. Socket-i është abstraksioni bazë mbi të cilin ndërtohen pothuajse të gjitha format e tjera të komunikimit në rrjet (UDP, TCP, e madje edhe HTTP).

- **Message Passing** (kalimi i mesazheve) — mundëson komunikimin midis proceseve të cilat përdoren si në programimin paralel, ashtu edhe në programimin e orientuar nga objekti. Në vend që proceset të ndajnë memorie të përbashkët, ato shkëmbejnë mesazhe eksplicite (dërgim/pranim).

- **Multicast** — paraqet komunikimin në grup, ku transmetimi i të dhënave adresohet **në të njëjtën kohë** te një grup kompjuterësh destinacion, në vend që t'u dërgohet një nga një secilit marrës veç e veç. Kjo rrit efikasitetin kur i njëjti mesazh duhet t'u shpërndahet shumë marrësve njëkohësisht.

- **Overlay Networks** (rrjetet e mbivendosjes) — paraqesin një rrjet kompjuterik të ndërtuar "mbi" një rrjet tjetër (zakonisht mbi internetin fizik). Të gjitha nyjet (nodes) e një rrjeti mbivendosës janë të lidhura me njëra-tjetrën përmes **lidhjeve logjike ose virtuale**, dhe secila nga këto lidhje logjike korrespondon në fakt me një ose më shumë shtigje (rrugë) në rrjetin themelor fizik. Kjo teknikë përdoret p.sh. te rrjetet peer-to-peer dhe sistemet e shpërndara që duan të organizojnë vetë topologjinë e tyre logjike, të pavarur nga topologjia fizike e internetit.

> **Pse është e rëndësishme kjo shtresë?** Middleware-i lejon zhvilluesit të mos merren me detaje të nivelit të ulët (adresa IP, porte, formatim bajtesh) dhe në vend të kësaj të programojnë duke përdorur koncepte më të natyrshme si "thirr këtë metodë në distancë" ose "dërgo këtë mesazh grupit X".

---

## 2. Komunikimi Ndërmjet Proceseve (Inter-Process Communication — IPC)

**IPC** është një grup ndërfaqesh programimi (API) që i mundësojnë programuesit të koordinojë aktivitetet mes proceseve të ndryshme të një programi, të cilat mund të ekzekutohen **njëkohësisht** në një sistem operativ të shpërndarë. Në thelb, IPC paraqet **shkëmbimin e të dhënave** ndërmjet dy ose më shumë proceseve ose thread-eve të pavarura — pavarësisht nëse këto procese ndodhen në të njëjtën makinë apo në makina të ndryshme të lidhura në rrjet.

Sistemet operative ofrojnë vetë disa mekanizma/burime bazë për IPC lokal, si:

- **radhët e mesazheve** (message queues),
- **semaforët** (semaphores) — për sinkronizim,
- **memorje e ndarë/e shpërndarë** (shared/distributed memory).

Megjithatë, në një sistem të shpërndarë (ku proceset gjenden në kompjuterë të ndryshëm), këto mekanizma të thjeshtë lokalë nuk mjaftojnë. Prandaj, sistemet e shpërndara mundësojnë përdorimin e **API-ve** (Application Programming Interface) që lejojnë IPC të programohet në një nivel më të lartë abstraksioni — p.sh. thjesht duke thirrur operacionet **`send`** (dërgo) dhe **`receive`** (prano), pa u shqetësuar për detajet e transportit në rrjet.

### 2.1 IPC — Unicast kundrejt Multicast

Në kompjuterikën e shpërndarë, dy ose më shumë procese angazhohen në IPC duke ndjekur një **protokoll** të përbashkët. Një proces i vetëm mund të luajë role të ndryshme gjatë ekzekutimit të protokollit:

- **Dërgues** (*sender*) në disa pika të komunikimit, dhe
- **Marrës** (*receiver*) në pika të tjera.

Në varësi të numrit të marrësve, komunikimi klasifikohet në dy lloje kryesore:

| Lloji | Përshkrimi | Shembull tipik |
|---|---|---|
| **Unicast** | Komunikimi shkon nga një proces te **një tjetër proces i vetëm** | Komunikimi me socket (p.sh. klient–server) |
| **Multicast** | Komunikimi shkon nga një proces te **një grup procesesh** | Modeli i mesazheve Publikim/Regjistrim (*Publish/Subscribe*) |

---

## 3. Operacionet e një API për Komunikim të Orientuar në Lidhje

Kur komunikimi ndërmjet dy entiteteve bazohet në krijimin paraprak të një **kanali të qëndrueshëm** (*connection-oriented*), API-ja që e realizon këtë ofron katër operacione themelore:

1. **Connect (Lidhja)**
   Ky operacion inicializon krijimin e një lidhjeje komunikimi ndërmjet dy entiteteve, duke specifikuar adresën e dërguesit dhe të marrësit. Është hapi thelbësor për vendosjen e një kanali të qëndrueshëm komunikimi, para se çdo shkëmbim i të dhënave të mund të fillojë.

2. **Send (Dërgimi)**
   Mundëson transmetimin e një mesazhi nga procesi dërgues drejt marrësit të caktuar, përmes lidhjes së krijuar paraprakisht me `connect`.

3. **Receive (Pranimi)**
   Përdoret nga pala marrëse për të pranuar mesazhet hyrëse. Mesazhi i pranuar ruhet në një strukturë të përshtatshme (p.sh. buffer) për përpunim të mëtejshëm nga aplikacioni.

4. **Disconnect (Shkëputja)**
   Përfundon lidhjen ekzistuese duke përdorur identifikuesin e lidhjes, duke liruar kështu burimet (memorie, porte, etj.) që ishin alokuar gjatë komunikimit.

> Këto katër operacione janë "gjuha universale" e çdo komunikimi të orientuar në lidhje — do t'i shohim konkretisht te TCP dhe te protokolli HTTP më poshtë.

### 3.1 Komunikimi Ndërprocese i bazuar në HTTP

HTTP-ja (protokolli mbi të cilin funksionon web-i) është një shembull real i komunikimit të orientuar në lidhje ndërmjet një shfletuesi web (klienti) dhe një serveri web. Sekuenca e komunikimit zhvillohet në katër faza:

**Faza 1 — Inicimi i komunikimit (nga klienti)**
- **C1 – krijo lidhjen**: Shfletuesi web fillon komunikimin duke krijuar një lidhje me serverin.
- **S1 – prano lidhjen**: Serveri web e pranon këtë kërkesë për lidhje.

**Faza 2 — Dërgimi i kërkesës HTTP**
- **C2 – dërgo kërkesën**: Klienti (shfletuesi) dërgon një kërkesë HTTP drejt serverit (p.sh. kërkon një faqe web).
- **S2 – prano kërkesën**: Serveri e pranon dhe e përpunon kërkesën e ardhur.

**Faza 3 — Përpunimi dhe përgjigjja nga serveri**
- **S3 – dërgo përgjigjen**: Serveri përgatit dhe dërgon një përgjigje HTTP (p.sh. HTML, JSON, etj.).
- **C3 – prano përgjigjen**: Klienti e pranon përgjigjen dhe e shfaq për përdoruesin.

**Faza 4 — Përfundimi i komunikimit**
- **C4 – shkëput lidhjen**: Klienti mbyll lidhjen.
- **S4 – shkëput lidhjen**: Serveri gjithashtu çliron burimet dhe mbyll lidhjen.

Rrjedha e plotë e këtij procesi mund të paraqitet si zinxhir ngjarjesh:

```
C1 → S1 → C2 → S2 → S3 → C3 → C4 → S4
```

**Interpretimi teorik i këtij modeli:**

- Komunikimi është **i strukturuar dhe sekuencial** (ndjek një radhë të saktë hapash).
- **Klienti gjithmonë fillon komunikimin** — serveri kurrë nuk e inicion vetë lidhjen.
- **Serveri përgjigjet vetëm pasi merr kërkesën** nga klienti (model pasiv/reaktiv).
- Ky model është **tipik për arkitekturën Client–Server** dhe në veçanti për protokollin HTTP.

---

## 4. Krahasimi i Datagram Socket-ve dhe Stream Socket-ve

Programimi me socket-e (*socket programming*) përdoret për komunikim në rrjet dhe bazohet zakonisht në njërin nga dy protokollet kryesore të shtresës së transportit: **UDP** (*User Datagram Protocol*) ose **TCP** (*Transmission Control Protocol*). Në varësi të protokollit të përdorur, socket-i klasifikohet si **Datagram Socket** (UDP) ose **Stream Socket** (TCP). Dallimet mes tyre janë thelbësore për zgjedhjen e protokollit të duhur për një aplikacion të caktuar:

| Karakteristika | Datagram Socket (UDP) | Stream Socket (TCP) |
|---|---|---|
| Protokolli | UDP | TCP |
| Lidhja | **Pa lidhje** (*connectionless*) | **I orientuar në lidhje** (*connection-oriented*) |
| Forma e transmetimit | Të dhënat dërgohen si **datagram** (pako të pavarura) | Të dhënat transmetohen si **rrjedhë e vazhdueshme** (*stream*) |
| Besueshmëria | **Jo e garantuar** — pakot mund të humbasin | **E lartë** — garantohet dërgimi |
| Shpejtësia | **E lartë** (më pak overhead) | **Më e ngadaltë** se UDP (për shkak të kontrollit) |
| Renditja e mesazheve | **Jo e garantuar** | **E garantuar** |
| Struktura e paketës | Çdo datagram përmban **Header** me adresat IP të burimit dhe destinacionit | — |
| Kontroll gabimesh | Minimal | **I fuqishëm** (rikonfirmim, ritransmetim) |
| Përdorimi tipik | Video streaming, DNS | Web (HTTP), Email, FTP |

**Përmbledhje konceptuale:**

- **UDP** është i shpejtë dhe i thjeshtë, por "best-effort" (nuk garanton se paketa mbërrin, as rendin e mbërritjes). Është i përshtatshëm kur humbja e ndonjë pakete e vogël nuk është kritike (p.sh. transmetim video/audio në kohë reale, ku vonesa është më e dëmshme se humbja e ndonjë kuadri), ose kur mesazhet janë të vetë-përmbajtura dhe të shkurtra (p.sh. kërkesat DNS).
- **TCP** siguron një kanal të besueshëm, të renditur dhe pa gabime, por me kosto shtesë performance (handshake, konfirmime, ritransmetime). Përdoret aty ku korrektësia e të dhënave është prioritet mbi shpejtësinë (web, email, transferim skedarësh).

---

## 5. Java Datagram Socket API (UDP)

API-ja e Java-s për komunikim UDP bazohet në dy klasa kryesore, të dyja pjesë e paketës `java.net`:

- **`DatagramSocket`** — përfaqëson vetë socket-in, pra "portën" në rrjet nëpërmjet së cilës dërgohen/merren të dhëna.
- **`DatagramPacket`** — përfaqëson një datagram, pra paketën e vetme të të dhënave që do të dërgohet ose pritet.

Një proces që dëshiron të dërgojë ose të marrë të dhëna duke përdorur këtë API duhet të krijojë instanca të të dyja këtyre klasave: një objekt `DatagramSocket` (socket-i) dhe një ose më shumë objekte `DatagramPacket` (pakot që transportohen). Rregull i rëndësishëm: çdo socket në procesin **marrës** duhet të jetë i lidhur (*bound*) me një **port UDP** specifik në makinën lokale të procesit, në mënyrë që sistemi operativ të dijë ku t'i dorëzojë të dhënat hyrëse.

### 5.1 Roli i Socket-eve dhe Portave në Komunikimin Ndërprocese

Një **socket** është pika fundore (*endpoint*) e komunikimit mes dy proceseve — mund të mendohet si një **ndërfaqe software-i** që lejon dërgimin dhe marrjen e të dhënave nëpër rrjet. Çdo socket identifikohet në mënyrë unike nga kombinimi i dy elementeve:

```
Socket = (Adresa IP) + (Numri i Portës)
```

Një **port** është një numër logjik në rangun **0–65535**, që shërben për të identifikuar një proces ose shërbim specifik brenda një makine. Meqë një makinë e vetme mund të ekzekutojë shumë aplikacione njëkohësisht që të gjitha komunikojnë në rrjet, portat janë ato që i dallojnë këto aplikacione nga njëri-tjetri (adresa IP identifikon *makinën*, porti identifikon *aplikacionin* brenda saj).

**Klasifikimi i portave:**

- **Well-known ports (0–1023)** — të rezervuara për shërbime standarde të njohura gjerësisht, p.sh. HTTP (porti 80), HTTPS (porti 443), FTP (porti 21).
- **Registered ports (1024–49151)** — të disponueshme për aplikacione të ndryshme të regjistruara (jo standarde universale, por të njohura).
- **Dynamic/Private ports (49152–65535)** — përdoren zakonisht për lidhje të përkohshme, tipike për anën e klientit (*client-side*), të cilat sistemi operativ i cakton automatikisht.

**Si funksionon komunikimi me socket-e dhe porta, hap pas hapi:**

- **Procesi server:**
  1. Hap një socket.
  2. Lidhet (*binds*) me një port specifik.
  3. Pret kërkesa nga klientët (dëgjon në atë port).

- **Procesi klient:**
  1. Krijon socketin e vet.
  2. Dërgon kërkesën drejt adresës IP të serverit dhe portës përkatëse të tij.

- **Sistemi operativ:**
  - Përdor informacionin e portave për të drejtuar (rutuar) të dhënat hyrëse te procesi i saktë brenda makinës.

**Shembull ilustrues:** Kur hapim një faqe web, shfletuesi (klienti) përdor një **port të përkohshëm** (nga rangu dinamik), ndërsa serveri web përdor **portin 80** (për HTTP) ose **portin 443** (për HTTPS). I gjithë ky komunikim realizohet nëpërmjet socket-eve.

### 5.2 Komunikimi me Datagram (UDP): Dërguesi dhe Marrësi

Për të kuptuar plotësisht se si funksionon shkëmbimi UDP, është e dobishme të shohim çka i duhet secilës palë dhe si ndodh aktiviteti hap pas hapi.

**Procesi i Dërguesit (Sender Process)** ka nevojë për:
- Një varg bajtesh (*Byte Array*) me të dhënat për t'u dërguar,
- Adresën e Marrësit,
- Një objekt `DatagramPacket`,
- Një objekt `DatagramSocket`,
- Operacionin `send` (dërgim).

*Si ndodh aktiviteti te dërguesi:*
1. Të dhënat ruhen në një varg bajtesh.
2. Shtohet adresa e marrësit (destinacionit).
3. Krijohet një objekt `DatagramPacket` që i "paketon" së bashku të dhënat dhe adresën.
4. Përdoret `DatagramSocket` për të dërguar paketën në rrjet.

**Procesi i Marrësit (Receiver Process)** ka nevojë për:
- Një objekt `DatagramSocket` (të lidhur me një port të caktuar),
- Operacionin `receive` (marrje),
- Një objekt `DatagramPacket` (bosh, që do të mbushet),
- Një varg bajtesh ku vendosen të dhënat e marra.

*Si ndodh aktiviteti te marrësi:*
1. `DatagramSocket` pret (në mënyrë bllokuese) për të dhëna hyrëse.
2. Kur mbërrin një paketë, ajo merret si objekt `DatagramPacket`.
3. Të dhënat brenda paketës ekstraktohen në vargun e bajteve për t'u përpunuar nga aplikacioni.

---

## 6. UDP Klienti dhe UDP Serveri — Implementim në Java

**Detyra:** Të shkruhet kodi në Java që mundëson krijimin e:
- një **UDP Klienti** që dërgon një mesazh te serveri dhe pret ta marrë përgjigjen, dhe
- një **UDP Serveri** që në mënyrë të përsëritur (në një cikël të pafund) pret kërkesa dhe ia dërgon përsëri (echo) klientit.

### 6.1 UDP Klienti

```java
import java.net.*; // Importon klasat për komunikim në rrjet (DatagramSocket, DatagramPacket, InetAddress, etj.)
import java.io.*;   // Importon klasat për hyrje/dalje (Input/Output)

public class UDPKlienti {                                  // Klasa publike UDPKlienti
    public static void main(String args[]) {                // args[0] = mesazhi, args[1] = emri i hostit (serverit)
        DatagramSocket aSocket = null;                       // Deklaron socketin UDP, fillimisht pa vlerë
        try {
            aSocket = new DatagramSocket();                  // Krijon socket UDP për klientin; sistemi zgjedh vetë një port të lirë
            byte[] m = args[0].getBytes();                   // Konverton mesazhin (String) në varg bajtesh
            InetAddress aHost = InetAddress.getByName(args[1]); // Merr adresën IP të serverit nga emri i hostit (p.sh. "localhost")
            int serverPort = 6789;                           // Porti ku serveri dëgjon

            DatagramPacket request =
                new DatagramPacket(m, m.length(), aHost, serverPort); // Paketa me mesazhin, adresën dhe portin e serverit
            aSocket.send(request);                           // Dërgon paketën te serveri

            byte[] buffer = new byte[1000];                  // Buffer për ruajtjen e përgjigjes
            DatagramPacket reply = new DatagramPacket(buffer, buffer.length); // Paketë bosh për përgjigjen
            aSocket.receive(reply);                          // Pret (bllokuese) dhe merr përgjigjen nga serveri
            System.out.println("Reply: " + new String(reply.getData())); // Shfaq mesazhin e marrë

        } catch (SocketException e) {
            System.out.println("Socket: " + e.getMessage()); // Gabime lidhur me socketin
        } catch (IOException e) {
            System.out.println("IO: " + e.getMessage());     // Gabime hyrje/daljeje
        } finally {
            if (aSocket != null) aSocket.close();             // Mbyllja e socketit — liron burimet, ekzekutohet gjithsesi
        }
    }
}
```

**Shpjegim i rrjedhës logjike:** Klienti krijon një socket "të hapur" (pa port të caktuar manualisht), e mbështjell mesazhin bashkë me adresën dhe portin e serverit në një `DatagramPacket`, e dërgon, dhe pastaj **pret në mënyrë bllokuese** (`receive`) derisa serveri t'i kthejë përgjigjen. Bllokimi `finally` siguron që socket-i mbyllet gjithmonë, pavarësisht a ka pasur përjashtim (*exception*) apo jo.

### 6.2 UDP Serveri

```java
import java.net.*; // Importon klasat për UDP, socket, datagram, etj.
import java.io.*;  // Importon klasat për hyrje/dalje

public class UDPServer {
    public static void main(String args[]) {
        DatagramSocket aSocket = null;                        // Deklaron socketin, fillimisht pa vlerë
        try {
            aSocket = new DatagramSocket(6789);                // Socket UDP që dëgjon në portin 6789
            byte[] buffer = new byte[1000];                    // Buffer 1000 bajtësh për të dhënat hyrëse

            while (true) {                                     // Cikël i pafund — serveri punon vazhdimisht
                DatagramPacket request =
                    new DatagramPacket(buffer, buffer.length);  // Paketë bosh për marrjen e kërkesës
                aSocket.receive(request);                       // Pret bllokueshëm derisa mbërrin një mesazh

                DatagramPacket reply = new DatagramPacket(
                    request.getData(),                          // Përdor të njëjtat të dhëna të marra (echo)
                    request.getLength(),
                    request.getAddress(),                        // Adresa e klientit dërgues
                    request.getPort());                          // Porti i klientit dërgues
                aSocket.send(reply);                             // Dërgon përgjigjen mbrapsht te klienti
            }
        } catch (SocketException e) {
            System.out.println("Socket: " + e.getMessage());
        } catch (IOException e) {
            System.out.println("IO: " + e.getMessage());
        } finally {
            if (aSocket != null) aSocket.close();                 // Mbyll socketin në fund
        }
    }
}
```

**Shpjegim i rrjedhës logjike:** Serveri hap një socket të lidhur eksplicit me portin **6789** dhe hyn në një cikël `while(true)` — pra funksionon vazhdimisht, duke pritur kërkesa njëra pas tjetrës. Për çdo kërkesë të marrë, serveri ndërton menjëherë një paketë përgjigjeje me **të njëjtat të dhëna** (duke përdorur `request.getAddress()` dhe `request.getPort()` për të ditur ku ta kthejë përgjigjen) — kjo është një server tipi **echo** (jehonë), sepse ia kthen klientit saktësisht atë që ka marrë prej tij.

> **Vërejtje:** Meqenëse UDP-ja është pa lidhje (*connectionless*), serveri nuk "pranon" ndonjë lidhje siç do të bënte një server TCP — ai thjesht dëgjon vazhdimisht në portin e caktuar dhe përpunon çdo paketë të pavarur që mbërrin.

---

## 7. Stream-Mode Socket API në Java (TCP)

Për komunikim të bazuar në rrjedhë (*stream*), Java ofron **Stream-Mode Socket API**, i cili gjithashtu bazohet në modelin klient–server dhe përbëhet nga dy klasa kryesore, të dyja pjesë e paketës `java.net`:

- **`ServerSocket`**
  - Përdoret për **pranimin e lidhjeve** (*accepting connections*).
  - Krijohet vetëm në anën e serverit.
  - Lidhet me një port specifik lokal dhe pret kërkesa nga klientët.
  - Kur pranon një lidhje (me metodën `accept()`), krijon një **connection socket** të ri për komunikimin me atë klient specifik.

- **`Socket`**
  - Përdoret për **shkëmbimin real të të dhënave** (*data exchange*).
  - Ekziston si në anën e klientit, ashtu edhe në anën e serverit (pas `accept()`).
  - Përfaqëson një **lidhje aktive rrjeti** mes dy pikave.
  - Mundëson dërgimin dhe marrjen e të dhënave përmes rrjedhave (*streams*) hyrëse/dalëse.
  - Njihet gjithashtu si **data socket**.

> **Dallimi konceptual kyç nga UDP:** Te TCP, para se të dhëna të mund të shkëmbehen, duhet **të krijohet fillimisht një lidhje** e qëndrueshme (përmes `accept()` në server dhe konstruktorit `Socket(...)` në klient). Vetëm pasi lidhja të jetë vendosur, të dhënat rrjedhin nëpër të si një kanal i vazhdueshëm.

---

## 8. TCP Klienti dhe TCP Serveri — Implementim në Java

**Detyra:** Të shkruhet kodi në Java që i mundëson:
- serverit të hapë portin dhe të presë lidhje,
- klientit të lidhet me serverin,
- klientit të dërgojë një mesazh, dhe
- serverit ta pranojë dhe ta shfaqë atë mesazh.

### 8.1 TCP Serveri

```java
import java.net.*; // Klasat për komunikim në rrjet (Socket, ServerSocket)
import java.io.*;  // Klasat për hyrje/dalje (BufferedReader, InputStreamReader, etj.)

public class TCPServeri {                                     // Klasa kryesore e serverit
    public static void main(String[] args) throws Exception { // Metoda main; gabimet nuk trajtohen brenda, shpallen me throws

        ServerSocket server = new ServerSocket(5000);          // Server që dëgjon në portin 5000 — "dera" ku lidhen klientët
        System.out.println("Serveri duke pritur...");          // Njofton që serveri pret lidhje

        Socket socket = server.accept();                       // Bllokohet derisa një klient të lidhet; krijon Socket për atë klient

        BufferedReader in = new BufferedReader(
            new InputStreamReader(socket.getInputStream()));   // Lexues i rrjedhës hyrëse të socketit, si tekst karakteresh

        String mesazhi = in.readLine();                        // Lexon një rresht të plotë tekst nga klienti
        System.out.println("Mesazhi nga klienti: " + mesazhi); // Shfaq mesazhin e marrë

        socket.close();                                        // Mbyll lidhjen me klientin
        server.close();                                        // Mbyll serverin — ndalon dëgjimin në portin 5000
    }
}
```

**Shpjegim i rrjedhës logjike:** `ServerSocket` hapet në portin 5000 dhe menjëherë thërret `accept()`, i cili **bllokon ekzekutimin** derisa ndonjë klient të kërkojë lidhje. Sapo lidhja pranohet, krijohet një objekt `Socket` i dedikuar për komunikim me atë klient specifik; nga ky socket lexohet rrjedha hyrëse me anë të `BufferedReader`, dhe mesazhi lexohet rresht pas rreshti me `readLine()`. Në fund, si socket-i i lidhjes ashtu edhe vetë serveri mbyllen.

### 8.2 TCP Klienti

```java
import java.net.*;
import java.io.*;

public class TCPKlienti {                                     // Klasa kryesore e klientit
    public static void main(String[] args) throws Exception {

        Socket socket = new Socket("localhost", 5000);         // Krijon lidhje me serverin në "localhost", port 5000

        PrintWriter out = new PrintWriter(
            socket.getOutputStream(), true);                   // Shkrues drejt rrjedhës dalëse; true = auto-flush (dërgim i menjëhershëm)

        out.println("Përshëndetje nga klienti!");               // Dërgon mesazhin tekst tek serveri

        socket.close();                                        // Mbyll lidhjen pasi mesazhi është dërguar
    }
}
```

**Shpjegim i rrjedhës logjike:** Klienti krijon një `Socket` që lidhet direkt me serverin në adresën `"localhost"` dhe portin `5000` — kjo është ekuivalenti i operacionit `connect` të përmendur në seksionin 3. Më pas ndërton një `PrintWriter` mbi rrymën dalëse të socket-it (me `auto-flush = true`, që siguron dërgim të menjëhershëm pa pritur mbushjen e buffer-it), i dërgon mesazhin me `println`, dhe në fund mbyll socket-in.

> **Krahasim me UDP:** Vini re ndryshimin thelbësor — te TCP nuk përdoren `DatagramPacket` apo adresa eksplicite për çdo mesazh; në vend të kësaj, pasi lidhja të jetë krijuar (`accept`/`new Socket(...)`), komunikimi bëhet thjesht duke shkruar/lexuar në rrjedha (*streams*), njësoj sikur të ishin skedarë.

---

## 9. Forma e Serializuar e IPC në Java

Kur një objekt duhet të dërgohet nëpër rrjet (ose të ruhet në disk), ai duhet së pari të **konvertohet** nga një strukturë e brendshme memorie (referenca, fusha të tipeve të ndryshme) në një **sekuencë bajtesh** të transmetueshme — ky proces quhet **serializim** (*serialization*), ndërsa procesi i kundërt quhet **deserializim**. Java e realizon këtë automatikisht për objektet që implementojnë ndërfaqen `Serializable`.

Forma e serializuar përfshin jo vetëm vlerat e të dhënave, por edhe **metadata** të nevojshme për ta rindërtuar objektin saktë në anën tjetër: emrin e klasës, numrin e versionit, si dhe tipin, numrin dhe emrin e secilës variabël instance.

**Shembull konceptual — serializimi i një objekti `Person`:**

| Përbërës | Vlera e serializuar | Shpjegimi |
|---|---|---|
| Kokë (header) klase | `Person`, versioni 8-bajtësh `h0` | Emri i klasës dhe numri i versionit |
| Përshkrimi i fushave | `3` `int` `viti`; `java.lang.String name:`; `java.lang.String place:` | Numri, tipi dhe emri i variablave të instancës |
| Vlerat | `1980`; `5 Smith`; `6 Prishtinë` `h1` | Vlerat aktuale të variablave të instancës |

Këtu `h0` dhe `h1` janë **trajtime** (*handles*) — referenca të brendshme që përdor mekanizmi i serializimit të Java-s për të identifikuar në mënyrë unike objektet dhe stringjet e serializuara, veçanërisht të dobishme kur i njëjti objekt referohet më shumë se një herë. Është e rëndësishme të theksohet se **forma e vërtetë e serializuar** përmban shumë më tepër tipe shënimesh (metadata) shtesë krahasuar me këtë paraqitje të thjeshtuar ilustruese.

### 9.1 Përfaqësimi alternativ: XML

Përveç formatit binar nativ të Java-s, të dhënat strukturore (si objekti `Person`) mund të përfaqësohen edhe në formate tekstuale të lexueshme nga njeriu, si **XML**, të cilat janë veçanërisht të dobishme për **ndërveprueshmëri** (*interoperability*) mes sistemeve/gjuhëve të ndryshme.

**Definimi bazë i strukturës `Person` në XML:**

```xml
<person id="123456789">
    <name>Smith</name>
    <place>Prishtinë</place>
    <year>1980</year>
    <!-- një koment -->
</person>
```

**Përdorimi i namespace-ve** (hapësirave të emrave) lejon që elementët XML të lidhen qartazi me një fjalor specifik termash, duke shmangur konfliktet e emrave kur kombinohen dokumente nga burime të ndryshme:

```xml
<person pers:id="123456789" xmlns:pers="http://fiek.uni-pr.edu/person">
    <pers:name>Smith</pers:name>
    <pers:place>Prishtinë</pers:place>
    <pers:year>1980</pers:year>
</person>
```

Këtu prefiksi `pers:` shoqëron secilin element/atribut me hapësirën e emrave të deklaruar (`xmlns:pers`), duke saktësuar pa dykuptimësi se elementi `name` i referohet fjalorit "person", jo ndonjë fjalori tjetër me emër të njëjtë elementi.

**Skema XML (XSD)** përcakton në mënyrë formale strukturën e lejuar për një dokument XML — pra çfarë elementësh, atributesh dhe tipesh të dhënash duhet të ketë një `person` i vlefshëm:

```xml
<xsd:schema xmlns:xsd="URL of XML schema definitions">
    <xsd:element name="person" type="personType" />
    <xsd:complexType name="personType">
        <xsd:sequence>
            <xsd:element name="name" type="xs:string"/>
            <xsd:element name="place" type="xs:string"/>
            <xsd:element name="year" type="xs:positiveInteger"/>
        </xsd:sequence>
        <xsd:attribute name="id" type="xs:positiveInteger"/>
    </xsd:complexType>
</xsd:schema>
```

Skema thotë, në thelb: "një element `person` është i tipit `personType`, i cili përbëhet nga një sekuencë e renditur e elementëve `name` (string), `place` (string) dhe `year` (numër i plotë pozitiv), plus një atribut `id` (gjithashtu numër i plotë pozitiv)". Përdorimi i skemave XML lejon **validimin automatik** të dokumenteve para se ato të përpunohen nga aplikacioni marrës.

---

## 10. Referenca për një Objekt në Distancë (Remote Object Reference)

Në sistemet e shpërndara të bazuara në objekte (si Java RMI — *Remote Method Invocation*), një koncept qendror është **referenca e objektit në distancë**: mundësia që një referencë drejt një objekti të "largët" (që jeton në një proces/makinë tjetër) të **kalohet si argument** ose **të kthehet si rezultat** i thirrjes së një metode — qoftë kjo thirrje lokale apo vetë e largët.

Një objekt i largët mund të dërgohet përmes cilësdo ndërfaqe të largët të mbështetur nga implementimi, duke përdorur sintaksën standarde të Java-s (p.sh. ndërfaqe që zgjerojnë `java.rmi.Remote`).

**Struktura tipike e një reference për objekt në distancë** përbëhet nga katër fusha, secila me gjatësi 32 bitë:

```
| Adresa e Internetit (32 bit) | Numri i Portës (32 bit) | Koha (32 bit) | Numri i Objektit (32 bit) | + ndërfaqja e objektit remote |
```

- **Adresa e internetit** — identifikon makinën ku ndodhet objekti.
- **Numri i portës** — identifikon procesin (serverin RMI) në atë makinë.
- **Koha** — përdoret shpesh si vulë kohore për të dalluar instanca të ndryshme (parandalon konfuzione kur objekte krijohen dhe shkatërrohen me kalimin e kohës).
- **Numri i objektit** — identifikon në mënyrë unike objektin specifik brenda procesit.
- **Ndërfaqja e objektit remote** — përcakton se cilat metoda mund të thirren në atë objekt nga distanca.

Kjo strukturë lejon që, edhe pse objekti fizikisht "jeton" në një JVM tjetër, klienti të mund ta trajtojë atë **si të ishte lokal**, thjesht duke thirrur metodat e tij përmes ndërfaqes së largët — vetë middleware-i (RMI) kujdeset për të "gjetur" objektin real duke përdorur këtë referencë.

### 10.1 Detyrë e zbatuar: kalimi dhe kthimi i referencave të objekteve të largëta

**Detyra:** Të shkruhet një program në Java ku një referencë e një objekti të largët kalohet si argument në një metodë dhe kthehet si rezultat nga një metodë tjetër. Programi duhet të përmbajë:
- një **ndërfaqe të largët** (*Remote Interface*) për shërbimin,
- një **ndërfaqe të dytë të largët** (p.sh. `ClientRemote`) që do të dërgohet si referencë,
- implementimin e serverit dhe të klientit.

**Kërkesat funksionale:**
- Referenca e objektit të largët `ClientRemote` të dërgohet si argument në server.
- Serveri ta ruajë këtë referencë dhe ta përdorë atë për **callback** (thirrje mbrapsht te klienti).
- Referenca të kthehet si rezultat nga metoda `getClient()`.
- Klienti ta përdorë përsëri këtë referencë për thirrje të mëtejshme.

#### 10.1.1 Ndërfaqet e largëta (Remote Interfaces)

```java
// Ndërfaqja për objektin e largët të shërbimit
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface RemoteService extends Remote {
    // Metodë që pranon një objekt të largët si argument
    void registerClient(ClientRemote client) throws RemoteException;
    // Metodë që kthen një objekt të largët si rezultat
    ClientRemote getClient() throws RemoteException;
}
```

```java
// Një tjetër ndërfaqe e largët që do të dërgohet si referencë (për klientin)
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface ClientRemote extends Remote {
    void notifyMessage(String msg) throws RemoteException;
}
```

Që një objekt të mund të thirret nga distanca në Java RMI, ai duhet t'i nënshtrohet një **ndërfaqeje** që zgjeron `Remote`, dhe çdo metodë e saj duhet të deklarojë `throws RemoteException` (sepse thirrja mund të dështojë për shkak rrjeti).

#### 10.1.2 Implementimi i Serverit

```java
public class RemoteServiceImpl extends UnicastRemoteObject implements RemoteService {

    private ClientRemote clientRef;                          // Ruajtja e referencës drejt klientit

    protected RemoteServiceImpl() throws RemoteException {
        super();
    }

    // Merr një referencë objekti të largët si argument
    @Override
    public void registerClient(ClientRemote client) throws RemoteException {
        System.out.println("Klienti u regjistrua.");
        this.clientRef = client;                              // Ruan referencën për përdorim të mëvonshëm
        // thirrje mbrapsht (callback) drejt objektit të largët të klientit
        client.notifyMessage("Mirë se erdhe nga serveri!");
    }

    // Kthen referencën e ruajtur si rezultat
    @Override
    public ClientRemote getClient() throws RemoteException {
        return clientRef;
    }
}
```

**Serveri (klasa Main):**

```java
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;

public class Server {
    public static void main(String[] args) {
        try {
            RemoteService service = new RemoteServiceImpl();
            Registry registry = LocateRegistry.createRegistry(1099); // Krijon regjistrin RMI në portin 1099
            registry.rebind("MyService", service);                  // Regjistron shërbimin me emrin "MyService"
            System.out.println("Serveri është gati...");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

Elementi qendror këtu është `clientRef` — objekti `RemoteServiceImpl` **ruan** referencën e klientit të marrë si argument te `registerClient`, dhe menjëherë e përdor për një **callback** (`client.notifyMessage(...)`) — pra serveri "thërret mbrapsht" drejt klientit, edhe pse zakonisht klienti është ai që inicion komunikimin. Kjo demonstron pikërisht fuqinë e referencave të objekteve në distancë: një objekt i largët mund t'i kalohet tjetrit dhe të thirret prej tij.

#### 10.1.3 Implementimi i Klientit

```java
import java.rmi.server.UnicastRemoteObject;
import java.rmi.RemoteException;

public class ClientImpl extends UnicastRemoteObject implements ClientRemote {

    protected ClientImpl() throws RemoteException {
        super();
    }

    @Override
    public void notifyMessage(String msg) throws RemoteException {
        System.out.println("Mesazh nga serveri: " + msg);
    }
}
```

**Klienti (klasa Main):**

```java
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;

public class Client {
    public static void main(String[] args) {
        try {
            Registry registry = LocateRegistry.getRegistry("localhost", 1099); // Lidhet me regjistrin RMI
            RemoteService service = (RemoteService) registry.lookup("MyService"); // Kërkon shërbimin

            // krijojmë një objekt të largët dhe e dërgojmë si argument
            ClientRemote clientObj = new ClientImpl();
            service.registerClient(clientObj);                     // Regjistrohet te serveri, që ruan referencën

            // marrim një referencë objekti të largët si rezultat
            ClientRemote returnedClient = service.getClient();
            // përdorim referencën e kthyer
            returnedClient.notifyMessage("Thirrje nga klienti për veten!");

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**Analiza e rrjedhës së plotë:**
1. Serveri regjistron shërbimin `MyService` në **RMI Registry** (portin 1099).
2. Klienti gjen shërbimin përmes `registry.lookup("MyService")`.
3. Klienti krijon vetë një objekt të largët `ClientImpl` dhe **e dërgon si argument** te `service.registerClient(clientObj)` — kjo është shembulli i "kalimit të referencës si argument".
4. Serveri e ruan këtë referencë (`clientRef`) dhe menjëherë e thërret (callback) me `notifyMessage`.
5. Klienti më pas thërret `service.getClient()`, që **kthen si rezultat** të njëjtën referencë që serveri e ruajti — kjo është shembulli i "kthimit të referencës si rezultat".
6. Klienti e përdor përsëri këtë referencë të kthyer për një thirrje shtesë (`returnedClient.notifyMessage(...)`), duke vërtetuar se referenca funksionon njësoj si origjinali, edhe pse ka "udhëtuar" nëpër rrjet dhe është kthyer mbrapsht.

---

## 11. Komunikimet Multicast

Siç u përmend në seksionin 2.1, **multicast** është forma e IPC-së në të cilën një proces dërgues i adreson të dhënat **njëkohësisht** te një grup i tërë procesesh marrëse (dhe jo te një marrës i vetëm, siç ndodh te unicast/socket). Java e mbështet komunikimin multicast përmes klasës `MulticastSocket`, e cila zgjeron `DatagramSocket` me aftësinë për t'u bashkuar me **grupe multicast** (identifikuar nga adresa IP speciale në rangun 224.0.0.0–239.255.255.255).

### 11.1 Detyrë e zbatuar: shfrytëzuesi (peer) multicast

**Detyra:** Të shkruhet kodi në Java për një përdorues (*peer*) multicast, i cili:
1. bashkohet me një grup multicast,
2. dërgon datagrame (mesazhe) drejt grupit,
3. merr 3 herë datagrame (mesazhe) nga grupi, dhe
4. në fund largohet nga grupi multicast.

```java
import java.net.*; // Klasat për rrjet (socket, multicast, IP adresa, etj.)
import java.io.*;  // Klasat për hyrje/dalje

public class MulticastPeer {
    public static void main(String args[]) {
        // args[0] = mesazhi, args[1] = adresa multicast (p.sh. "228.5.6.7")
        MulticastSocket s = null;                                  // Socket multicast, fillimisht bosh
        try {
            InetAddress group = InetAddress.getByName(args[1]);    // Merr adresën IP të grupit multicast
            s = new MulticastSocket(6789);                         // Krijon socket multicast në portin 6789
            s.joinGroup(group);                                    // Bashkohet me grupin — fillon të pranojë mesazhet e tij

            byte[] m = args[0].getBytes();                         // Konverton mesazhin në varg bajtesh
            DatagramPacket messageOut =
                new DatagramPacket(m, m.length, group, 6789);      // Paketë me të dhënat, adresën e grupit dhe portin
            s.send(messageOut);                                    // Dërgon paketën drejt gjithë grupit

            byte[] buffer = new byte[1000];                        // Buffer për mesazhet hyrëse
            for (int i = 0; i < 3; i++) {                          // Përsërit marrjen 3 herë
                DatagramPacket messageIn =
                    new DatagramPacket(buffer, buffer.length);      // Paketë bosh për marrje
                s.receive(messageIn);                                // Pret dhe merr një mesazh nga grupi
                System.out.println("Received:" + new String(messageIn.getData())); // Shfaq mesazhin
            }

            s.leaveGroup(group);                                    // Del/largohet nga grupi multicast

        } catch (SocketException e) {
            System.out.println("Socket: " + e.getMessage());        // Gabime të socketit
        } catch (IOException e) {
            System.out.println("IO: " + e.getMessage());            // Gabime hyrje/daljeje
        } finally {
            if (s != null) s.close();                                 // Mbyll socketin, pavarësisht rezultatit
        }
    }
}
```

**Shpjegim i rrjedhës logjike:** Ky program demonstron ciklin e plotë të jetës së një pjesëmarrësi (*peer*) në një komunikim multicast:

1. **Bashkimi me grupin** (`s.joinGroup(group)`) — regjistron këtë socket si dëgjues të adresës multicast të dhënë; nga ky moment, sistemi i dorëzon çdo trafik të dërguar te ai grup.
2. **Dërgimi** (`s.send(messageOut)`) — paketa i dërgohet **vetë adresës së grupit** (jo një marrësi individual), dhe infrastruktura e rrjetit (routerat me mbështetje multicast) e shpërndan atë te të gjithë anëtarët e grupit.
3. **Marrja** — meqë vetë procesi është pjesë e grupit, ai gjithashtu mund të marrë mesazhet e dërguara në grup (përfshirë potencialisht mesazhin e vet, në varësi të konfigurimit); këtu marrja përsëritet tri herë me anë të një cikli `for`.
4. **Largimi nga grupi** (`s.leaveGroup(group)`) — çregjistron pjesëmarrjen, duke ndaluar marrjen e mëtejshme të trafikut nga ai grup.

> **Vërejtje krahasuese:** Struktura e kodit është shumë e ngjashme me atë të `DatagramSocket`/UDP (të njëjtat klasa `DatagramPacket`), gjë që tregon se multicast-i në thelb është një **shtresë shtesë mbi UDP** — ndryshimi kryesor qëndron te përdorimi i `MulticastSocket` dhe operacionet `joinGroup`/`leaveGroup`, si dhe te fakti që adresa e destinacionit i referohet një **grupi** dhe jo një marrësi të vetëm.

---

## Përmbledhje

Në këtë kapitull u trajtuan bazat e komunikimit ndërprocese në sistemet e shpërndara, nga niveli konceptual te implementimi konkret në Java:

- **Middleware-i** vendos mes aplikacionit dhe rrjetit, duke ofruar abstraksione si socket-et, message passing-un, multicast-in dhe rrjetet e mbivendosjes (overlay).
- **IPC** është mekanizmi i shkëmbimit të të dhënave mes proceseve, i realizuar në sisteme të shpërndara përmes API-ve të nivelit të lartë (send/receive); komunikimi klasifikohet si **unicast** (një-me-një) ose **multicast** (një-me-shumë).
- Një API i orientuar në lidhje ofron katër operacione themelore: **connect, send, receive, disconnect** — ilustruar konkretisht me sekuencën C1→S1→C2→S2→S3→C3→C4→S4 të protokollit **HTTP**.
- **Datagram socket-et (UDP)** janë pa lidhje, të shpejta por jo të besueshme, ndërsa **stream socket-et (TCP)** janë të orientuara në lidhje, të besueshme dhe të renditura — çdo lloj socket-i i përshtatet aplikacioneve të ndryshme.
- Programimi UDP në Java përdor `DatagramSocket` dhe `DatagramPacket`; programimi TCP përdor `ServerSocket` (për pranim lidhjesh) dhe `Socket` (për shkëmbim të dhënash) — pamë implementime të plota klient-server për të dyja.
- **Serializimi** konverton objektet në një sekuencë bajtesh (ose në XML, me skemë validuese XSD) për transmetim nëpër rrjet ose ruajtje.
- **Referencat e objekteve në distancë** (si te Java RMI) lejojnë që objekte të largëta t'i kalohen njëri-tjetrit si argumente ose rezultate metodash, duke mundësuar modele si callback-u — realizuar praktikisht përmes `Remote`, `UnicastRemoteObject` dhe `RMI Registry`.
- **Multicast-i** në Java realizohet përmes `MulticastSocket`, me operacionet `joinGroup`, `send`/`receive` dhe `leaveGroup`, duke lejuar një dërgues t'i arrijë njëkohësisht të gjithë anëtarët e një grupi.

## Pyetje kontrolli (vetë-vlerësim)

1. Çfarë është një socket, dhe si përcaktohet ai në mënyrë unike (nga cilat dy vlera)?
2. Cili është ndryshimi kryesor midis komunikimit *unicast* dhe atij *multicast*? Jepni nga një shembull për secilin.
3. Renditni dhe shpjegoni katër operacionet themelore të një API të orientuar në lidhje (connect, send, receive, disconnect), duke i lidhur me sekuencën C1–S4 të HTTP-së.
4. Krahasoni Datagram Socket-in (UDP) me Stream Socket-in (TCP) sipas: besueshmërisë, shpejtësisë dhe renditjes së mesazheve. Kur do të zgjidhnit UDP në vend të TCP?
5. Cilat dy klasa përdor Java Datagram Socket API dhe çfarë roli luan secila?
6. Shpjegoni ndryshimin mes klasave `ServerSocket` dhe `Socket` në Stream-Mode Socket API.
7. Pse metoda `server.accept()` te TCP serveri konsiderohet një thirrje "bllokuese" (*blocking*)?
8. Çfarë përmban forma e serializuar e një objekti në Java, përveç vlerave të thjeshta të fushave?
9. Përshkruani rolin e katër fushave 32-bitëshe në një referencë objekti në distancë.
10. Në shembullin e `RemoteService`/`ClientRemote`, si realizohet mekanizmi i "callback-ut" — pra si arrin serveri të thërrasë mbrapsht te klienti?
11. Cilat janë tri veprimet kryesore që duhet të kryejë një pjesëmarrës (peer) në një komunikim multicast, sipas shembullit `MulticastPeer`?
12. Pse `MulticastSocket` konsiderohet një zgjerim i `DatagramSocket`, dhe jo i `Socket` (TCP)?
