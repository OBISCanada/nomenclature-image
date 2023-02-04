# Identifiants d'imagerie
Codes d'identification pour les fichiers d'imagerie (images et vidéos).
Il est proposé d'utiliser la concaténation de quatre champs, soit, `<no_mission>`, `<id_activite>`, `<id_appareil>` et `<id_photo>`, séparés par un trait de soulignage (underscore): `_`.

``` xml
<no_mission>_<id_activite>_<id_appareil>_<id_photo>
```
## Le champ `no_mission`

Un identifiant unique (portée à l'échelle national) de missions.

### example de l'Institut Maurice Lamontagne du MPO
Dans le contexte MPO, cette identifiant est fournis pas l'institut quand la proposition de mission à été accepté/confirmé. Elle corréspond au numéro d'autorisation de la mission.

Elle est généralement composé de trois sous-parties (`<ID_ANNÉE>`,`<ID_INSTITUT>`, `<ID_MISSION>` ) séparées par des tirets `-`.

le champ `no_mission`:
- a la forme `<ID_ANNÉE>-<ID_INSTITUT>-<ID_MISSION>`
- contient une sous partie `<ID_ANNÉE>`
  - composée de quatre charactères numériques
- contient sous partie `<ID_INSTITUT>`
  - composée de trois charactères alpha-numériques
  - a toujours la valeur `IML`
- contient sous partie `<ID_MISSIONS>`
  - est composé de trois charactères numériques
  - utilise `0` comme charactère de remplissage
- est fournit par l'IML
- a toujours 12 charactères
- ex. `no_mission = 2022-IML-034` (Le broutage et la dynamique des forêts de laminaires d'une côte à l'autre, du  1 juin 2022 au 31 mars 2023)


## Le champ `id_appareil`
Un identifiant unique (portée à l'échelle de la mission) de l'appareil d'acquisition d'imagerie.

Cela peux être:
-  caméra d'un miscroscope
-  caméra a main libre
-  téléphone mobile
-  caméra vidéo sous-marine
-  caméra de vidéo surveillance

Bien que ce champ s'applique seulement à l'échelle de la mission, le même identifiant pourrait être utilisé sur plusieurs missions (si elle demeure unique).
Une description (incluant son identifiant `id_appareil`) de cette appareil de mesure devrait faire partie de la feuille de mission.
Il est bonne pratique d'affixer une étiquette avec cette identifiant sur l'appareil.

Le champ `id_appareil`:
- a toujours 2 charactères
- est composé de charactères alpha-numérique
- est fournis par l'équipe
- ex. `id_appareil = C1` (la Cannon #1)
- ex. `id_appareil = C2` (la Cannon #2)
- ex. `id_appareil = OL` (la Olympus)
- ex. `id_appareil = CN` (la meme Cannon #1, dans un autre mission)
- ex. `id_appareil = C2` (la meme Cannon #2, dans un autre mission)

## Le champ `id_activité`
Un identifiant unique (portée à l'échelle de la mission) de l'activité (trait de chalut, CTD, etc).
Elle est normalement (mais pas obligatoirement) séquentiel: commençant par `001`, et est incrémenté pour chaque activité.
Un deuxieme trait de chalut à la même station constitut une nouvelle activité, même ci cela n'est q'une reprise d'un mauvais trait.

Le champ `id_activité`:
 - a toujours 3 charactères
 - charactères numériques
 - utilise `0` comme charactère de remplissage
 - commence avec la valeur `001`
 - cas-spécial de `000` réservé pour médias n'ayant pas de contexte d'activité
 - ex. `id_activité = 004` (drague a la station 16)
 - ex. `id_activité = 005` (reprise de drague a la station 16)
 - ex. `id_activité = 006` (CTD a la station 16)
 - ex. `id_activité = 000` (un gros réquin lors de transit)
 - ex. `id_activité = 000` (vol de sandwich par un goéland)

**Quoi faire avec plusieurs engines/filets pour le meme trait?**

## Le champ `id_photo`
Un identifiant unique (portée à l'échelle l'appareil dans une mission) du fichier d'imagerie (photos ou vidéo).

Le champ `id_photo`:
 - charactères numériques
- a toujours 4 charactères
- commence avec `0001`
- gestion (incrémentation) par l'appareil
- retour à zéro pour chaque mission
- pas de cas avec `0000`
- ex. `0007`

# Formatage de strings

String avec formatage "%s_%03d_%s_%04d"

## exemple en R
``` R
no_mission = "2022-IML-034"
id_activite = 4
id_appareil = "C2"
id_photo = 7
code_photo = sprintf("%s_%03d_%s_%04d", no_mission, id_activite, id_appareil, id_photo)
```
``` R
"2022-IML-034_004_C2_0007"
```
## example en python
``` Python
no_mission = "2022-IML-034"
id_activite = 4
id_appareil = "C2"
id_photo = 7
code_photo = f"{no_mission}_{id_activite:03d}_{id_appareil}_{id_photo:04}"
```

``` Python
"2022-IML-034_004_C2_0007"
```
