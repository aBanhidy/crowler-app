# Crawler platform – POC koncepció

> **Állapot:** kezdeti koncepció, a beszélgetésben összegyűjtött igények alapján. Ez még nem végleges műszaki specifikáció.

## 1. Cél és termékötlet

Egy webes felülettel induló alkalmazás készül, amely a felhasználók által kiválasztott weboldalakon termékeket és akciókat keres. A felhasználó megadhat egy terméknevet – például „zöldségszeletelő” –, opcionálisan kategóriát választ, majd a rendszer a fiókjához rendelt, releváns oldalakon elérhető friss adatok között keres.

A webes alkalmazás később Android- és iOS-alkalmazással egészül ki. A fiók, a beállítások és a keresési adatok minden felületen ugyanahhoz a háttérrendszerhez kapcsolódnak.

## 2. Tervezett felhasználói működés

1. A felhasználó weben vagy mobilalkalmazásban regisztrál, illetve bejelentkezik.
2. Oldalakat ad hozzá a saját listájához, vagy kiválaszt egy már ismert oldalt a közös oldaljegyzékből.
3. Kereséskor megadja a terméket, és igény szerint kategóriát választ.
4. A rendszer csak a felhasználóhoz rendelt, a kategória alapján releváns oldalakon talált adatokat jeleníti meg.
5. Kevés vagy nulla találat esetén a felhasználó kérhet célzott, frissebb ellenőrzést. Ez háttérben fut; az alkalmazás jelzi, amikor elkészült.

## 3. Oldalak és kategóriák

- A támogatott weboldalakról közös, központi jegyzék készül.
- A felhasználó és az oldal között külön kapcsolat tárolódik. Egy oldal közös feldolgozása nem jelenti azt, hogy minden felhasználó automatikusan felveszi a saját listájára.
- Ha a felhasználó már ismert oldalt ad hozzá, csak a saját fiókjához rendeli.
- Az újonnan megadott oldalt ellenőrizni kell, mielőtt a központi crawler feldolgozza.
- Egy oldal több kategóriához is tartozhat. Például egy áruházhoz élelmiszer, ruházat, játék és kert kategória is kapcsolódhat.
- Kereséskor az oldal és a kiválasztott kategória kapcsolata szűkíti a vizsgálandó, illetve megjelenítendő forrásokat.

## 4. Crawler és frissítési stratégia

### Ütemezett frissítés

A crawler ütemezetten, háttérfeladatként gyűjti és frissíti az oldalakról az adatokat. Kiinduló ötlet a néhány óránkénti ellenőrzés – körülbelül 2–6 órás időtartomány –, de ez nem végleges gyakoriság. Az ütemezést oldalanként érdemes kialakítani, az oldal viselkedéséhez, a tartalom változásához és a megengedett terheléshez igazítva.

### Célzott friss keresés

Ha a meglévő adatok alapján kevés vagy nulla találat van, a felhasználó kérhet frissebb ellenőrzést. Ilyenkor csak a saját fiókjához rendelt és a kategóriába illő oldalakon indul ellenőrzés. A feladat nem tartja nyitva a keresőoldalt: állapota követhető, a befejezésről később értesítés adható.

### Keresési adatok

A normál keresés elsődlegesen a crawler által korábban összegyűjtött és tárolt adatok között történik. Így nem kell minden felhasználói kereséskor minden oldalt élőben bejárni, ami lassú és szükségtelenül terhelné a forrásoldalakat.

## 5. Tervezett felépítés

- **Webes alkalmazás és fiókkezelés:** Symfony. A Symfony-választás mellett szól a projektgazda mintegy tízéves Symfony-tapasztalata.
- **Crawler:** Pythonban futó, a webes kérésektől elkülönített háttérfolyamat, a crawleres és adatfeldolgozó eszközök miatt.
- **Közös adatok:** az oldalak és a crawler által gyűjtött adatok központilag tárolódnak; a felhasználói oldalválasztás és beállítások fiókhoz kötöttek.
- **Mobilalkalmazások:** későbbi Android- és iOS-kliensek ugyanazt a háttérrendszert használják. A webes és mobilos regisztráció ugyanazt a felhasználói fiókot hozza létre.
- **Keresztplatformos adatkezelés:** a szerver a közös adatforrás, ezért az egyik felületen végzett módosítás a többi felületen is elérhető.

A webes MVP legyen reszponzív, de az Android- és iOS-felületek megvalósítását a webes működés kipróbálása után kell megtervezni. A crawler, az adatmodell és a fiókkezelés már eleve úgy készüljön, hogy több kliens is használhassa.

## 6. Felhasználói szokások – későbbi lehetőség

Később menthetők lehetnek a felhasználói szokások, például a kedvelt kategóriák és keresések. Ezek segíthetik a személyre szabást és az értesítéseket, de nem szükségesek az első működő változathoz. A pontos adatokat, a mentés módját és a felhasználó általi szabályozást később kell meghatározni.

## 7. POC-javaslat és fokozatos megvalósítás

1. A felhasználói folyamatok és az első verzió funkcióinak pontosítása.
2. Egy kisebb számú, kiválasztott oldal feldolgozhatóságának kipróbálása crawlerrel.
3. Az oldalak, kategóriák, felhasználói oldalválasztások és találatok adatmodelljének kialakítása.
4. Webes MVP készítése regisztrációval, oldalkezeléssel és termékkereséssel.
5. Ütemezett frissítés és célzott, háttérben futó friss keresés hozzáadása.
6. A működés és a használhatóság ellenőrzése után döntés az Android- és iOS-alkalmazások megvalósításáról.

## 8. Korlátok és kockázatok

- Minden weboldal eltérő szerkezetű; az oldalak feldolgozásához forrásonkénti szabályok vagy feldolgozók válhatnak szükségessé.
- A crawler csak az adott oldalak technikai, szerződéses és jogi feltételeit figyelembe véve működhet. A terhelést korlátozni kell, és tiszteletben kell tartani az oldalak szabályait.
- Az akciók és árak változhatnak; a találat nem garantálja, hogy az ajánlat a megnyitás pillanatában még érvényes. A találatoknál ezért hasznos lehet a legutóbbi ellenőrzés idejének megjelenítése.
- Az új oldalak központi feldolgozása tárhely- és futtatási költséggel, valamint karbantartási igénnyel járhat.

## 9. Nyitott döntések

- A kategóriák közösek és az alkalmazás által kezelt értékek legyenek-e, vagy a felhasználók saját kategóriákat is létrehozhassanak? Kiinduló javaslat: közös kategóriák az MVP-ben, egyéni címkék később.
- Pontosan mit tekintünk akciónak, és milyen adatokat mutassunk a találatnál?
- Hogyan kerül egy új oldal a közös oldaljegyzékbe, és ki ellenőrzi, hogy feldolgozható-e?
- Milyen feltételek indítsák el az ütemezett és a felhasználó által kért frissítést?
- Milyen adatbázis, háttérfeladat-kezelés, értesítési csatorna és üzemeltetési környezet szükséges?
- Milyen feltételek mellett legyenek a későbbi mobilalkalmazások natív appok, illetve milyen mobilfunkciókat igényelnek?

## 10. A projekt jelenlegi állapota

A repóban jelenleg egy minimális FastAPI-váz található (`main.py`), amely egy állapotot visszaadó végpontot szolgál ki. Ebben még nincs crawler, felhasználói regisztráció vagy webes felület. Ez a POC-dokumentum a megbeszélt célirányt rögzíti: Symfony-alapú webes alkalmazás és külön Python crawler. A meglévő FastAPI-váz sorsáról a megvalósítás megkezdésekor kell dönteni.