Jsi expert na vývoj webových her a algoritmizaci. Tvojí úlohou je vytvořit kompletní, plně funkční a vysoce optimalizovanou HTML5 hru "Sonar Maze" obsaženou v jediném souboru který vytvoříš - `index.html`. Nesmíš použít žádné externí knihovny, obrázky, zvuky ani fonty. Vše řeš čistě přes vanilla JavaScript, CSS a HTML5 Canvas.

# Herní mechaniky a logika
1. Tma a Echolokace: Hrací plocha je po startu zcela černá. Hráč nevidí stěny generovaného bludiště. Hráč není jen obyčejný bod, ale je reprezentován detailnější postavičkou (vykreslenou pomocí složených tvarů přes Canvas API - např. tělo, hlava, oči s pohybujícíma se nohama), která vyzařuje mírnou auru.
2. Ovládání: Pohyb přes WASD nebo šipky. Na mobilních zařízeních vygeneruj ve spodním rohu responzivní virtuální joystick.
3. Sonar (Ping): Stiskem mezerníku (nebo tapnutím mimo joystick) vyšle hráč sonarovou vlnu. Vlna je rychle se zvětšující kružnice.
4. Odhalení stěn: Zdi jsou bez použití sonaru zcela neviditelné. Pokud hrana vlny protne stěnu bludiště, průsečík se rozzáří neonově tyrkysovou barvou a začne plynule mizet (fade-out v řádu 3 sekund). 
5. Cíl a Překážky: Úkolem je najít pulzující zelený východ. V mapě se pohybují 3 červené entity vyzařující červenou auru (nepřátelé by měli mít vizuálně zajímavější vzhled poskládaný ze základních tvarů, např. s očima a detaily, ne jen prosté kruhy), které se odráží od stěn. Kontakt s entitou znamená okamžitý Game Over.Po vstupu do východu hra končí vítěznou obrazovkou. 
6. Omezení: Hráč má k dispozici pouze 15 sonarových pulzů (UI s počítadlem v horní části obrazovky).

# Technické požadavky a Edge Cases
- Herní smyčka: Hra musí běžet plynule na stabilních 60 FPS s využitím nativní animační smyčky prohlížeče a delta-time. Světelné efekty (záře, neonové linie, aura hráče) musí používat aditivní blending a dynamické rozostření pro realistický glow.
- Procedurální generování: Na začátku každé hry algoritmicky vygeneruj bludiště (např. pomocí upraveného algoritmu náhodné procházky nebo rekurzivního dělení), aby byla zaručena opakovatelná hratelnost a existence cesty do cíle.
- Fyzika a Kolize:
    - Implementuj substep/multi-step pohyb – pohyb hráče rozděl v každém snímku na minimálně 4 podkroky (move → resolve → clamp v každém podkroku). Tím se zabrání průchodu zdí (tunneling) i při výpadku FPS.
    - Kolize typu kruh vs. úsečka musí ošetřit i edge case, kdy se střed kruhu ocitne přesně na úsečce (distance ≈ 0) – v tom případě hráče vytlač podél kolmice ke zdi.
    - K vyřešené kolizi vždy přidej malý epsilon k push vektoru, aby nedošlo k numerickému oscilování na hraně zdi.
    - Fyzikální výpočty (kolize, pohyb) musí být zcela nezávislé na vizuálních efektech – sonarový ping, partikly ani zvuk nesmí ovlivnit detekci kolizí.
    - Hráč se nesmí zaseknout ve zdi ani po rychlém pohybu do rohu.
- Responzivita a stabilní velikost bludiště: Herní plátno musí nativně vyplňovat celé okno prohlížeče (`canvas.width = window.innerWidth` atd.). Při změně velikosti okna se hra automaticky pauzne a plátno se přizpůsobí novému rozlišení. 
    - Aby byla zachována stabilní složitost bludiště napříč zařízeními (aby bludiště nebylo na 4K monitoru nehratelně obrovské a na mobilu triviální), nepoužívej fixní velikost buňky v pixelech.
    - Místo toho vypočítej velikost buňky (cell size) dynamicky tak, aby se na kratší rozměr obrazovky (šířku nebo výšku) vešlo vždy zhruba 20 buněk. Počet sloupců a řádků se pak dopočítá podle aktuálního poměru stran.
- Prevence zahlcení: Zaveď 1000ms cooldown na sonar, aby hráč nemohl nekonečným spamováním mezerníku přetížit vykreslování v paměti.
- Zajisti aby hře nepadaly fps při aktivaci sonaru
- Ujisti se že hra je vždy je dokončitelná, například vygeneruj mapu, kde je vždy cesta k východu. To samé platí i pro červené davy, které by neměly blokovat cestu k východu.
- Východ je vidět i když na něj hráč nemá aktivovaný sonar.

# UI a Stavy aplikace (Handling prázdných stavů)
- Implementuj moderní CSS překryvné vrstvy pro stavy:
  1. Main Menu (Pravidla hry a tlačítko Start).
  2. Pause Menu (Při ztrátě focusu okna nebo změně rozlišení).
  3. Game Over (Smrt nebo nedostatek pulzů).
  4. Victory (Dosažení cíle).
- Tlačítko "Hrát znovu" musí korektně vymazat veškeré event listenery, vyčistit pole částic/vln a resetovat stavový automat bez nutnosti reloadu stránky. Design UI by měl být minimalistický, font sans-serif, bílý text na černém poloprůhledném pozadí s neonovými akcenty.

# Audio (Zvukové efekty)
Implementuj nativní zvukové efekty pomocí Web Audio API (nepoužívej žádné externí soubory):
- Inicializace (initAudio): Vytvoř AudioContext a ošetři `resume()` pro prohlížečové autoplay politiky.
- Ping (Sonar): Krátký zvuk při vyslání sonaru. Použij `sine` oscilátor s rychle klesající frekvencí (např. z 1400 Hz na 180 Hz) a exponenciálně klesající hlasitostí v průběhu cca 0.9 vteřiny.
- Smrt (Game Over): Drsnější zvuk prohry. Zkombinuj `square` a `sawtooth` oscilátory s klesající frekvencí (např. 150/90 Hz na 30 Hz) a postupným ztlumením za 0.6 vteřiny.
- Vítězství: Veselý zvuk výhry. Přehraj arpeggio 4 po sobě jdoucích tónů (např. frekvence 523, 659, 784, 1047 Hz) pomocí `sine` oscilátoru, s plynulým náběhem a útlumem hlasitosti pro každý tón.

Vygeneruj pouze čistý kód do jednoho bloku. Neuváděj žádný vysvětlující text před ani po kódu.