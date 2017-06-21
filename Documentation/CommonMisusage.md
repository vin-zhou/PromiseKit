# Doubling Up Promises

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


# `Pending Promise Deallocated!`

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
