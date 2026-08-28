# JSON for Silex

The `JSON` package parses, inspects, constructs, and serializes JSON values
without losing the textual precision of numbers.

[Lire cette documentation en français.](../FR/README.md)

## Install the package

```text
silex install JSON
```

Then import its public module:

```sx
use JSON
```

JSON requires Silex 0.39.0 or newer.

## Read and modify a configuration

`read(path)` loads a local file with safe limits. The direct API returns the
requested value or stops with a precise diagnostic when the file or its
expected structure is invalid:

```sx
var config = JSON.read("application.json")
var window = config.require_object("window")

let width = window.require_integer("width")
window.set("width", 1920)
window.set("fullscreen", true)

JSON.write("application.json", config)
```

This fragment assumes that `application.json` contains a `window` object.
Objects provide `member`, `require`, the typed `require_*` projections, `set`,
and `remove`. Arrays provide `at`, `append`, `set_at`, and `remove_at`. Nested
values retain their identity, so changing `window` changes `config`.

`write(path, value)` uses pretty JSON by default. Pass an explicit `Format`
for compact storage. `require_integer` only accepts JSON numbers written in
integer form and rejects fractions and exponents instead of converting through
floating point.

Follow the [file editing recipe](Recipes/Edit-configuration.md) for the complete
workflow.

## Parse text or an embedded asset

`JSON.parse(text)` returns a `JSON.Value` or stops with a source-located
diagnostic. Its `maximum_depth` overload bounds recursion; the default limit is
128.

An embedded JSON file remains ordinary text:

```sx
let defaults = JSON.parse(embed_text("Defaults.json"))
```

Inspect `Value.kind()` or use the optional projections `as_boolean`,
`as_string`, `as_number_text`, `as_array`, and `as_object`. Numbers retain their
source decimal text. Object members preserve their names and values, while
duplicate names are rejected.

The recipes show how to [embed a configuration](Recipes/Read-embedded-configuration.md)
and [inspect every value kind](Recipes/Inspect-value-kinds.md).

## Construct output

Use `Value.null_value`, `boolean`, `string`, `number`, `array`, and `object`.
The direct API rejects invalid number text, non-finite floating-point values,
and duplicate object names.

`JSON.stringify(value)` and `JSON.stringify(value, Format.compact())` minimize
bytes; `Format.pretty()` produces indented output. `Value.encoded` retains the
historical boolean choice. Prefer `JSON.stringify` in new code because the
format is named at the call site.

The [report recipe](Recipes/Write-report.md) constructs every level before
producing readable text.

## Configure file limits

Ordinary calls use `read(path)` without options. `ReadOptions` lets an
application impose its own policy:

```sx
let limits = JSON.ReadOptions(
    maximum_bytes:64 * 1024 * 1024,
    maximum_depth:512,
)
let scene = JSON.read("Scenes/world.json", limits)
```

## Recover from errors

An application that must continue can use `try_parse`, `try_read`, `try_write`,
the `try_require_*` projections, and the `try_set`, `try_append`, `try_at`, and
`try_set_at` mutations.

`ReadError` distinguishes file errors from source-located parse errors.
`AccessError` distinguishes missing members, wrong kinds, invalid integer
projections, and array bounds. Recoverable construction uses
`Value.try_number`, `Value.try_number_text`, and `Value.try_object`.
