# Troubleshooting

Sadly, Swift often gives misleading diagnostic messages for compile errors with
PromiseKit.

## Cannot convert return expression of type … to return type AnyPromise

Specify the return type for the closure. DON’T return `AnyPromise` (unless you
wanted to).

## Missing return in a closure expected to return AnyPromise

Specify the return type for the closure. DON’T return `AnyPromise` (unless you
wanted to).

## Ambiguous *bar*

Specify the return type for the closure.

## Neither `catch` or `then` are called in my chain

Check something didn’t throw a `CancellableError`. Easiest way is to amend your
`catch`:

```swift
foo.then {
    //…
}.catch(policy: .allErrorsIncludingCancellation) {
    // cancelled errors are handled *as well*
}
```


# Pain Free Swift Promises

The best way is to appease Swift is to decompose your chains into separated
local functions. For example:

```swift
func foo() -> Promise<Int>
    func step1() -> Promise<String> {
        // many
        // lines
        // of
        // code
    }
    
    func step2() -> Promise<Int> {
        // many
        // lines
        // of
        // code
    }

    return firstly {
        step1()
    }.then {
        step2()
    }
}
```
