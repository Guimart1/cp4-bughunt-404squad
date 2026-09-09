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
No Spring Data JPA, `ConteudoRepository` e `UsuarioRepository` sao apenas interfaces Java que nao possuem implementacao concreta escrita pelo desenvolvedor. Em Java, e impossivel instanciar uma interface diretamente utilizando o operador `new`. Quando a aplicacao Spring Boot sobe, o container de Inversao de Controle (IoC) escaneia essas interfaces e cria dinamicamente em tempo de execucao uma classe proxy concreta via reflexao. Essa classe gerada pelo framework embute toda a infraestrutura de conexao JDBC, gerenciamento de sessoes do `EntityManager` do Hibernate, pool de conexoes e controle transacional. Ao anotar o atributo com `@Autowired` no `ConteudoController`, o Spring injeta esse Bean proxy gerenciado em escopo Singleton. Se utilizassemos `new ConteudoRepository()`, o codigo sequer compilaria; e caso instanciassemos uma classe manual sem o Spring, nao haveria conexao gerenciada com o banco Oracle, resultando em `NullPointerException` em qualquer chamada como `findById` ou `save`.

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)
Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

**Resposta:**  
O Spring Data JPA automatiza todo o codigo repetitivo (boilerplate) da camada de dados: abre e fecha conexoes, cria e parametriza `PreparedStatements`, itera linha a linha sobre o `ResultSet` e faz o mapeamento objeto-relacional (ORM) convertendo tabelas relacionais em entidades Java como `Conteudo` e `Usuario`. Em contrapartida, o JDBC tradicional com DAO manual ainda e insubstituivel quando se necessita de performance extrema em processamento em lote (Batch Processing de milhares de registros por segundo), consultas analiticas muito customizadas com multiplos joins/subqueries ou quando e preciso ter controle milimetrico sobre cursores e transacoes de baixo nivel no Oracle. O metodo `findByCategoria(String categoria)` funciona sem uma unica linha de codigo gracas ao recurso de Derived Query Methods: em tempo de inicializacao, o Spring Data quebra o nome do metodo usando convencoes semanticas (`findBy` + `Categoria`), identifica que a propriedade `categoria` existe na entidade `Conteudo` e gera em tempo de execucao a query JPQL/SQL equivalente (`SELECT c FROM Conteudo c WHERE c.categoria = :categoria`).

### 3. Exceções checked vs unchecked (Aula 11)
A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

**Resposta:**  
Excecoes que herdam de `Exception` sao verificadas (*checked*): o compilador Java obriga que elas sejam tratadas com `try-catch` ou declaradas explicitamente na assinatura dos metodos com a clausula `throws`, gerando alto acoplamento entre as camadas. Ja as excecoes que herdam de `RuntimeException` sao nao verificadas (*unchecked*): representam condicoes anormais ou quebras de regras de negocio que podem propagar livremente pela pilha de execucao sem sujar as assinaturas dos metodos intermediarios. Originalmente, a `ClassificacaoIndicativaException` era checked e nao possuia interceptador no `GlobalExceptionHandler`, fazendo com que o Spring Boot a interpretasse como uma falha interna nao tratada do servidor, retornando HTTP 500 generico. A solucao foi alterar a classe para `extends RuntimeException`, remover os `throws` de `Usuario.alugar` e `AluguelController.alugar`, e cadastrar um metodo no `@RestControllerAdvice` anotado com `@ExceptionHandler(ClassificacaoIndicativaException.class)` que devolve o status HTTP 403 Forbidden (ou 400) com o JSON `{ "erro": e.getMessage() }`, entregando a mensagem descritiva diretamente ao consumidor da API.

### 4. Sobrescrita vs sobrecarga (Aula 7)
Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

**Resposta:**  
Sobrescrita (*override*) e o mecanismo de polimorfismo dinâmico onde uma classe filha redefine um metodo herdado da classe pai, mantendo rigorosamente a mesma assinatura (mesmo nome, mesma quantidade e tipos de parametros e retorno compativel). Ja sobrecarga (*overload*) ocorre quando criamos metodos com o mesmo nome na mesma classe ou hierarquia, porem com listas de parametros diferentes (tipos ou quantidades distintas), sendo resolvidos estaticamente em tempo de compilacao. Na classe `Serie`, o metodo foi criado como `calcularPrecoAluguel(double desconto)`. Como recebia um argumento `double`, o compilador o considerou um metodo novo e independente, e nao uma sobrescrita do metodo sem argumentos definido em `Conteudo`. Quando o endpoint de aluguel invocava polimorficamente `conteudo.calcularPrecoAluguel()`, o Java executava o metodo da classe mae (que cobrava 9,90 ou 0,00), ignorando totalmente a regra de 4,90 por temporada. Se a anotacao `@Override` estivesse presente sobre o metodo com parametro, o compilador apontaria erro de compilacao imediato avisando que a superclasse nao possui um metodo com essa assinatura, impedindo o bug antes mesmo de subir a aplicacao.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)
Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

**Resposta:**  
A blindagem do modelo de dominio deve ser distribuida de acordo com o ciclo de vida dos dados:
1. **Construtores:** Devem conter validacoes de existencia fundamental (invariantes de criacao), tais como impedir `duracaoMinutos <= 0`, `nome == null` ou `creditos < 0`. Isso assegura o principio do Fail-Fast, garantindo que nenhum objeto nasca em estado ilegal na memoria da JVM.
2. **Setters:** Devem validar qualquer mutacao individual de atributo que ocorra durante o ciclo de vida do objeto (ex.: `setDuracaoMinutos` e `setCreditos`), garantindo que alteracoes posteriores nao corrompam o estado. Uma boa pratica e fazer o proprio construtor delegar a atribuicao aos setters (`setDuracaoMinutos(duracaoMinutos)`).
3. **Metodos de Dominio:** Devem validar regras de transicao de estado que dependem do relacionamento entre multiplos objetos ou campos compostos. No projeto, o metodo `Usuario.alugar(Conteudo c)` e o lugar correto para checar se `!conteudo.isDisponivel()`, se `usuario.getIdade() < conteudo.getClassificacaoEtaria()` e se `temCreditosSuficientes(preco)`.
Validar apenas em um lugar e insuficiente: se validarmos apenas no controller, metodos internos, servicos ou testes unitarios podem instanciar objetos invalidos diretamente via Java; se validarmos apenas no banco, teremos erros de SQL tardios e caros; e validar apenas no construtor permite corrupcao posterior via setters publicos.

### 6. Abstração e interface (Aulas 8 e 9)
`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

**Resposta:**  
A classe abstrata `Conteudo` define a identidade estrutural e o nucleo comum do dominio atraves de uma relacao de heranca (*"e-um"*): todo Filme, Serie ou Documentario **e** um Conteudo, herdando atributos persistidos no banco (`id`, `titulo`, `categoria`, `duracaoMinutos`, `classificacaoEtaria`, `disponivel`) e sendo forcado a implementar a regra essencial de precificacao pelo metodo abstrato `calcularPrecoAluguel()`. Ja a interface `Promocionavel` define um comportamento/capacidade contratual opcional (*"faz-um"* ou *"pode-ser"*): apenas conteudos que possuem a capacidade de receber descontos a implementam. Se o `Documentario` passasse a ter promocoes:
- **Classes modificadas:** Apenas `Documentario.java` (adicionando `implements Promocionavel` na declaracao da classe e implementando o metodo `@Override public double aplicarPromocao(double preco) { return preco * 0.8; }`).
- **Classes que ficariam intactas:** `Conteudo.java`, `Filme.java`, `Serie.java`, `Usuario.java`, `ConteudoController.java`, `AluguelController.java`, `GlobalExceptionHandler.java` e todos os repositories.
Isso evidencia o principio **Open/Closed Principle (OCP)** do SOLID: a arquitetura do StreamFIAP esta aberta para extensao (novos tipos e novas capacidades promocionais podem ser adicionados facilmente), mas fechada para modificacao, preservando os controladores e as entidades existentes sem risco de regressao de bugs.

---

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```
O checkpoint proporcionou uma excelente experiência prática de code review e refatoração de código legado, simulando o dia a dia de uma equipe de desenvolvimento ao lidar com bugs de polimorfismo, convenções do Spring Boot e regras de negócio de domínio.
```
