# Framework de Collections em Java

O framework de Collections em Java fornece uma arquitetura para armazenar e manipular grupos de objetos. 

## Hierarquia Principal

- **Iterable**: A interface raiz, permite o uso do loop "for-each"
- **Collection**: Estende Iterable, base do framework
- **List**: Coleção ordenada com elementos duplicados
- **Queue**: Coleção FIFO (first-in, first-out)
- **Set**: Coleção sem elementos duplicados

## A Interface Iterable

A interface Iterable é a base para todas as coleções em Java. Permite iteração sem conhecer a estrutura interna.

### Implementação Básica

```java
public class GenericList<T> implements Iterable<T> {
    // Implementação básica
}
```

### Uso do Iterator

```java
var list = new GenericList<String>();
var iterator = list.iterator();

while (iterator.hasNext()) {
    var current = iterator.next();
    System.out.println(current);
}
```

### Implementação Completa do Iterator

```java
private class ListIterator implements Iterator<T> {
    private GenericList<T> list;
    private int index;

    public ListIterator(GenericList<T> list) {
        this.list = list;
    }
    
    @Override
    public boolean hasNext() {
        return (index < list.size());
    }

    @Override
    public T next() {
        return list.get(index++);
    }
}

@Override
public Iterator<T> iterator() {
    return new ListIterator(this);
}
```

## A Interface Collection

Representa um contêiner para objetos com operações básicas como `add()`, `remove()` e `contains()`.

### Exemplo Básico

```java
Collection<String> collection = new ArrayList<>();
collection.add("a");
collection.add("b");
collection.add("c");

// Iteração
for (var item : collection) {
    System.out.println(item);
}
```

### Métodos Úteis

```java
// Adicionar múltiplos itens
Collections.addAll(collection, "b", "a", "c");

// Operações básicas
collection.size();        // número de elementos
collection.remove("a");   // remove elemento
collection.clear();       // remove todos
collection.isEmpty();     // verifica se vazia
collection.contains("b"); // verifica existência

// Conversão para array
var objectArray = collection.toArray();
var stringArray = collection.toArray(new String[0]);
```

## A Interface List

Representa uma coleção ordenada com acesso por índice.

### Métodos Específicos

```java
List<String> list = new ArrayList<>();
Collections.addAll(list, "a", "b", "c");

list.add(0, "!");           // adiciona em posição
list.get(0);                // obtém elemento
list.set(0, "a+");          // substitui elemento
list.remove(0);             // remove por índice
list.indexOf("a");          // encontra índice
list.subList(0, 2);         // obtém sublista
```

## As Interfaces Comparable e Comparator

### Comparable - Ordem Natural

```java
public class Customer implements Comparable<Customer> {
    private String name;
    
    @Override
    public int compareTo(Customer other) {
        return name.compareTo(other.name);
    }
}
```

### Comparator - Ordem Alternativa

```java
public class EmailComparator implements Comparator<Customer> {
    @Override
    public int compare(Customer c1, Customer c2) {
        return c1.getEmail().compareTo(c2.getEmail());
    }
}
```

## A Interface Queue

Usada para processamento FIFO (First-In-First-Out).

```java
Queue<String> queue = new LinkedList<>();
queue.offer("a");    // adiciona
queue.peek();        // visualiza primeiro
queue.poll();        // remove primeiro
```

## A Interface Set

Coleção sem elementos duplicados.

```java
Set<String> set = new HashSet<>();
set.add("a");
set.add("a"); // ignorado (duplicata)

// Operações entre sets
Set<String> set1 = new HashSet<>(Arrays.asList("a", "b", "c"));
Set<String> set2 = new HashSet<>(Arrays.asList("b", "c", "d"));

set1.addAll(set2);    // união
set1.retainAll(set2); // interseção
set1.removeAll(set2); // diferença
```

## A Interface Map

Estrutura chave-valor, similar a dicionários.

### Operações Básicas

```java
Map<String, Customer> map = new HashMap<>();
map.put("key", customer);           // adiciona
map.get("key");                     // obtém
map.getOrDefault("key", default);   // obtém com padrão
map.containsKey("key");             // verifica existência
map.replace("key", newCustomer);    // substitui
```

### Iteração sobre Map

```java
// Sobre chaves
for (String key : map.keySet()) {
    System.out.println(key + ": " + map.get(key));
}

// Sobre valores
for (Customer customer : map.values()) {
    System.out.println(customer);
}

// Sobre entries (mais eficiente)
for (Map.Entry<String, Customer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}
```