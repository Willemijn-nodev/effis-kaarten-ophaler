# EFFIS kaarten-ophaler

Statische webtool (één `index.html`, geen server nodig) die bij het openen automatisch
24 kaarten klaarzet: **6 brandgevaar-indexen × 4 dagen (dag+1 t/m dag+4)**, al uitgesneden
op Nederland — klaar om met rechtermuisknop → "Afbeelding kopiëren" in een analyse te plakken.

Gebouwd voor gebruik tijdens dienst bij natuurbrand-risicoanalyse (KNMI/EFFIS-werk).

## Functies

- **6 indexen**: FWI, FFMC, DC, ISI, BUI, DMC
- **4 dagen**: dag+1 t/m dag+4, automatisch berekend vanaf de datum van openen
- **Databron**: haalt dezelfde WMTS-tegels op die de officiële
  [EFFIS Current Situation Viewer](https://forest-fire.emergency.copernicus.eu/apps/effis.csv/)
  gebruikt — dus identieke kleuren/stijl, geen eigen interpretatie
- **Twee modellen**: ECMWF (~8 km) of Météo-France (~10 km), te wisselen bovenin
- **Twee achtergronden**: transparant (zee/geen-data blijft leeg) of "EFFIS-kaart"
  (satellietbasis Sentinel-2 cloudless, dezelfde die de EFFIS-viewer zelf toont)
- **Landsgrenzen** (NL, België, Duitsland, Luxemburg, Frankrijk) als aparte lijnlaag,
  aan/uit te zetten
- **Statusindicator**: laat direct zien of de EFFIS-server bereikbaar is, zodat je weet
  of een storing aan de bron ligt of aan iets anders
- **Wachtwoordbeveiliging**: eenvoudig login-scherm (zie hieronder)
- Werkt met een simpele `BBOX`-instelling (uitsnede) die je zelf kan aanpassen

## Gebruik

1. Open de pagina, log in met het wachtwoord
2. Kaarten laden automatisch (in kleine groepjes, om de EFFIS-server niet te overvragen)
3. Rechtermuisknop op een kaart → **Afbeelding kopiëren** → plakken in je analyse/document
4. Bij een storing: bekijk de statusbalk bovenin, of klik "↻ Alles (opnieuw) laden"

## Wachtwoord wijzigen

Open `index.html`, zoek naar deze regel bovenaan het `<script>`-blok:

```js
const APP_WACHTWOORD = 'brand2026';
```

Vervang `'brand2026'` door je eigen wachtwoord en sla op. Dit is een eenvoudige,
zichtbare controle in de broncode (geen hashing, geen server-check) — bedoeld om
toevallige bezoekers buiten te houden, niet als volledige beveiliging.

## Deployen (GitHub Pages + eigen domein)

Korte samenvatting — zie eerdere instructies voor het volledige stappenplan:

1. Upload `index.html` naar een publieke GitHub-repository
2. Zet **Settings → Pages** aan (Deploy from branch → `main` / root)
3. Vul bij **Custom domain** je subdomein in (bv. `effiskaartenophaler.vrolijkweer.nl`)
4. Voeg bij je DNS-provider een **CNAME-record** toe:
   `effiskaartenophaler` → `GEBRUIKERSNAAM.github.io`
5. Wacht tot GitHub "DNS check successful" toont, zet daarna "Enforce HTTPS" aan

## Databronnen & attributie

- **Brandgevaar-indexen**: EFFIS / Copernicus Emergency Management Service —
  WMTS-endpoint `https://maps.effis.emergency.copernicus.eu/effist/wmts`
  ([datalicentie](https://forest-fire.emergency.copernicus.eu/about-effis/data-license))
- **Achtergrondkaart** (optioneel): © EOX IT Services GmbH — Sentinel-2 cloudless
  (bevat gewijzigde Copernicus Sentinel-data), via `tiles.maps.eox.at`
- **Landsgrenzen**: vereenvoudigd, gebaseerd op GISCO `CNTR_RG_20M_2020_4326`
  (Eurostat / Europese Commissie) — dezelfde brondata als de EFFIS-viewer gebruikt

## Technische kanttekeningen

- Puur statisch (HTML/CSS/JS in één bestand) — geen build-stap, geen dependencies
  op npm; Leaflet-achtige kaartlogica is zelf geschreven (Web Mercator-projectie),
  geen externe kaartbibliotheek nodig
- De EFFIS-servers zijn soms tijdelijk overbelast of uit de lucht (503-foutmeldingen);
  de statusbalk en de "↻ Opnieuw"-knoppen per kaart vangen dit op
- Afhankelijk van twee externe diensten buiten je eigen beheer (EFFIS/Copernicus en
  EOX) — als een van beide structureel verandert van URL-structuur, moet de tool
  worden aangepast
- Wachtwoordbeveiliging is client-side en dus in principe te omzeilen door iemand
  die de broncode bekijkt; voor echte toegangscontrole zou een dienst als
  Cloudflare Access nodig zijn
