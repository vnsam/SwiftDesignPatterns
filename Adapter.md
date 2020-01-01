**Adapter Pattern**

It is a structural pattern. This pattern is also know as `Wrapper`.

Conside the scenario where a web service returns metadata for a `Book` in the following format.

```swift
struct Book: Decodable {
    let title: String
    let author: String
    let ISBN: String
    let publishedOn: TimeInterval
}
```

Let's look the property `publishedOn` which is of type `TimeInterval` but in the UI the client needs to display the date in a readable format as `2010-07-29`.

To achive this we can create an `Adapter`. This adapter could be in any form like a class function, an extension function, or an extension to the `Book` struct.

```swift
protocol DisplayableBook {
    func getDisplayDateFormat(forTimeInterval timeinterval: TimeInterval) -> String
}
```

```swift
extension DisplayableBook {
	func getDisplayDateFormat(forTimeInterval timeinterval: TimeInterval) -> String {
		// custom date formatter implementation for displaying in the UI.
	}
}
```

This protocol defines a format for displaying meta data in the UI. Now one way to adapt this is using a protocol and extend it with a default implementation.

**Testing:**

To test the adapter since the protocol carries with it a default implementation and we could just test implementation in itself.
