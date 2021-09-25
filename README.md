# tuya-camera-mod

Het is mogelijk om de meest Tuya ip-camera's aan te passen zodat je deze kunt gebruiken in secundaire systemen.
Het probleem met deze camera's is namelijk dat deze de rtsp streams en Onvif poort dicht hebben staan en je die normaliter dus niet kunt gebruiken.
Met een simpele aanpassing kun je deze wel activeren, het enige wat je nodig hebt is een SD-kaart die je nu waarschijnlijk toch al in de camera hebt zitten.
Onderstaande handleiding is gebaseerd op https://github.com/guino/Merkury720 maar mijn doel is om het hier wat korter en makkelijker neer te zetten.
Het is trouwens geen garantie dat dit voor jouw camera gaat werken, je beschadigd er niets mee dus proberen kan totaal geen kwaad.
Werkt het niet, simpelweg je SD-kaart formateren en alles is weer hersteld.

## Wat heb je nodig?
- Tuya-based IP-Camera met firmware 2.7x of hoger (vindt je terug in de Tuya app)
- Een SD kaart (die je nu wss toch al in je camera hebt zitten)
- SD-kaartreader of slot op je PC/Laptop

Let op! Je gaat bestanden op je SD-kaart zetten die daar ook moeten blijven staan. Zodra je kaart vol begint te raken door je opnamens dan kan je camera deze bestanden verwijderen/overschrijven. Om dit te voorkomen kun je in custom.sh aangeven wat de retentie moet zijn. Zet deze bv op 2-3 dagen. Zet deze ook weer niet te lang om te voorkomen dat je kaart alsnog volloopt. Als je toch al opneemt op een secundair systeem kun je de opnameregistratie zelfs beter uitzetten in camera instellingen->Sd-kaart in de Tuya app.

## Here we go!

Let op! Er zijn 720P en 1080P camera's. Hier zitten kleine verschillen in qua stappen, waar nodig zal ik dat aangeven.

1. Zorg ervoor dat je camera werkt en compleet is aangemeld in je Tuya app. Hij moet dus al compleet werken en zal altijd nog verbonden blijven met Tuya en de app. 
2. Zet de camera uit door de usb kabel of voeding los te halen.
3. Haal de SD-kaart uit je camera en koppel deze aan je PC.
4. Download deze repo naar je PC: https://github.com/guino/Merkury720 .
5. Zet alle bestanden uit het mapje <b>MMC</b> direct in de root van je SD-kaart.
6. Dowload Busybox DIRECT naar je SD-kaart (rechtsklikken->"opslaan als" en dan SD-kaart selecteren): https://github.com/guino/Merkury720/blob/main/mmc/busybox?raw=true .
7. Download ppsFactoryTool.txt DIRECT naar de root van je SD-kaart (dus niet de code copy-pasten!)(rechtsklikken->"opslaan als" en dan SD-kaart selecteren): https://github.com/guino/Merkury720/raw/main/ppsFactoryTool.txt
8. Open "ppsFactoryTool.txt" in Kladblok en wijzig de SSID en Wachtwoord naar die van jouw wireless netwerk. Verander verder helemaal niets, geen enter, geen extra lijn helemaal niks! Zodra dit bestandje straks gezien wordt door jouw camera dan zal deze de webserver opstarten op poort 80 (of 8090 voor 4.0x). Zonder deze stap werken straks alle nodige url's niet.
9. <b>Alleen voor 1080P camera:</b> Download deze repo en overschrijf de 3 bestandjes op je SD kaart met de 3 bestandjes uit de map <b>MMC</b> uit deze repo: https://github.com/guino/Merkury1080P .
10. Eject/unmount de SD-kaart VEILIG van je PC (dus niet simpelweg eruit halen maar eerst rechtsklikken en "Uitwerpen" klikken).

Je SD-kaart is nu voorbereid en nu gaan we je camera daadwerkelijk ROOTEN.

11. Doe de SD-kaart in je camera.
12. Houdt de resetknop <b>ingedrukt</b>
13. Sluit de usb/voedingskabel weer aan terwijl je de resetknop VAST blijft houden.
14. Blijf de resetknop minimaal 5 seconden vasthouden of liever nog tot je de camera hoort piepen.
15. Open een browser op je PC en ga naar http://admin:056565099@192.168.1.x/proc/cmdline (wijzig IP adres naar die van jouw camera). Sommige camera's gebruiken "admin" ipv "056565099" dus http://admin:admin@192.168.1.x/proc/cmdline. <b>Voor 1080P</b> gebruik je http://admin:056565099@192.168.1.x:8090/proc/cmdline of http://admin:admin@192.168.1.x:8090/proc/cmdline omdat daar de webserver op poort 8090 draait ipv poort 80.
16. Als alles goed is gegaan zie je nu dit ongeveer in je browser: 
```
mem=64M console=ttySAK0,115200n8 mtdparts=spi0.0:256k(bld)ro,64k(env)ro,64k(enc)ro,64k(sysflg)ro,2496k(sys),4608k(app),640k(cfg) ppsAppParts=5 ip=0 - ip=30;/mnt/mmc01/initrun.sh)&:::::;date>/tmp/hack;(sleep
```
Zie je dit niet probeer dan een van de andere bovengenoemde url's, werkt ook dat niet herstart je camera, dan nog niet check of je de goede SSID en password opgegegevn hebt in de textfile, werkt het dan nog niet....tja....kijk dan even in de originele repo. Zie je wel wat maar niet het laatste gedeelte ( <b>- ip=30...</b>) dan heeft de hack niet gewerkt. Dit kan soms komen door het type SD-kaart. Raar genoeg werkt een cheapass kaartje het beste.

Zie je dit wel? Mooi! De hack heeft gewerkt!
Maar we zijn er nog niet....

17. Open de URL http://admin:056565099@192.168.1.x/proc/self/root/mnt/mmc01/hack of http://admin:admin@192.168.1.x/proc/self/root/mnt/mmc01/hack, <b>voor 1080P:</b> http://admin:056565099@192.168.1.x:8090/proc/self/root/mnt/mmc01/hack of http://admin:admin@192.168.1.x:8090/proc/self/root/mnt/mmc01/hack . Er zou nu de tekst "Done" moeten staan wat aangeeft dat de hack daadwerkelijk gewerkt heeft. Soms werkt deze link niet maar heeft de hack wel gewerkt. Zet de camera uit en haal de SD-kaart eruit en doe deze in je PC. ALs de hack gewerkt heeft dan heb je nu een "hack" file en een "home" folder.

Het rooten is nu gebeurd! Maar we zijn er nogsteeds niet...pfff ;-)

## ppsapp patchen:
Nu gaan we de PPSAPP file patchen. Dit moet voor elke camera specifiek gebeuren want dit hangt samen met het serienummer etc van jouw camera. Die dit dus ook voor elke camera mocht je er meerdere hebben.
<b>LET OP!</b> Zorg ervoor dat je het patchbestand gebruikt die bij jouw camera hoort. Dus niet van een ander type camera. Dit zou je camera zelfs kunnen bricken! Dit check je aan de hand van het hardware type verderop besproken. Is er nog geen patch voor jouw camera dan kan de developer deze voor je maken. Ook deze stappen worden hieronder beschreven. Kortgezegd, onderstande is het lastigste dus check goed dat al het nodige overeenkomt met jouw camera.

18. Ga naar http://admin:056565099@192.168.1.x/devices/deviceinfo of http://admin:admin@192.168.1.x/devices/deviceinfo <b>voor 1080P:</b> http://admin:056565099@192.168.1.x:8090/devices/deviceinfo of http://admin:admin@192.168.1.x:8090/devices/deviceinfo . Hier zie je diverse info over jouw camera en hier vindt je ook je firmwareversie en hardwareversie. Noteer deze.
19. Zet je camera uit en haal de SD-kaart eruit en doe deze in je PC.
20. Ga naar https://github.com/guino/ppsapp-rtsp/issues/1 en kijk of jouw patch al beschikbaar is in de lijst en download de ppsapp-rtsp.zip file naar je PC. Kijk ook even of er evt speciale instructies vermeld staan welke je moet volgen.  Is er nog geen file voor jouw camera? Volg dan de stappen in het volgende hoofdstuk dan heb je binnen enkele dagen alsnog een patchfile en vervolg dan deze instructies.
21. Ga naar https://www.marcrobledo.com/RomPatcher.js/ <b>Klik NIET op "Creator Mode"!!!</b>.
22. Klik "Choose file" bij Rom File en selecteer je originele ppsapp op je sd-kaart in de map /home/app/ppsapp.
23. Klik "Choose file" bij Patch File en selecteer de patch zip die je in stap 20 gedownload hebt.
24. Klik "Apply Patch" en download het bestand direct naar de root van je SD-kaart. Het bestand heet waarschijnlijk "ppsapp-rtsp".
25. Hernoem dat bestand op je SD-kaart naar "<b>ppsapp</b>" en controleer dat het bestand GEEN extensie heeft zoals bv .txt.
26. Verwijder de SD-kaart veilig van je PC (eject/unmount) en stop deze in je camera.
27. Start de camera op zoals normaal. Je hoor dan als het goed is 2x het opstartgeluid. 1x voor de camera en even later een 2e keer voor de patch.
28. Zodra de camera volledig is opgestart zijn je streams beschikbaar onder rtsp://admin:056565099@192.168.1.x:8554 of rtsp://admin:admin@192.168.1.x:8554. Sommige camera's hebben andere urls, bv 1 voor SD en 1 voor HD, dat staat dan vermeld bij de patchfile uit stap 20.

Veel plezier!!

Ervaar je problemen maak dan gerust een issue aan. Bedenk wel dat dit niet mijn repo is dus support zal beperkt zijn. Het beste kun je dan ook direct in de originele repo een issue starten. Staan er fouten in deze manual laat het mij dan weten dan zal ik dat aanpassen.

## Mijn camera heeft nog geen patchfile, wat nu?

Als je camera nog niet in de lijst staat op https://github.com/guino/ppsapp-rtsp/issues/1 kun je 2 dingen doen.

1. Als je zelf handig genoeg bent: https://github.com/guino/ppsapp-rtsp

of

2. De patchfile laten maken. Dit Is de meest makkelijke weg. Maak simpelweg een issue aan op https://github.com/guino/ppsapp-rtsp/issues en copy/paste daarin alle info uit http://admin:056565099@192.168.1.x/devices/deviceinfo of http://admin:admin@192.168.1.x/devices/deviceinfo <b>voor 1080P:</b> http://admin:056565099@192.168.1.x:8090/devices/deviceinfo of http://admin:admin@192.168.1.x:8090/devices/deviceinfo. Voeg ook jouw PPSAPP file uit home/app/ppsapp toe als bijlage. Enkele dagen later ontvang je dan een patch voor jouw camera on wordt deze ook gelijk toegevoegd aan de lijst zodat andere gebruikers hem kunnen gebruiken. Volg verder de instructies vanaf punt 21.


