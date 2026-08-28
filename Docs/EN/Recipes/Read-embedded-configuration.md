# Embed a configuration

This recipe includes default values in the executable. The `Defaults.json`
path is relative to the `.sx` file.

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

The program prints all three names in document order. `embed_text` includes the
text at compile time; `JSON.parse` then applies the same contract as it does to
a string loaded at runtime.

[Return to the JSON documentation](../README.md)
