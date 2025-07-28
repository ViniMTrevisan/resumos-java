# Java Básico – Estruturas de Controle, Operadores e Loops

## Operadores de Comparação

Como em outras linguagens:

- `==` igual  
- `!=` diferente  
- `>`  maior que  
- `<`  menor que  
- `>=` maior ou igual  
- `<=` menor ou igual  

---

## Operadores Lógicos

- `&&` (AND) – ambas as expressões devem ser verdadeiras  
- `||` (OR) – pelo menos uma deve ser verdadeira  
- `!` (NOT) – inverte o valor booleano  

---

## Estruturas Condicionais (`if`, `else if`, `else`)

Exemplo:

```java
int temp = 32;

if (temp > 30) {
    System.out.println("It is a hot day");
    System.out.println("Drink Water");
}
else if (temp > 20)
    System.out.println("Beautiful day");
else
    System.out.println("Cold day");
```

- Chaves `{}` são opcionais quando há apenas uma linha dentro do bloco.  
- A indentação torna o código mais legível.

---

## Operador Ternário (`? :`)

Permite escrever uma condição em uma única linha.

```java
int income = 120_000;
String className = income > 100_000 ? "First" : "Economy";
System.out.println(className);
```

---

## `switch` Statement

Usado para avaliar diferentes casos com base no valor de uma variável.

```java
String role = "Admin";

switch (role) {
    case "Admin":
        System.out.println("Admin");
        break;
    case "User":
        System.out.println("User");
        break;
    case "Moderator":
        System.out.println("Moderator");
        break;
    default:
        System.out.println("Invalid role");
}
```

- Use `break` após cada `case`.  
- `default` funciona como o `else`.

---

## `for` Loop

```java
for (int i = 0; i < 5; i++) {
    System.out.println("Hello World");
}
```

---

## `while` Loop

```java
int i = 0;
while (i < 5) {
    System.out.println("Hello World");
    i++;
}
```

Com entrada do usuário:

```java
Scanner scanner = new Scanner(System.in);
String input = "";

while (!input.equals("quit")) {
    System.out.print("Input: ");
    input = scanner.next().toLowerCase();
    System.out.println(input);
}
```

---

## `do-while` Loop

Executa pelo menos uma vez:

```java
do {
    System.out.print("Input: ");
    input = scanner.next().toLowerCase();
    System.out.println(input);
} while (!input.equals("quit"));
```

---

## `break` e `continue`

```java
while (true) {
    System.out.print("Input: ");
    input = scanner.next().toLowerCase();

    if (input.equals("pass")) {
        continue; // volta ao início do loop
    }

    if (input.equals("quit")) {
        break; // encerra o loop
    }

    System.out.println(input);
}
```

---

## `for-each` Loop

```java
String[] fruits = {"Apple", "Banana", "Mango"};

for (String fruit : fruits) {
    System.out.println(fruit);
}
```

- O tipo da variável deve bater com o tipo da array.  
- Exemplo: se a array for `String[] oi`, o loop seria `for (String i : oi)`  
- Não permite acessar o índice.  
- Só itera no sentido crescente.