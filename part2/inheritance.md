# Herança e Polimorfismo em Programação Orientada a Objetos

Herança e Polimorfismo são dois dos pilares fundamentais da Programação Orientada a Objetos (POO). Eles permitem a criação de código reutilizável, flexível e organizado.

## 1. Herança (Inheritance)

Herança é o mecanismo que permite que uma classe (chamada de classe filha ou subclasse) herde atributos (campos) e comportamentos (métodos) de outra classe (chamada de classe pai ou superclasse).

Isso estabelece uma relação do tipo "é um(a)". Por exemplo, se uma classe Carro herda de Veiculo, todo Carro é um Veiculo.

### Como Funciona:
Para uma classe herdar de outra em Java, usamos a palavra-chave `extends`.

```java
// A classe TextBox herda de UIControl
public class TextBox extends UIControl {
    // ...
}
```

Neste caso, todo objeto TextBox também é um objeto UIControl e herda seus membros públicos.

### Visibilidade de Membros:
- Membros declarados como `private` na classe pai não são acessíveis diretamente pela classe filha
- Eles pertencem exclusivamente à classe onde foram declarados

### Hierarquia e Boas Práticas:
> Evite criar hierarquias de herança muito profundas (mais de 2 ou 3 níveis). Hierarquias complexas podem tornar o código difícil de entender e manter.

### Construtores na Herança
Quando uma instância de uma classe filha é criada, o construtor da classe pai é chamado primeiro.

**Classe Pai (UIControl):**
```java
public class UIControl {
    private boolean isEnabled;

    public UIControl(boolean isEnabled) {
        this.isEnabled = isEnabled;
        System.out.println("UI Control");
    }
}
```

**Classe Filha (TextBox):**
```java
public class TextBox extends UIControl {
    public TextBox() {
        // 'super()' chama o construtor da classe pai (UIControl)
        // Deve ser a primeira linha no construtor da classe filha
        super(true);
        System.out.println("Text Box");
    }
}
```

### Modificador de Acesso protected
O modificador `protected` oferece um nível de acesso intermediário entre `private` e `public`. Um membro protected:
- É tratado como `public` para todas as classes dentro do mesmo pacote
- É acessível por classes filhas, mesmo que elas estejam em pacotes diferentes

## 2. Polimorfismo e Conceitos Relacionados

### Sobrescrita de Métodos (Method Overriding)
Acontece quando uma classe filha fornece uma implementação específica para um método que já é definido por sua classe pai.

Usamos a anotação `@Override` para indicar ao compilador nossa intenção de sobrescrever um método.

**Exemplo com toString():**
```java
public class TextBox extends UIControl {
    private String text;

    @Override
    public String toString() {
        return text; // Retorna o texto atual do objeto
    }
}
```

**Uso:**
```java
public class Main {
    public static void main(String[] args) {
        var textBox = new TextBox();
        textBox.setText("Hello World");
        System.out.println(textBox); // Saída: Hello World
    }
}
```

### Polimorfismo (Polymorphism)
A palavra significa "muitas formas". Em POO, é a capacidade de um objeto se comportar de maneiras diferentes dependendo do contexto.

```java
public class Main {
    public static void main(String[] args) {
        UIControl[] controls = { new TextBox(), new CheckBox() };
        
        for (var control : controls) {
            control.render();
        }
    }
}
```

**Resultado no Console:**
```
Render TextBox
Render CheckBox
```

## 3. Manipulando Tipos e Comparando Objetos

### Upcasting e Downcasting

**Upcasting:** (Conversão segura)
```java
var textBox = new TextBox();
UIControl control = textBox; // Upcasting implícito
```

**Downcasting:** (Requer verificação)
```java
public static void show(UIControl control) {
    if (control instanceof TextBox) {
        var textBox = (TextBox) control;
        textBox.setText("Hello World");
    }
    System.out.println(control);
}
```

### Comparando Objetos (.equals() e hashCode())

**Implementação Robusta do equals():**
```java
@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (obj == null || !(obj instanceof Point)) return false;
    
    var other = (Point) obj;
    return other.x == x && other.y == y;
}
```

**Implementação do hashCode():**
```java
@Override
public int hashCode() {
    return Objects.hash(x, y);
}
```

## 4. Controlando a Estrutura da Herança

### Classes e Métodos Abstratos (abstract)
- **Classe Abstrata:** Declarada como `public abstract class ...`
- **Método Abstrato:** Declarado como `public abstract void ...;`

### Classes e Métodos Finais (final)
- **Classe Final:** Declarada como `public final class ...`
- **Método Final:** Declarado como `public final void ...;`

---

## Chamando membros da superclasse

Use `super` para acessar implementação da classe pai.

```java
public class LoggerTextBox extends TextBox {
    @Override
    public String toString() {
        String s = super.toString(); // mantém comportamento base
        return "[LOG] " + s;
    }
}
```

---

## Composição sobre Herança

Prefira composição quando a herança não modela um verdadeiro "é um".

```java
// Em vez de extends
public class CachedRepository {
    private final Repository repo; // delega chamadas
}
```

Benefícios: menor acoplamento, controle fino de exposição de API, evolução mais segura.

---

## Liskov Substitution Principle (LSP)

Subtipos devem poder substituir seus supertypos sem quebrar expectativas.

Contraexemplo clássico: `Square` estendendo `Rectangle` viola invariantes de largura/altura independentes.

---

## Classes Seladas (Java 17+)

Controle da hierarquia de herança:

```java
public sealed abstract class Shape permits Circle, Rectangle {}
public final class Circle extends Shape {}
public non-sealed class Rectangle extends Shape {}
```

---

## Quando usar `final`

- Classes utilitárias imutáveis (ex: `String`)
- Para impedir extensão acidental de tipos de domínio
- Métodos críticos que não devem ser alterados
