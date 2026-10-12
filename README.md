# Grafiekbouwer 0.1.5 voor apexcharts-card

Datum: 12 oktober 2026
Werkt met: apexcharts-card 2.3.1-apex8 (ook 2.3.0) (de build met ApexCharts 8.0.0)

## Wat het is

Een eigen pagina in de HA-zijbalk (custom panel) waarin je een apexcharts-card visueel opbouwt:

- links het formulier met alle card-opties, gegroepeerd in tabbladen;
- rechts een live voorbeeld met je echte data, in je eigen thema;
- daaronder de YAML, met een kopieerknop.

Geen app en geen container: het is één JS-bestand dat HA laadt. Het panel krijgt dezelfde verbinding als je dashboard, dus geen token en geen aparte login.

## Installeren

1. Zorg dat apexcharts-card 2.3.0-apex8 als dashboard-resource geladen is (zie `apexcharts-card-apex8.md`). De bouwer gebruikt die card voor het voorbeeld.
2. Zet `apexcharts-builder.js` in `/config/www/`.
3. Voeg toe aan `configuration.yaml`:

```yaml
panel_custom:
  - name: apexcharts-builder
    sidebar_title: Grafiekbouwer
    sidebar_icon: mdi:chart-line
    url_path: grafiekbouwer
    module_url: /local/apexcharts-builder.js?v=0.1.5
    require_admin: true
```

4. Configuratie controleren en HA herstarten (eenmalig; voor latere versies volstaat het bestand vervangen en `?v=` ophogen).
5. "Grafiekbouwer" staat in de zijbalk, alleen voor beheerders.

`/local/` is zonder login bereikbaar, ook via je DuckDNS-adres. Er staat niets geheims in het bestand.

## Gebruiken

1. Kies een entity en een sjabloon: Standaard lijn, 24 uur trend, Dagteller per dag, Statistics 60 dagen of Leeg.
2. Pas aan in de tabbladen:
   - **Algemeen:** grafieksoort, periode, uitlijnen, verschuiven, gestapeld, verversen, kleurenlijst.
   - **Series:** per serie de basis, lijn en vlak, data ophalen en groeperen (group_by of statistics), weergave en drempelkleuren. Series zijn te verschuiven, te dupliceren en te verwijderen.
   - **Y-assen:** min en max (ook `~90` en `|+10|`), decimalen, links of rechts, aantal stappen, astitel. Bij een tweede as worden bestaande series automatisch aan de eerste as gekoppeld.
   - **Kop en tijd:** kop, titel, actuele waarden, nu-lijn.
   - **Opmaak:** de ApexCharts-opties die je het meest gebruikt (hoogte, vulling, kolombreedte, raster, x-as-formaat, tooltip, legenda, uitdunnen, kleurenblind-palet) plus de volledige `apex_config` als YAML.
   - **Extra:** experimentele opties en brush.
3. Klik **Kopieer YAML**. Plak in het dashboard bij Kaart toevoegen, Handmatig, of in de code-editor van een bestaande kaart.

Een bestaande kaart verder bewerken: plak de YAML in het YAML-vak. Het formulier neemt alles over. Wat de bouwer niet kent (`card_mod`, `EVAL`-functies, `config_templates`, ...) blijft ongewijzigd staan.

Bij elke optie zie je de standaardwaarde. Uitleg staat achter de kleine (i) naast het label: klik erop om hem te openen, klik nog eens, op het kruisje, ergens anders of druk Escape om hem te sluiten. Een tweestand-optie heeft drie standen: **std** (niet in de YAML), **aan** en **uit**. In de YAML komt alleen wat je zelf instelt.

## Wat de bouwer voor je controleert

Boven het formulier verschijnen meldingen, waar mogelijk met een knop om het meteen op te lossen:

- drempelkleuren zonder de experimentele schakelaar (knop: aanzetten);
- "begint met vorige waarde" samen met maximum, de dagteller-valkuil (knop: uitzetten);
- statistics en groeperen tegelijk (knop: groeperen weghalen);
- standaard verborgen of in brush zonder de bijbehorende experimentele optie (zet je het in het formulier aan, dan gebeurt dat automatisch; de melding komt alleen nog bij geplakte YAML);
- meerdere y-assen zonder ID, of series zonder gekozen as;
- gedeelde tooltip met series die verschillend gegroepeerd zijn;
- stapelgroep zonder "Gestapeld", datalabels op ruwe data, radialBar zonder max.

Daarnaast controleert de card zelf elke wijziging. Weigert ze een config, dan staat de reden boven het voorbeeld. Ongeldige duurwaarden (periode, groeperen, verschuiven) worden al bij het invoeren tegengehouden.

Ongedaan maken onthoudt de laatste 60 stappen. Je werk-in-uitvoering blijft in de browser bewaard, ook als je even naar een dashboard gaat.

## 0.1.5

- "Standaard verborgen" bij een serie aanzetten zet nu ook `experimental.hidden_by_default: true` aan. Zonder die schakelaar negeert apexcharts-card de optie stil.
- Hetzelfde voor "In brush": dat zet `experimental.brush: true` aan.
- Daarmee werken beide nu net als drempelkleuren, die dit al deden. De melding met knop "Aanzetten" blijft voor YAML die je plakt.
- Afhankelijkheden opnieuw opgehaald (lit 3.3.3, js-yaml 4.3.2); daardoor is het bestand iets groter.

## 0.1.4

- Uitleg bij elke optie, achter een kleine (i) in een rondje naast het label. Alleen klikken opent hem, er zijn geen hover-tooltips; dat werkt ook op de telefoon.
- 79 velden hadden nog geen uitleg; alle 116 velden hebben er nu een.
- De uitleg staat niet meer standaard onder de velden, zodat het formulier rustiger is.

## 0.1.3

- Versie van de bouwer en van de geladen apexcharts-card staan in de kopbalk (op de telefoon onder de titel). De card-versie komt uit de card zelf, niet uit de `?v=` van de resource.
- Het voorbeeldvak krijgt de hoogte van de kaart; het YAML-vak vult de rest. Scrollen is alleen nog nodig als de kaart niet past.
- Het YAML-vak is in te klappen (klik op "YAML"), dan krijgt het voorbeeld alle ruimte. De bouwer onthoudt die keuze in deze browser.

## 0.1.2

- Entity-zoeker zoekt ook op ruimte en label: "klimaat woonkamer" vindt alles met het label Klimaat in de woonkamer.
- Twee filters onder het zoekveld: Ruimte (gegroepeerd per verdieping, plus "Zonder ruimte") en Label (alleen labels die echt gebruikt worden). Een entity zonder eigen ruimte of label erft die van zijn apparaat.
- In de resultaten staan ruimte en labels bij elke entity; onder een gekozen entity staat de ruimte.
- De filters gelden voor alle zoekvelden tegelijk, zodat je ze niet per serie opnieuw hoeft te kiezen.

## 0.1.1

- De controles houden nu rekening met `all_series_config` (gebruik je in 44 van je 80 kaarten) en met series uit een `data_generator`. Dat haalde drie onterechte waarschuwingen over de gedeelde tooltip weg.
- "Statistics en groeperen tegelijk" is nu een opmerking zonder knop: delta per dag op uurstatistieken is een geldige combinatie.
- Getest met je 80 bestaande kaarten: alle 80 gaan zonder verlies door de bouwer heen.

## Hoe getest

In headless Chromium met een nagebootste `hass` en de echte apexcharts-card 2.3.0-apex8 als voorbeeld:

- alle vijf sjablonen, donker en licht thema, desktop en telefoonbreedte (390 px, geen horizontaal scrollen);
- je vijf bestaande apexcharts-YAML's geplakt en weer uitgelezen: alle vijf zonder verlies, inclusief `EVAL`-functies en de `data_generator` als blok-tekst;
- één veld wijzigen in een geïmporteerde config verandert alleen dat ene veld;
- drempelkleuren zetten de experimentele optie aan, y-assen koppelen series, ongedaan maken werkt, kapotte YAML en ongeldige opties geven een leesbare melding;
- geen console-fouten.

**Niet getest:** een echte HA-instance. Juist het laden als panel, de zijbalk-knop op de telefoon en de HA-themakleuren kan ik hier niet nabootsen. Probeer het bij voorkeur eerst op henk.

## Bekende beperkingen

- Gemaakt en getest voor apexcharts-card 2.3.0-apex8. De opties onder "ApexCharts 8" bestaan niet in oudere versies.
- Klikacties (`tap_action` en dergelijke) en `all_series_config` zijn alleen als YAML te bewerken.
- De entity-zoeker is eigen werk en gebruikt geen HA-componenten. Ruimtes en labels komen uit de registers die HA aan elke pagina meegeeft, plus één opvraag van de labelnamen. Daardoor breekt hij niet bij HA-updates, maar hij oogt iets anders dan de HA-picker.
- Op een laag scherm kan een hoge kaart nog steeds niet passen; klap dan de YAML in.

## Bestanden

- `apexcharts-builder.js`: het panel (140 kB), SHA-256 `484d0ecb9ce79aa96679d2e34cd8f08444e1b38c01d6b15ca7981067efffced1`
- `grafiekbouwer-bron/`: de broncode (Lit en js-yaml, bouwen met `npm install` en `npx rollup -c`)
- `grafiekbouwer-voorbeeld.png`: schermafbeelding
