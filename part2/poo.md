# A Essência da Programação e a Resolução de Problemas

No desenvolvimento de software, nosso objetivo principal é resolver problemas. Para isso, utilizamos diferentes paradigmas de programação, sendo a Programação Orientada a Objetos (POO) um dos mais poderosos e difundidos.

## O que é a Programação Orientada a Objetos (POO)?

A POO é uma abordagem na qual o código é estruturado em torno do conceito de objetos. Em vez de pensar em funções e dados de forma separada, a POO nos ensina a pensar em unidades autocontidas que representam entidades do mundo real ou conceitos abstratos.

### Um objeto contém duas coisas principais:

1. **Dados (Estado):**
   - Características ou atributos do objeto
   - Exemplo Carro: cor, marca, velocidade atual
   - Exemplo ContaBancaria: saldo, número da conta, titular

2. **Operações (Comportamento):**
   - Ações que o objeto pode executar
   - Exemplo Carro: `acelerar()`, `frear()`, `ligar()`
   - Exemplo ContaBancaria: `depositar(valor)`, `sacar(valor)`, `consultarSaldo()`

> A grande força da POO é juntar os dados e os métodos que trabalham esses dados em um único objeto, criando componentes lógicos, reutilizáveis e mais fáceis de gerenciar.

## O Processo de Resolução de Problemas (Problem Solving)

### Quatro Etapas Principais:

1. **Definir o Problema**
   - Entender o desafio
   - Identificar requisitos
   - Estabelecer objetivos

2. **Identificar e Explorar Soluções**
   - Gerar diferentes abordagens
   - Considerar estratégias alternativas

3. **Analisar e Comparar as Soluções**
   - Avaliar prós e contras
   - Considerar eficiência, custo e complexidade

4. **Escolher e Implementar a Melhor Solução**
   - Selecionar a abordagem mais adequada
   - Implementar considerando o contexto

## Conectando a POO com a Resolução de Problemas

### 1. Definindo o Problema (Modelagem)
- Identificar "substantivos" do problema
- Transformar em classes
- Estruturar em componentes lógicos

### 2. Identificando Soluções (Interação de Objetos)
- Como objetos interagem entre si
- Fluxos e lógicas de negócio
- Padrões de comunicação

### 3. Analisando e Comparando (Coesão e Acoplamento)
- Avaliar coesão dos objetos
- Verificar nível de acoplamento
- Buscar independência entre componentes

## Exemplo Prático: Sistema de uma Biblioteca

### Definição do Problema
Criar um sistema para gerenciar empréstimos de livros.

### Classes Principais
```java
public class Livro {
    private String titulo;
    private String autor;
    private boolean estaDisponivel;
    
    public void marcarComoEmprestado() {
        this.estaDisponivel = false;
    }
}

public class Membro {
    private String nome;
    private String idMembro;
    
    public void pegarLivroEmprestado(Livro livro) {
        livro.marcarComoEmprestado();
        new Emprestimo(livro, this);
    }
    
    public void devolverLivro(Livro livro) {
        // Implementação
    }
}

public class Emprestimo {
    private Livro livro;
    private Membro membro;
    private LocalDate dataDevolucao;
    
    public boolean verificarAtraso() {
        return LocalDate.now().isAfter(dataDevolucao);
    }
}
```

### Escolha da Solução
A solução ideal mantém responsabilidades claras:
- `Membro` inicia o empréstimo
- `Livro` controla sua disponibilidade
- `Emprestimo` gerencia prazos e status

> Em resumo, a POO oferece um método estruturado para decompor problemas complexos em partes menores e mais gerenciáveis (os objetos), tornando o processo de encontrar e implementar a melhor solução muito mais interessante.
