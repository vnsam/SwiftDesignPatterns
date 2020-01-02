# SwiftDesignPatterns
Implementations of various design patterns in Swift.

**Key takeaways:**

- Always begin designing a system with it's `behavior` rather than beginning with `data`. This approach adds clarity to identify components in the `system`. This is said otherwise in the famous saying `Program to an interface not an implementation!`

**Swift specific inputs:**

- Prefer defualt implementations in protocol over inheritence.
- Init could go on protocol. This'd enforce `required` initialization.
- Avoid concrete classes as much as your design allows. Leverage protocol extensions over concrete classes.

**References:**

1. https://sourcemaking.com/design_patterns
2. https://swiftcraft.io/decorator-pattern-swift/
3. http://www.oopwithswiftasapro.com/2017/08/11/decorator-pattern/
4. https://learning.oreilly.com/library/view/design-patterns-elements/0201633612/
5. https://stablekernel.com/swift-design-patterns-in-practice-the-decorator-pattern/
6. https://benoitpasquier.com/observer-design-pattern-swift/
