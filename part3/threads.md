# Threads em Java 🧵

## Conceitos Básicos: Processos e Threads 💡

- **Processo**: Instância de um programa em execução, com seu próprio espaço de memória isolado
- **Thread**: Menor unidade de execução dentro de um processo, compartilhando recursos de memória

## Iniciando uma Thread

```java
// Main.java
package threads;

public class Main {
    public static void main(String[] args) {
        System.out.println("Thread principal: " + Thread.currentThread().getName());

        for (var i = 0; i < 10; i++) {
            Thread thread = new Thread(new DownloadFileTask());
            thread.start();
        }
    }
}

// DownloadFileTask.java
package threads;

public class DownloadFileTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Baixando arquivo: " + Thread.currentThread().getName());
    }
}
```

## Pausando uma Thread ⏸️

```java
public class DownloadFileTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Baixando arquivo: " + Thread.currentThread().getName());
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        System.out.println("Download concluído: " + Thread.currentThread().getName());
    }
}
```

## Aguardando uma Thread (Join) 🤝

```java
public class Main {
    public static void main(String[] args) {
        Thread thread = new Thread(new DownloadFileTask());
        thread.start();

        try {
            thread.join();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }

        System.out.println("Arquivo baixado, processo finalizado.");
    }
}
```

## Interrompendo uma Thread 🛑

```java
// Main
public static void main(String[] args) {
    Thread thread = new Thread(new DownloadFileTask());
    thread.start();

    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }

    thread.interrupt();
}

// DownloadFileTask
@Override
public void run() {
    System.out.println("Downloading file: " + Thread.currentThread().getName());

    for(var i = 0; i < Integer.MAX_VALUE; i++) {
        if (Thread.currentThread().isInterrupted()) {
            System.out.println("Download interrompido.");
            return;
        }
        System.out.println("Downloading byte " + i);
    }
}
```

## Condições de Corrida (Race Conditions) 🏎️

### Problema

```java
public class DownloadStatus {
    private int totalBytes;
    
    public int getTotalBytes() { return totalBytes; }
    public void incrementBytes() { totalBytes++; }
}

public class DownloadFileTask implements Runnable {
    private final DownloadStatus status;
    
    public DownloadFileTask(DownloadStatus status) {
        this.status = status;
    }
    
    @Override
    public void run() {
        for(var i = 0; i < 10_000; i++) {
            status.incrementBytes();
        }
    }
}
```

## Soluções para Condições de Corrida

### 1. Locks 🔒

```java
public class DownloadStatus {
    private int totalBytes;
    private final Lock lock = new ReentrantLock();

    public void incrementBytes() {
        lock.lock();
        try {
            totalBytes++;
        } finally {
            lock.unlock();
        }
    }
}
```

### 2. Synchronized

```java
public class DownloadStatus {
    private int totalBytes;
    private final Object lock = new Object();

    public void incrementBytes() {
        synchronized (lock) {
            totalBytes++;
        }
    }
}
```

### 3. Objetos Atômicos

```java
public class DownloadStatus {
    private final AtomicInteger totalBytes = new AtomicInteger();

    public int getTotalBytes() { 
        return totalBytes.get(); 
    }

    public void incrementBytes() {
        totalBytes.incrementAndGet();
    }
}
```

### 4. Adders

```java
public class DownloadStatus {
    private final LongAdder totalBytes = new LongAdder();

    public int getTotalBytes() { 
        return totalBytes.intValue(); 
    }

    public void incrementBytes() {
        totalBytes.increment();
    }
}
```

## Coleções Thread-Safe

### Coleções Sincronizadas
```java
List<String> synchronizedList = Collections.synchronizedList(new ArrayList<>());
Map<String, String> synchronizedMap = Collections.synchronizedMap(new HashMap<>());
```

### Coleções Concorrentes
```java
ConcurrentMap<String, String> concurrentMap = new ConcurrentHashMap<>();
List<String> copyOnWriteList = new CopyOnWriteArrayList<>();
```

## Palavras-chave Importantes

### volatile
```java
public class DownloadStatus {
    private volatile boolean isDone;
    // Garante visibilidade entre threads, mas não atomicidade
}
```

---

## Daemon threads e prioridade

```java
Thread t = new Thread(task);
t.setDaemon(true);      // não impede JVM de encerrar
t.setPriority(Thread.NORM_PRIORITY);
```

---

## Espera e notificação (`wait`/`notify`)

```java
synchronized (lock) {
    while (!condicao) lock.wait();
    // ...
}

synchronized (lock) {
    condicao = true;
    lock.notifyAll();
}
```

Use sempre dentro de blocos `synchronized` no mesmo `lock`.

---

## ThreadLocal

```java
private static final ThreadLocal<SimpleDateFormat> formatter =
    ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));
```

---

## Deadlocks: como evitar

- Ordem consistente na aquisição de locks
- Tente usar locks com timeout (`tryLock`)
- Prefira estruturas de alto nível (`ExecutorService`, `Concurrent*`)

---

## Modelo de Memória Java (JMM) – visibilidade

- `synchronized` e `Lock` estabelecem relação happens-before
- `volatile` garante visibilidade, não atomicidade
- Campos `final` têm visibilidade segura após o construtor terminar

---

## Boas práticas

- Prefira pools de threads a threads manuais
- Projete cancelamento cooperativo (`interrupt`, flags)
- Evite compartilhar mutabilidade; use imutabilidade e coleções concorrentes