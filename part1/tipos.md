# Java - Resumo Completo

## Declaração de Variáveis

```java
tipo nome = valor;
int age = 18;
```

---

## Tipos em Java

### Tipos Primitivos

Armazenam valores simples diretamente na memória:

- `byte` → 1 byte → -128 a 127  
- `short` → 2 bytes  
- `int` → 4 bytes  
- `long` → 8 bytes  
- `float` → 4 bytes  
- `double` → 8 bytes  
- `char` → 2 bytes (caractere único)  
- `boolean` → 1 byte (true ou false)  

**Dicas:**

```java
int number = 123_456_789;
long views = 3_123_456_789L;
float price = 10.99F;
```

---

### Tipos Referenciais

Referenciam objetos mais complexos (ex: `String`, `Date`, `Scanner`, etc):

```java
Date now = new Date();
```

---

## Primitivo vs Referencial

- Primitivos guardam o valor diretamente.  
- Referenciais guardam o endereço na memória.

**Exemplo com primitivos:**

```java
byte x = 1;
byte y = x;
x = 2;
System.out.println(y); // 1
```

**Exemplo com objetos:**

```java
Point p1 = new Point(1, 1);
Point p2 = p1;
p1.x = 2;
System.out.println(p2); // java.awt.Point[x=2,y=1]
```

---

## Strings

```java
String message = "Hello World!" + "!";
```

**Métodos úteis:**

- `endsWith`  
- `startsWith`  
- `length`  
- `indexOf`  
- `replace`  
- `toLowerCase`  
- `toUpperCase`  
- `trim`  

> **Importante:** Strings são imutáveis em Java

---

## Escape Sequences

- `"Hello \"Vini\""` — aspas internas  
- `"c:\\Windows\\..."` — barra invertida  
- `"linha1\\nlinha2"` — nova linha  
- `"abc\\tdef"` — tabulação  

---

## Arrays

```java
int[] numbers = new int[5];
numbers[0] = 1;
System.out.println(Arrays.toString(numbers));
```

**Inicialização direta:**

```java
int[] numbers = {2, 4, 6, 8};
Arrays.sort(numbers);
```

---

## Arrays Multidimensionais

```java
int[][] matrix = new int[2][3];
matrix[0][0] = 1;
System.out.println(Arrays.deepToString(matrix));
```

**Ou diretamente:**

```java
int[][] matrix = { {1, 2, 3}, {4, 5, 6} };
```

---

## Constantes

```java
final float PI = 3.14f;
```

> Por convenção, constantes devem ser declaradas em **MAIÚSCULAS**.

---

## Expressões Aritméticas

```java
10 + 3
10 - 3
10 * 3
10 / 3
10 % 3
```

**Incremento / Decremento:**

```java
x++;
x--;
x += 2;
x -= 2;
```

---

## Casting e Conversão de Tipos

**Implícito:**

```java
short x = 1;
int y = x + 2;
```

**Explícito:**

```java
double x = 1.1;
int y = (int) x + 2;
```

**De String para número:**

```java
Integer.parseInt("123");
Short.parseShort("10");
```

---

## Matemática

```java
Math.round(1.1f); // 1
Math.ceil(1.1);   // 2
Math.floor(1.9);  // 1
Math.max(10, 20); // 20
Math.min(10, 20); // 10
Math.random();    // número entre 0.0 e 1.0
```

**Número aleatório entre 0 e 100 (inteiro):**

```java
int n = (int) (Math.random() * 100);
```

---

## Formatando Números

**Formato monetário:**

```java
NumberFormat currency = NumberFormat.getCurrencyInstance();
currency.format(1234567.891); // R$ 1.234.567,89
```

**Formato percentual:**

```java
NumberFormat percent = NumberFormat.getPercentInstance();
percent.format(0.1); // 10%
```

**Forma encadeada:**

```java
NumberFormat.getPercentInstance().format(0.1);
```

---

## Leitura de Input

```java
Scanner scanner = new Scanner(System.in);
System.out.print("Age: ");
byte age = scanner.nextByte();
System.out.println("You are " + age);
```

---

```java
System.out.print("Name: ");
String name = scanner.nextLine().trim();
System.out.println("You are " + name);
```

---

## Wrappers e Autoboxing

Tipos primitivos possuem classes wrapper: `Integer`, `Long`, `Double`, `Boolean`, etc.

```java
int x = 10;              // primitivo
Integer y = Integer.valueOf(10); // wrapper

List<Integer> list = List.of(1, 2, 3); // coleções usam wrappers
```

Autoboxing/desboxing converte automaticamente entre primitivo e wrapper:

```java
Integer a = 1; // autoboxing
int b = a;     // unboxing
```

---

## Precisão monetária: BigDecimal

Evite `float`/`double` para dinheiro. Use `BigDecimal` com `MathContext`/`RoundingMode`.

```java
BigDecimal price = new BigDecimal("10.90");
BigDecimal tax = price.multiply(new BigDecimal("0.18"));
BigDecimal total = price.add(tax).setScale(2, RoundingMode.HALF_UP);
```

---

## Inferência de tipo com `var` (Java 10+)

```java
var name = "Vini";          // String
var numbers = List.of(1, 2); // List<Integer>
```

Use quando o tipo for óbvio pelo lado direito. Não use em APIs públicas.

---

## Enums

```java
public enum Status { OPEN, CLOSED }

Status s = Status.OPEN;
```

Enums podem ter campos e métodos.

---

## Records (Java 16+)

Classes imutáveis concisas para transportar dados.

```java
public record Point(int x, int y) {}

var p = new Point(1, 2);
```

---

## Datas e Tempo (java.time)

```java
LocalDate today = LocalDate.now();
LocalDateTime now = LocalDateTime.now();
Period age = Period.between(LocalDate.of(2000, 1, 1), today);
```

Evite `java.util.Date`/`Calendar` em código novo.

---

## Valores padrão de campos

- `int`, `long`, `short`, `byte` → 0
- `float`, `double` → 0.0
- `boolean` → `false`
- Referências → `null`

Em variáveis locais, inicialize explicitamente.

---

## Overflow/Underflow

```java
int max = Integer.MAX_VALUE;
int overflow = max + 1; // valor negativo
```

Use `Math.addExact`, `multiplyExact` para detectar overflow.

---

## Comparações de ponto flutuante

Evite `==` com `double`/`float`. Compare com tolerância (epsilon).

```java
double a = 0.1 + 0.2; // 0.30000000000000004
boolean equals = Math.abs(a - 0.3) < 1e-9;
```