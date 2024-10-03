# Principios SOLID

Los principios SOLID son un conjunto de buenas prácticas en el diseño de software orientado a objetos que buscan mejorar la calidad, flexibilidad y mantenibilidad del código. Son una guía para estructurar sistemas de manera que sean más fáciles de entender, extender y modificar con el tiempo. Los principios son:

1. **Single Responsibility Principle (SRP)**: Cada clase debe tener una única responsabilidad.
2. **Open/Closed Principle (OCP)**: El código debe estar abierto a la extensión, pero cerrado a la modificación.
3. **Liskov Substitution Principle (LSP)**: Los objetos de una subclase deben ser reemplazables por objetos de su superclase sin alterar la funcionalidad.
4. **Interface Segregation Principle (ISP)**: Los clientes no deben estar forzados a depender de interfaces que no utilizan.
5. **Dependency Inversion Principle (DIP)**: Los módulos de alto nivel no deben depender de módulos de bajo nivel, ambos deben depender de abstracciones.

Estos principios son la base para crear sistemas modulares y escalables. Veamos en detalle cada uno de ellos:

## 1. **Principio de Responsabilidad Única (Single Responsibility Principle - SRP)**

El principio de responsabilidad única establece que una clase debe tener una única razón para cambiar, es decir, una sola responsabilidad o propósito. Este principio busca evitar que una clase tenga múltiples motivos para cambiar, lo cual dificulta el mantenimiento y la comprensión del código.

**Objetivo del SRP:**

El SRP tiene como objetivo mantener las clases enfocadas en una única tarea o aspecto del sistema. Al limitar el alcance de las responsabilidades, el código se vuelve más modular, fácil de mantener y probar. Si una clase tiene más de una responsabilidad, los cambios en una parte del código pueden afectar otras partes de la clase, lo que puede generar errores inesperados.

**Ejemplo detallado en TypeScript**:

Supongamos que tienes una clase que maneja tanto los datos de un usuario como la lógica de persistencia de datos.

```typescript
class User {
  constructor(public name: string, public email: string) {}

  saveUser() {
    console.log(`Guardando usuario: ${this.name}`);
  }
}
```

En este ejemplo, la clase `User` tiene dos responsabilidades:
1. Manejar la representación de los datos del usuario (`name`, `email`).
2. Manejar la lógica de persistencia con el método `saveUser()`.

Esto viola el SRP porque si cambiamos la manera de guardar los datos, tendríamos que modificar la clase `User`, que debería estar enfocada solo en representar los datos del usuario. Este enfoque puede causar problemas a largo plazo, ya que las clases con múltiples responsabilidades tienden a crecer y volverse más complejas con el tiempo.

**Aplicación correcta del SRP:**

Separar las responsabilidades en clases diferentes:

```typescript
class User {
  constructor(public name: string, public email: string) {
    // Solo maneja datos de usuario
  }
}

class UserService {
  saveUser(user: User) {
    console.log(`Guardando usuario: ${user.name}`);
  }
}
```

Ahora, tenemos dos clases con responsabilidades claras:
- `User` solo maneja los datos del usuario.
- `UserService` maneja la lógica de persistencia (guardar el usuario).

Con este diseño, si necesitamos cambiar la manera en que los datos se guardan (por ejemplo, cambiar de una base de datos a otra), solo tendríamos que modificar `UserService`, mientras que la clase `User` seguiría sin cambios.

**Beneficios de aplicar el SRP:**
1. **Modularidad**: Cada clase tiene un propósito claro, lo que facilita el mantenimiento y la expansión del código.
2. **Pruebas**: Las clases con una única responsabilidad son más fáciles de probar, ya que sus funciones están limitadas a un solo propósito.
3. **Mantenibilidad**: Los cambios en una parte del sistema no afectan otras partes no relacionadas, lo que reduce el riesgo de introducir errores.
4. **Reusabilidad**: Las clases que tienen una única responsabilidad son más fáciles de reutilizar en otros contextos.

**Otro ejemplo para resaltar su importancia:**

Imaginemos que estamos desarrollando una aplicación que envía notificaciones por correo electrónico. Si tenemos una clase que genera el contenido del correo y también lo envía, estaríamos mezclando responsabilidades:

```typescript
class EmailNotification {
  constructor(public recipient: string, public message: string) {}

  sendEmail() {
    console.log(`Enviando correo a: ${this.recipient}, con el mensaje: ${this.message}`);
  }
}
```

Esto viola el SRP porque la clase maneja tanto la generación del contenido del correo como el envío. Si quisiéramos cambiar la forma en que se envían los correos (por ejemplo, cambiar de SMTP a un servicio de terceros), tendríamos que modificar la misma clase.

Para cumplir con el SRP, podemos refactorizar de la siguiente manera:

```typescript
class EmailMessage {
  constructor(public recipient: string, public message: string) {
    // Solo contiene los datos del mensaje
  }
}

class EmailSender {
  sendEmail(email: EmailMessage) {
    console.log(`Enviando correo a: ${email.recipient}, con el mensaje: ${email.message}`);
  }
}
```

Ahora, `EmailMessage` se encarga únicamente de los datos, mientras que `EmailSender` es responsable del envío. Si el proceso de envío cambia, solo tenemos que modificar `EmailSender` sin tocar la estructura de los mensajes.

**Conclusión**:
El SRP fomenta un diseño de código más limpio y fácil de mantener al asegurarse de que las clases no acumulen responsabilidades innecesarias. Al dividir las responsabilidades, el código se vuelve más flexible ante los cambios y es más fácil de extender y mantener.

## 2. **Principio Abierto/Cerrado (Open/Closed Principle - OCP)**

El principio abierto/cerrado establece que una entidad de software (clase, módulo, función, etc.) debe estar **abierta para extensión** pero **cerrada para modificación**. En otras palabras, deberíamos poder agregar nueva funcionalidad a una clase sin tener que modificar su código existente.

Este principio promueve el uso de la herencia y la composición, ya que permiten extender el comportamiento de una clase sin cambiar su estructura interna. El objetivo es evitar modificar código ya escrito, lo que minimiza el riesgo de introducir nuevos errores en un sistema que ya funciona correctamente.

**Objetivo del OCP:**
El objetivo del OCP es mantener el código existente estable, sin necesidad de modificarlo cuando se requieran nuevas características. En lugar de modificar las clases, se pueden extender a través de la creación de nuevas clases o mediante el uso de interfaces y polimorfismo.

**Ejemplo detallado en TypeScript:**

Supongamos que tenemos una clase para calcular el área de un rectángulo.

```typescript
class Rectangle {
  constructor(public width: number, public height: number) {}

  area(): number {
    return this.width * this.height;
  }
}
```

Este código está bien si solo necesitamos manejar rectángulos. Sin embargo, si en algún momento queremos agregar nuevas formas (por ejemplo, un círculo), podríamos sentir la tentación de modificar la clase para incluir una lógica adicional:

```typescript
class Shape {
  area(): number {
    throw new Error('Este método debe ser implementado por subclases');
  }
}

class Rectangle extends Shape {
  constructor(public width: number, public height: number) {
    super();
  }

  area(): number {
    return this.width * this.height;
  }
}

class Circle extends Shape {
  constructor(public radius: number) {
    super();
  }

  area(): number {
    return Math.PI * this.radius * this.radius;
  }
}

function printArea(shape: Shape) {
  console.log(shape.area());
}

const rect = new Rectangle(5, 10);
const circ = new Circle(5);

printArea(rect);
printArea(circ);
```

Ahora tenemos un diseño mucho más flexible que está **abierto para extensión**: podemos agregar tantas formas como necesitemos (triángulos, polígonos, etc.) simplemente creando nuevas subclases de `Shape` sin tener que modificar el código de `Rectangle` o de cualquier otra forma existente.

**Aplicando el OCP con Interfaces (Mejor enfoque en TypeScript):**

Si bien el uso de clases abstractas y herencia es una forma válida de aplicar el OCP, en TypeScript también es común usar **interfaces** para definir contratos que pueden ser implementados por varias clases. Esto favorece aún más la extensión del comportamiento sin modificar código existente.

```typescript
interface Shape {
  area(): number;
}

class Rectangle implements Shape {
  constructor(public width: number, public height: number) {}

  area(): number {
    return this.width * this.height;
  }
}

class Circle implements Shape {
  constructor(public radius: number) {}

  area(): number {
    return Math.PI * this.radius * this.radius;
  }
}

function printArea(shape: Shape) {
  console.log(`Área: ${shape.area()}`);
}
```

Aquí usamos la interfaz `Shape`, y las clases `Rectangle` y `Circle` la implementan. Si más adelante necesitamos una nueva forma (por ejemplo, un `Triangle`), podemos agregarla sin modificar el código existente:

```typescript
class Triangle implements Shape {
  constructor(public base: number, public height: number) {}

  area(): number {
    return 0.5 * this.base * this.height;
  }
}

const triangle = new Triangle(10, 5);
printArea(triangle);
```

De esta manera, la función `printArea()` no necesita ser modificada, ya que todas las nuevas clases implementan la misma interfaz.

**Beneficios del OCP:**
1. **Mantenibilidad**: Al no modificar el código existente, se minimizan los errores inesperados y se facilita el mantenimiento del sistema.
2. **Extensibilidad**: El código es más fácil de extender, lo que permite añadir nuevas características sin necesidad de cambiar lo que ya funciona.
3. **Polimorfismo**: Utilizando interfaces o clases abstractas, se puede aprovechar el polimorfismo para tratar diferentes tipos de objetos de manera uniforme.

**Ejemplo en un sistema real:**

Imagina que tienes un sistema de pago. Al principio, solo soportas pagos con tarjeta de crédito, pero luego se agrega la funcionalidad para pagar con PayPal. Con el OCP, en lugar de modificar el código existente para agregar PayPal, puedes extender el sistema:

```typescript
interface PaymentMethod {
  pay(amount: number): void;
}

class CreditCardPayment implements PaymentMethod {
  pay(amount: number) {
    console.log(`Pagando ${amount} con tarjeta de crédito.`);
  }
}

class PayPalPayment implements PaymentMethod {
  pay(amount: number) {
    console.log(`Pagando ${amount} con PayPal.`);
  }
}

class PaymentProcessor {
  process(paymentMethod: PaymentMethod, amount: number) {
    paymentMethod.pay(amount);
  }
}

const creditCard = new CreditCardPayment();
const payPal = new PayPalPayment();

const processor = new PaymentProcessor();

processor.process(creditCard, 100);
processor.process(payPal, 200);
```

Si más adelante necesitas agregar una nueva forma de pago, como criptomonedas, puedes simplemente crear una nueva clase que implemente la interfaz `PaymentMethod`, sin modificar las clases `CreditCardPayment`, `PayPalPayment` o `PaymentProcessor`.

**Conclusión:**

El principio abierto/cerrado es crucial para diseñar sistemas extensibles y robustos. En lugar de modificar código ya existente (y potencialmente introducir errores), el OCP nos guía a crear un sistema que permite agregar nueva funcionalidad sin afectar lo que ya está en uso.

## 3. **Principio de Sustitución de Liskov (Liskov Substitution Principle - LSP)**

El principio de sustitución de Liskov establece que los objetos de una clase derivada deben poder ser reemplazados por objetos de la clase base sin alterar el comportamiento correcto del programa. En otras palabras, una subclase debe poder usarse donde se espera una clase base, y esto no debe romper la funcionalidad del sistema.

Este principio está estrechamente relacionado con el polimorfismo y garantiza que las subclases mantengan las expectativas de comportamiento de la clase base. Si una subclase modifica significativamente el comportamiento, entonces viola el principio de sustitución de Liskov, lo que podría resultar en errores y comportamientos inesperados.

**Objetivo del LSP:**
El objetivo del LSP es asegurar que el diseño de clases siga un contrato predefinido, es decir, que las subclases no cambien el comportamiento esperado de la clase base. El incumplimiento de este principio puede llevar a un código que es difícil de entender, probar y mantener.

**Ejemplo detallado en TypeScript:**

Imaginemos que tenemos una clase `Bird` (ave) con un método `fly()` que permite a las aves volar.

```typescript
class Bird {
  fly() {
    console.log("Volando...");
  }
}

class Eagle extends Bird {
  fly() {
    console.log("El águila está volando alto...");
  }
}
```

Hasta aquí, todo parece funcionar bien. Ahora supongamos que queremos agregar una subclase `Penguin`, pero los pingüinos no pueden volar. Si intentamos modificar el método `fly()` en `Penguin` para lanzar un error, violamos el principio de sustitución de Liskov:

```typescript
class Penguin extends Bird {
  fly() {
    throw new Error("¡Los pingüinos no pueden volar!");
  }
}

function letBirdFly(bird: Bird) {
  bird.fly();
}

const eagle = new Eagle();
const penguin = new Penguin();

letBirdFly(eagle);   // Funciona bien.
letBirdFly(penguin); // Rompe el programa.
```

El problema aquí es que `Penguin` no puede sustituir correctamente a `Bird`. Al ser una subclase, debería ser posible utilizar a `Penguin` en cualquier lugar donde se espere un `Bird`, pero en este caso, la funcionalidad `fly()` no cumple con la expectativa.

**Solución respetando el LSP:**

Para solucionar este problema, podemos refactorizar el diseño para que las clases que heredan de `Bird` sigan el comportamiento esperado. En este caso, creamos una nueva jerarquía que defina qué aves pueden volar y cuáles no.

```typescript
class Bird {
  // Clase base para todas las aves
}

class FlyingBird extends Bird {
  fly() {
    console.log("Volando...");
  }
}

class Eagle extends FlyingBird {
  fly() {
    console.log("El águila está volando alto...");
  }
}

class Penguin extends Bird {
  // Los pingüinos no vuelan, por lo que no implementan fly()
  swim() {
    console.log("El pingüino está nadando...");
  }
}

function letFlyingBirdFly(bird: FlyingBird) {
  bird.fly();
}

const eagle = new Eagle();
const penguin = new Penguin();

letFlyingBirdFly(eagle);   // Funciona bien
// letFlyingBirdFly(penguin); // Ahora no tiene sentido y no romperá el programa
```

Con este diseño, respetamos el principio de sustitución de Liskov. `Penguin` ya no intenta "volar", y solo los objetos de `FlyingBird` pueden ser pasados a la función `letFlyingBirdFly()`. Esto asegura que cualquier instancia de una subclase de `FlyingBird` pueda reemplazar a `FlyingBird` sin romper la funcionalidad.

**Aplicación del LSP en la vida real:**

Un ejemplo más realista podría ser un sistema de manejo de cuentas bancarias, donde hay cuentas de ahorro y cuentas de inversión. Imaginemos que tenemos una clase base `BankAccount` con un método `withdraw()` para retirar fondos:

```typescript
class BankAccount {
  balance: number;

  constructor(initialBalance: number) {
    this.balance = initialBalance;
  }

  withdraw(amount: number) {
    if (amount <= this.balance) {
      this.balance -= amount;
      console.log(`Retirados ${amount}, saldo restante: ${this.balance}`);
    } else {
      throw new Error("Saldo insuficiente");
    }
  }
}

class SavingsAccount extends BankAccount {
  // No tiene restricciones adicionales
}

class InvestmentAccount extends BankAccount {
  // Tiene una restricción de que no se puede retirar hasta después de 1 año.
  withdraw(amount: number) {
    throw new Error("No se puede retirar de la cuenta de inversión antes de 1 año.");
  }
}

function withdrawFromAccount(account: BankAccount, amount: number) {
  account.withdraw(amount);
}

const savings = new SavingsAccount(1000);
const investment = new InvestmentAccount(5000);

withdrawFromAccount(savings, 200); // Funciona bien
withdrawFromAccount(investment, 100); // Rompe el programa
```

Aquí, `InvestmentAccount` viola el principio de sustitución de Liskov porque altera el comportamiento esperado de la clase base `BankAccount`. Un cliente que espera poder retirar dinero de cualquier `BankAccount` se sorprendería si no puede hacerlo con `InvestmentAccount`.

**Solución respetando el LSP:**

Podemos refactorizar este código para que solo las cuentas que permiten retiros hereden el método `withdraw()`:

```typescript
class BankAccount {
  balance: number;

  constructor(initialBalance: number) {
    this.balance = initialBalance;
  }
}

class WithdrawableAccount extends BankAccount {
  withdraw(amount: number) {
    if (amount <= this.balance) {
      this.balance -= amount;
      console.log(`Retirados ${amount}, saldo restante: ${this.balance}`);
    } else {
      throw new Error("Saldo insuficiente");
    }
  }
}

class SavingsAccount extends WithdrawableAccount {
  // Sin restricciones adicionales
}

class InvestmentAccount extends BankAccount {
  // No tiene método withdraw
}

function withdrawFromAccount(account: WithdrawableAccount, amount: number) {
  account.withdraw(amount);
}

const savings = new SavingsAccount(1000);
const investment = new InvestmentAccount(5000);

withdrawFromAccount(savings, 200); // Funciona bien
// withdrawFromAccount(investment, 100); // No tiene sentido ahora
```

Ahora, solo las cuentas que permiten retiros (herederas de `WithdrawableAccount`) implementan el método `withdraw()`, lo que respeta el LSP.

**Beneficios del LSP:**
1. **Polimorfismo sin sorpresas**: Permite el uso de subclases sin alterar el comportamiento esperado de la clase base.
2. **Mayor coherencia**: Asegura que todas las subclases mantengan un comportamiento coherente y evitan casos inesperados que rompan el sistema.
3. **Facilidad de mantenimiento**: Al garantizar que las subclases respeten el contrato de la clase base, se facilita la comprensión y el mantenimiento del código.

**Conclusión:**

El principio de sustitución de Liskov es fundamental para asegurar que las jerarquías de clases mantengan comportamientos consistentes. Al respetar este principio, creamos sistemas más predecibles, seguros y fáciles de extender.

## 4. **Principio de Segregación de Interfaces (Interface Segregation Principle - ISP)**

El principio de segregación de interfaces establece que **ningún cliente** (clase o módulo) debería verse forzado a depender de interfaces que no utiliza. En lugar de tener una interfaz "gorda" con muchos métodos, es mejor dividirlas en varias interfaces más pequeñas y específicas. Esto permite que las clases implementen solo los métodos que realmente necesitan, evitando la necesidad de implementar métodos inútiles o irrelevantes.

El ISP ayuda a reducir el acoplamiento entre las clases y promueve un diseño más modular y flexible. Las clases que implementan una interfaz no deben verse obligadas a tener dependencias que no utilizan.

**Objetivo del ISP:**
El objetivo principal es evitar que las clases implementen métodos innecesarios o que no tienen sentido en su contexto. Al aplicar el ISP, se logra un diseño más flexible y fácil de mantener, ya que las clases solo dependen de aquello que realmente necesitan.

**Ejemplo detallado en TypeScript:**

Supongamos que estamos diseñando un sistema para representar diferentes tipos de máquinas. Tenemos una interfaz `Machine` con métodos para arrancar, detener y reiniciar la máquina.

```typescript
interface Machine {
  start(): void;
  stop(): void;
  restart(): void;
}
```

Ahora, tenemos diferentes clases de máquinas que deben implementar esta interfaz:

```typescript
class Printer implements Machine {
  start() {
    console.log("La impresora ha comenzado a funcionar.");
  }

  stop() {
    console.log("La impresora se ha detenido.");
  }

  restart() {
    console.log("Reiniciando la impresora...");
  }
}

class CoffeeMachine implements Machine {
  start() {
    console.log("La cafetera está encendida.");
  }

  stop() {
    console.log("La cafetera está apagada.");
  }

  restart() {
    throw new Error("Una cafetera no puede reiniciarse.");
  }
}
```

El problema aquí es que `CoffeeMachine` está implementando un método `restart()` que no tiene sentido para ella, lo cual viola el ISP. Estamos obligando a `CoffeeMachine` a implementar un método que no debería tener.

**Solución aplicando el ISP:**

En lugar de una interfaz "gorda" como `Machine`, podemos dividirla en varias interfaces más pequeñas y específicas para que cada clase implemente solo los métodos que necesita:

```typescript
interface Startable {
  start(): void;
}

interface Stoppable {
  stop(): void;
}

interface Restartable {
  restart(): void;
}
```

Ahora, cada clase puede implementar solo las interfaces que realmente necesita:

```typescript
class Printer implements Startable, Stoppable, Restartable {
  start() {
    console.log("La impresora ha comenzado a funcionar.");
  }

  stop() {
    console.log("La impresora se ha detenido.");
  }

  restart() {
    console.log("Reiniciando la impresora...");
  }
}

class CoffeeMachine implements Startable, Stoppable {
  start() {
    console.log("La cafetera está encendida.");
  }

  stop() {
    console.log("La cafetera está apagada.");
  }
}
```

Con esta estructura, `CoffeeMachine` no necesita implementar un método de reinicio que no tiene sentido para ella, mientras que `Printer` puede implementar todos los métodos que le corresponden. Este diseño es más limpio y flexible.

**Otro ejemplo más realista:**

Supongamos que estamos desarrollando un sistema para manejar diferentes tipos de usuarios en una aplicación (administradores y usuarios regulares). Una interfaz "gorda" podría verse así:

```typescript
interface User {
  login(): void;
  logout(): void;
  managePermissions(): void;
}
```

Este diseño obliga a todos los tipos de usuarios a implementar un método `managePermissions()`, que quizás solo es relevante para los administradores.

Una solución más adecuada sería separar estas responsabilidades en interfaces más específicas:

```typescript
interface Authenticable {
  login(): void;
  logout(): void;
}

interface PermissionManageable {
  managePermissions(): void;
}
```

De esta forma, podemos implementar las interfaces de manera específica según el tipo de usuario:

```typescript
class RegularUser implements Authenticable {
  login() {
    console.log("Usuario regular ha iniciado sesión.");
  }

  logout() {
    console.log("Usuario regular ha cerrado sesión.");
  }
}

class AdminUser implements Authenticable, PermissionManageable {
  login() {
    console.log("Administrador ha iniciado sesión.");
  }

  logout() {
    console.log("Administrador ha cerrado sesión.");
  }

  managePermissions() {
    console.log("El administrador está gestionando permisos.");
  }
}
```

Ahora, `RegularUser` no está obligado a implementar el método `managePermissions()`, ya que no tiene sentido para él. Este diseño sigue el principio de segregación de interfaces y es más modular.

**Beneficios del ISP:**
1. **Flexibilidad**: Permite que las clases sean más específicas en sus responsabilidades al implementar solo lo que necesitan, lo que facilita el cambio y la extensión del código.
2. **Desacoplamiento**: Al dividir las interfaces, se reduce el acoplamiento entre las clases, ya que dependen solo de lo que necesitan.
3. **Mantenibilidad**: Las clases son más fáciles de mantener y modificar, ya que no están cargadas con métodos innecesarios.
4. **Claridad en el diseño**: Las interfaces pequeñas y específicas mejoran la legibilidad del código, ya que reflejan mejor las necesidades reales de las clases.

**Conclusión:**

El principio de segregación de interfaces es esencial para evitar crear dependencias innecesarias en las clases. Al dividir las interfaces grandes en pequeñas y específicas, logramos un diseño más modular y limpio, lo que facilita el mantenimiento y la escalabilidad del sistema.

## 5. **Principio de Inversión de Dependencias (Dependency Inversion Principle - DIP)**

El principio de inversión de dependencias establece dos reglas fundamentales:
1. Los **módulos de alto nivel** no deben depender de los módulos de bajo nivel. Ambos deben depender de **abstracciones**.
2. Las **abstracciones** no deben depender de los detalles. Los **detalles** deben depender de las abstracciones.

Este principio se basa en la idea de que los módulos de mayor nivel, aquellos que representan la lógica de negocio, no deben estar directamente acoplados a los módulos de bajo nivel, como los que implementan detalles técnicos (acceso a bases de datos, API, etc.). En cambio, ambos deben depender de abstracciones (interfaces o clases abstractas), lo que permite que los detalles técnicos cambien sin afectar la lógica de negocio.

**Objetivo del DIP:**
El DIP busca desacoplar las diferentes partes de un sistema, especialmente la lógica de negocio de los detalles técnicos. Esto facilita la evolución del código, permitiendo reemplazar módulos de bajo nivel (por ejemplo, una base de datos o una API externa) sin afectar el funcionamiento global del sistema.

**Ejemplo básico en TypeScript (Violación del DIP):**

Supongamos que tienes una clase `UserService` que guarda la información de un usuario en una base de datos específica (por ejemplo, MySQL):

```typescript
class MySQLDatabase {
  saveData(data: string) {
    console.log(`Guardando en MySQL: ${data}`);
  }
}

class UserService {
  database: MySQLDatabase;

  constructor() {
    this.database = new MySQLDatabase();
  }

  saveUser(user: string) {
    this.database.saveData(user);
  }
}
```

En este ejemplo, `UserService` está directamente acoplado a `MySQLDatabase`, lo que significa que si queremos cambiar la base de datos (por ejemplo, a MongoDB), tendríamos que modificar la clase `UserService`. Esto viola el principio de inversión de dependencias porque el módulo de alto nivel (`UserService`) depende de un módulo de bajo nivel (`MySQLDatabase`).

**Solución aplicando el DIP:**

Para aplicar el DIP, necesitamos hacer que tanto `UserService` como `MySQLDatabase` dependan de una abstracción común, como una interfaz `Database`. De esta manera, `UserService` no necesita saber los detalles de la base de datos específica, solo que debe guardar datos en algo que implemente la interfaz `Database`.

```typescript
// Definimos la abstracción (interface) que ambos módulos usarán.
interface Database {
  saveData(data: string): void;
}

// Implementación concreta de MySQL que depende de la abstracción.
class MySQLDatabase implements Database {
  saveData(data: string) {
    console.log(`Guardando en MySQL: ${data}`);
  }
}

// Otra implementación concreta, por ejemplo, MongoDB.
class MongoDBDatabase implements Database {
  saveData(data: string) {
    console.log(`Guardando en MongoDB: ${data}`);
  }
}

// Ahora, UserService depende de la abstracción Database, no de un detalle concreto.
class UserService {
  database: Database;

  constructor(database: Database) {
    this.database = database;
  }

  saveUser(user: string) {
    this.database.saveData(user);
  }
}
```

Ahora, `UserService` depende de la abstracción `Database`, y podemos inyectar cualquier implementación de `Database` (MySQL, MongoDB, etc.) en el constructor de `UserService`.

```typescript
const mySQLDatabase = new MySQLDatabase();
const mongoDBDatabase = new MongoDBDatabase();

const userServiceMySQL = new UserService(mySQLDatabase);
userServiceMySQL.saveUser("Usuario en MySQL");

const userServiceMongoDB = new UserService(mongoDBDatabase);
userServiceMongoDB.saveUser("Usuario en MongoDB");
```

Este diseño sigue el DIP porque:
- **UserService** ya no depende de los detalles de implementación de una base de datos específica.
- Podemos cambiar la implementación de la base de datos sin modificar la lógica de negocio en `UserService`.

**Uso del DIP con la inyección de dependencias:**

El DIP generalmente se implementa mediante **inyección de dependencias**, que es una técnica en la que las dependencias (como bases de datos o servicios) se inyectan a las clases a través de constructores o setters, en lugar de ser instanciadas directamente dentro de las clases.

Esto permite que el módulo de alto nivel (como `UserService`) dependa de abstracciones (interfaces), mientras que los módulos de bajo nivel (como `MySQLDatabase` o `MongoDBDatabase`) se pasan como parámetros, lo que sigue el principio de inversión de dependencias.

Por ejemplo, usando inyección de dependencias en TypeScript:

```typescript
class Application {
  static main() {
    const database: Database = new MongoDBDatabase();  // Inyectamos la dependencia
    const userService = new UserService(database);     // UserService no necesita conocer los detalles
    userService.saveUser("Usuario en MongoDB");
  }
}

Application.main();
```

**Ejemplo más avanzado: Sistema de notificaciones**

Imaginemos que estamos desarrollando un sistema de notificaciones que puede enviar correos electrónicos o mensajes de texto (SMS). Si no aplicamos el DIP, podríamos tener algo como esto:

```typescript
class EmailSender {
  send(message: string) {
    console.log(`Enviando correo: ${message}`);
  }
}

class NotificationService {
  emailSender: EmailSender;

  constructor() {
    this.emailSender = new EmailSender();
  }

  sendNotification(message: string) {
    this.emailSender.send(message);
  }
}
```

Aquí, `NotificationService` está acoplado a `EmailSender`, lo que significa que si queremos enviar notificaciones a través de SMS, tenemos que modificar `NotificationService`.

Aplicando el DIP, primero definimos una abstracción `MessageSender` y hacemos que tanto `EmailSender` como `SmsSender` dependan de esa abstracción:

```typescript
interface MessageSender {
  send(message: string): void;
}

class EmailSender implements MessageSender {
  send(message: string) {
    console.log(`Enviando correo: ${message}`);
  }
}

class SmsSender implements MessageSender {
  send(message: string) {
    console.log(`Enviando SMS: ${message}`);
  }
}

class NotificationService {
  sender: MessageSender;

  constructor(sender: MessageSender) {
    this.sender = sender;
  }

  sendNotification(message: string) {
    this.sender.send(message);
  }
}
```

Ahora, podemos usar `NotificationService` con cualquier implementación de `MessageSender` (correo electrónico o SMS) sin modificar la lógica de negocio.

```typescript
const emailSender = new EmailSender();
const smsSender = new SmsSender();

const notificationServiceEmail = new NotificationService(emailSender);
notificationServiceEmail.sendNotification("Notificación por correo");

const notificationServiceSms = new NotificationService(smsSender);
notificationServiceSms.sendNotification("Notificación por SMS");
```

**Beneficios del DIP:**
1. **Desacoplamiento**: Los módulos de alto nivel no dependen de los detalles de implementación, lo que permite cambiar esos detalles sin afectar la lógica de negocio.
2. **Flexibilidad**: Es fácil intercambiar implementaciones de bajo nivel (por ejemplo, cambiar de MySQL a MongoDB) sin modificar el código que depende de ellas.
3. **Reutilización**: Al depender de abstracciones, las clases son más reutilizables, ya que no están atadas a implementaciones específicas.
4. **Mantenimiento**: Los cambios en los módulos de bajo nivel no afectan la lógica de los módulos de alto nivel, lo que facilita el mantenimiento del código.

**Conclusión:**

El principio de inversión de dependencias es clave para desacoplar los módulos de un sistema y permitir que las dependencias se manejen de manera flexible y extensible. Al depender de abstracciones y no de implementaciones concretas, el código se vuelve más fácil de mantener, escalar y adaptar a cambios futuros.