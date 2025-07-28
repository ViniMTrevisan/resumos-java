# Criando um Novo Método em Java

## Fora do método `main`

Criamos um novo método assim:

```java
public static String greetUser(String firstName, String lastName) {
    return "Hello " + firstName + " " + lastName;
}
```

**Explicação dos componentes:**

- `public static` — tipos do método (serão explicados melhor no futuro)
- `String` — o tipo de retorno da função
- `greetUser` — nome do método
- `(String firstName, String lastName)` — parâmetros da função (funcionam como variáveis locais)

---

## Como chamar o método dentro do `main`

```java
public class Main {

    public static void main(String[] args) {
        String msg = greetUser("Vinicius", "Trevisan");
        System.out.println(msg);
    }

    public static String greetUser(String firstName, String lastName) {
        return "Hello " + firstName + " " + lastName;
    }
}
```

---

# Refactoring

Refatorar é mudar a **estrutura do código** sem alterar o seu **comportamento**.

## Como escolher o que refatorar?

1. Conceitos ou linhas de código que **sempre estão juntos**  
2. **Padrões repetitivos**

---

# Breakpoint

Um **breakpoint** marca a linha onde a execução do código será **pausada**.

É muito útil para aplicações grandes, onde queremos descobrir **onde está o bug**.