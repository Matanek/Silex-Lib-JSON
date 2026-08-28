# Modifier un fichier de configuration

Cette recette lit, modifie puis réécrit une configuration locale.

Créez `application.json` :

```json
{
  "window": {
    "width": 1280,
    "fullscreen": false
  }
}
```

Placez ce programme à côté du fichier :

```sx
use JSON

func main() {
    let path = "application.json"
    var config = JSON.read(path)
    var window = config.require_object("window")

    window.set("width", 1920)
    window.set("fullscreen", true)
    JSON.write(path, config)
}
```

`require_object` vérifie la structure attendue. Le nœud `window` conserve son
identité dans `config`, puis `write` produit un fichier indenté avec les deux
nouvelles valeurs.

[Revenir à la documentation JSON](../README.md)
