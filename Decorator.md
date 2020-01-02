**Decorator Pattern**

This is a structural pattern. This pattern helps change a core object without modifying its existing implementation.

Consider the following scenario where there are multiple types of objects with just different property values but the same property types.

The biggest problem this pattern solves is `class explosion`.

Another advantage this pattern adds is **the order in which the decorators are applied does not affect any behavior** of the object.

```swift
protocol Speaker {
    var maxPower: Float { get }
    var maxBass: Float { get }
}
```

The concrete class implementation of this protocol will look like

```swift
class ConcreteSpeaker: Speaker {
    let maxPower: Float
    let maxBass: Float
    
    init(maxPower: Float, maxBass: Float) {
        self.maxPower = maxPower
        self.maxBass = maxBass
    }
}
```

Now if there's a speaker with just different bass rating value we can define the decarator as follows:

```swift
protocol BassUpgradeDecorator: Speaker {
  var speaker: Speaker { get }

  init(speaker: Speaker)
}
```

And I want to keep the default implementation for the `BassUpgradeDecorator` in the protocol extension to help unit testing. Also doing it this way the client doesn't have to worry about the implementation for the decorator and test objects could easily be mocked.

```swift
extension BassUpgradeDecorator { // only `maxBass` is updated
    var maxBass: Float {
        return 5.0 + speaker.maxBass
    }
    
    var maxPower: Float { return speaker.maxPower }
}
```

Notice the `BassUpgradeDecorator` has a wrapped `Speaker` property. From this property all the modifications to values are performed.

A similar approach for a different power rating would go as:

```swift
protocol PowerUpgradeDecorator: Speaker {
	var speaker: Speaker { get }

    init(speaker: Speaker)
}
```

and the default implementation for adjusted power rating is:

```swift
extension PowerUpgradeDecorator { // only `maxPower` is updated
    var maxPower: Float {
        return 10.0 + speaker.maxPower
    }
    
    var maxBass: Float { return speaker.maxBass }
}
```

Concreate class would be:

```swift
class ConcreteBassUpgradedSpeaker: BassUpgradeDecorator {
    let speaker: Speaker
    
    required init(speaker: Speaker) {
        self.speaker = speaker
    }
}
```

and 

```swift
class ConcretePowerUpgradedSpeaker: PowerUpgradeDecorator {
    let speaker: Speaker
    
    required init(speaker: Speaker) {
        self.speaker = speaker
    }
}
```

Let's see how the client consumes the decorators:

```swift
var speaker: Speaker = ConcreteSpeaker(maxPower: 110.0, maxBass: 1.0)

print("ConcreteSpeaker: Bass - \(speaker.maxBass) -- Power - \(speaker.maxPower)\n") 

// prints -> Bass - 1.0 -- Power - 110.0

speaker = ConcreteBassUpgradedSpeaker(speaker: speaker)

print("PowerUpgradedSpeaker: Bass - \(speaker.maxBass) -- Power - \(speaker.maxPower)\n") 

// prints -> Bass - 6.0 -- Power - 110.0

speaker = ConcretePowerUpgradedSpeaker(speaker: speaker)

print("PowerUpgradedSpeaker: Bass - \(speaker.maxBass) -- Power - \(speaker.maxPower)\n") 

// prints -> Bass - 6.0 -- Power - 120.0

Notice how the variable `speaker` is typed to `Speaker` type.
```

**A Swift Bonus 🎉**

While implementing this I found the `speaker` property in the `Decorator` protocols to be leaky. I want to limit the visibility of the property in the implementation.

What do I mean by this? For example- we can create an instance of `ConcretePowerUpgradedSpeaker` and when doing so the `speaker` property is visible outside.

```swift
let powerUpgradedSpeaker = ConcretePowerUpgradedSpeaker(speaker: speaker)
powerUpgradedSpeaker.speaker // leaky implementation
```

By default in Swift all the access levels for protocol definitions are internal. This forces me mark `speaker` property of `ConcretePowerUpgradedSpeaker` internal. Now, I want to limit the access level of this property.

One way to do this is:

- to introduce a new component that'd encapsulate the `speaker` property with a `fileprivate` access level as follows:

```swift
struct SpeakerDecoratorComponent {
    fileprivate var speaker: Speaker
    
    init(speaker: Speaker) {
        self.speaker = speaker
    }
}
```

Now the decorator protocols would change to accept the component and the `speaker` property's visibility is limited to the defining file.

```swift
protocol BassUpgradeDecorator: Speaker {
    var decoratorComponent: SpeakerDecoratorComponent { get }
    init(decoratorComponent: SpeakerDecoratorComponent)
}
```

and the extension would change as:

```swift
extension BassUpgradeDecorator { // only `maxBass` is updated
    var maxBass: Float {
        return 5.0 + decoratorComponent.speaker.maxBass
    }
    
    var maxPower: Float { return decoratorComponent.speaker.maxPower }
}
```

```swift
protocol PowerUpgradeDecorator: Speaker {
    var decoratorComponent: SpeakerDecoratorComponent { get }
    init(decoratorComponent: SpeakerDecoratorComponent)
}
```

and the extension would change as:

```swift
extension PowerUpgradeDecorator { // only `maxPower` is updated
    var maxPower: Float {
        return 10.0 + decoratorComponent.speaker.maxPower
    }
    
    var maxBass: Float { return decoratorComponent.speaker.maxBass }
}
```

Even if the outside world has access to `decoratorComponent` they could not read any property from it.

And the conrete classes would also change as follows:

```swift
class ConcreteBassUpgradedSpeaker: BassUpgradeDecorator {
    var decoratorComponent: SpeakerDecoratorComponent
    
    required init(decoratorComponent: SpeakerDecoratorComponent) {
        self.decoratorComponent = decoratorComponent
    }
}
```

```swift
class ConcretePowerUpgradedSpeaker: PowerUpgradeDecorator {
    var decoratorComponent: SpeakerDecoratorComponent
    
    required init(decoratorComponent: SpeakerDecoratorComponent) {
        self.decoratorComponent = decoratorComponent
    }
}
```

**Testing:**

The default implementations in the `Decorator` protocols would help in unit testing. The mock objects would have to do nothing. In fact we only need the mock object to create instances of `PowerUpgradeDecorator` and `BassUpgradeDecorator` protocols.

protocol Pizza {
    var description: String { get }
    var cost: Double { get }
}

enum PizzaCrust {
    case thin
    case thick
    
    var description: String {
        switch self {
        case .thin:
            return "A thin crust Pizza"
        case .thick:
            return "A thick crust Pizza"
        }
    }
    
    var cost: Double {
        switch self {
        case .thin: return 0.50
        case .thick: return 0.70
        }
    }
}

class ConcretePizza: Pizza {
    private var crust: PizzaCrust
    
    var description: String {
        return crust.description
    }
    
    var cost: Double { return 1.99 + crust.cost } // 1.99 Pizza cost
    
    required init(crust: PizzaCrust) {
        self.crust = crust
    }
}

enum PizzaTopping {
    case cheese
    case olive
    case pepper
    
    var description: String {
        switch self {
        case .cheese: return "cheese topping."
        case .olive: return "olive topping."
        case .pepper: return "pepper topping."
        }
    }
    
    var cost: Double {
        switch self {
        case .cheese: return 0.10
        case .olive: return 0.30
        case .pepper: return 0.70
        }
    }
}

protocol PizzaToppingDecorator: Pizza {
    var pizza: Pizza { get }
    var topping: PizzaTopping { get }
    
    init(pizza: Pizza, topping: PizzaTopping)
}

extension PizzaToppingDecorator {
    var description: String {
        return "\(pizza.description + " with \(topping.description)")"
    }
    
    var cost: Double { return pizza.cost + topping.cost }
}

class ConcretePizzaTopping: PizzaToppingDecorator {
    var pizza: Pizza
    var topping: PizzaTopping
    
    required init(pizza: Pizza, topping: PizzaTopping) {
        self.pizza = pizza
        self.topping = topping
    }
}

var pizza: Pizza = ConcretePizza(crust: .thin)
print("Pizza - \(pizza.description) -- \(pizza.cost)\n")
// prints "Pizza - A thin crust Pizza -- 2.49"

pizza = ConcretePizzaTopping(pizza: pizza, topping: .cheese)
print("Decorated Pizza - \(pizza.description) -- \(pizza.cost)\n")
// prints "Decorated Pizza - A thin crust Pizza with cheese topping. -- 2.5900000000000003"

**Testing:**

In this approach the `enum` can be tested independently since the core logic `description` and `cost` is computed in the `enum.`
