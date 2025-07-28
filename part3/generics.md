# Generics em Java

## Introdução

Generics permitem criar classes e métodos reutilizáveis com diferentes tipos. Eles resolvem o problema de reusabilidade de código quando precisamos trabalhar com diferentes tipos de dados.

> **Importante**: Generics só aceitam tipos referenciais (classes). Para tipos primitivos (int, boolean, etc.), use wrappers (Integer, Boolean, etc.).

### Exemplo Básico

```java
public class GenericList<T> {
    // T representa o tipo que será definido na instanciação
}

// Uso
GenericList<User> users = new GenericList<>();
```

## Comparable Interface

Permite que objetos de uma classe sejam comparáveis entre si.

```java
public class User implements Comparable<User> {
    private int points;

    public User(int points) {
        this.points = points;
    }

    @Override
    public int compareTo(User other) {
        return points - other.points;
    }
}
```

### Exemplo de Uso

```java
public static void main(String[] args) {
    User user1 = new User(10);
    User user2 = new User(2);

    if (user1.compareTo(user2) < 0)
        System.out.println("User 2 is bigger");
    else if (user1.compareTo(user2) == 0)
        System.out.println("Equal");
    else
        System.out.println("User 1 is bigger");
}
```

## Type Erasure

Durante a compilação, o Java substitui parâmetros genéricos:
- `T`, `K`, `V` → tipo especificado ou `Object`
- `<T extends Number>` → todos `T` viram `Number`
- Com múltiplas restrições, usa a primeira

## Métodos Genéricos

```java
public static <T extends Comparable<T>> T max(T first, T second) {
    return first.compareTo(second) < 0 ? second : first;
}

// Uso
User user1 = new User(10);
User user2 = new User(3);
User max = Utils.max(user1, user2);
```

## Múltiplos Parâmetros Genéricos

### Em Métodos

```java
public static <K, V> void print(K key, V value) {
    System.out.println(key + "=" + value);
}
```

### Em Classes

```java
public class KeyValuePair<K, V> {
    private K key;
    private V value;

    public KeyValuePair(K key, V value) {
        this.key = key;
        this.value = value;
    }
}
```

## Herança com Generics

```java
public class Instructor extends User {
    public Instructor(int points) {
        super(points);
    }
}
```

> **Nota**: `GenericList<Instructor>` não é considerado igual a `GenericList<User>`

## Wildcards (?)

### Wildcard Básico
```java
public static void printUsers(GenericList<?> users) {}
```

### Wildcard com Extends
```java
public