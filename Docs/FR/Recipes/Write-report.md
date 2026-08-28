# Construire un rapport

Cette recette construit explicitement les tableaux et membres d’objet, puis
produit un JSON indenté.

```sx
use JSON
use JSON.Format
use JSON.Member
use JSON.Value

func main() {
    var checks:Value[] = [Value.string("compile"), Value.string("test")]
    var members:Member[] = [
        Member(name:"project", value:Value.string("Silex")),
        Member(name:"passed", value:Value.boolean(true)),
        Member(name:"checks", value:Value.array(checks)),
    ]

    var report = Value.object(members)
    print(JSON.stringify(report, Format.pretty()))
}
```

`Value.object` vérifie l’unicité des noms. Les nombres, booléens, chaînes,
tableaux et objets restent des `Value`, ce qui permet de composer le document
avant de choisir son format de sortie.

[Revenir à la documentation JSON](../README.md)
