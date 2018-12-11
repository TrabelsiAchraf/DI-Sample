# DI-Sample
Swift dependency injection design pattern Sample

## Types of Dependency Injection:
Most developers consider three forms or types of dependency injection:

#### Dependency injection through an initializer (initializer injection) :

```swift
protocol Encoder {
    func encode<T>(_ value: T) throws -> Data where T: Encodable
}

extension JSONEncoder: Encoder {}
extension PropertyListEncoder: Encoder {}

// Custructor injection

class Post: Encodable {
    
    var title: String
    var content: String
    
    private var encoder: Encoder
    
    private enum CodingKeys: String, CodingKey {
        case title
        case content
    }
    
    init(title: String, content: String, encoder: Encoder) {
        self.title = title
        self.content = content
        self.encoder = encoder
    }
    
    func encoded() throws -> Data {
        return try self.encoder.encode(self)
    }
}

let post = Post(title: "Hello DI!", content: "Constructor injection", encoder: JSONEncoder())

if let data = try? post.encoded(), let encoded = String(data: data, encoding: .utf8) {
    print(encoded)
}
```

#### Dependency injection using properties (property injection) :

``` Swift
// Property injection

class PostProp: Encodable {
    var title: String
    var content: String
    
    var encoder: Encoder?
    
    private enum CodingKeys: String, CodingKey {
        case title
        case content
    }
    
    init(title: String, content: String) {
        self.title = title
        self.content = content
    }
    
    func encoded() throws -> Data {
        guard let encoder = self.encoder else {
            fatalError("Encoding is only supported with a valid encoder object.")
        }
        return try encoder.encode(self)
    }
}

let postProp = PostProp(title: "Hello DI!", content: "Property injection")
postProp.encoder = JSONEncoder()

if let data = try? postProp.encoded(), let encoded = String(data: data, encoding: .utf8) {
    print(encoded)
}
```

#### Dependency injection in methods (method injection)

``` Swift
// Method injection

class PostMeth: Encodable {
    var title: String
    var content: String
    
    init(title: String, content: String) {
        self.title = title
        self.content = content
    }
    
    func encode(using encoder: Encoder) throws -> Data {
        return try encoder.encode(self)
    }
}

let postMeth = PostMeth(title: "Hello DI!", content: "Method injection")

if let data = try? postMeth.encode(using: JSONEncoder()), let encoded = String(data: data, encoding: .utf8) {
    print(encoded)
}
```