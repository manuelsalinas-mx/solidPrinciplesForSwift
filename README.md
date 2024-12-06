# SOLID Principles for Swift
![1*OzwARbvHUg1RlZ7LYyLCrg](https://user-images.githubusercontent.com/110424672/182413972-a3cb4fb2-e32e-4f83-9073-76e74398d294.png)

## Content: 
- [English](#english)
- [Español](#spanish)

--- 
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
This principle states that any class should be able to be overridden by one of its subclasses without affecting its performance.

For example, suppose we have a class, `UserService`, which has the responsibility of contacting users (_for example, sending a mail_). If the business logic changes and, for example, mails can only be sent to users over 17 years old, we can create a subclass that adds the new business logic:

```
class UserService {
    func contact(user: User) {
        // Retrieve user from database
    }
}

class ValidUserService: Handler {
    override func contact(user: User) {
        guard user.age > 17 else { return }

        super.contact(user: User)
    }
}
```

In this case, the Liskov Substitution Principle is not fulfilled, since the subclass adds a condition (_that the user's age is greater than 17 years_), which a client of the `UserService` class would not expect. We can work around this problem by not creating the subclass, and adding to `UserService` the precondition (including a default value):
```
class UserService {
    func contact(user: User, minAge: Int = 0) {
        guard user.age > minAge else { return }
        // Retrieve user from database
    }
}
```



### Interface segregation principle
The Interface Segregation Principle states that it is better to have different interfaces (protocols) that are specific to each client than a general interface. Also, it indicates that a client should not have to implement methods that it doesn't use.

For example, we can create an interface for animals that includes scroll methods:
```
protocol AnimalProtocol {
    func walk()
    func swimm()
    func fly()
}

struct Animal: AnimalProtocol {
    func walk() {}
    func swimm() {}
    func fly() {}
}

struct Wale: AnimalProtocol {
    func swimm() {
        // Walw only needs to implement this function
        // All the other functions are irrelavant
    }
    
    func walk() {}
    
    func fly() {}
}
```

However, although **Wale** adopts the protocol, there are two methods that it does not implement. The solution is to set 3 interfaces (protocols), one per method:
```
protocol WalkProtocol {
    func walk()
}

protocol SwimmProtocol {
    func swimm()
}

protocol FlyProtocol {
    func fly()
}

struct Wale: SwimmProtocol {
    func swimm() {}
}

struct Crocodile: WalkProtocol, SwimmProtocol {
    func walk()
    func swimm() {}
}
```



### Dependency inversion principle
According to the Dependency Inversion Principle:
- High level classes shouldn't depend on low level classes. Both should depend on abstractions.
- Abstractions shouldn't depend on details. The details should depend on the abstractions.

The idea is to reduce dependencies between modules, and thus achieve less coupling between classes.

Let's look at the following example:
```
class User {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class CoreData {
    func save(user: User) {
        // Save user on database
    }
}

class UserService {
    func save(user: User) {
        let database = CoreData()
        database.save(user: user)
    }
}
```

What if instead of using **CoreData** to store the data, we want to use the **Realm** database? The fact of instantiating the class as we have done in the example generates a strong coupling, so if we want to use another database, we would have to redo the code.

To fix this, we can do what was explained in a previous article, when a database layer was set up.
``` 
protocol Storable { }

extension Object: Storable { } // Realm Database

extension NSManagedObject: Storable { } // Core Data Database

protocol StorageManager {
  /// Save Object into Realm database
  /// - Parameter object: Realm object (as Storable)
  func save(object: Storable)
}
```

Now we make the **User** adopt the `Storable` protocol and the **UserService** class adopt the `StorageManager` protocol, so that even if we change the database, we don't need to change all the implementation code:
```
class User: Storable {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class UserService: StorageManager {
    func save(object: Storable) {
        // Saves user to database
    }
}
```




---
## SPANISH

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
Este principio establece que cualquier clase debería poder ser reemplazado por una de sus subclases sin que se vea afectado su funcionamiento.

Por ejemplo, supongamos que tenemos una clase, `UserService`, que tiene la responsabilidad de contactar con los usuarios (_por ejemplo, enviar un mail_). Si cambia la lógica de negocio y, por ejemplo, solo se pueden enviar mails a usuarios de más de 17 años, podemos crear una subclase que añada la nueva lógica de negocio:

```
class UserService {
    func contact(user: User) {
        // Retrieve user from database
    }
}

class ValidUserService: Handler {
    override func contact(user: User) {
        guard user.age > 17 else { return }

        super.contact(user: User)
    }
}
```

En este caso no se cumple el Principio de sustitución de Liskov, ya que la subclase añade una condición (_que la edad del usuario sea mayor de 17 años_), que no esperaría un cliente de la clase `UserService`. Podemos solucionar este problema no creando la subclase, y añadiendo a `UserService` la precondición (incluyendo un valor por defecto):
```
class UserService {
    func contact(user: User, minAge: Int = 0) {
        guard user.age > minAge else { return }
        // Retrieve user from database
    }
}
```



### Principio de segregación de la interfaz
El Principio de segregación de la interfaz indica que es mejor tener diferentes interfaces (protocolos) que sean específicos para cada cliente, que tener un interfaz general. Además, indica que un cliente no tendría que implementar métodos que no utilice.

Por ejemplo, podemos crear una interfaz para animales que incluya métodos de desplazamiento:
```
protocol AnimalProtocol {
    func walk()
    func swimm()
    func fly()
}

struct Animal: AnimalProtocol {
    func walk() {}
    func swimm() {}
    func fly() {}
}

struct Wale: AnimalProtocol {
    func swimm() {
        // Walw only needs to implement this function
        // All the other functions are irrelavant
    }
    
    func walk() {}
    
    func fly() {}
}
```

Sin embargo, aunque **Wale** adopta el protocolo, hay dos métodos que no implementa. La solución es establecer 3 interfaces (protocolos), una por método:
```
protocol WalkProtocol {
    func walk()
}

protocol SwimmProtocol {
    func swimm()
}

protocol FlyProtocol {
    func fly()
}

struct Wale: SwimmProtocol {
    func swimm() {}
}

struct Crocodile: WalkProtocol, SwimmProtocol {
    func walk()
    func swimm() {}
}
```



### Principio de inversión de la dependencia
Según el Principio de inversión de la dependencia:
- Las clases de alto nivel no deberían depender de la clases de bajo nivel. Ambas deberían depender de abstracciones.
- Las abstracciones no deberían depender de los detalles. Los detalles deberían depender de las abstracciones.

Lo que se busca es reducir las dependencias entre módulos, y así alcanzar un menor acoplamiento entre clases.

Observemos el siguiente ejemplo:
```
class User {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class CoreData {
    func save(user: User) {
        // Save user on database
    }
}

class UserService {
    func save(user: User) {
        let database = CoreData()
        database.save(user: user)
    }
}
```

¿Qué ocurre si en vez de utilizar **CoreData** para guardar los datos, queremos utilizar la base de datos **Realm**? El hecho de instanciar la clase como hemos hecho en el ejemplo genera un fuerte acoplamiento, por lo que si queremos utilizar otra base de datos, habría que rehacer el código.

Para solucionar esto, podemos hacer lo que se explicó en un artículo anterior, cuando se estableció una capa de base de datos.
``` 
protocol Storable { }

extension Object: Storable { } // Realm Database

extension NSManagedObject: Storable { } // Core Data Database

protocol StorageManager {
  /// Save Object into Realm database
  /// - Parameter object: Realm object (as Storable)
  func save(object: Storable)
}
```

Ahora hacemos que **User** adopte el protocolo `Storable` y que la clase **UserService** adopte el protocolo `StorageManager`, de forma que aunque cambiemos la base de datos, no necesitaremos cambiar todo el código de implementación:
```
class User: Storable {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class UserService: StorageManager {
    func save(object: Storable) {
        // Saves user to database
    }
}
```


