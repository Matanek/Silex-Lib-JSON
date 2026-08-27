# JSON for Silex

`JSON` parses, inspects, constructs and serializes JSON values without losing
the textual precision of numbers.

```text
silex install JSON
```

```sx
use JSON
```

## Read and modify a configuration

`read(path)` loads a local JSON file with safe default limits. The ordinary API
returns the requested value directly and stops with a precise diagnostic when
the file or its expected structure is invalid:

```sx
var config = JSON.read("application.json")
var window = config.require_object("window")

let width = window.require_integer("width")
window.set("width", 1920)
window.set("fullscreen", true)

JSON.write("application.json", config)
```

Objects support `member`, `require`, the typed `require_*` projections, `set`
and `remove`. Arrays support `at`, `append`, `set_at` and `remove_at`. Nested
values retain their identity, so modifying `window` above modifies `config`.
`write(path, value)` uses readable pretty JSON by default; pass an explicit
`Format` when compact storage is preferred.

`require_integer` accepts JSON numbers written in integer form and rejects
fractional or exponent notation instead of converting through floating point.

The complete file workflow is preserved in
[Docs/Recipes.md](Docs/Recipes.md#editconfiguration).

## Parse text or embedded files

`JSON.parse(text)` returns a `JSON.Value` or stops with a source-located
diagnostic. The overload with `maximum_depth` bounds recursive input; the
default limit is 128.

An embedded JSON file is ordinary text and uses the same parser:

```sx
let defaults = JSON.parse(embed_text("Defaults.json"))
```

Inspect `Value.kind()` or use the optional typed projections `as_boolean`,
`as_string`, `as_number_text`, `as_array` and `as_object`. JSON numbers retain
their source decimal text instead of silently losing precision. Object members
preserve names and values, while duplicate names are rejected.

[Docs/Recipes.md](Docs/Recipes.md) documents embedded configuration, every
typed projection, explicit parse depth and the direct compatibility encoder.

## Construct output

Use `Value.null_value`, `boolean`, `string`, `number`, `array` and `object`.
Invalid explicit number text, non-finite floating-point values and duplicate
object names stop with a diagnostic in the ordinary API.

`JSON.stringify(value)` and `JSON.stringify(value, Format.compact())` minimize bytes;
`Format.pretty()` produces indented human-readable output. `Value.encoded`
offers the same choice through a boolean for compatibility. Prefer
`JSON.stringify` in new code because the format is named at the call site.
See the complete [report recipe](Docs/Recipes.md#writereport).

## Advanced file limits

Ordinary callers should use `read(path)` without constructing options.
`ReadOptions` is available when an application must impose its own file size
or nesting policy:

```sx
let limits = JSON.ReadOptions(
    maximum_bytes:64 * 1024 * 1024,
    maximum_depth:512,
)
let scene = JSON.read("Scenes/world.json", limits)
```

## Handle errors deliberately

Applications that recover from malformed input can opt into the structured
variants `try_parse`, `try_read`, `try_write`, `try_require_*`, `try_set`,
`try_append`, `try_at` and `try_set_at`:

```sx
match JSON.try_read("application.json") {
    success(config) => { print(config.require_string("application")) }
    failure(error) => { /* recover, report or use defaults */ }
}
```

`ReadError` distinguishes file failures from source-located parse failures;
`AccessError` distinguishes missing members, wrong kinds, invalid integer
projections and array bounds. Fallible construction similarly uses
`Value.try_number`, `Value.try_number_text` and `Value.try_object`.

## Development

Register the checkout, then exercise the public API through its consumer tests:

```text
silex link .
silex test "$PWD/Tests"
```
