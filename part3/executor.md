## Java Concorrência: Thread Pools, ExecutorService e CompletableFuture

### Visão geral
- **Objetivo**: Guia prático sobre thread pools, `ExecutorService`, `Callable`/`Future` e `CompletableFuture` em Java
- **Foco**: Programação não bloqueante/assíncrona, composição e timeouts

---

## Thread Pools e ExecutorService

**Thread pool**: conjunto de threads trabalhadoras que são reutilizadas para executar tarefas. Quando uma thread termina, ela volta ao pool para ser reutilizada.

- **Representação**: interface `ExecutorService`
- **Criação**: prefira os factories de `Executors` ao invés de instanciar `ThreadPoolExecutor` diretamente

```java
import java.util.concurrent.Executors;

public class ExecutorsDemo {
    public static void show() {
        var executor = Executors.newFixedThreadPool(2); // pool fixo com 2 threads
        executor.submit(() -> System.out.println(Thread.currentThread().getName()));
        executor.shutdown();
    }
}
```

- Use `submit(...)` para enviar tarefas. Aceita `Runnable` ou `Callable` e retorna um `Future`.
- Boa prática: garanta `shutdown()` com `try/finally`.

```java
import java.util.concurrent.Executors;

public class ExecutorsDemo {
    public static void show() {
        var executor = Executors.newFixedThreadPool(2);
        try {
            executor.submit(() -> System.out.println(Thread.currentThread().getName()));
        } finally {
            executor.shutdown();
        }
    }
}
```

- O executor mantém uma fila; tarefas aguardam nela se não há threads disponíveis.

---

## `Callable` e `Future`

- **`Callable<T>`**: semelhante ao `Runnable`, mas retorna valor (via `call()`) e pode lançar exceções checadas
- **`Future<T>`**: referência ao resultado de um processamento assíncrono
  - `get()` é bloqueante
  - Outros métodos: `cancel()`, `isCancelled()`, `isDone()`, etc.

```java
import java.util.concurrent.*;

public class LongTask {
    public static void simulate() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}

public class ExecutorsDemo {
    public static void show() {
        var executor = Executors.newFixedThreadPool(2);
        try {
            Future<Integer> future = executor.submit(() -> {
                LongTask.simulate();
                return 1; // Callable retorna um valor
            });

            System.out.println("Do more");

            try {
                Integer result = future.get(); // bloqueia até o resultado estar pronto
                System.out.println(result);
            } catch (InterruptedException | ExecutionException e) {
                throw new RuntimeException(e);
            }
        } finally {
            executor.shutdown();
        }
    }
}
```

Saída:

```text
Do more
1
```

---

## Programação Assíncrona com `CompletableFuture`

- `CompletableFuture<T>` implementa `Future<T>` e permite completar explicitamente, encadear e compor estágios assíncronos
- Pool padrão: `ForkJoinPool.commonPool()` (a menos que um `Executor` seja fornecido)

### Criando `CompletableFuture`s

- Sem retorno: `runAsync(Runnable)`
- Com retorno: `supplyAsync(Supplier<T>)`

```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.function.Supplier;

public class CompletableFuturesDemo {
    public static void runWithoutReturn() {
        Runnable task = () -> System.out.println("something");
        CompletableFuture<Void> future = CompletableFuture.runAsync(task);
    }

    public static void runWithReturn() {
        Supplier<Integer> task = () -> 1;
        CompletableFuture<Integer> future = CompletableFuture.supplyAsync(task);
        try {
            Integer result = future.get();
            System.out.println(result);
        } catch (InterruptedException | ExecutionException e) {
            throw new RuntimeException(e);
        }
    }
}
```

Saída:

```text
1
```

### Implementando uma API Assíncrona

```java
import java.util.concurrent.CompletableFuture;

public class MailService {
    public void send() {
        LongTask.simulate();
        System.out.println("mail was sent.");
    }

    public CompletableFuture<Void> sendAsync() {
        return CompletableFuture.runAsync(this::send);
    }
}

public class Main {
    public static void main(String[] args) {
        var service = new MailService();
        service.sendAsync();
        System.out.println("Hello world");

        // Apenas para a demo não encerrar o main antes do async terminar
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}
```

Saída:

```text
Hello world
// ~3s depois
mail was sent.
```

Notas:
- I/O (arquivos, rede) costuma ser demorado; evite fazer na thread principal.
- Sufixo `Async` é uma convenção comum para métodos assíncronos.

### Executando código na conclusão

Use os métodos de `CompletionStage` (a maioria começa com `then...`).

- `thenRun(Runnable)`: executa após concluir, sem acesso ao resultado
- `thenRunAsync(Runnable)`: executa em outra thread (geralmente no common pool)

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFuturesDemo {
    public static void thenRunExamples() {
        var future = CompletableFuture.supplyAsync(() -> 1);

        future.thenRun(() -> System.out.println("done"));

        future.thenRunAsync(() -> {
            System.out.println(Thread.currentThread().getName());
            System.out.println("done");
        });
    }
}
```

- `thenAccept(Consumer<T>)`: consome o resultado
- `thenAcceptAsync(Consumer<T>)`: consome em outra thread

```java
public class CompletableFuturesDemo {
    public static void thenAcceptExamples() {
        CompletableFuture.supplyAsync(() -> 1)
                .thenAccept(result -> {
                    System.out.println(Thread.currentThread().getName());
                    System.out.println(result);
                });
    }
}
```

Possível saída:

```text
ForkJoinPool.commonPool-worker-1
1
```

### Transformação de resultados com `thenApply`

- `thenApply(Function<T, U>)`: transforma o resultado; retorna novo `CompletableFuture<U>`

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFuturesDemo {
    public static int toFahrenheit(int celsius) {
        return (int) (celsius * 1.8) + 32;
    }

    public static void transform() {
        CompletableFuture.supplyAsync(() -> 20)
                .thenApply(CompletableFuturesDemo::toFahrenheit)
                .thenAccept(System.out::println);
    }
}
```

### Compondo estágios com `thenCompose`

- Inicia a segunda tarefa após a primeira, usando o resultado da primeira
- `thenCompose(Function<T, CompletionStage<U>>)` “achata” futuros aninhados

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFuturesDemo {
    public static CompletableFuture<String> getUserEmailAsync() {
        return CompletableFuture.supplyAsync(() -> "email");
    }

    public static CompletableFuture<String> getUserPlaylistAsync(String email) {
        return CompletableFuture.supplyAsync(() -> "playlist");
    }

    public static void compose() {
        getUserEmailAsync()
                .thenCompose(CompletableFuturesDemo::getUserPlaylistAsync)
                .thenAccept(System.out::println);
    }
}
```

### Combinando futuros independentes com `thenCombine`

- Combina resultados de duas computações independentes

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFuturesDemo {
    public static void combine() {
        var priceInUsd = CompletableFuture
                .supplyAsync(() -> "20USD")
                .thenApply(label -> {
                    var price = label.replace("USD", "");
                    return Integer.parseInt(price);
                });

        var exchangeRate = CompletableFuture.supplyAsync(() -> 0.9);

        priceInUsd
                .thenCombine(exchangeRate, (price, rate) -> price * rate)
                .thenAccept(System.out::println);
    }
}
```

### Esperando várias tarefas com `allOf`

- `CompletableFuture.allOf(...)` completa quando todas as futures fornecidas completam

```java
import java.util.concurrent.*;

public class CompletableFuturesDemo {
    public static void waitAll() {
        var first = CompletableFuture.supplyAsync(() -> 1);
        var second = CompletableFuture.supplyAsync(() -> 2);
        var third = CompletableFuture.supplyAsync(() -> 3);

        CompletableFuture.allOf(first, second, third)
                .thenRun(() -> {
                    try {
                        System.out.println(first.get());
                        System.out.println(second.get());
                        System.out.println(third.get());
                        System.out.println("all tasks completed successfully");
                    } catch (InterruptedException | ExecutionException e) {
                        throw new RuntimeException(e);
                    }
                });
    }
}
```

Saída:

```text
1
2
3
all tasks completed successfully
```

Observação: Aqui, os `get()` dentro do `thenRun` não bloqueiam antes do agendamento, pois o `thenRun` só é disparado após todas as tarefas concluírem.

### Esperando a primeira tarefa com `anyOf`

- `CompletableFuture.anyOf(...)` completa quando qualquer uma das futures fornecidas completa

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFuturesDemo {
    public static void anyFirst() {
        var first = CompletableFuture.supplyAsync(() -> {
            LongTask.simulate();
            return 20;
        });

        var second = CompletableFuture.supplyAsync(() -> 20);

        CompletableFuture.anyOf(first, second)
                .thenAccept(System.out::println);
    }
}
```

Saída:

```text
20
```

### Tratando timeouts

- `orTimeout(long, TimeUnit)`: falha a future com `TimeoutException` após o prazo
- `completeOnTimeout(T, long, TimeUnit)`: completa com valor padrão se o prazo for excedido

```java
import java.util.concurrent.*;

public class CompletableFuturesDemo {
    public static void timeouts() {
        var future = CompletableFuture.supplyAsync(() -> {
            LongTask.simulate();
            return 1;
        });

        try {
            Integer result = future
                    .completeOnTimeout(1, 1, TimeUnit.SECONDS) // valor padrão se estourar o tempo
                    .get();
            System.out.println(result);
        } catch (InterruptedException | ExecutionException e) {
            throw new RuntimeException(e);
        }
    }
}
```

Saída:

```text
1
```

---

## Boas práticas
- **Sempre finalize** `ExecutorService` com `shutdown()` (use `try/finally`)
- **Prefira factories** (`Executors.newFixedThreadPool`, etc.) a menos que precise de ajustes finos
- **Evite bloquear** a thread principal/UI; use padrões assíncronos para I/O
- **Componha** (`thenCompose`, `thenCombine`) para fluxos assíncronos declarativos e legíveis
- **Escolha o método certo** de estágio:
  - Transformar: `thenApply`
  - Consumir: `thenAccept`
  - Efeito colateral: `thenRun`
  - Combinar paralelo: `thenCombine`
  - Sequenciar dependências: `thenCompose`
- **Timeouts**: prefira `completeOnTimeout` para fallback gracioso; use `orTimeout` para falhar rápido
- **Modelo de threading**: `thenRun`/`thenAccept` executam na thread que completa o estágio anterior; use variantes `Async` para desacoplar

---

### Referência rápida
- Criar pool: `Executors.newFixedThreadPool(n)`
- Enviar tarefa: `executor.submit(callableOuRunnable)` → retorna `Future`
- `Future.get()` é bloqueante
- Assíncrono sem valor: `CompletableFuture.runAsync(runnable)`
- Assíncrono com valor: `CompletableFuture.supplyAsync(supplier)`
- Transformar: `thenApply(fn)`
- Consumir: `thenAccept(consumer)`
- Efeito colateral: `thenRun(runnable)`
- Sequência dependente: `thenCompose(fn)`
- Combinar independentes: `thenCombine(outro, combinador)`
- Esperar todos: `CompletableFuture.allOf(f1, f2, ...)`
- Esperar qualquer: `CompletableFuture.anyOf(f1, f2, ...)`
- Timeout: `orTimeout(prazo, unidade)` / Fallback: `completeOnTimeout(valor, prazo, unidade)`