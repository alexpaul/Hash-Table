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

Collision occurs when more than one element uses the same index. In our example we will be resolving collisions by using `separate chaining`.

![hash table sketch](https://user-images.githubusercontent.com/1819208/97247042-320b3400-17d5-11eb-8d64-a4306ad4806c.jpg)

## Hash Table Implementation 

```swift 
struct HashTable<Key: Hashable, Value> {
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
  
  public mutating func update(value: Value, for key: Key) -> Value? {
    let index = self.index(for: key)
    for (i, element) in buckets[index].enumerated() {
      if element.key == key {
        let oldValue = element.value
        buckets[index][i].value = value
        return oldValue
      }
    }
    let element = Element(key, value)
    buckets[index].append(element)
    count += 1
    return nil
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
  
  public mutating func removeValue(for key: Key) -> Value? {
    let index = self.index(for: key)
    for (i, element) in buckets[index].enumerated() {
      if element.key == key {
        let removedValue = element.value
        buckets[index].remove(at: i)
        count -= 1
        return removedValue
      }
    }
    return nil
  }
  
  subscript(_ key: Key) -> Value? {
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

var jobSearch = HashTable<String, String>(capacity: 10)
jobSearch.update(value: "Applied", for: "Apple") // nil
jobSearch["Google"] = "Rejected" // Rejected
jobSearch["Zoc Doc"] = "Need to Apply"
jobSearch["Bloomberg"] = "Applied"
jobSearch["Fox"] = "Interview"
jobSearch.count // 2
print(jobSearch)
// HashTable<String, String>(buckets: [[], [(key: "Google", value: "Rejected")], [], [(key: "Zoc Doc", value: "Need to Apply")], [], [], [], [(key: "Apple", value: "Applied"), (key: "Fox", value: "Interview")], [], [(key: "Bloomberg", value: "Applied")]], count: 5)

jobSearch.update(value: "Offer", for: "Apple")
print(jobSearch)
// HashTable<String, String>(buckets: [[], [(key: "Google", value: "Rejected")], [], [(key: "Zoc Doc", value: "Need to Apply")], [], [], [], [(key: "Apple", value: "Offer"), (key: "Fox", value: "Interview")], [], [(key: "Bloomberg", value: "Applied")]], count: 5)
jobSearch["Fox"] = nil
jobSearch.removeValue(for: "Fox")
print(jobSearch)
// HashTable<String, String>(buckets: [[], [(key: "Google", value: "Rejected")], [], [(key: "Zoc Doc", value: "Need to Apply")], [], [], [], [(key: "Apple", value: "Offer")], [], [(key: "Bloomberg", value: "Applied")]], count: 5)
```

## Resources 

1. [Wikipedia - Hash Table](https://en.wikipedia.org/wiki/Hash_table)
1. [Swift Algorithm Club - Hash Table](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Hash%20Table)
1. [Video - HackerRank - HackerRank](https://www.youtube.com/watch?v=shs0KM3wKv8)
1. [GeeksForGeeks - Separate Chaining](https://www.geeksforgeeks.org/hashing-set-2-separate-chaining/)
