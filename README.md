![PromiseKit](http://promisekit.org/public/img/logo-tight.png)

![badge-pod] ![badge-languages] ![badge-pms] ![badge-platforms] ![badge-mit]

[繁體中文](README.zh_Hant.md) [简体中文](README.zh_CN.md)

---

Modern development is highly asynchronous: isn’t it about time we had tools that
made programming asynchronously powerful, easy and delightful?

```swift
UIApplication.shared.isNetworkActivityIndicatorVisible = true

firstly {
    when(URLSession.dataTask(with: url).asImage(), CLLocationManager.promise())
}.then { image, location -> Void in
    self.imageView.image = image
    self.label.text = "\(location)"
}.always {
    UIApplication.shared.isNetworkActivityIndicatorVisible = false
}.catch { error in
    self.show(UIAlertController(for: error), sender: self)
}
```

PromiseKit is a thoughtful and complete implementation of promises for any
platform with a `swiftc` (indeed, this includes *Linux*), it has
*excellent* Objective-C bridging and *delightful* specializations for iOS,
macOS, tvOS and watchOS.

* Promises are a sensible pattern for modern development in an asynchronous world.
* Promises are easy to learn *and* easy to master.
* Code written with promises is simple to follow, making your code more useful to your teams.


# Quick Start

```ruby
# CocoaPods
use_frameworks!
swift_version = "3.0"
pod "PromiseKit", "~> 4.0"

# Carthage
github "mxcl/PromiseKit" ~> 4.0

# SwiftPM
package.dependencies.append(
    .Package(url: "https://github.com/mxcl/PromiseKit", majorVersion: 4)
)
```

Also you can just drop `PromiseKit.xcodeproj` into your project and then add
`PromiseKit.framework` to your app’s embedded frameworks.

If you need to use PromiseKit with an older Xcode or Swift or an older
PromiseKit, see [our documentation](Documentation/PromiseKitVersions.md).


# Documentation

* [Getting Started with PromiseKit](Documentation/101.md)
* [Common Patterns](Documentation/CommonPatterns.md)
* [FAQ](Documentation/FAQ.md)
* [Objective-C Guide](Documentation/ObjectiveC.md)

---

* [Troubleshooting & Common Misusages](Documentation/Troubleshooting&Misusage.md) (compiler errors & bad practices)
* [Older & Newer PromiseKit Versions](Documentation/PromiseKitVersions.md) (including support for older or newer Swift versions).

---

If you are looking for a function’s documentation, then please note the
[PromiseKit sources](Sources/) are thoroughly documented.


# Choose Your Networking Library

Since the most common starting point for promise chains is a network source we
provide two useful options; either [Alamofire] or [OMGHTTPURLRQ]:

```swift
// pod 'PromiseKit/Alamofire'  
Alamofire.request("http://example.com", withMethod: .GET).responseJSON().then { json in
    //…
}.catch { error in
    //…
}

// pod 'PromiseKit/OMGHTTPURLRQ'
URLSession.GET("http://example.com").asDictionary().then { json in
    //…
}.catch { error in
    //…
}
```

For [AFNetworking] we recommend [csotiriou/AFNetworking].

*Naturally* you can just use `NSURLSession`, and we [provide promises for that
too](https://github.com/PromiseKit/Foundation).


# Support

Ask your question at our [Gitter chat channel](https://gitter.im/mxcl/PromiseKit) or on
[our bug tracker](https://github.com/mxcl/PromiseKit/issues/new).


[badge-pod]: https://img.shields.io/cocoapods/v/PromiseKit.svg?label=version
[badge-pms]: https://img.shields.io/badge/supports-CocoaPods%20%7C%20Carthage%20%7C%20SwiftPM-green.svg
[badge-languages]: https://img.shields.io/badge/languages-Swift%20%7C%20ObjC-orange.svg
[badge-platforms]: https://img.shields.io/badge/platforms-macOS%20%7C%20iOS%20%7C%20watchOS%20%7C%20tvOS-lightgrey.svg
[badge-mit]: https://img.shields.io/badge/license-MIT-blue.svg
[OMGHTTPURLRQ]: https://github.com/mxcl/OMGHTTPURLRQ
[Alamofire]: http://alamofire.org
[AFNetworking]: https://github.com/AFNetworking/AFNetworking
[csotiriou/AFNetworking]: https://github.com/csotiriou/AFNetworking-PromiseKit
