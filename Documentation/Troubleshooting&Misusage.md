# Troubleshooting

Sadly, Swift often gives misleading diagnostic messages for compile errors with
PromiseKit.

### Cannot convert return expression of type … to return type AnyPromise

Specify the return type for the closure. DON’T return `AnyPromise` (unless you
wanted to).

### Missing return in a closure expected to return AnyPromise

Specify the return type for the closure. DON’T return `AnyPromise` (unless you
wanted to).

### Ambiguous *bar*

Specify the return type for the closure.

### Neither `catch` or `then` are called in my chain

Check something didn’t throw a `CancellableError`. Easiest way is to amend your
`catch`:

```swift
foo.then {
    //…
}.catch(policy: .allErrorsIncludingCancellation) {
    // cancelled errors are handled *as well*
}
```

## Pain Free Swift Promises

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

# Common Misusage

## Doubling Up Promises

Don’t do this:

```swift
func toggleNetworkSpinnerWithPromise<T>(funcToCall: () -> Promise<T>) -> Promise<T> {
    return Promise { fulfill, reject in
        firstly {
            setNetworkActivityIndicatorVisible(true)
            return funcToCall()
        }.then { result in
            fulfill(result)
        }.always {
            setNetworkActivityIndicatorVisible(false)
        }.error { err in
            reject(err)
        }
    }
}
```

Do this:

```swift
func toggleNetworkSpinnerWithPromise<T>(funcToCall: () -> Promise<T>) -> Promise<T> {
    return firstly {
        setNetworkActivityIndicatorVisible(true)
        return funcToCall()
    }.always {
        setNetworkActivityIndicatorVisible(false)
    }
}
```

You already *had* a promise, you don’t need to wrap it in another promise.


## `Pending Promise Deallocated!`

If you see this warning you have a path in your `Promise` initializer where
neither `fulfill` or `reject` are called:

```swift
Promise<String> { fulfill, reject in
    task { value, error in
        if let value = value as? String {
            fulfill(value)
        } else if let error = error {
            reject(error)
        }
    }
}
```

There are two missing paths here and if either occur the promise will soon be
deallocated without resolving. This will show itself as a bug in your app,
probably the awful: infinite spinner.

So let’s be thorough:

```swift
Promise<String> { fulfill, reject in
    task { value, error in
        if let value = value as? String {
            fulfill(value)
        } else if let error = error {
            reject(error)
        } else if value != nil {
            reject(MyError.valueNotString)
        } else {
            // should never happen, but we have an `PMKError` for task being called with `nil`, `nil`
            reject(PMKError.invalidCallingConvention)
        }
    }
}
```
