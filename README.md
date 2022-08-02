# SOLID Principles for Swift
![1*OzwARbvHUg1RlZ7LYyLCrg](https://user-images.githubusercontent.com/110424672/182413972-a3cb4fb2-e32e-4f83-9073-76e74398d294.png)

## ENGLISH

**Advantages of applying these 5 principles:**
- We'll have flexible code, which we can easily change and which will be both reusable and maintainable.
- The developed software will be robust, stable and scalable where we can easily add new features.
- Dependency between elements would be low.

**The acronym SOLID comes from:**

- **S** (SRP): Single responsibility principle
- **O** (OCP): Open/closed principle
- **L** (LSP): Liskov substitution principle
- **I** (ISP): Interface segregation principle
- **D** (DIP): Dependency inversion principle

### Single responsibility principle
According to this principle, a class should have only one reason to change. I mean, a class should only have one responsibility.
Example:

```
class LoginUser {
    func login() {
        let data = authenticateUserViaAPI()
        let user = decodeUser(data: data)
        saveToDB(array: array)
    }
    
    private func authenticateUserViaAPI() -> Data {
        // Call server to authenticate and return user's info
    }
    
    private func decodeUser(data: Data) -> User {
        // Decode data (Codable protocol) into User object
    }
    
    private func saveUserInfoOnDatabase(user: User) {
        // Save User info onto database
    }
}
```

This class has 3 responsibilities: 
1. Authentification
2. Decode info
3. Save info. 

To apply the single responsibility principle, we must extract each of these responsibilities into other smaller classes.
Example:

```
class LoginUser {
    let oAuthHelper: OAuthHelper
    let decodeHelper: DecodeHelper
    let databaseHelper: DataBaseHelper
    
    init(oAuthHelper: OAuthHelper, decodeHelper: DecodeHelper, databaseHelper: DataBaseHelper) {
        self.oAuthHelper = oAuthHelper
        self.decodeHelper = decodeHelper
        self.databaseHelper = databaseHelper
    }
    
    func login() {
        let data = oAuthHelper.authenticareUserViaAPI()
        let user = decodeHelper.decodeUser(data: data)
        databaseHelper.saveUserInfoOnDatabase(user: user)
    }
}

// MARK: - HELPERS
class OAuthHelper {
    func authenticateUserViaAPI() -> Data {
        // Call server to authenticate and return user's info
    }
}

class DecodeHelper {
    func decodeUser(data: Data) -> User {
        // Decode data (Codable protocol) into User object
    }
}

class DataBaseHelper {
    func saveUserInfoOnDatabase(user: User) {
        // Save User info onto database
    }
}
```


### Open/closed principle
According to this principle, we should be able to extend a class without changing its behavior. This is achieved by abstraction.

```
class Scrapper {

    func scrapVehicles() {
        let cars = [
            Car(brand: "Ford"),
            Car(brand: "Peugeot"),
            Car(brand: "Toyota"),
        ]

        cars.forEach { car in
            print(car.getScrappingAddress())
        }

        let trucks = [
            Truck(brand: "Volvo"),
            Truck(brand: "Nissan"),
        ]

        trucks.forEach { truck in
            print(truck.getScrappingAddress())
        }
    }
}

class Car {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrappingAddress() -> String {
        return "Cars scrapping address"
    }
}

class Truck {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrappingAddress() -> String {
        return "Trucks scrapping address"
    }
}
```

For each new vehicle type, the `getScrapingAddress()` function must be implemented again, this breaks the open/close principle. To resolve this point, we introduce the **_Scrappable_** protocol that contains the `getScrappingAddress()` method:

```
protocol Scrappable {
    func getScrapingAddress() -> String
}

class Scrapper {

    func getScrapingAddress() {
        let vehicles: [Scrappable] = [
            Car(brand: "Ford"),
            Car(brand: "Peugeot"),
            Car(brand: "Toyota"),
            Truck(brand: "Volvo"),
            Truck(brand: "Nissan"),
        ]

        vehicles.forEach { vehicle in
            print(vehicle.getScrapingAddress())
        }
    }
}

class Car: Scrappable {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrapingAddress() -> String {
        return "Cars scrapping address"
    }
}

class Truck: Scrappable {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrapingAddress() -> String {
        return "Trucks scrapping address"
    }
}
```



### Liskov substitution principle
### Interface segregation principle
### Dependency inversion principle




---
## ESPAÑOL

**Ventajas de aplicar estos 5 principios:**
- Tendremos un código flexible, que podremos cambiar fácilmente y que sera tanto reusable como mantenible.
- El software desarrollado será robusto, estable y escalable donde podremos añadir nuevas funcionalidades fácilmente.
- El grado de dependencia entre elementos es bajo.


**El acrónimo SOLID proviene de:**

- **S** (SRP): Principio de responsabilidad única
- **O** (OCP): Principio de abierto/cerrado
- **L** (LSP): Principio de sustitución de Liskov
- **I** (ISP): Principio de segregación de la interfaz
- **D** (DIP): Principio de inversión de la dependencia

### Principio de responsabilidad única
Según este principio, una clase debería tener una razón, y solo una, para cambiar. Es decir, una clase solo debe tener una responsabilidad.
Ejemplo:

```
class LoginUser {
    func login() {
        let data = authenticateUserViaAPI()
        let user = decodeUser(data: data)
        saveToDB(array: array)
    }
    
    private func authenticateUserViaAPI() -> Data {
        // Call server to authenticate and return user's info
    }
    
    private func decodeUser(data: Data) -> User {
        // Decode data (Codable protocol) into User object
    }
    
    private func saveUserInfoOnDatabase(user: User) {
        // Save User info onto database
    }
}
```

Esta clase presenta 3 responsabilidades: 
1. Authentification
2. Decode info
3. Save info. 

Para cumplir el principio de responsabilidad única debemos extraer cada una de estas responsabilidades en otras clases más pequeñas. 
Ejemplo:

```
class LoginUser {
    let oAuthHelper: OAuthHelper
    let decodeHelper: DecodeHelper
    let databaseHelper: DataBaseHelper
    
    init(oAuthHelper: OAuthHelper, decodeHelper: DecodeHelper, databaseHelper: DataBaseHelper) {
        self.oAuthHelper = oAuthHelper
        self.decodeHelper = decodeHelper
        self.databaseHelper = databaseHelper
    }
    
    func login() {
        let data = oAuthHelper.authenticareUserViaAPI()
        let user = decodeHelper.decodeUser(data: data)
        databaseHelper.saveUserInfoOnDatabase(user: user)
    }
}

// MARK: - HELPERS
class OAuthHelper {
    func authenticateUserViaAPI() -> Data {
        // Call server to authenticate and return user's info
    }
}

class DecodeHelper {
    func decodeUser(data: Data) -> User {
        // Decode data (Codable protocol) into User object
    }
}

class DataBaseHelper {
    func saveUserInfoOnDatabase(user: User) {
        // Save User info onto database
    }
}
```


### Principio de abierto/cerrado
Según este principio, debemos poder extender una clase sin que cambie su comportamiento. Esto se consigue mediante abstracción.

```
class Scrapper {

    func scrapVehicles() {
        let cars = [
            Car(brand: "Ford"),
            Car(brand: "Peugeot"),
            Car(brand: "Toyota"),
        ]

        cars.forEach { car in
            print(car.getScrappingAddress())
        }

        let trucks = [
            Truck(brand: "Volvo"),
            Truck(brand: "Nissan"),
        ]

        trucks.forEach { truck in
            print(truck.getScrappingAddress())
        }
    }
}

class Car {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrappingAddress() -> String {
        return "Cars scrapping address"
    }
}

class Truck {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrappingAddress() -> String {
        return "Trucks scrapping address"
    }
}
```

Para cada tipo nuevo de vehículo hay que implementar de nuevo la función `getScrapingAddress()`, lo que rompe el principio de abierto/cerrado. Para resolver este punto, introducimos el protocolo **_Scrappable_** que contiene el método `getScrappingAddress()`:

```
protocol Scrappable {
    func getScrapingAddress() -> String
}

class Scrapper {

    func getScrapingAddress() {
        let vehicles: [Scrappable] = [
            Car(brand: "Ford"),
            Car(brand: "Peugeot"),
            Car(brand: "Toyota"),
            Truck(brand: "Volvo"),
            Truck(brand: "Nissan"),
        ]

        vehicles.forEach { vehicle in
            print(vehicle.getScrapingAddress())
        }
    }
}

class Car: Scrappable {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrapingAddress() -> String {
        return "Cars scrapping address"
    }
}

class Truck: Scrappable {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrapingAddress() -> String {
        return "Trucks scrapping address"
    }
}
```


### Principio de sustitución de Liskov
### Principio de segregación de la interfaz
### Principio de inversión de la dependencia





