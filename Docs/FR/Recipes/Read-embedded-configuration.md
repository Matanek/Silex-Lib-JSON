# Incorporer une configuration

Cette recette intègre des valeurs par défaut dans l’exécutable. Le chemin de
`Defaults.json` est relatif au fichier `.sx`.

```json
{
  "application": "Silex",
  "debug": true,
  "workers": 4
}
```

```sx
use JSON

func main() {
    let source = embed_text("Defaults.json")
    var config = JSON.parse(source)
    if members = config.as_object() {
        print("Configuration entries: $(members.count())")
        for member in members { print("- $(member.name)") }
    }
}
```

Le programme affiche les trois noms dans l’ordre du document. `embed_text`
incorpore le texte pendant la compilation ; `JSON.parse` applique ensuite le
même contrat que pour une chaîne chargée à l’exécution.

[Revenir à la documentation JSON](../README.md)
