# JSON pour Silex

Le package `JSON` analyse, inspecte, construit et sérialise des valeurs JSON
sans perdre la précision textuelle des nombres.

[Read this documentation in English.](../EN/README.md)

## Installer le package

```text
silex install JSON
```

Importez ensuite son module public :

```sx
use JSON
```

Cette révision en développement requiert la toolchain qui distingue l’appel
ordinaire et la capture `try` des opérations `Result`.

## Lire et modifier une configuration

`read(path)` charge un fichier local avec des limites sûres. L’API directe
retourne la valeur demandée ou s’arrête avec un diagnostic précis lorsque le
fichier ou sa structure attendue est invalide :

```sx
var config = JSON.read("application.json")
var window = config.require_object("window")

let width = window.require_integer("width")
window.set("width", 1920)
window.set("fullscreen", true)

JSON.write("application.json", config)
```

Ce fragment suppose que `application.json` contient un objet `window`.
Les objets fournissent `member`, `require`, les projections typées `require_*`,
`set` et `remove`. Les tableaux fournissent `at`, `append`, `set_at` et
`remove_at`. Les valeurs imbriquées gardent leur identité : modifier `window`
modifie donc `config`.

`write(path, value)` emploie un JSON indenté par défaut. Passez un `Format`
explicite pour un stockage compact. `require_integer` accepte uniquement les
nombres JSON écrits sous forme entière et rejette les fractions et exposants
au lieu de passer par un nombre flottant.

Suivez la [recette de modification d’un fichier](Recipes/Edit-configuration.md)
pour le parcours complet.

## Analyser du texte ou un asset incorporé

`JSON.parse(text)` retourne un `JSON.Value` ou s’arrête avec un diagnostic
localisé dans la source. Sa surcharge `maximum_depth` borne la récursion ; la
limite par défaut vaut 128.

Un fichier JSON incorporé reste du texte ordinaire :

```sx
var defaults = JSON.parse(embed_text("Defaults.json"))
```

Inspectez `Value.kind()` ou utilisez les projections optionnelles
`as_boolean`, `as_string`, `as_number_text`, `as_array` et `as_object`. Les
nombres conservent leur texte décimal source. Les membres d’objet conservent
leur nom et leur valeur, tandis que les noms en double sont rejetés.

Les recettes montrent comment [incorporer une configuration](Recipes/Read-embedded-configuration.md)
et [inspecter chaque type de valeur](Recipes/Inspect-value-kinds.md).

## Construire une sortie

Utilisez `Value.null_value`, `boolean`, `string`, `number`, `array` et `object`.
L’API directe rejette un texte numérique invalide, un flottant non fini ou des
noms d’objet en double.

`JSON.stringify(value)` et `JSON.stringify(value, Format.compact())` minimisent
les octets ; `Format.pretty()` produit une sortie indentée. `Value.encoded`
conserve le choix booléen historique. Préférez `JSON.stringify` dans le nouveau
code, car le format est nommé au point d’appel.

La [recette de rapport](Recipes/Write-report.md) construit chaque niveau avant
de produire un texte lisible.

## Configurer les limites de fichiers

Les appels ordinaires emploient `read(path)` sans options. `ReadOptions` permet
d’imposer une politique propre à l’application :

```sx
let limits = JSON.ReadOptions(
    maximum_bytes:64 * 1024 * 1024,
    maximum_depth:512,
)
var scene = JSON.read("Scenes/world.json", limits)
```

## Récupérer les erreurs

Les mêmes opérations ont un seul nom et une seule déclaration `Result`. Un
appel ordinaire rend la valeur attendue ou arrête le programme sur erreur.
Ajoutez `try` pour conserver le `Result` et décider comment traiter l’erreur :
`var résultat = try JSON.parse(texte)`, puis `match résultat`. Les projections
`require_*` et les mutations `set`, `append`, `at` et `set_at` suivent ce modèle.

`ReadError` distingue les erreurs de fichier des erreurs d’analyse localisées.
`AccessError` distingue les membres absents, types incorrects, projections
entières invalides et dépassements de tableau. La construction récupérable
emploie `Value.number`, `Value.number_text` et `Value.object`.
