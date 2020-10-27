# Hash Table

Hash Table implementation in Swift.

## Hash Table Implementation 

```swift 
struct HashTable<Key: Hashable, Value> {
  
  // data structures
  private typealias Element = (key: Key, value: Value)
  private typealias Bucket = [Element]
  private var buckets: [Bucket]
  
  private (set) public var count = 0
  
  init(capacity: Int) {
    assert(capacity > 0)
    buckets = Array(repeating: [], count: capacity)
  }
  
  private func index(for key: Key) -> Int {
    return abs(key.hashValue % buckets.count)
  }
  
  public func value(for key: Key) -> Value? {
    let index = self.index(for: key)
    for (_, element) in buckets[index].enumerated() {
      if element.key == key {
        return element.value
      }
    }
    return nil
  }
  
  public mutating func update(value: Value, for key: Key) -> Value? {
    let index = self.index(for: key)
        
    for (i, element) in buckets[index].enumerated() {
      if element.key == key {
        let oldValue = element.value
        buckets[index][i].value = value
        return oldValue
      }
    }
    
    buckets[index].append((key: key, value: value))
    
    count += 1
    
    return nil
  }
  
  public mutating func removeValue(for key: Key) -> Value? {
    let index = self.index(for: key)
    
    for (i, element) in buckets[index].enumerated() {
      if element.key == key {
        let removedValue = element.value
        buckets[index].remove(at: i)
        return removedValue
      }
    }
    return nil
  }
  
  subscript(key: Key) -> Value? {
    set {
      if let value = newValue {
        update(value: value, for: key)
      } else {
        removeValue(for: key)
      }
    } get {
      return value(for: key)
    }
  }
  
}

var dict = HashTable<String, String>(capacity: 10)

dict.update(value: "iOS Developer", for: "Alex Paul") // nil

dict.count // 1

dict.update(value: "Full Stack Dev", for: "Alex Paul") // iOS Developer

dict.value(for: "Alex Paul") // Full Stack Dev

dict.removeValue(for: "Alex Paul") // Full Stack Dev

dict.value(for: "Alex Paul") // nil

dict["Tim Cook"] = "Apple CEO" // Apple CEO

dict.update(value: "Reggae Legend", for: "Bob Marley") // nil

dict["Bob Marley"] // Reggae Legend

dict["Tim Cook"] // Apple CEO

dict.count // 3

```
