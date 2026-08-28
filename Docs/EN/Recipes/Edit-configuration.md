# Edit a configuration file

This recipe reads, changes, then rewrites a local configuration.

Create `application.json`:

```json
{
  "window": {
    "width": 1280,
    "fullscreen": false
  }
}
```

Place this program next to the file:

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

`require_object` verifies the expected structure. The `window` node retains
its identity inside `config`, then `write` produces a pretty file with both new
values.

[Return to the JSON documentation](../README.md)
