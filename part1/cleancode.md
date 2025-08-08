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

---

## Princípios de Clean Code (Java)

- Nomes claros e descritivos
  - Classes: substantivos no singular (ex: `InvoiceService`)
  - Métodos: verbos (ex: `calculateTotal`, `findById`)
  - Variáveis: evite abreviações crípticas (ex: `hp` → `hourlyPrice`)

- Funções pequenas e coesas
  - Uma função deve fazer uma coisa, e bem (SRP – Single Responsibility Principle)
  - Evite muitos parâmetros; agrupe-os em objetos quando fizer sentido

- DRY e KISS
  - DRY (Don’t Repeat Yourself): extraia código duplicado
  - KISS (Keep It Simple, Stupid): evite complexidade desnecessária

- Evite efeitos colaterais inesperados
  - Prefira métodos puros sempre que possível
  - Retorne novos objetos em vez de mutar parâmetros

- Erros e Exceções
  - Lance exceções com mensagens úteis (contexto do erro)
  - Converta exceções de baixo nível em exceções de domínio quando apropriado
  - Não engula exceções (evite `catch (Exception e) {}` vazio)

- Null-safety
  - Prefira `Objects.requireNonNull(...)` para validar entradas
  - Considere usar `Optional` em retornos para ausências válidas

- Imutabilidade por padrão
  - Prefira campos `final` e classes imutáveis para reduzir bugs de estado

- Comentários
  - Explique o “porquê”, não o “como”
  - Remova código morto e comentários obsoletos

- Formatação consistente
  - Organização de imports, quebras de linha e limites de coluna
  - Uma declaração por linha; evite linhas extremamente longas

---

## Antes x Depois (exemplos)

### Antes (função grande, muitos níveis):

```java
public BigDecimal calc(BigDecimal a, BigDecimal b, String type) {
    if (type != null) {
        if (type.equals("SUM")) {
            if (a != null && b != null) return a.add(b);
        } else if (type.equals("SUB")) {
            if (a != null && b != null) return a.subtract(b);
        }
    }
    throw new IllegalArgumentException("Invalid input");
}
```

### Depois (SRP + validação explícita):

```java
public BigDecimal calculate(BigDecimal left, BigDecimal right, Operation operation) {
    Objects.requireNonNull(left, "left is required");
    Objects.requireNonNull(right, "right is required");
    Objects.requireNonNull(operation, "operation is required");
    return switch (operation) {
        case SUM -> left.add(right);
        case SUB -> left.subtract(right);
    };
}

public enum Operation { SUM, SUB }
```

---

## Dicas práticas de depuração

- Use breakpoints condicionais (ex: pausar apenas quando `x > 100`)
- Step Over (F8): executa a linha atual e vai para a próxima
- Step Into (F7): entra na chamada de método
- Step Out (Shift+F8): sai do método atual
- Watches: monitore variáveis/expressões
- Avaliação de expressões no debug (Evaluate Expression)

### Logging eficaz

```java
private static final Logger log = LoggerFactory.getLogger(CheckoutService.class);

void process(String orderId) {
    log.info("Processing orderId={}", orderId);
    try {
        // ...
    } catch (PaymentException ex) {
        log.warn("Payment failed for orderId={} cause={}", orderId, ex.getMessage(), ex);
        throw ex;
    }
}
```

---

## Checklist rápido

- O nome da função revela sua intenção?
- A função tem até ~10-15 linhas e faz uma única coisa?
- Há validações explícitas para entradas inválidas?
- Há código duplicado que pode ser extraído?
- Exceções têm mensagens úteis com contexto?
- Existem efeitos colaterais inesperados?
- Há testes automatizados para os fluxos principais?