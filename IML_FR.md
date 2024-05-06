# Introduction

Ce document vise à définir l'approche de codification utilisé dans les publications OBIS par de la section benthique de l'Institut Maurice Lamontagne (IML).

OBIS laisse permet un peu de flexibilité à l'utilisateur pour choisir leur identifiants.
Il est fortement recommander par OBIS d'utiliser des identifiants globalement unique, c'est à dire, unique dans l'ensemble des publications sur OBIS. 
Étant donné que ce n'est pas obligatoire, aucune vérification de collision entre identifiants (entre les archives) est fait par OBIS.
Bien qu’il existe des méthodes informatiques pour la création d'identifiant universellement uniques (UUID), les identifiants générées (e.x., `65206704-950c-4793-afcd-506669105927`) ne communique pas d'information utile à l'utilisateur (voir [page wikipedia sur UUID](https://fr.wikipedia.org/wiki/Universally_unique_identifier).
Nous proposons ici une méthode de construction d'identifiants unique (dans le contexte restreint du MPO) un peu plus conviviale que le UUID.
Plus spécifiquement, nous proposons une méthode de composition d'identifiant d'évènement (`eventId`), ainsi que pour l'identifiant d'une occurrence taxonomique (`occurrenceId`), pour les archive OBIS structurées en Event-core.
L'applicabilité de cette méthode codification a été vérifié pour les missions suivantes : 
- FWI relevé mer de beaufort
- IML relevé commercial pétoncle d'islande  16E, mission Minganie (même mission que le relevé de la 16F)
- IML relevé commercial pétoncle d'islande  16F, mission Minganie (même mission que le relevé de la 16E)
- IML relevé commercial pétoncle géant zone 20 mission îles-de-la-Madeleine
- IML relevé commercial buccin commun, zones 1 et 2 mission Basse-Côte-Nord
- IML relevé commercial concombre de mer zones A, B et C mission Gaspésie
- IML relevé commercial concombre de mer zone 3 mission Minganie


## OBIS Event Core
Les archives de données sur OBIS (ainsi que GBIF) doivent être structurées en suivant un de trois modèles: i) Taxon-Core; ii) Occurrence-Core ou iii) Event-Core. 

OBIS impose une structure relationnel ayant une seule table cœur (`core`) avec des correspondances (un-à-plusieurs) vers des tables d'extensions.
Il existe un type de table (la table `eMoF`) qui fait l'exception de cette règle, elle peut avoir une correspondance non seulement avec la table cœur, mais aussi une table d'extension.

Nos données/activités de terrain peuvent être bien disposé dans la structure Event-core sans compromettre à la qualité des données.
Nos archives suivent donc la structure Event-core avec une table cœur `Event`(données de missions, traits, échantionnage, sous-échantionnage… ); une table d'extension `Occurrence` (taxons, abondance) et une table de mesures additionnels `eMoF` (poids, longueurs, nombres,…)

Les données d'archives suivent une structure simple ayant des liens relationnels entre des tables.
Les données ne sont pas échangées dans un format base de données relationnel, mais en simple fichiers (`event.txt`, `occurrence.txt`, `extendedmeasurementorfact.txt`…) distinct représentant chacun les tables.
Les données relationnels sont donc disposé, en quelque sorte, avec un format technologique agnostique. Les liens de correspondance entre les tables ce font par l'entremise d'identifiants.

Pour plus amples informations, voir [le manuel d’OBIS](https://manual.obis.org/).

# eventID
Composés de plusieurs groupes hiérarchiques séparés avec des traits d'unions: `-`.
``` 
<MISSION/PROJET>-<ZONE/SECTEUR/RELEVE>-<STATION>-<TRAIT/ACTIVITE>-<ECHANTILLON>-<SOUS-ECHANTILLON>
```

Comme les groupes peuvent contenir eux-mêmes des traits d’unions (ex., `MISSION/PROJET=IML-2023-011`, il est difficile/impossible de décomposer chacun des groupes sans contexte additionnel.
Exemple : le `eventId=TOTO-TATA-TUTU` est la `mission=TOTO`, `zone=TATA` et `station=TUTU` ? est-il `mission=TOTO-TATA` et `zone=TUTU`? est-il `mission=TOTO` avec `zone=TATA-TUTU` ?
Il est entendu qu’un minimum de contexte (autre que seul le `eventId`) soit disponible afin de pouvoir décomposer les parties.

Il serait bien d'éviter l'usage du trait de soulignment, car son usage est réserver pour l'`occurenceId` (voir section occurrenceId ci bas).


## le groupe `MISSION/PROJET`
Le premier groupe hiérarchique représente  la mission ou le projet.
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
Bien que nous prévoyons inclure certain de ces données dans l'archive, nous souhaitons quand même créer un lien administratif direct en utilisant le numéro de notification.


## le groupe `ZONE/SECTEUR/RELEVE`

Ceci peut varier un peu selon le contexte (type de mission).
Généralement une courte série de charactères utilisées pour donner un contexte au groupe `STATION` (voir groupe `STATION` ci-bas).
 
exemples:
 - `16E` pour désigner que l'activité provient de la zone 16E (pétoncle Minganie) 
 - `16F` pour désigner que l'activité provient de la zone 16F (pétoncle Minganie)
 - `20` [facultatif] pour désigner que l'activité provient de la zone 20 (pétoncle Îles-de-la-Madeleine)
 - `FOR` [facultatif] pour désigner que l'activité provient du secteur Forestville (buccin)

Ce contexte est obligatoire pour du relevé de pétoncle en Minganie, car il a chevauchement des numéros de stations dans la même mission (i.e., il existe la station `E4` dans la zone 16E et la station `E4` dans la zone 16F, il faut préciser la zone ainsi que le nom de la station pour éviter l’ambiguïté).

## le groupe `STATION`

Numéro/nom de la station pour où l'activité à eu lieu.
exemples:

 - `E02` pour désigner la station E02 (pétoncle Minganie)
 - `MTI-03` pour désigner la station MTI-03 (mission mer de Beaufort)
 - `01` pour désigner la station 1 (buccin Basse-Côte-Nord)


## le groupe `TRAIT/ACTIVITÉ`
Certains missions comprendre plusieurs type d’évènements a la même station.

Ex. (de la mission mer de Beaufort `CBS-MEA-2021`-`BEAUFORT`-`MTI-03`-`????`) :
 - `BBT-011` (benthique beam trawl)
 - `WA2-012`
 - `WA2-021` 
 - `BBT-028` (benthique beam trawl)

Ex. mission pétoncle `IML-2023-011`-`16F`-`A02`-`???`
 - `1` (premier trait)
 - `2` (deuxième trait pour reprise)

Ex. mission homard `IML-2023-011`-`A02`-`???`
 - `1` (premier trait )
 - `2` (premier trait réplica)
 - `3` (deuxième trait pour reprise)
 - `4` (deuxième trait pour reprise réplica)


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
Ceci est surtout un choix pratique, car la *vrai* correspondance (d'un point de vue base de données relationnel) est fait par la valeur de la colonne `eventId`.

## le groupe `occurrence` 
Une simple valeur numérique séquentielle suffit pour le groupe `occurrence`. 

Exemple:
 - `01` la première capture saisie pour l'évenement
 - `33` la trente-troisième capture saisie pour l'évenement
  
Le numéro utilisé pour une capture saisie recommence à 1 pour une nouvelle entrée d'évènement (i.e., `eventId`).
