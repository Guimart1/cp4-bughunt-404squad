# Checkpoint 4 — Bug Hunt StreamFIAP

> Copie este arquivo para a raiz do seu repositório com o nome **README.md**
> e preencha todas as seções.

## Identificação

**Grupo:** 404Squad

| Integrante | RM | Turma |
|---|---|---|
| Guilherme Martins | 566570 | 2CCPX |
| Gabriel Rodrigues | 566475 | 2CCPX |
| Julia Lopes | 566557 | 2CCPX |
| Claus Moreira | 566503 | 2CCPX |

| Campo | |
|---|---|
| **Total de bugs corrigidos** | 12 / 12 |
| **Total de ajustes de Clean Code** | 6 / 6 |

---

## Parte 1 — Bugs encontrados

> Uma linha por bug, na ordem em que você os encontrou. Use a numeração dos seus
> commits (`fix: bug01 ...`). Preencha TODAS as colunas — metade da nota está aqui.

| # | Sintoma observado (o que fiz/vi) | Causa raiz (arquivo e linha aproximada) | Correção aplicada | Conceito da disciplina |
|---|---|---|---|---|
| bug01 | Cadastro aceitava `duracaoMinutos <= 0` sem recusar a requisicao | `Conteudo.java` (~L54): Ausencia de validacao defensiva no setter e construtor de `duracaoMinutos` | Adicionada verificacao no setter lancando `IllegalArgumentException` se `duracaoMinutos <= 0` | Encapsulamento e Invariantes de Dominio (Aulas 3 e 4) |
| bug02 | Preco promocional de Filme aumentava 20% em vez de conceder desconto | `Filme.java` (~L25): `aplicarPromocao` multiplicava o preco por `1.2` | Ajustada a expressao para `preco * 0.8`, aplicando o desconto correto de 20% | Polimorfismo e Interfaces (Aulas 8 e 9) |
| bug03 | Subclasses podiam herdar preco fixo indevido de 9.90 | `Conteudo.java` (~L33): `calcularPrecoAluguel` possuia implementacao concreta com retorno fixo | Metodo tornado `abstract`, forcando cada subclasse a implementar sua regra de preco | Classes Abstratas e Metodos Abstratos (Aula 8) |
| bug04 | Documentario cobrava R$ 9,90 de aluguel em vez de ser gratuito | `Documentario.java` (~L18): Nao sobrescrevia `calcularPrecoAluguel()`, herdando valor da superclasse | Sobrescrito `calcularPrecoAluguel()` retornando explicitamente `0.0` | Sobrescrita de Metodos e Heranca (Aulas 7 e 8) |
| bug05 | Construtor de Serie nao inicializava dados da classe mae Conteudo | `Serie.java` (~L14): Construtor nao chamava `super(...)` e omitia `disponivel` | Adicionada chamada `super(titulo, categoria, duracaoMinutos, classificacaoEtaria, disponivel)` | Construtores em Heranca e palavra-chave super (Aulas 5 e 6) |
| bug06 | Serie ignorava preco por temporada (R$ 4,90) ao ser alugada | `Serie.java` (~L21): Metodo declarado com parametro `double desconto`, gerando sobrecarga acidental | Removido o parametro e adicionada anotacao `@Override` para correta sobrescrita polimorfica | Sobrescrita vs Sobrecarga de Metodos (Aula 7) |
| bug07 | Cadastro de documentario acessava campo privado duracaoMinutos diretamente | `ConteudoController.java` (~L83): Controller tentava acessar atributo privado direto | Ajustado para utilizar o metodo acessor `getDuracaoMinutos()` | Encapsulamento e Metodos Acessores (Aula 3) |
| bug08 | Cadastro de usuario salvava campo nome como null no banco | `Usuario.java` (~L22): Expressao `nome = nome;` no construtor causava shadowing | Alterado para `this.nome = nome;`, atribuindo o parametro ao atributo de instancia | Escopo de Variaveis e Referencia this (Aula 2) |
| bug09 | Cadastro de usuario falhava por chave primaria nula ou sem auto-incremento | `Usuario.java` (~L11-12): Campo `@Id private Long id;` sem anotacao de geracao | Adicionada anotacao `@GeneratedValue(strategy = GenerationType.IDENTITY)` sobre o id | Mapeamento JPA e Chaves Primarias (Aula 13) |
| bug10 | Usuario com 0 creditos alugava e usuario com creditos suficientes era bloqueado | `Usuario.java` (~L28): Operador relacional invertido em `temCreditosSuficientes` (`preco >= creditos`) | Corrigida a comparacao para `return this.creditos >= preco;` | Logica Booleana e Metodos de Dominio (Aula 2) |
| bug11 | Conteudo indisponivel (disponivel == false) podia ser alugado normalmente | `Usuario.java` (~L37): Metodo `alugar` nao checava a flag `isDisponivel()` de Conteudo | Inserida validacao `if (!c.isDisponivel()) throw new ConteudoIndisponivelException(...)` | Validacao de Estado em Metodos de Negocio (Aula 4) |
| bug12 | Usuario menor que classificacao etaria recebia erro 500 generico | `ClassificacaoIndicativaException.java` estendia `Exception` (checked) sem handler | Convertida para `RuntimeException` e adicionado `@ExceptionHandler` retornando HTTP 403 | Checked vs Unchecked Exceptions e Advice (Aulas 11 e 13) |

---

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 | `Conteudo.java` e `ConteudoController.java` | **Quebra de Encapsulamento:** Campo `public int duracaoMinutos;` acessado diretamente nos controllers | Atributo tornado `private`, com criacao de `getDuracaoMinutos()` e `setDuracaoMinutos(int)` com validacao |
| clean02 | `ConteudoController.java` (L87 a 101) | **Codigo Morto e Comentarios Obsoletos (YAGNI/KISS):** Metodo nao utilizado `calcularDescontoAntigo` e codigo de cupom comentado | Removido o metodo zumbi e os blocos comentados de codigo inativo, confiando a rastreabilidade ao Git |
| clean03 | `Usuario.java` (L52 a 60) | **Single Responsibility Principle (SRP):** Entidade de dominio JPA executando `System.out.println` de comprovante/recibo no console | Removidas todas as impressoes no console da entidade de dominio, mantendo a classe focada em regras de negocio |
| clean04 | `Usuario.java` (`alugar`) | **Nomes Reveladores de Intencao:** Variaveis e parametros de uma letra (`c`, `p`) que prejudicam a legibilidade | Renomeados os identificadores para nomes autoexplicativos (`conteudo`, `preco`) |
| clean05 | `ConteudoController.java` e `GlobalExceptionHandler.java` | **Re-invencao da Roda e Tratamento Centralizado:** Loop manual em memoria e falta de interceptacao de `IllegalArgumentException` | Uso do derived query method `findByCategoria` e adicao de `@ExceptionHandler(IllegalArgumentException.class)` com status 400 |
| clean06 | `Usuario.java` (construtor e setter) | **Blindagem de Estado e Programacao Defensiva (Fail-Fast):** Objeto aceitava saldo de creditos negativo em memoria | Adicionada validacao defensiva em `setCreditos(double)` e no construtor impedindo saldo negativo |

---

## Parte 3 — Perguntas de reflexão

> Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do projeto
> como exemplo**. Respostas genéricas de tutorial não pontuam.

### 1. Injeção de dependência (Aula 13)
Os controllers recebem os repositories via `@Autowired` (ex.: `ConteudoController`
usa `ConteudoRepository`). Explique por que o Spring precisa gerenciar esses objetos
em vez de criarmos com `new ConteudoRepository()`. O que exatamente o Spring faz ao
injetar um bean, e por que isso não funcionaria com um `new` comum?

**Resposta:**  
O primeiro motivo é bem simples: ConteudoRepository é uma interface Java, então nem sequer é possível dar new ConteudoRepository(), pois interfaces não podem ser instanciadas diretamente. O que o Spring faz por baixo dos panos é criar uma classe concreta em tempo de execução que implementa essa interface, já com toda a conexão com o banco Oracle configurada e pronta para salvar e buscar dados. Quando colocamos a anotação @Autowired no ConteudoController, o Spring simplesmente entrega essa instância pronta para o controller usar. Se tentássemos usar um new comum, além de não compilar, não haveria nenhuma conexão com o banco configurada, gerando NullPointerException assim que chamássemos qualquer método como findById ou save.

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)
Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

**Resposta:**  
O Spring Data JPA automatiza todo aquele trabalho repetitivo que fizemos na Aula 12: abrir e fechar conexões, montar comandos SQL no PreparedStatement, percorrer o ResultSet linha por linha e preencher os atributos de Conteudo ou Usuario na mão. Ele faz todo esse mapeamento sozinho. Por outro lado, o JDBC tradicional com DAO ainda é insubstituível quando precisamos de altíssima performance, como rodar consultas analíticas muito pesadas ou fazer inserções de milhares de registros em lote, onde o JPA seria mais lento. Já o método findByCategoria(String categoria) funciona porque o Spring lê o nome do método: ele reconhece o prefixo findBy e junta com o atributo categoria da entidade Conteudo, gerando a consulta SQL (WHERE categoria = ?) de forma automática em tempo de execução.

### 3. Exceções checked vs unchecked (Aula 11)
A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

**Resposta:**  
A diferença principal é que exceções que herdam de Exception são do tipo checked, o que obriga o código a tratá-las com try-catch ou declarar throws em todos os métodos por onde passam. Já as que herdam de RuntimeException são unchecked, permitindo que o erro suba livremente pela aplicação sem poluir as assinaturas dos métodos intermediários. No projeto, como a ClassificacaoIndicativaException era checked e não tinha interceptador, o Spring tratava o erro como uma falha interna inesperada e devolvia o status HTTP 500 genérico para o cliente. A solução foi mudar a classe para extends RuntimeException e cadastrar um método com @ExceptionHandler no GlobalExceptionHandler. Assim, quando um menor de idade tenta alugar um filme não permitido, a API captura a exceção e responde com status HTTP 403 Forbidden e um JSON contendo a mensagem exata da regra.

### 4. Sobrescrita vs sobrecarga (Aula 7)
Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

**Resposta:**  
Sobrescrita (override) acontece quando uma classe filha reescreve um método herdado da mãe mantendo rigorosamente a mesma assinatura (mesmo nome, mesmos parâmetros e retorno compatível). Já a sobrecarga (overload) ocorre quando criamos um método com o mesmo nome, porém com parâmetros diferentes, virando um método novo e independente. Na classe Serie, o método estava declarado como calcularPrecoAluguel(double desconto). Como recebia esse argumento desconto, o compilador entendeu como sobrecarga e não como sobrescrita. Com isso, na hora do aluguel, o controller chamava o método da mãe Conteudo (sem parâmetros), ignorando o cálculo por temporadas da série. Se o desenvolvedor tivesse colocado a anotação @Override no método da Serie, o compilador teria acusado erro imediatamente, avisando que a mãe não possuía nenhum método com aquele parâmetro double.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)
Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

**Resposta:**  
Cada tipo de validação protege um momento da vida do objeto:

No construtor, validamos dados obrigatórios para que o objeto não nasça com valores ilegais na memória, como impedir duracaoMinutos <= 0 ou titulo nulo ao criar um Filme.
Nos setters, validamos alterações individuais para evitar que o estado seja corrompido depois de criado, como fizemos no setCreditos(double) de Usuario para barrar valores negativos.
Nos métodos de negócio do model, validamos regras que dependem de vários dados juntos, como no método Usuario.alugar(Conteudo c), que precisa checar ao mesmo tempo se o conteúdo está disponível, se o usuário tem a idade mínima e se possui saldo suficiente.
Validar em apenas um lugar não basta: se validarmos só no controller, chamadas internas ou testes unitários em Java podem criar objetos inválidos; e validar só no banco gera erros de SQL tardios em vez de mensagens claras no sistema.

### 6. Abstração e interface (Aulas 8 e 9)
`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

**Resposta:**  
A classe abstrata Conteudo representa o que o objeto é (herança): ela centraliza os atributos comuns a qualquer item do catálogo (id, título, categoria, duração) e obriga todas as subclasses a implementarem o cálculo de aluguel. Já a interface Promocionavel define o que o objeto pode fazer (um comportamento opcional): apenas alguns tipos de conteúdo participam de promoções. Se o Documentario passasse a ter promoções, precisaríamos alterar apenas a classe Documentario.java, adicionando implements Promocionavel na declaração e escrevendo o método aplicarPromocao. Todas as outras classes (Conteudo, Filme, Serie, Usuario, controllers e repositories) continuariam 100% intactas. Isso demonstra que o sistema tem baixo acoplamento e segue o princípio de estar aberto para novas extensões sem risco de quebrar o que já funciona

---

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```
```
