
# Bekendmakingen API uitvragen

Dit script haalt records op van een API, verwerkt de geometrieën en schrijft de gegevens naar een PostGIS-database. Het is ontworpen om de database te vullen met geografische gegevens vanaf een startdatum. Vervolgens is er een tweede script dat de records bij blijft werken door de meest recente datum weg te gooien en alles vanaf die datum tot de huidige datum op te vragen en toe te voegen. Hieronder vindt je een gedetailleerde uitleg van de functionaliteit en het gebruik van het script.

## Vereisten

- Python 3.x
- Vereiste bibliotheken: `requests`, `xml.etree.ElementTree`, `datetime`, `time`, `pandas`, `geopandas`, `shapely`, `sqlalchemy`, `geoalchemy2`, `configparser`

Installeer de vereiste bibliotheken met pip:

```bash
pip install requests pandas geopandas shapely sqlalchemy geoalchemy2 configparser
```

## Configuratie

Het script leest configuratiegegevens uit een `config.ini` bestand. Het bestand moet de volgende structuur hebben:

```ini
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
