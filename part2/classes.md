# Conceitos Fundamentais da Programação Orientada a Objetos (POO)

## Classe
É um *blueprint* ou modelo usado para criar objetos. Define um conjunto de atributos (campos) e comportamentos (métodos) que os objetos terão.

## Objeto
É uma instância de uma classe. Enquanto a classe é o projeto, o objeto é a construção real feita a partir desse projeto.

## Fields (Campos ou Atributos)
São variáveis declaradas dentro de uma classe. Armazenam o estado de um objeto.

## Instância
É uma ocorrência única de uma classe. “Objeto” e “instância” são usados de forma intercambiável.

> 💡 Por organização, geralmente separamos cada classe em seu próprio arquivo.

---

## Exemplo Prático: Classe `TextBox`

Vamos criar uma classe para representar uma caixa de texto:

```java
public class TextBox {
    public String text;

    public void setText(String text) {
        this.text = text;
    }

    public void clear() {
        text = "";
    }
}
```

Para usá-la:

```java
var textBox1 = new TextBox();
textBox1.setText("Olá!");
```

---

## Diferenças entre Programação Procedural e POO

### Código Procedural

Dados e funções são separados. Exemplo:

```java
public class Main {
    public static void main(String[] args) {
        int baseSalary = 50_000;
        int extraHours = 10;
        int hourlyRate = 20;

        int wage = calculateWage(baseSalary, extraHours, hourlyRate);
        System.out.println(wage);
    }

    public static int calculateWage(int baseSalary, int extraHours, int hourlyRate) {
        return baseSalary + (extraHours * hourlyRate);
    }
}
```

**Sintomas**:
- Muitos parâmetros
- Classes extensas
- Baixa reutilização e alto acoplamento

---

## Aplicando Princípios da POO

### 1. Encapsulamento

Agrupa dados e métodos em um objeto. Exemplo:

```java
public class Employee {
    public int baseSalary;
    public int hourlyRate;

    public int calculateWage(int extraHours) {
        return baseSalary + (hourlyRate * extraHours);
    }
}
```

Uso:

```java
var employee1 = new Employee();
employee1.baseSalary = 50_000;
employee1.hourlyRate = 20;
System.out.println(employee1.calculateWage(10));
```

---

### Refinando com Getters e Setters

```java
public class Employee {
    private int baseSalary;
    private int hourlyRate;

    public int calculateWage(int extraHours) {
        return getBaseSalary() + (getHourlyRate() * extraHours);
    }

    public void setBaseSalary(int baseSalary) {
        if (baseSalary <= 0)
            throw new IllegalArgumentException("Salary must be above 0");
        this.baseSalary = baseSalary;
    }

    public int getBaseSalary() {
        return baseSalary;
    }

    public void setHourlyRate(int hourlyRate) {
        if (hourlyRate <= 0)
            throw new IllegalArgumentException("Hourly Rate must be above 0");
        this.hourlyRate = hourlyRate;
    }

    public int getHourlyRate() {
        return hourlyRate;
    }
}
```

Uso:

```java
var employee1 = new Employee();
employee1.setBaseSalary(50_000);
employee1.setHourlyRate(20);
System.out.println(employee1.calculateWage(10));
```

---

### 2. Abstração

Reduz a complexidade escondendo detalhes desnecessários. Expor apenas o necessário usando `private`, `getters` e `setters`.

---

### 3. Acoplamento

Quanto menos uma classe depende de outra, melhor. Campos e métodos privados ajudam a reduzir o acoplamento.

---

## Tópicos Adicionais

### Construtores

```java
public class Employee {
    private int baseSalary;
    private int hourlyRate;

    public Employee(int baseSalary, int hourlyRate) {
        setBaseSalary(baseSalary);
        setHourlyRate(hourlyRate);
    }

    // ... (getters, setters, calculateWage)
}
```

Uso:

```java
var employee1 = new Employee(50_000, 20);
System.out.println(employee1.calculateWage(10));
```

---

### Sobrecarga de Métodos

Permite múltiplas versões de um método com o mesmo nome, mas diferentes parâmetros.

---

### Membros Estáticos

- **Instance Members**: pertencem ao objeto.
- **Static Members**: pertencem à classe.

Exemplo:

```java
public class Employee {
    public static final int NUMERO_MESES_NO_ANO = 12;
}
```

---

## Como Decidir Quais Classes Criar?

Identifique os substantivos e responsabilidades do sistema. Exemplo: em um sistema de hipoteca, classes como `Hipoteca`, `CalculadoraDeJuros`, `Relatorio`.

---

## Palavras-chave e recursos importantes

### `this` e encadeamento de construtores

```java
public class Rectangle {
    private final int width;
    private final int height;

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    public Rectangle() {
        this(1, 1); // delega ao outro construtor
    }
}
```

### Métodos de fábrica estáticos

Oferecem nomes melhores e possibilidade de cache/validação.

```java
public final class Money {
    private final BigDecimal amount;
    private final Currency currency;

    private Money(BigDecimal amount, Currency currency) { /* validação */
        this.amount = amount; this.currency = currency;
    }

    public static Money of(BigDecimal amount, Currency currency) {
        return new Money(amount, currency);
    }
}
```

### Imutabilidade por padrão

- Campos `private final`
- Sem setters; exponha comportamento, não estado
- Construa instância válida no construtor

### Igualdade e representação textual

```java
@Override public String toString() { return "Point(" + x + "," + y + ")"; }

@Override public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Point other)) return false;
    return x == other.x && y == other.y;
}

@Override public int hashCode() { return Objects.hash(x, y); }
```

### Composição vs. Herança

Prefira composição quando a relação não for estritamente "é um". Ela reduz acoplamento e torna a evolução mais segura.

```java
public class Car {
    private final Engine engine; // composição
}
```

### Builder para objetos complexos

```java
public class User {
    private final String name;
    private final String email;
    private final int age;

    private User(Builder b) { this.name = b.name; this.email = b.email; this.age = b.age; }

    public static class Builder {
        private String name; private String email; private int age;
        public Builder name(String v) { this.name = v; return this; }
        public Builder email(String v) { this.email = v; return this; }
        public Builder age(int v) { this.age = v; return this; }
        public User build() { return new User(this); }
    }
}

var user = new User.Builder().name("Ana").email("ana@ex.com").age(30).build();
```