# Build a report

This recipe explicitly constructs arrays and object members, then produces
pretty JSON.

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

`Value.object` verifies name uniqueness. Numbers, booleans, strings, arrays,
and objects all remain `Value` instances, so the document can be composed
before choosing its output format.

[Return to the JSON documentation](../README.md)
