# Java Streams

## Introdução

Streams foram introduzidos no Java para processar coleções de dados de forma declarativa, semelhante ao SQL. Um stream é uma sequência de objetos que permite extrair e manipular dados de uma coleção.

### Programação Imperativa vs. Declarativa

**Imperativa (Como fazer):**
```java
int count = 0;
for (var movie : movies) {
    if (movie.getLikes() > 10) {
        count++;
    }
}
```

**Declarativa (O que fazer):**
```java
var count = movies.stream()
    .filter(movie -> movie.getLikes() > 10)
    .count();
```

## Criando Streams

### A partir de Arrays
```java
int[] numbers = { 1, 2, 3 };
Arrays.stream(numbers)
    .forEach(n -> System.out.println(n));
```

### Com valores arbitrários
```java
Stream.of(1, 2, 3, 4);
```

### Streams Infinitos
```java
// Números aleatórios
Stream.generate(() -> Math.random())
    .limit(3)
    .forEach(n -> System.out.println(n));

// Sequência
Stream.iterate(1, n -> n + 1)
    .limit(10)
    .forEach(n -> System.out.println(n));
```

## Operações Intermediárias

### map()
Transforma cada elemento:
```java
movies.stream()
    .map(Movie::getTitle)
    .forEach(System.out::println);
```

### flatMap()
"Achata" streams aninhados:
```java
var stream = Stream.of(List.of(1, 2, 3), List.of(4, 5, 6));
stream
    .flatMap(List::stream)
    .forEach(System.out::println);
```

### Slicing Operations

```java
// limit
movies.stream()
    .limit(2)
    .forEach(m -> System.out.println(m.getTitle()));

// skip
movies.stream()
    .skip(2)
    .forEach(m -> System.out.println(m.getTitle()));

// takeWhile
movies.stream()
    .takeWhile(m -> m.getLikes() < 20)
    .forEach(m -> System.out.println(m.getTitle()));

// dropWhile
movies.stream()
    .dropWhile(m -> m.getLikes() < 20)
    .forEach(m -> System.out.println(m.getTitle()));
```

### Sorting and Distinct
```java
// Sorting
movies.stream()
    .sorted(Comparator.comparing(Movie::getTitle))
    .forEach(m -> System.out.println(m.getTitle()));

// Distinct
movies.stream()
    .map(Movie::getLikes)
    .distinct()
    .forEach(System.out::println);
```

### peek()
Para debugging:
```java
movies.stream()
    .filter(m -> m.getLikes() > 10)
    .peek(m -> System.out.println("filtered: " + m.getTitle()))
    .map(Movie::getTitle)
    .peek(t -> System.out.println("mapped: " + t))
    .forEach(System.out::println);
```

## Operações Terminais (Reducers)

### Operações Básicas
```java
// count
var count = movies.stream().count();

// matches
boolean hasPopular = movies.stream().anyMatch(m -> m.getLikes() > 15);
boolean allPopular = movies.stream().allMatch(m -> m.getLikes() > 15);
boolean nonePopular = movies.stream().noneMatch(m -> m.getLikes() > 15);

// find
var first = movies.stream().findFirst().get();
var any = movies.stream().findAny().get();

// max/min
var highest = movies.stream()
    .max(Comparator.comparing(Movie::getLikes))
    .get();
```

## Collectors

```java
// To List
.collect(Collectors.toList());

// To Set
.collect(Collectors.toSet());

// To Map
.collect(Collectors.toMap(
    Movie::getTitle,
    Movie::getLikes
));

// Statistics
.collect(Collectors.summarizingInt(Movie::getLikes));

// Joining
.map(Movie::getTitle)
.collect(Collectors.joining(", "));
```

### Grouping and Partitioning
```java
// Group by genre
var byGenre = movies.stream()
    .collect(Collectors.groupingBy(
        Movie::getGenre,
        Collectors.toSet()
    ));

// Partition by likes
var byPopularity = movies.stream()
    .collect(Collectors.partitioningBy(
        m -> m.getLikes() > 10
    ));
```

## Primitive Type Streams

```java
// Range (exclusive end)
IntStream.range(1, 5);  // 1,2,3,4

// RangeClosed (inclusive end)
IntStream.rangeClosed(1, 5);  //