# INTERFACES

* Usamos interfaces para construir aplicações com baixo acoplamento, extensíveis e testáveis.
* Na prática, uma interface é um tipo, similar a uma classe, mas contém apenas declarações de métodos — ou seja, não tem implementação, apenas define quais funcionalidades uma classe deve ter.

## Diferença entre interface e classe:
* Interface: define O QUE deve ser feito.
* Classe: define COMO isso será feito.

> Interfaces não possuem campos (como estado ou dados). Elas contêm somente declarações de métodos.

## Exemplo: como uma classe implementa uma interface

### Interface:
```java
package taxes;
public interface TaxCalculator {
    double calculateTax();
}
```

### Classe que implementa a interface:
```java
public class TaxCalculator2018 implements TaxCalculator {
    private double taxableIncome;

    public TaxCalculator2018(double taxableIncome) {
        this.taxableIncome = taxableIncome;
    }

    @Override
    public double calculateTax() {
        return taxableIncome * 0.3;
    }
}
```

O método `calculateTax()` é definido na interface e implementado pela classe `TaxCalculator2018`.

---

# DEPENDENCY INJECTION (Injeção de Dependência)

Princípio: nossas classes não devem instanciar diretamente suas dependências. Em vez disso, essas dependências devem ser injetadas de fora.

## Tipos de injeção:
1. Constructor Injection
2. Setter Injection
3. Method Injection

---

## 1. Constructor Injection

Mais comum. A dependência é passada através do construtor da classe.

### Em TaxReport:
```java
package taxes;
public class TaxReport {
    private TaxCalculator calculator;

    public TaxReport(TaxCalculator calculator) {
        this.calculator = calculator;
    }

    public void show() {
        var tax = calculator.calculateTax();
        System.out.println(tax);
    }
}
```

### Em Main:
```java
public class Main {
    public static void main(String[] args) {
        var calculator = new TaxCalculator2018(100_000);
        var report = new TaxReport(calculator);
        report.show();
    }
}
```

---

## 2. Setter Injection

Boa quando você precisa trocar a dependência durante a execução do programa.

### Em TaxReport:
```java
public class TaxReport {
    private TaxCalculator calculator;

    public void setCalculator(TaxCalculator calculator) {
        this.calculator = calculator;
    }

    public void show() {
        var tax = calculator.calculateTax();
        System.out.println(tax);
    }
}
```

### Em Main:
```java
var report = new TaxReport();
report.setCalculator(new TaxCalculator2019());
report.show();
```

---

## 3. Method Injection

A dependência é passada diretamente no método que irá utilizá-la. Evita o uso de campos e construtores.

### Em TaxReport:
```java
public class TaxReport {
    public void show(TaxCalculator calculator) {
        var tax = calculator.calculateTax();
        System.out.println(tax);
    }
}
```

### Em Main:
```java
var report = new TaxReport();
report.show(new TaxCalculator2018(100_000));
report.show(new TaxCalculator2019());
```

---

# INTERFACE SEGREGATION PRINCIPLE (Princípio da Segregação de Interface)
* Quebre interfaces grandes em interfaces menores.
* Isso evita que classes sejam forçadas a implementar métodos que não usam.
* O objetivo é segregar diferentes capacidades.

---

# NOVAS FUNCIONALIDADES RECENTES EM INTERFACES (e por que evitar):

## 1. Campos (fields) em interfaces
* São sempre public, static e final.
* Isso viola o princípio de abstração.
* Como não podem ser alterados, não faz sentido declará-los na interface. Prefira colocá-los nas implementações.

## 2. Métodos estáticos (static)
* Interfaces não devem ter lógica.
* Caso necessário, crie uma classe abstrata para isso.

## 3. Métodos privados (private)
* São detalhes de implementação.
* Não fazem sentido dentro de interfaces, que devem focar apenas em comportamentos expostos.

---

# QUANDO USAR INTERFACES?

Use interfaces quando quiser desacoplar uma classe de suas dependências diretas.

## Benefícios:
* Permite trocar implementações facilmente
* Facilita a extensão da aplicação
* Facilita testes unitários e