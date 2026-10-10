# Inspecter les types de valeurs

Cette recette combine `kind()` et les projections optionnelles pour décrire
une valeur sans supposer sa structure.

```sx
use JSON

func describe_boolean(value:JSON.Value) str {
    if actual = value.as_boolean() { return "boolean $(actual)" }
    return "invalid boolean"
}

func describe_string(value:JSON.Value) str {
    if actual = value.as_string() { return "string $(actual)" }
    return "invalid string"
}

func describe_number(value:JSON.Value) str {
    if actual = value.as_number_text() { return "number $(actual)" }
    return "invalid number"
}

func describe_array(value:JSON.Value) str {
    if values = value.as_array() { return "array of $(values.count()) values" }
    return "invalid array"
}

func describe_object(value:JSON.Value) str {
    if members = value.as_object() { return "object of $(members.count()) members" }
    return "invalid object"
}

func describe(value:JSON.Value) str {
    return match value.kind() {
        null_value => "null"
        boolean => describe_boolean(value)
        string => describe_string(value)
        number => describe_number(value)
        array => describe_array(value)
        object => describe_object(value)
    }
}

func main() {
    match (try JSON.Value.parse_text("[null,true,\"Silex\",42]", 8)) {
        failure(error) => { panic(error.detail) }
        success(value) => {
            print(describe(value))
            print(value.encoded(true))
        }
    }
}
```

La branche de `kind()` garantit la projection attendue. Les projections restent
optionnelles afin que la même fonction puisse aussi traiter une valeur reçue
sans contrat préalable.

[Revenir à la documentation JSON](../README.md)
