# Recettes JSON

Ces recettes documentent des parcours complets sans maintenir un catalogue
d’exécutables dans le package.

## Defaults

```json
{
  "application": "Silex",
  "debug": true,
  "workers": 4
}
```

## EditConfiguration

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

## InspectValueKinds

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
    match JSON.Value.parse_text("[null,true,\"Silex\",42]", 8) {
        failure(error) => { panic(error.detail) }
        success(value) => {
            print(describe(value))
            print(value.encoded(true))
        }
    }
}
```

## ReadConfiguration

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

## WriteReport

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
