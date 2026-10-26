# Git parancssori eszközök használata
## Git CLI telepítése
Bár a legtöbb IDE-ben és kódszerkesztő-ben megtalálható a Git integráltan, ez a leírás a Git parancssori eszközeivel foglalkozik, Windows operációs rendszer alatt.

A Git CLI letölthető Windows-ra a hivatalos honlapon: https://git-scm.com/install/windows

Ez a leírás a Git rendszerszintű telepítésére épül (nem a portable verzióra), de legalább arra, hogy a Git eszközök elérési útvonala a `$PATH` környezeti változóban el lett helyezve.

Előfordulhat a frissebb Git telepítőknél, hogy a Microsoft Defender SmartScreen nem javasolja a futtatását. Ha a hivatalos forrásból töltöttük le, akkor szinte mindig egy "false positive" jelzést kapunk, vagyis a telepítő valójában biztonságos, de a SmartScreen még nem ismeri fel megbízhatóként.

Ha megbízunk a forrásban, akkor "További információ", majd "Futtatás mindenképpen".

### A telepítés menete

1. Engedélyezzük, hogy a telepítő módosításokat hajtson végre a rendszeren
2. Fogadjuk el a licensz feltételeket
3. Adjuk meg a telepítési helyet (Az alapértelmezett `C:\Program Files\Git` is megfelelő)
4. Komponensválasztás (Ebben a projektben igazából egyikre sincs szükség)
    - Kérhetünk asztali ikont
    - Kérhetjük a nagy fájlok támogatását (Git LFS)
    - Kérhetjük, hogy a git config fájlokat az alapértelmezett szövegszerkesztő nyissa meg
    - Kérhetjük a Scalar bővítményt
5. Kérhetünk Start menü ikont
6. Ki kell választani az alapértelmezett szövegszerkesztőt (Javasoltan Notepad++ vagy Visual Studio Code, ha egyik sincs telepítve, akkor Notepad)
7. A kezdeti ág (branch) neve: javasolt az új standardek szerint kézzel kérni a main név használatát
8. A `$PATH` beállításai: Git from the command line and also 3-rd party software (ez az ajánlott beállítás is egyben)
9. SSH választás: A bundled opció esetén a Git telepít saját ssh.exe-t, az external esetén azt használja, amit talál a `$PATH`-on.
10. HTTPS backend: Windows native
11. Sorvégződések átalakítása/megtartása: Checkout as-is, commit as-is
12. Terminál választás: Windows's default console window
13. git pull működése: Fast-forward only
14. Credential helper: Git Credential Manager
15. Extra lehetőségek: Cache: igen, Symlink: nem

## A Git alapbeállításai

### Saját profil (identitás) megadása

Egy cmd ablakban adjuk ki a következő Git utasításokat:

`git config --global user.name "A teljes neved idézőjelek között"`

`git config --global user.email az_email_cimed_amivel@regisztraltal.githubra`

Ezek a beálítások a `C:\Users\<felhasználói_fiókod>\.gitconfig` fájlban találhatók meg. Ha szeretnéd lekérdezni a beállításaidat, add ki a `git config --list --show-origin` utasítást.

Ha több projektben is dolgozunk (eltérő e-mail címmel) akkor a másik repositoryban is ki lehet adni ezeket a parancsokat a --global nélkül. Ekkor a megadott név/e-mail cím kizárólag abban a tárolóban fog változni.

### Helyi tároló (local repository) létrehozása

Miután a Git ismer bennünket, ki kell alakítanunk egy helyi tárolót. Erre a célra érdemes egy üres mappát létrehozni, majd terminálablakban kiadni a következő két utasítás egyikét:

`git init`

Helyben üres repository-t hoz létre.

`git clone https://github.com/felhasznalo-nev/repository-nev.git`

Kialakítja a helyi tárolót a megadott címen található repository tartalmával, illetve felveszi a távoli kapcsolatot origin néven.
Az elérési címet a GitHub felületén lehet lekérdezni. A főtartalom jobb felső sarkában található `<> Code` menüben. Több lehetőség is van, kezdésnek a HTTPS-t ajánlom, mivel egyszerűbb a csatlakozás.

### Távoli kapcsolat (szerver) elérésének beállítása

Ahhoz, hogy a saját gépről tudjunk kommunikálni a központi GitHub repository irányába, fel kell vennünk a kapcsolatok közé a következő utasítással:

`git remote add origin https://github.com/felhasznalo-nev/repository-nev.git`

**Fontos**: a mintában az origin a kapcsolat neve, nem kötelező ezt a nevet használni, de ez a standard elnevezés.

Az elérési címet a GitHub felületén lehet lekérdezni. A főtartalom jobb felső sarkában található `<> Code` menüben. Több lehetőség is van, kezdésnek a HTTPS-t ajánlom, mivel egyszerűbb a csatlakozás.

Ez még önmagában nem ad teljes elérést a távoli tárolóhoz, az első authentikációt (bejelentkezést) igénylő művelet futtatásakor a Git Credential Manager segítségével bejelentkezéssel kell igazolnunk, hogy van hozzáférésünk a szerveroldalhoz.

## Alapvető utasítások

### Letöltés és feltöltés

`git pull origin ag-nev`

A távoli szerverről a megadott ágról megpróbál mindent letölteni.

`git push origin ag-nev`

A helyi módosításaidat megpróbálja feltölteni a távoli szerver megadott ágára.

### Ágak (branching)

Alapvetően egy időben a projekt eltérő verzióival dolgozunk, úgynevezett ágakon (branch-eken). Minden ágnak lesz egy kiinduló pontja (egy másik ág állapotát másoljuk), majd megkezdjük a fejlesztést. Amikor végeztünk, az ágat rendszerint belefésüljük (merge) egy másikba, ezzel a módosításokat megpróbáljuk átvinni.

A tároló alapága a `main`. Itt kizárólag hibamentes, a késztermékben megtartani kívánt kódot tárolunk. Gyakran használnak mellé `deploy` ágat, amire pedig a kiadásra kész kódokat helyezik el.
A munkánk közben viszont elkerülhetetlen, hogy legyenek félkész, vagy éppen még hibás kódrészeink.
Ennek feloldására létrehozunk egy új ágat a `main`-re alapozva, majd azon folytatjuk a fejlesztést. Ha végeztünk egy adott résszel, akkor a fejlesztői ágat belefésüljük (merge) a `main`-be.

**Fontos**: a `main`-be mindig az online felületen keresztül, úgynevezett `pull request` segítségével egyesítünk ágakat. Fejlesztői ágakat lehet helyben is összevonni, majd feltölteni a változást a szerverre.

`git checkout -b ag-nev`

Létrehoz egy új ágat ag-nev néven, melynek alapja az az ág lesz, amiben benne voltál a parancs kiadásakor.

Honnan lehet tudni, hogy hol vagyunk?

`git branch`

Az összes ágat felsorolja, a csillagozotton állsz éppen.

Hogy lehet átmenni egy másikra?

`git checkout ag-nev`

Átvált az ag-nev ágra, betölti annak tartalmát (állapotát).

### Fejlesztés

Miután kialakítottunk egy új ágat, megkezdhetjük a fejlesztést. A fejlesztés során új fájlok jöhetnek létre. Ezeket a verziókezelés alá kell vonnunk, hozzá kell őket adni az úgynevezett `stage`-hez. (Már ha szükségünk van rájuk, a segédállományokat nem kell bevonni.)

Hogy lehet megtudni, hogy milyen fájljaink vannak a stage-en kívül?

`git status`

Igen hasznos parancs, az adott ág minden lényegi információját kiadja. Listázza a nem stage-elt fájlokat és a stage-elt fájlokat is.
Most hogy tudjuk, milyen új fájljaink vannak, hogy lehet őket betenni a verziókezelés alá?

`git add .`

Ha mindent szeretnénk felvenni, de a . helyére egy-egy fájl elérési útvonalát betehetjük, ha mi szeretnénk dönteni arról, mit vegyünk fel és mit nem.

**Fontos**: a . helyett ne használjunk *-ot, mivel előfordulhat, hogy szükségtelen fájlokat is beletesz a stage-re.

Most már vannak állományok a stage-en. Ezek a módosításainkat tárolják. Érdemes rövid időközönként ezeket menteni, hogy egy tiszta fejlesztési képet kapjunk, illetve hogy legyenek biztonsági mentéseink egy lehetséges visszatöltéshez.

A stage tartalmát véglegesíteni így lehet:

`git commit -m "commit message"`

Minden stage-elt fájlt, és azok módosításait fogja rögzíteni. A -m kapcsoló után egy rövid szöveges leírást adhatunk a változás jellegéről. Ezt nem érdemes kihagyni.

## A .gitignore fájl lényege

A távoli szerverre sok esetben nem akarunk mindent feltölteni. Például nem akarjuk, hogy a lefordított állományok ott foglalják a helyet.
Erre a célra használjuk a .gitignore fájlokat, amik elérési utak és fájltípusok alapján zárnak ki a szerverre felesleges tartalmakat.

Honnan lehet ilyet szerezni?
Sok IDE és kódszerkesztő maga generál egyet a projekt típusához, de az internetről is lehet szerezni, sőt ma már az AI chatbotok is igen jó .gitignore fájlokat tudnak generálni.
