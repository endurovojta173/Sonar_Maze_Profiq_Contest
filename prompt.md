Jsi expert na vývoj webových her a algoritmizaci. Tvojí úlohou je vytvořit kompletní, plně funkční a vysoce optimalizovanou HTML5 hru "Sonar Maze" obsaženou v jediném souboru který vytvoříš - index.html. Nesmíš použít žádné externí knihovny, obrázky, zvuky ani fonty. Vše řeš čistě přes vanilla JavaScript, CSS a HTML5 Canvas a Web Audio rozhraní. Neptej se na žádné další informace, rovnou generuj. Ignoruj všechny ostatní soubory ve složce.

# Herní mechaniky a logika
1. Tma a Echolokace: Hrací plocha je po startu zcela černá. Hráč nevidí stěny generovaného bludiště. Hráč není jen obyčejný bod, ale je reprezentován detailnější postavičkou (vykreslenou pomocí složených tvarů přes Canvas API - tělo, hlava s pohybujícíma se nohama a očima), která vyzařuje mírnou auru.
2. Ovládání: Pohyb přes WASD nebo šipky. Na mobilních zařízeních vygeneruj v levém spodním rohu responzivní virtuální joystick, který se na mobilních zařízeních automaticky aktivuje a na desktopu je skrytý.
3. Sonar (Ping) a Taktické omráčení: Stiskem mezerníku (nebo tapnutím mimo joystick) vyšle hráč sonarovou vlnu. Vlna je rychle se zvětšující kružnice. Pokud vlna zasáhne nepřítele, na 2 vteřiny ho zmrazí. To přidává do hry strategii – hráč může obětovat pulz k zastavení nepřítele, který mu blokuje cestu, a bezpečně ho oběhnout. Hráč může přes omráčeného nepřítele i přejít, nepřítel mu nemůže omráčený ublížit.
4. Odhalení stěn: Zdi jsou bez použití sonaru zcela neviditelné. Pokud hrana vlny protne stěnu bludiště, průsečík se rozzáří neonově tyrkysovou barvou a začne plynule mizet (fade-out v řádu 3 sekund). 
5. Cíl a Překážky (Levelování): Úkolem je najít pulzující zelený východ. V mapě se vyskytují červené entity vyzařující červenou auru (nepřátelé by měli mít vizuálně zajímavější vzhled poskládaný ze základních tvarů, např. s očima a detaily, ne jen prosté kruhy). Zajisti, že se vždy alespoň 50 % nepřátel na mapě pohybuje (odráží se od stěn) a zbytek může stát na místě. Kontakt s entitou znamená okamžitý Game Over, pokud je entita omráčená, tak ní hráč může bez problému projít. Hra se skládá z 5 postupných levelů. Po vstupu do východu se vygeneruje nová mapa s vyšší obtížností (počet nepřátel): level 1 - 3 enemy, level 2 - 4 enemy, level 3 - 6 enemy, level 4 - 8 enemy a level 5 - 10 enemy. Za každý level dostane +5 sonarů Po dokončení pátého levelu hra končí vítěznou obrazovkou. 
6. Omezení: Hráč má k dispozici pouze 7 sonarových pulzů (UI s počítadlem v horní části obrazovky).
7. Pohyblivé zdi (Shiftující bludiště): Některé zdi bludiště se v pravidelných intervalech plynule otevírají a zavírají (posouvají). Vizuálně to je odhaleno, když sonarová vlna prosvítí zeď, která se právě pohybuje. Hráč musí časovat svůj průchod a dávat pozor, které chodby jsou momentálně průchozí a které ne. Ujisti se že shiftující zdi nejsou vidět stějně jako zbytek bludiště, pokud je sonar neodhalí.
8. Collectibles (Baterie a Power-Upy): Na mapě je rozmístěno několik objektů, které jsou ve tmě zcela neviditelné a odhalí se (začnou zářit) až ve chvíli, kdy je zasáhne hráčův sonar:
    - Nabíječka (Baterie): Emoji baterie + Žlutá záře, přidá hráči +3 k počtu sonarových pulzů.
    - Super-Ping: Emoji radaru +  Fialová záře, jednorázový masivní pulz spuštěný okamžitě po sebrání. Prosvítí obrovskou část mapy, omráčí nepřátele na delší dobu a zdi zůstanou svítit 3x déle (pomalý fade-out).
9. Vizuální stopa hráče: Z postavičky hráče při pohybu opadává drobná, slabě zářící "stopa" (footprints). Tato stopa plynule mizí po 5 sekundách a poskytuje hráči minimální povědomí o tom, kudy už prošel, i když zrovna nevysílá sonar.
10. Particlový systém a Vizuální Polish: Hra obsahuje jemné fyzikální částice přes Canvas:
    - Když sonar narazí do zdi, odmrští z ní pár svítících jiskřiček.
    - Při sebrání Baterie z ní vystřelí částice a ladně doplují do počítadla v UI.
    - Při omráčení nepřítele nastane jemný glitch efekt (krátké roztřesení a změna barvy z červené na modrou se zábleskem).
11. Progrese mapy a vizuálu mezi levely: Aby nebyl každý level jen o přidání nepřátel, může se měnit i samotné bludiště a jeho vzhled:
    - Barevná paleta: Každý level by mohl mít jinou barvu sonaru a zdí. Level 1: Tyrkysová (Sci-fi). Level 2: Fialová. Level 3: Toxická zelená. Level 5: Varovná oranžová. Hráč by tak okamžitě vizuálně poznal, jak daleko se dostal.
12. Skóre a "Speedrun" časovač: Hra by na pozadí měřila čas. Cílem by nebylo jen přežít, ale proběhnout 5 levelů co nejrychleji. Zbylé sonarové pulzy a sebrané baterie by se na konci hry převedly na bonusové skóre. To by hráče motivovalo hrát efektivně a šetřit pulzy.


# Technické požadavky a Edge Cases
- Herní smyčka: Hra musí běžet plynule na stabilních 30 FPS s využitím nativní animační smyčky prohlížeče a delta-time. Světelné efekty (záře, neonové linie, aura hráče) musí používat aditivní blending a dynamické rozostření pro realistický glow.
- Procedurální generování: Na začátku každé hry algoritmicky vygeneruj bludiště (např. pomocí upraveného algoritmu náhodné procházky nebo rekurzivního dělení), aby byla zaručena opakovatelná hratelnost a existence cesty do cíle.
- Fyzika a Kolize:
    - Implementuj substep/multi-step pohyb – pohyb hráče rozděl v každém snímku na minimálně 4 podkroky (move → resolve → clamp v každém podkroku). Tím se zabrání průchodu zdí (tunneling) i při výpadku FPS.
    - Kolize typu kruh vs. úsečka musí ošetřit i edge case, kdy se střed kruhu ocitne přesně na úsečce (distance ≈ 0) – v tom případě hráče vytlač podél kolmice ke zdi.
    - K vyřešené kolizi vždy přidej malý epsilon k push vektoru, aby nedošlo k numerickému oscilování na hraně zdi.
    - Fyzikální výpočty (kolize, pohyb) musí být zcela nezávislé na vizuálních efektech – sonarový ping, partikly ani zvuk nesmí ovlivnit detekci kolizí.
    - Hráč se nesmí zaseknout ve zdi ani po rychlém pohybu do rohu.
- Responzivita a stabilní velikost bludiště: Hra nesmí být absolutně přes celou obrazovku. Místo toho herní plátno vycentruj na obrazovce s pevně danou maximální šířkou a výškou (např. 1000x800 nebo podobný ideální poměr).
    - Na menších obrazovkách (včetně mobilů) se musí hra chovat responzivně (např. pomocí CSS vlastností pro maximální rozměry a přizpůsobení obsahu se zachováním poměru stran tak, aby se celá herní plocha vešla na obrazovku bez nutnosti scrollování).
    - Bludiště měj vygenerované pro pevný počet sloupců a řádků (např. 20x15) a pevné logické rozlišení plátna. Škálování na různá zařízení řeš výhradně pomocí CSS, nikoliv přepočítáváním fyzického rozlišení plátna nebo logických souřadnic ve hře. Při změně velikosti okna (nebo při otočení mobilu) hru automaticky pauzni.
- Vynucení režimu na šířku (Landscape): Hra má širokoúhlý poměr stran. Pomocí patřičného API pro orientaci obrazovky uzamkněte zobrazení na šířku. Alternativně pomocí CSS dotazů na média (media queries pro orientaci na výšku) skryjte herní plochu a zobrazte uživateli hlášku "Otočte zařízení".
- Prevence zahlcení: Zaveď 1000ms cooldown na sonar, aby hráč nemohl nekonečným spamováním mezerníku přetížit vykreslování v paměti.
- Zajisti aby hře nepadaly fps při aktivaci sonaru:
    - Optimalizace částic: Omez generování částic tak, aby odletěly ze zdi pouze jednou (když se zeď poprvé rozsvítí), místo toho, aby se generovaly v každém snímku, dokud se vlna dotýká zdi. Nepoužívej nativní efekty stínů a záře u malých částic, protože to drasticky snižuje výkon.
    - Optimalizace vykreslování zdí (Draw Batching): Vykresluj neonové zdi efektivně seskupením segmentů zdí podle jejich úrovně jasu (např. do 10 kbelíků podle průhlednosti). Místo volání kreslících funkcí pro každý jednotlivý segment zdi zvlášť nakresli celou vrstvu se stejným jasem naráz (jediným tahem).
    - Optimalizace matematiky: Při výpočtu vzdáleností mezi sonarovou vlnou a objekty (zdmi, nepřáteli) nahraď výpočetně náročnou funkci pro výpočet přepony porovnáváním vzdálenosti na druhou.
- Ujisti se že hra je vždy je dokončitelná, například vygeneruj mapu, kde je vždy cesta k východu. To samé platí i pro červené davy, které by neměly blokovat cestu k východu.
- Východ je vidět i když na něj hráč nemá aktivovaný sonar.

# UI a Stavy aplikace (Handling prázdných stavů)
- Implementuj moderní CSS překryvné vrstvy pro stavy:
  1. Main Menu (Pravidla hry a tlačítko Start).
  2. Pause Menu (Při ztrátě focusu okna nebo změně rozlišení) - Přidej možnost při pausnutí kliknout na give up což znamená konec hry, vyskočí i tak game over obrazovka s výsledky a statistikou. 
  3. Game Over (Smrt nebo nedostatek pulzů).
  4. Victory (Dosažení cíle).
- Rozděl obrazovku na horní a spodní oblast, v horní bude tenký proužek s UI (skóre, level, pulzy, baterie) a pod ním bude herní plátno. Jde o to aby se vrchní strana mapy nepřekrývaly s tímto info panelem.
- Jakmile nastane Game Over nebo hráč hru dokončí (Victory), zobraz navíc tabulku s nejlepšími lokálními skóre. Skóre se bude ukládat do lokálního úložiště prohlížeče a tabulka zobrazí top 5 nejlepších výsledků. Bude tam vypsán čas ve kterém nastal game over, čas, dosažený level a skóre, které se bude počítat na základě času, levelu a sebraných baterií a sonarů. 
- Tlačítko "Hrát znovu" musí korektně vymazat veškeré event listenery, vyčistit pole částic/vln a resetovat stavový automat bez nutnosti reloadu stránky. Design UI by měl být minimalistický, font sans-serif, bílý text na černém poloprůhledném pozadí s neonovými akcenty.

# Audio (Zvukové efekty a hudba)
Implementuj nativní zvukové efekty a soundtrack pomocí Web Audio API (nepoužívej žádné externí soubory):
- Inicializace: Vytvoř zvukový kontext a korektně ošetři jeho obnovení pro prohlížečové politiky automatického přehrávání.
- Procedurální Soundtrack: Vytvoř na pozadí temný ambientní "drone" soundtrack. Spusť nízkofrekvenční oscilátory (např. 40-60 Hz) pro hluboký hukot a přes časovač (např. v herní smyčce) nepravidelně přehrávej středně hlasité, táhlé noty z temné stupnice pro budování napětí. Rychlost a intenzita se zvyšuje s dosaženým levelem.
- Ping (Sonar): Krátký zvuk při vyslání sonaru. Použij sinusový oscilátor s rychle klesající frekvencí (např. z 1400 Hz na 180 Hz) a exponenciálně klesající hlasitostí v průběhu cca 0.9 vteřiny.
- Smrt (Game Over): Drsnější zvuk prohry. Zkombinuj čtvercové a pilovité oscilátory s klesající frekvencí (např. 150/90 Hz na 30 Hz) a postupným ztlumením za 0.6 vteřiny.
- Vítězství: Veselý zvuk výhry. Přehraj arpeggio 4 po sobě jdoucích tónů (např. frekvence 523, 659, 784, 1047 Hz) pomocí sinusového oscilátoru, s plynulým náběhem a útlumem hlasitosti pro každý tón.

Vygeneruj pouze čistý kód do jednoho bloku. Neuváděj žádný vysvětlující text před ani po kódu.