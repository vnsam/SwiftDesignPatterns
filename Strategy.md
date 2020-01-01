**Strategy Pattern**

The use case is:

- To enable `Share` and `Edit` options based on the version of the app the user has installed.

```swift
/*
 - Functionalities: take, edit, save, share
 - edit - each version(basic, plus) of the app has custom edit functionalities.
 */
```

```swift
 protocol PhoneCameraApp {
    var shareOptions: [Shareable] { get }
    var editor: Editable { get }
    
    init(shareOptions: [Shareable], editor: Editable)
    
    func take()
    func save()
}
```

```swift
extension PhoneCameraApp {
    func take() {
        // default implementation
    }
    
    func save() {
        // default implementation
    }
}
```

In `Swift` we can add default implementations to protocols. Hence avoiding the need for a base class in this scenario.
So unless we want a different implementation for `take()` and `save()` the classes conforming to `PhoneCameraApp` protocol don't have to implement these 2 functions.

And notice the `PhoneCameraApp` protocol has an `init` this way the compiler would force the conforming classes to mark the initializer above as `required`. This way we can enforce to inject dependencies `Editable`, `Shareable` via the initializer.

```swift
protocol Editable {
    func edit()
}
```

The `Editable` protocol defines the `edit()` functionality.
Now users could have different edit filters based on the version installed.
For example a basic user would have a basic editor `EditBasic` and a plus version user would have a premium editor `EditPlus`.

```swift
class EditBasic: Editable {
    func edit() {
        // implementation for basic edit functionality.
    }
}
```

```swift
class EditPlus: Editable {
    func edit() {
        // implementation for plus version edit functionality.
    }
}
```

```swift
protocol Shareable {
    func share()
}
```

The `Shareable` protocol defines the `share()` functionality.
A basic user could only share via email - `EmailShare`. A plus user could share via email - `EmailShare` and social media - `SocialMediaShare`. Both `EmailShare` and `SocialMediaShare` implement `share()` in their own way.

```swift
class EmailShare: Shareable {
    func share() {
        // implementation to share via email.
    }
}
```

```swift
class SocialMediaShare: Shareable {
    func share() {
        // implementation to share via social media.
    }
}
```

Now these can be consumed in the following way:

A basic version class would look like this:

```swift
class BasicCameraApp: PhoneCameraApp {
    private(set) var shareOptions: [Shareable]
    private(set) var editor: Editable
    
    required init(shareOptions: [Shareable], editor: Editable) {
        self.shareOptions = shareOptions
        self.editor = editor
    }
    
    // actions
    
    func takePhotoButtonTapped() {
        take()
    }
    
    func editPhotoButtonTaped() {
        editor.edit()
    }
    
    func savePhotoButtonTapped() {
        save()
    }
}
```

and a plus version class would look like this:

```swift
class CameraAppPlus: PhoneCameraApp {
    private(set) var shareOptions: [Shareable]
    private(set) var editor: Editable
    
    required init(shareOptions: [Shareable], editor: Editable) {
        self.shareOptions = shareOptions
        self.editor = editor
    }
    
    // actions
    
    func takePhotoButtonTapped() {
        take()
    }
    
    func editPhotoButtonTaped() {
        editor.edit()
    }
    
    func savePhotoButtonTapped() {
        save()
    }
}
```

Now both `BasicCameraApp` and `CameraAppPlus` are of type `PhoneCameraApp`. This way we can pick a strategy based on the version of the app user has installed.

**Testing:**
And when it comes to testing - each implementation is testable in itself.
