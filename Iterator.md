**Iterator Pattern**

This is a behavioral pattern. This pattern is also known as `Cursor` pattern.

The problem this pattern solves is seperating the `traversal implementation` over a `Collection`.

Swift language has `iterator` pattern in many forms;

Everything starts with the `IteratorProtocol` and `Sequence` defined in the `Swift Standard Library.`

Conside the following piece of code:

```swift
let numbers = [1, 2, 3, 4, 5, 6]

var numbersIterator = numbers.makeIterator()

while let number = numbersIterator.next() {
    print(number)
}

// prints
/*
1
2
3
4
5
6
*/

```

```swift
let books = [
    Book(title: "The Alchemist"),
    Book(title: "Code Complete"),
    Book(title: "Apprenticeship Patterns")
]

var booksIterator = books.makeIterator()
while let book = booksIterator.next() {
    print(book.title)
}

// prints
/*
The Alchemist
Code Complete
Apprenticeship Patterns
*/
```

If we want to apply `filter`, `sort` or any other custom iterating operation over a collection, the implementation of those custom iteration operation will be seperated. The object still conforms to `IteratorProtocol` and `Sequence` but how it implements `next` and `hasNext` is completely upto the object.

**A custom `Iterator` example**

```swift
protocol Iterator: IteratorProtocol, Sequence {
    associatedtype Element
    
    init(collection: [Element])
    
    var hasNext: Bool { get }
    mutating func next() -> Element?
}
```

Now a concrete implementation of `Iterator` over a type would look like:

```swift
struct Book {
    let title: String
}

struct BooksIterator: Iterator {
    typealias Element = Book
    
    init(collection: [Element]) {
        self.collection = collection
    }
    
    var hasNext: Bool {
        guard collection.count > index else { return false }
        
        return true
    }
    
    mutating func next() -> Book? {
        defer { index += 1 }
        
        guard hasNext else { return nil }
        
        return collection[index]
    }
    
    private(set) var collection: [Book]
    private var index = 0
}

extension BooksIterator {
    __consuming func makeIterator() -> BooksIterator {
        return BooksIterator(collection: collection)
    }
}
```

and the client would call this custom iterator as follows:

```swift
let books = [
    Book(title: "The Alchemist"),
    Book(title: "Code Complete"),
    Book(title: "Apprenticeship Patterns")
]


var iterator = BooksIterator(collection: books)
while let book = iterator.next() {
    print(book.title)
}

// prints

/*
The Alchemist
Code Complete
Apprenticeship Patterns
*/
```

Now revisit and apply the `sort` or `filter` iterating opertation over this concrete implementation.

The `SortedBooksIterator` and `FilteredBooksIterator`  (not coded here) will have their own implementation of `hasNext` and `next()`.
