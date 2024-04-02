# Identifiants d'imagerie de biodiversité
Codes d'identification pour les fichiers d'imagerie (images et vidéos).
Il est proposé d'utiliser la concaténation de quatre champs, soit, `<no_mission>`, `<id_activite>`, `<id_appareil>` et `<id_photo>`, séparés par un trait de soulignage (underscore): `_`.

``` xml
<no_mission>_<id_activite>_<id_appareil>_<id_photo>
```
## Le champ `no_mission`

Un identifiant unique (porté à l'échelle nationale) de missions.

### exemple de l'Institut Maurice-Lamontagne du MPO
Dans le contexte MPO, cet identifiant est fourni par l'institut quand la proposition de mission a été acceptée/confirmée. Elle correspond au numéro d'autorisation de la mission.

Elle est généralement composée de trois sous-parties (`<ID_INSTITUT>`, `<ID_ANNÉE>`, `<ID_MISSION>`) séparées par des tirets `-`.

le champ `no_mission`:
- a la forme `<ID_INSTITUT>-<ID_ANNÉE>-<ID_MISSION>`
- contient une sous partie `<ID_INSTITUT>`
  - composée de trois charactères alphanumériques
  - a toujours la valeur `IML`
- contient une sous partie `<ID_ANNÉE>`
  - composée de quatre charactères numériques
- contient sous partie `<ID_MISSION>`
  - est composé de trois charactères numériques
  - utilise `0` comme charactère de remplissage
- est fourni par l'IML
- a toujours 12 charactères
- ex. `no_mission = IML-2022-034` (Le broutage et la dynamique des forêts de laminaires d'une côte à l'autre, du  1 juin 2022 au 31 mars 2023)

## Le champ `id_appareil`
Un identifiant unique (porté à l'échelle de la mission) de l'appareil d'acquisition d'imagerie.

Cela peut être:
-  caméra d'un microscope
-  caméra à main libre
-  téléphone mobile
-  caméra vidéo sous-marine
-  caméra de vidéo surveillance

Bien que ce champ s'applique seulement à l'échelle de la mission, le même identifiant pourrait être utilisé sur plusieurs missions (s'il demeure unique).
Une description (incluant son identifiant `id_appareil`) de cette appareil de mesure devrait faire partie de la feuille de mission.
Il est une bonne pratique d'affixer une étiquette avec cette identifiant sur l'appareil.

Le champ `id_appareil`:
- a toujours 3 charactères
- est composé de charactères alphanumérique
- est fournis par l'équipe
- ex. `id_appareil = C01` (la Cannon #1)
- ex. `id_appareil = C02` (la Cannon #2)
- ex. `id_appareil = OL0` (la Olympus)
- ex. `id_appareil = Can` (la même Cannon #1, dans un autre mission)
- ex. `id_appareil = C02` (la même Cannon #2, dans un autre mission)

## Le champ `id_activite`
Un identifiant unique (portée à l'échelle de la mission) de l'activité (trait de chalut, CTD, etc).
Il est normalement (mais pas obligatoirement) séquentiel: commençant par `001`, et est incrémenté pour chaque activité.
Un deuxième trait de chalut à la même station constitue une nouvelle activité, même si cela n'est qu’une reprise d'un mauvais trait.


Le champ `id_activite`:
 - zone de pêche `16E`
 - utilise `0` comme caractère de remplissage
 - commence avec la valeur `001`
 - cas-spécial de `000` réservé pour médias n'ayant pas de contexte d'activité
 - ex. `id_activité = 16E_004` (drague a la station 16)
 - ex. `id_activité = 16E_005` (reprise de drague a la station 16)
 - ex. `id_activité = 16E_006` (CTD à la station 16)
 - ex. `id_activité = 16E_000` (un gros requin lors de transit)
 - ex. `id_activité = 16E_000` (vol de sandwich par un goéland)

**Quoi faire avec plusieurs engines/filets pour le même trait?**

## Le champ `id_photo`
Un identifiant unique (porté à l'échelle de l'appareil dans une mission) du fichier d'imagerie (photos ou vidéo).

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
no_mission = "IML-2022-034"
id_activite = 4
id_appareil = "C2"
id_photo = 7
code_photo = sprintf("%s_%03d_%s_%04d", no_mission, id_activite, id_appareil, id_photo)
```
``` R
"IML-2022-034_004_C2_0007"
```
## exemple en python
``` Python
no_mission = "IML-2022-034"
id_activite = 4
id_appareil = "C2"
id_photo = 7
code_photo = f"{no_mission}_{id_activite:03d}_{id_appareil}_{id_photo:04}"
```

``` Python
"IML-2022-034_004_C2_0007"
```
