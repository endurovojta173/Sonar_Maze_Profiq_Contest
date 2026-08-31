Jsi expert na vývoj webových her a algoritmizaci. Tvojí úlohou je vytvořit kompletní, plně funkční a vysoce optimalizovanou HTML5 hru "Sonar Maze" obsaženou v jediném souboru `index.html`. Nesmíš použít žádné externí knihovny, obrázky, zvuky ani fonty. Vše řeš čistě přes vanilla JavaScript, CSS a HTML5 Canvas.

# Herní mechaniky a logika
1. Tma a Echolokace: Hrací plocha je po startu zcela černá. Hráč nevidí stěny generovaného bludiště. Hráč je reprezentován zářícím bodem s mírnou aurou.
2. Ovládání: Pohyb přes WASD nebo šipky. Na mobilních zařízeních vygeneruj ve spodním rohu responzivní virtuální joystick.
3. Sonar (Ping): Stiskem mezerníku (nebo tapnutím mimo joystick) vyšle hráč sonarovou vlnu. Vlna je rychle se zvětšující kružnice.
4. Odhalení stěn: Pokud hrana vlny protne stěnu bludiště, průsečík se rozzáří neonově tyrkysovou barvou a začne plynule mizet (fade-out v řádu 3 sekund). 
5. Cíl a Překážky: Úkolem je najít pulzující zelený východ. V mapě se pohybují 3 červené entity, které se odráží od stěn. Kontakt s entitou znamená okamžitý Game Over. Na mapě se také mohou nacházet červené dlaždice, které fungují jako pressure plates, při sešlápnutí dlaždice zezelená, v ten moment se otevře zeď v bludišti, aby hráč mohl pokračovat dále k východu. Po 3 sekundách se dlaždice opět zbarví dočervena a cesta k východu se znovu uzavře. Po vstupu do východu hra končí vítěznou obrazovkou. 
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
- Responzivita: Herní plátno musí vyplňovat celé okno prohlížeče. Při změně velikosti okna se hra automaticky pauzne a plátno se přizpůsobí novému rozlišení bez deformace poměru stran.
- Prevence zahlcení: Zaveď 1000ms cooldown na sonar, aby hráč nemohl nekonečným spamováním mezerníku přetížit vykreslování v paměti.
- Zajisti aby hře nepadaly fps při aktivaci sonaru

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