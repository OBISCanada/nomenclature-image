# Introduction

Ce document vise à définir l'approche de codification utilisée dans les publications OBIS de la section benthique de l'Institut Maurice Lamontagne (IML).

OBIS permet un peu de flexibilité à l'utilisateur pour choisir leur identifiants.
Il est fortement recommander par OBIS d'utiliser des identifiants globalement uniques, c'est à dire uniques dans l'ensemble des publications sur OBIS. 
Étant donné que ce n'est pas obligatoire, aucune vérification de collision entre identifiants (entre les archives) est fait par OBIS.
Bien qu’il existe des méthodes informatiques pour la création d'identifiant universellement uniques (UUID), les identifiants générées (e.x., `65206704-950c-4793-afcd-506669105927`) ne communique pas d'information utile à l'utilisateur (voir [page wikipedia sur UUID](https://fr.wikipedia.org/wiki/Universally_unique_identifier).
Nous proposons ici une méthode de construction d'identifiants unique (dans le contexte restreint du MPO) un peu plus conviviale que le UUID.
Plus spécifiquement, nous proposons une méthode de composition d'identifiant d'évènement (`eventId`), ainsi que pour l'identifiant d'une occurrence taxonomique (`occurrenceId`), pour les archive OBIS structurées en Event-core.
L'applicabilité de cette méthode codification a été vérifié pour les missions suivantes : 
- FWI relevé écosystémique mer de Beaufort
- IML relevé commercial pétoncle d'Islande zone 16E, mission Minganie (même mission que le relevé de la 16F)
- IML relevé commercial pétoncle d'Islande zone 16F, mission Minganie (même mission que le relevé de la 16E)
- IML relevé commercial pétoncle géant zone 20A, mission Îles-de-la-Madeleine
- IML relevé commercial buccin commun zones 1 et 2, mission Haute-Côte-Nord
- IML relevé commercial concombre de mer zones A, B et C, mission Gaspésie
- IML relevé commercial concombre de mer zone 3, mission Minganie


## OBIS Event Core
Les archives de données sur OBIS (ainsi que GBIF) doivent être structurées en suivant un des trois modèles: i) Taxon-Core; ii) Occurrence-Core ou iii) Event-Core. 

OBIS impose une structure relationnelle ayant une seule table cœur (`core`) avec des correspondances (un-à-plusieurs) vers des tables d'extensions.
Il existe un type de table (la table `eMoF`) qui fait l'exception à cette règle, elle peut avoir une correspondance non seulement avec la table cœur (ex. Event-Core), mais aussi avec une table d'extension (ex. Occurrence-Extension).

Nos données/activités de terrain peuvent être bien disposées dans la structure Event-core sans compromettre la qualité des données.
Nos archives suivent donc la structure Event-core avec une table cœur `Event`(données de missions, traits, échantillonnage, sous-échantillonnage… ); une première table d'extension `Occurrence` (taxons) et une deuxième table d'extension `eMoF` de mesures additionnelles (abondances, masses, longueurs, température de l'eau, salinité de l'eau,…)

Les données d'archives suivent une structure simple ayant des liens relationnels entre des tables.
Les données ne sont pas échangées dans un format de base de données relationnelle, mais en simples fichiers (`event.txt`, `occurrence.txt`, `extendedmeasurementorfact.txt`…) distinct représentant chacun les tables.
Les données relationnelles sont donc disposées, en quelque sorte, avec un format technologique agnostique. Les liens de correspondance entre les tables ce font par l'entremise d'identifiants uniques.

Pour de plus amples informations, voir [le manuel d’OBIS](https://manual.obis.org/).

## Champs à usage spécial / Champs à éviter
Nous évitons l'usage de certains champs.

### Le champ `taxonID`
Il faut éviter d'utiliser des codes internes (`STRAP`, `COD_ESP_GEN`, `RVAN`), donc le `aphia_id` serait un bon candidat possible, par contre il est déja disponible sous la colonne `scientificNameID`. En plus, OBIS va mettre a jour la valeur `scientificNameID` selon les services WoRMS (mais ne va pas changer `taxonID`. Donc il vaux mieux éviter `taxonID` pour ne pas créer de confusion.

### Les champs `decimalLatitude`, `decimalLongitude`, `coordinateUncertaintyInMeters` et `footprintWKT` pour un trait de drague.
Nous avons l'habitude de noter la position géographique et la profondeur du début et fin de trait. Donc comment faire pour pouvoir conserver ces données dans le format DwC.

Les positions du début et fin de trait sont combinées ensemble pour créer une seule `decimalLatitude` et `decimalLongitude` qui représent la position moyenne centrale du trait (entre début et fin). Il faut donc calculer cette valeur ainsi qu'une marge d'érreur `coordinateUncertaintyInMeters` suffisament grande pour inclure les coordonées de début et fin.

La position du début et fin de trait devrait être présente dans le champ `footprintWKT`. L'ordre d'entrée des coordonées est important, la première coordonnée correspond au début du trait, et la dernière correspond à la fin de trait. Il est possible d'inclure des profondeurs en utilisant la coordonée d'élévation:

``` wkt
LINESTRING (-68.174436 48.647513 -20.2, -68.166668 48.649483 -20.5)
```

# eventID
Composés de plusieurs groupes hiérarchiques séparés avec des traits d'unions: `-`.
``` 
<MISSION/PROJET>-<ZONE/SECTEUR/RELEVE>-<STATION>-<TRAIT/ACTIVITE>-<ECHANTILLON>-<SOUS-ECHANTILLON>
```

Comme les groupes peuvent contenir eux-mêmes des traits d’unions (ex., `MISSION/PROJET=IML-2023-011`, il est difficile/impossible de décomposer chacun des groupes sans contexte additionnel.
Exemple : le `eventId=TOTO-TATA-TUTU` est la `mission=TOTO`, `zone=TATA` et `station=TUTU` ? est-il `mission=TOTO-TATA` et `zone=TUTU`? est-il `mission=TOTO` avec `zone=TATA-TUTU` ?
Il est entendu qu’un minimum de contexte (autre que seul le `eventId`) soit disponible afin de pouvoir décomposer les parties.

Il serait bien d'éviter l'usage du trait de soulignement, car son usage est réservé pour l'`occurenceId` (voir section occurrenceId ci bas).


## le groupe `MISSION/PROJET`
Le premier groupe hiérarchique représente la mission ou le projet.
Pour les missions issue de l'Institute Maurice Lamontagne, nous utilisons le *numéro de notification de recherche*.
Numéro de notification de recherche, (ex. `IML-2023-001`) Parfois appelé "numéros de mission" ou "code de mission", ce numéro est fourni par la direction régionale des sciences (DRS). 
Avec le numéro de notification, la DRS serait en mesure de fournir plus amples détails sur la mission, tel que:
- le titre;
- la durée;
- ville de départ et arrivée;
- le nom du navire;
- le chef de missions;
- les membre de l'équipe scientifique;
- l'engin de pêche;
- l'interaction potentiels avec des espèce en péril;
- la proximité potentiels avec zones protégées;
- les région de travail;
- les objectifs de la missions;
- 
ainsi que le sommaire et un rapport scientifique post-mission.
Bien que nous prévoyons inclure certaines de ces données dans l'archive, nous souhaitons quand même créer un lien administratif direct en utilisant le numéro de notification.


## le groupe `ZONE/SECTEUR/RELEVE`

Ceci peut varier un peu selon le contexte (type de mission).
Généralement une courte série de caractères utilisés pour donner un contexte au groupe `STATION` (voir groupe `STATION` ci-bas).
 
exemples:
 - `16E` pour désigner que l'activité provient de la zone 16E (pétoncle Minganie) 
 - `16F` pour désigner que l'activité provient de la zone 16F (pétoncle Minganie)
 - `20A` [facultatif] pour désigner que l'activité provient de la zone 20A (pétoncle Îles-de-la-Madeleine)
 - `FOR` [facultatif] pour désigner que l'activité provient du secteur Forestville (buccin)

Ce contexte est obligatoire pour le relevé de pétoncle en Minganie, car il y a chevauchement des numéros de stations dans la même mission (i.e., il existe la station `E4` dans la zone 16E et la station `E4` dans la zone 16F, il faut préciser la zone ainsi que le nom de la station pour éviter l’ambiguïté).

## le groupe `STATION`

Numéro/nom de la station pour où l'activité à eu lieu.
exemples:

 - `E02` pour désigner la station E02 (pétoncle Minganie)
 - `MTI-03` pour désigner la station MTI-03 (mission mer de Beaufort)
 - `01` pour désigner la station 1 (buccin Haute-Côte-Nord)


## le groupe `TRAIT/ACTIVITÉ`
Certains missions comprendre plusieurs type d’évènements a la même station.

Ex. (de la mission mer de Beaufort `CBS-MEA-2021`-`BEAUFORT`-`MTI-03`-`????`) :
 - `BBT-011` (benthique beam trawl)
 - `WA2-012`
 - `WA2-021` 
 - `BBT-028` (benthique beam trawl)

Ex. mission pétoncle `IML-2023-011`-`16F`-`A02`-`???`
 - `1` (premier trait, souvent état annulé)
 - `2` (deuxième trait pour reprise, souvent état conservé)

Ex. mission homard `IML-2023-011`-`A02`-`???`
 - `1` (premier trait du réplicat 1)
 - `2` (premier trait du réplicat 2)
 - `3` (deuxième trait pour reprise du réplicat 1?)
 - `4` (deuxième trait pour reprise du réplicat 2?)


## le groupe `ECHANTILLON`
description à venir

## le groupe `SOUS-ECHANTILLON`
description à venir

# occurrenceId

Composés de deux groupes séparés avec un trait de soulignement: `_`.

``` 
<eventId>_<occurrence>
```

## le groupe `eventId` 
La valeur du groupe `eventId` est une copie verbatim de l'identifiant `eventId` venant de la colonne `eventId` de la table `Occurrence`.
Donc chaque `occurrenceId` comprends dans sa valeur même, une correspondance avec un élément de la table `Event`.
Ceci est surtout un choix pratique, car la *vraie* correspondance (d'un point de vue base de données relationnelle) est fait par la valeur de la colonne `eventId`.

## le groupe `occurrence` 
Une simple valeur numérique séquentielle suffit pour le groupe `occurrence`. 

Exemple:
 - `01` la première capture saisie pour l'évenement
 - `33` la trente-troisième capture saisie pour l'évenement
  
Le numéro utilisé pour une capture saisie recommence à 1 pour une nouvelle entrée d'évènement (i.e., `eventId`).
