**Observer Pattern**

This is a behavioral pattern. This pattern helps loose couple state or data when there are many dependants listening to a subject. This pattern is also known as Publisher-Subscriber pattern. *( geeky term: `Pub-Sub` :) )*

Sometimes the publishing object is referred to as `Subject` and the listening object is referred to as `Observer`.

**For anyone who's developed iOS code this pattern is not new. Go ahead and skip!**

Let's take an example of `WeatherStation` where many systems have to update when there's a weather change.

The subject must have the capability to register, remove and notify the observers.

One to many observers can register for a subject.

```swift
protocol Subject {
    func registerObserver(_ observer: Observer)
    func removeObserver(_ observer: Observer)
    func notifyObservers(withData data: WeatherData)
}
```

```swift
class WeatherSubject: Subject {
    private var observers: [Observer] = []
    
    func registerObserver(_ observer: [Observer]) {
        // register observer
    }
    
    func removeObserver(_ observer: Observer) {
        // remove observer
    }
    
    func notifyObservers(withData data: WeatherData) {
        // notify ALL observers on data update
    }
}
```

And the observer is defined as

```swift
protocol Observer {
    func update(weatherData: WeatherData)
}
```

And the implementation at client side is

```swift
class UserInterface: Observer {
    func update(weatherData: WeatherData) {
        // refresh UI
    }
}

class Logger: Observer {
    func update(weatherData: WeatherData) {
        // log weather
    }
}

class AlertSystem: Observer {
    func update(weatherData: WeatherData) {
        // alert system implementation
    }
}
```

So the weather station could work!

```swift
class WeatherStation {
    let userInterface = UserInterface()
    let logger = Logger()
    let alertSystem = AlertSystem()
}
```
