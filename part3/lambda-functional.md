# Lambda Expressions e Interfaces Funcionais em Java

## Interfaces Funcionais

São interfaces que possuem apenas um método abstrato. Este método precisa ser implementado por classes que usam a interface.

### Exemplo Básico

```java
public interface Printer {
    void print(String message);
}

public class ConsolePrinter implements Printer {
    @Override
    public void print(String message) {
        System.out.println(message);
    }
}

// Uso
public static void main(String[] args) {
    greet(new ConsolePrinter());
}

public static void greet(Printer printer) {
    printer.print("Hello world");
}
```

## Anonymous Inner Class

Implementação direta da interface no ponto de uso:

```java
greet(new Printer() {
    @Override
    public void print(String message) {
        System.out.println(message);
    }
});
```

## Lambda Expressions

Formas simplificadas de implementar interfaces funcionais:

```java
// Forma completa
greet((String message) -> { 
    System.out.println(message); 
});

// Forma simplificada
greet(message -> System.out.println(message));

// Armazenando em variável
Printer printer = message -> System.out.println(message);
```

## Method Reference

Sintaxe mais concisa para lambdas que apenas chamam métodos existentes:

```java
// Referência a método estático
greet(Main::print);

// Referência a método de instância
var demo = new Main();
greet(demo::print);

// Referência a construtor
greet(Main::new);
```

## Interfaces Funcionais Comuns

### 1. Consumer<T>
Recebe um argumento e não retorna valor.

```java
// Forma imperativa
for (var item : list) {
    System.out.println(item);
}

// Forma declarativa
list.forEach(item -> System.out.println(item));

// Encadeamento
Consumer<String> print = item -> System.out.println(item);
Consumer<String> printUpper = item -> System.out.println(item.toUpperCase());
list.forEach(print.andThen(printUpper));
```

### 2. Supplier<T>
Fornece um valor sem receber argumentos.

```java
Supplier<Double> getRandom = () -> Math.random();
var random = getRandom.get();
```

### 3. Function<T,R>
Recebe um argumento e retorna um valor.

```java
Function<String, Integer> map = str -> str.length();
var length = map.apply("Sky");

// Composição
Function<String, String> replaceColon = str -> str.replace(":", "=");
Function<String, String> addBraces = str -> "{" + str + "}";
var result = replaceColon.andThen(addBraces).apply("key:value");
```

### 4. Predicate<T>
Testa uma condição e retorna boolean.

```java
Predicate<String> isLongerThan5 = str -> str.length() > 5;
var result = isLongerThan5.test("sky");

// Combinando predicates
Predicate<String> hasLeftBrace = str -> str.startsWith("{");
Predicate<String> hasRightBrace = str -> str.endsWith("}");
Predicate<String> braces = hasLeftBrace.and(hasRightBrace);
```

### 5. BinaryOperator<T>
Recebe dois argumentos do mesmo tipo e retorna o mesmo tipo.

```java
BinaryOperator<Integer> add = (a, b) -> a + b;
Function<Integer, Integer> square = a -> a * a;
var result = add.andThen(square).apply(1, 2); // 9
```

### 6. UnaryOperator<T>
Recebe um argumento e retorna valor do mesmo tipo.

```java
UnaryOperator<Integer> square = n -> n * n;
UnaryOperator<Integer> increment = n -> n + 1;
var result = increment.andThen(square).apply(1); // 4
```

## Variable Capture

> Lambdas podem acessar variáveis locais (efetivamente finais), campos estáticos e de instância da classe.

---

## java.util.function (guia rápido)

- `Consumer<T>`: recebe T, retorna void (`accept`)
- `Supplier<T>`: retorna T (`get`)
- `Function<T,R>`: T → R (`apply`), compose/andThen
- `Predicate<T>`: T → boolean (`test`), and/or/negate
- `UnaryOperator<T>`: T → T
- `BinaryOperator<T>`: (T,T) → T (com `minBy`/`maxBy`)

---

## Tratando exceções em lambdas

```java
Function<String, Integer> parse = s -> {
    try { return Integer.parseInt(s); }
    catch (NumberFormatException e) { return 0; }
};
```

---

## Comparators com lambdas

```java
Comparator<User> cmp = Comparator.comparing(User::getPoints)
                                 .thenComparing(User::getName);
```

---

## Optional (estilo funcional)

```java
Optional<User> maybe = repo.findById(id);
int points = maybe.map(User::getPoints).orElse(0);
```