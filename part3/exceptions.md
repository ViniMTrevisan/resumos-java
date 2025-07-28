# Exceptions em Java

Uma exception (exceção) é um objeto que contém informações sobre um erro ocorrido em tempo de execução.

> **Stack trace**: mostra os métodos que foram chamados em ordem reversa, ajudando a identificar onde o erro ocorreu.

## Tipos de Exceções

1. **Checked Exception**
   - Exceções que devemos antecipar e tratar corretamente
   - Exemplo: `FileNotFoundException`

2. **Unchecked / Runtime Exception**
   - Ocorrências causadas por erros de programação
   - Exemplo: `NullPointerException`, `IllegalArgumentException`

3. **Error**
   - Problemas externos à aplicação, relacionados ao ambiente da JVM
   - Exemplo: `OutOfMemoryError`

## Hierarquia de Exceções

Todas são objetos:
- `Throwable`: classe base que define características comuns
- `Exception`: pai de todas as checked e unchecked exceptions
- `Error`: representa erros externos ao programa
- `RuntimeException`: pai das unchecked exceptions

## Tratando Exceções

### Exemplo básico try/catch:

```java
try {
    var reader = new FileReader("File.txt");
    System.out.println("File Open");
} catch (FileNotFoundException ex) {
    System.out.println("File does not exist");
    // Ou use: System.out.println(ex.getMessage());
}
```

## Polimorfismo em Exceções

Para lidar com múltiplas exceções no mesmo catch:

```java
catch (IOException | ParseException e) {
    System.out.println("Couldn't read data");
}
```

## Bloco Finally

Usado para liberar recursos, independente de exceções:

```java
finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## Try-With-Resources

Forma moderna de gerenciar recursos:

```java
try (var reader = new FileReader("File.txt")) {
    // código
}
```

## Lançando Exceções (throw)

### Exemplo com unchecked exception:

```java
public void deposit(float value) {
    if (value <= 0) {
        throw new IllegalArgumentException();
    }
}
```

### Exemplo com checked exception:

```java
public void deposit(float value) throws IOException {
    if (value <= 0) {
        throw new IllegalArgumentException();
    }
}

public static void show() {
    var account = new Account();
    try {
        account.deposit(1);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}
```

## Criando Exceções Personalizadas

```java
public class InsufficientFundsException extends Exception {
    public InsufficientFundsException() {
        System.out.println("Insufficient funds in your account");
    }

    public InsufficientFundsException(String message) {
        super(message);
    }
}
```

## Chaining Exceptions

### 1. Criar exceção genérica:

```java
public class AccountException extends Exception {
    public AccountException(Exception cause) {
        super(cause);
    }
}
```

### 2. Encapsular exceção específica:

```java
public void withdraw(float value) throws AccountException {
    if (value > balance) {
        throw new AccountException(new InsufficientFundsException());
    }
}
```

### Boas Práticas

1. Use checked exceptions para condições recuperáveis
2. Use unchecked exceptions para