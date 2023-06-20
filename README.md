# Test Task

### Задание
В файле задекларирована структура `Product` и протокол `Shop`

```swift
struct Product {
    var id: String; // unique identifier
    var name: String;
    let producer: String;
}
```
```swift
protocol Shop {
        /**
     Adds a new product object to the Shop.
     - Parameter product: product to add to the Shop
     - Returns: false if the product with same id already exists in the Shop, true – otherwise.
     */
    func addNewProduct(product: Product) -> Bool
    
    /**
     Deletes the product with the specified id from the Shop.
     - Returns: true if the product with same id existed in the Shop, false – otherwise.
     */
    func deleteProduct(id: String) -> Bool
    
    /**
     - Returns: 10 product names containing the specified string.
     If there are several products with the same name, producer's name is added to product's name in the format "<producer> - <product>",
     otherwise returns simply "<product>".
     */
    func listProductsByName(searchString: String) -> Set<String>
    
    /**
     - Returns: 10 product names whose producer contains the specified string,
     result is ordered by producers.
     */
    func listProductsByProducer(searchString: String) -> [String]
}
```

Необходимо написать класс, который реализует протокол Shop согласно спецификации, используя SDK. **Отладка кода** осуществляется посредством утверждений `(assert)`

### Решение
```swift
class ShopImpl: Shop {
    
    private var shop: [String: Product] = [:]
    
    func addNewProduct(product: Product) -> Bool {
        if shop[product.id] != nil {
            return false
        }
        shop[product.id] = product
        return true
    }
    
    func deleteProduct(id: String) -> Bool {
        if shop[id] == nil {
            return false
        }
        shop[id] = nil
        return true
    }
    
    func listProductsByName(searchString: String) -> Set<String> {
        let products = shop.values.filter { $0.name.contains(searchString) }
        var result = Set<String>()
        
        for item in products {
            if let duplicate = products.first(where: { $0.name == item.name && $0.id != item.id }) {
                result.insert("\(item.producer) - \(item.name)")
            } else {
                result.insert(item.name)
            }
        }
        
        return Set(result.prefix(10))
    }
    
    func listProductsByProducer(searchString: String) -> [String] {
        let products = shop.values.filter { $0.producer.contains(searchString) }.sorted { $0.producer > $1.producer }
        let result = products.map { $0.name }.prefix(10).map { String($0) }
        
        return result
    }
}
```

*Файл с полным кодом находится в репозитории*
