# Criando Funções e Classes em Java

## Sintaxe de uma Função em Java

Para criar uma função em Java, usamos:

```java
ReturnType nomeDaFuncao(parametros) {
    // código
}
```

Se a função **não** tiver um tipo de retorno específico, usamos:

```java
void nomeDaFuncao(parametros) {
    // código
}
```

## Função `main`

Todo programa em Java sempre tem uma função `main()`.  
Essa função é chamada **sempre que executamos o código**.

> Porém, as funções não podem existir sozinhas e devem estar dentro de uma **classe**.

## Classe Principal (`Main`)

Uma classe é um container para funções (métodos) relacionadas a ela.

Para criar uma classe principal:

```java
class Main {
    void Main() {
        // ...
    }
}
```

As funções dentro das classes são chamadas de **métodos**, ou seja, funções que fazem parte de uma classe.  
**Todas as funções em Java pertencem a uma classe.**

## Modificadores de Acesso (Access Modifiers)

Todas as classes devem ter um *Access Modifier*, que é uma *keyword* que determina quais outras classes e métodos podem acessar essa classe/método.

Exemplos:

- `public`
- `private`

Exemplo:

```java
public class Main {
    void Main() {
        // ...
    }
}
```

## Convenções de Nomenclatura

- A primeira letra de **toda classe** é **maiúscula**
- Em **funções (métodos)**, a primeira letra é **minúscula**, mas a primeira letra da **segunda palavra** é **maiúscula**

Exemplo:

```java
public class Oi {
    void oiTudoBem() {
        // ...
    }
}
```

## Packages

Usamos **packages** para organizar bem as nossas classes.

- A base do nome de um *package* costuma ser o **nome do domínio ao contrário**, por exemplo:

```
rideday
```

## Strings

Usar **aspas duplas (`"`)** para declarar strings.

## Por Trás dos Panos

### Compilação

A IDE compila o código Java para **Java ByteCode**.

### Execução

O **Java Runtime Environment (JRE)** tem uma **Java Virtual Machine (JVM)**,  
que pega o ByteCode e o transforma em **Native Code** (código específico do sistema operacional).