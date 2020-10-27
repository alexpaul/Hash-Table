# Hash Table

Hash Table implementation in Swift.

> A dictionary is a type of hash table, providing fast access to the entries it contains. Each entry in the table is identified using its key, which is a hashable type such as a string or number. You use that key to retrieve the corresponding value, which can be any object. In other languages, similar data types are known as hashes or associated arrays.

## Hash function 

The hash function calculates a hash value that will be used to determine which index in the `buckets` array to store the `key, value` pair.   

```swift 
var buckets = Array(repeating: [], count: 5)
let index = abs("alex".hashValue % buckets.count)
print(index) // index 4 - this index will vary as a new hash value is calculated every time
```

## Collision

Collision occurs when more than one element uses the same index. In our example we will be resolving collisions by using `chaining`.

![hash table sketch](https://user-images.githubusercontent.com/1819208/97247042-320b3400-17d5-11eb-8d64-a4306ad4806c.jpg)

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

## Resources 

1. [Wikipedia - Hash Table](https://en.wikipedia.org/wiki/Hash_table)
