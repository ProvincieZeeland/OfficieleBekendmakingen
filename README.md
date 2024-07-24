
# Bekendmakingen API uitvragen

Wat zijn bekendmakingen? Officiële bekendmakingen worden gepubliceerd op [officielebekendmakingen.nl](https://www.officielebekendmakingen.nl/) door de verschillende overheden. Officiële bekendmakingen bevatten publicaties van de overheid zoals wetten, besluiten en verordeningen. Onder officiële bekendmakingen wordt verstaan alle publicaties in het Staatsblad, de Staatscourant, het Tractatenblad, de Gemeentebladen, Provinciale bladen, Waterschapsbladen en Bladen gemeenschappelijke regeling. De gegevensverzameling met parlementaire documenten omvat publicaties van de Eerste en Tweede Kamer en de Verenigde Vergadering. Alle data is open en kan worden bevraagd door middel van een API, een handleiding daarvan is [hier](https://data.overheid.nl/sites/default/files/dataset/d0cca537-44ea-48cf-9880-fa21e1a7058f/resources/Handleiding%2BSRU%2B2.0.pdf) te vinden.


Dit script haalt records op van een API, verwerkt de geometrieën en schrijft de gegevens naar een PostGIS-database. Het is ontworpen om de database te vullen met geografische gegevens vanaf een startdatum. Vervolgens is er een tweede script dat de records bij blijft werken door de meest recente datum weg te gooien en alles vanaf die datum tot de huidige datum op te vragen en toe te voegen. Hieronder vindt je een gedetailleerde uitleg van de functionaliteit en het gebruik van het script. 

**Kanttekeningen** 
1. Het script haalt eerste alle records op voordat er op de geometrie wordt gefilterd. Dit omdat de data af en toe ongestructureerd is en op een andere plek geregistreerd worden dan dat ze zich daadwerkelijk bevinden. Door eerst alles op te halen en vervolgens de opgehaalde geometriën te filteren duurt het script vele malen langer om te runnen maar wordt wel **alles** opgehaald binnen de aangegeven grenzen.
2. Op verzoek van gebruikers wordt ook het referentienummer toegevoegd aan de gegenereerde tabel, echter bevindt deze zich in de metadata van een record. Als deze niet nodig is wordt het aangeraden om dit stukje van het script te verwijderen. Deze stap maakt het script vele malen langzamer om te draaien. 

### Vereiste bibliotheken en versies

- `GeoAlchemy2==0.15.1.dist`
- `SQLAlchemy==2.0.31.dist`
- `certifi==2024.6.2.dist`
- `charset_normalizer==3.3.2.dist`
- `geopandas==1.0.0.dist`
- `greenlet==3.0.3.dist`
- `idna==3.7.dist`
- `numpy==2.0.0.dist`
- `packaging==24.1.dist`
- `pandas==2.2.2.dist`
- `pip==23.2.1.dist`
- `psycopg2==2.9.9.dist`
- `pyogrio==0.9.0.dist`
- `pyproj==3.6.1.dist`
- `python_dateutil==2.9.0.post0.dist`
- `pytz==2024.1.dist`
- `requests==2.32.3.dist`
- `shapely==2.0.4.dist`
- `six==1.16.0.dist`
- `typing_extensions==4.12.2.dist`
- `tzdata==2024.1.dist`
- `urllib3==2.2.2.dist`

## Configuratie

Het script leest configuratiegegevens uit een `config.ini` bestand. Het bestand moet de volgende structuur hebben:

```ini
#Connectie gegevens van de PostGIS database
[database]
username = jouw_gebruikersnaam
password = jouw_wachtwoord
host = jouw_host
port = jouw_port
database_name = jouw_database_naam
schema = jouw_schema
layer_point = jouw_punt_laag
layer_line = jouw_lijn_laag
layer_polygon = jouw_polygoon_laag

[api]
geometry_bounds = jouw_geometry_bounds_in_WKT
```

## Script Overzicht

1. **Configuratie Lezen**: Het script begint met het lezen van de database- en API-configuratie uit het `config.ini` bestand.

2. **Timer Decorator**: Een decorator om de uitvoeringstijd van functies te meten en af te drukken.

3. **Database Verbinding**: Maakt een verbinding met de PostGIS-database met behulp van de verstrekte inloggegevens.

4. **Geometrieën Valideren**: Een functie om ongeldige geometrieën uit een GeoDataFrame te verwijderen.

5. **Records Ophalen**: Haalt records op van de API op basis van de start- en einddatum en vooraf gedefinieerde queryparameters.

6. **Data Extractie**: Haalt relevante velden uit de opgehaalde records.

7. **Referentienummer Ophalen**: Haalt het referentienummer op uit de metadata-URL indien beschikbaar.

8. **Geometrieën Parssen**: Parst geometrieën van WKT-formaat naar Shapely-geometrieën.

9. **Schrijven naar PostGIS**: Schrijft het verwerkte GeoDataFrame naar de respectieve PostGIS-lagen.

## Functies

- **`timer_decorator(func)`**: Meet de uitvoeringstijd van de versierde functie.
- **`validate_geometries(gdf)`**: Verwijdert ongeldige geometrieën uit een GeoDataFrame.
- **`fetch_records(start_date, end_date)`**: Haalt records op van de API tussen de opgegeven start- en einddatum.
- **`extract_data(record)`**: Haalt de nodige velden uit de opgehaalde records.
- **`fetch_referentienummer(metadata_url, retries=3, backoff_factor=0.3)`**: Haalt het referentienummer op uit metadata.
- **`parse_geometries(df)`**: Parst geometrieën uit de opgehaalde data.
- **`parse_geometry(geo_str)`**: Parst een enkele geometriestring naar een Shapely-geometrie.
- **`write_to_postgis(gdf, layer_name, db_url, schema='geo')`**: Schrijft een GeoDataFrame naar een PostGIS-laag.

## Hoofdworkflow

1. **Bepaalt de start- en einddatum** voor het ophalen van records.
2. **Haalt records op** van de API binnen de opgegeven datumbereiken.
3. **Parst geometrieën** uit de opgehaalde data.
4. **Valideert en reinigt** de geometrieën.
5. **Filtert geometrieën** binnen de gespecificeerde geometriegrenzen.
6. **Schrijft de nieuwe data** naar de PostGIS-lagen.
7. **Print een samenvatting** van de uitgevoerde acties.

## Opmerkingen

- Zorg ervoor dat het `config.ini` bestand correct is geconfigureerd met geldige database-inloggegevens en API-parameters.
- Het script gaat ervan uit dat de geometrieën in WKT-formaat worden verstrekt en dat de SRID 28992 is (Amersfoort / RD New).

## Contact

Voor vragen of problemen, neem contact op via [data@zeeland.nl](mailto:data@zeeland.nl).
