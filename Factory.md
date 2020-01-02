**Factory Pattern**

This is a creational pattern.

This pattern solves the problem of `moving the creational logic away from the client`.

Let's consider the following scenario where a `zone` is conditionally set to a `Calendar` based on the `Zone` requested.

```swift
protocol Zone {
    var displayName: String { get }
    var offset: Int { get }
}
```

and the implementing conrete structures are

```swift
struct Central: Zone {
    var displayName: String { return "US/Central" }
    var offset: Int { return -6 }
}

struct Eastern: Zone {
    var displayName: String { return "US/Eastern" }
    var offset: Int { return -5 }
}

struct Mountain: Zone {
    var displayName: String { return "US/Mountain" }
    var offset: Int { return -7 }
}

struct Pacific: Zone {
    var displayName: String { return "US/Pacific" }
    var offset: Int { return -8 }
}
```

There are a many ways to implement a `Factory`, I used enum to implement the factory. This could even be a static method. Your choice!

```swift
enum ZoneFactory {
    case central
    case eastern
    case mountain
    case pacific
    
    var zone: Zone {
        switch self {
        case .central:
            return Central()
        case .eastern:
            return Eastern()
        case .mountain:
            return Mountain()
        case .pacific:
            return Pacific()
        }
    }
}
```

The `client` will request the `ZoneFactory` for a `zone`.

```swift
class Calendar {
    var zone: Zone
    
    init(zone: Zone) {
        self.zone = zone
    }
}
```

```swift
let zone = ZoneFactory.central.zone

let calendar = Calendar(zone: zone)

print(calendar.zone.displayName) // prints US/Central
print(calendar.zone.offset) // prints -6
```

**Testing:**

The factory could be tested by requesting an instance of interest and asserting the behavior.
