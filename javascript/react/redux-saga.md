# Redux Saga Course - Daniel Stern (Pluralsight)

## Introducing Redux Saga

### Installing and Configuring

- O middleware deve ser primeiro da cadeia (***validar na doc***)
- ***Keywords***:
  - generator functions
  - Saga utilities
    - delay - cria uma promise que resolve depois de um tempo (delay)
  - yield

### Creating your first Saga

- agrupar as sagas por funcionalidade
- criar função que inicializa todas as sagas
- Executa a inicialização das sagas após ter a store configurada (**obrigatório**)

## Asynchronous ES6 and yield

### yield
  - delay execution of subsequent
  - Precisa de uma generator function: function*
  - Funciona muito bem com promises
  - **Vantagens**:
    - Menos código
    - Sem ***Callback Hell***
    - Mais fácil de ler e debugar
    - Execução para nos erros não tratados (!)
  - **Desvantagens**:
    - Precisar estar dentro de uma generator function
    - Exige mais dependências
    - Tratamento de erros deve ser explícito
    - Execução para nos erros não tratados (!)  

### Generator Functions
  - `function* () {}`
    - iterator creators
  - libs: **co**, **koa**, ...
    - co ***consome*** o generator e retorna uma promise
    - `co.wrap()` retorna uma função wrapper para consumir o generator em um outro momento
  - redux-saga `run()`
    - executa uma saga
    - Não retorna nada (***undefined***)
    - Apenas consome o generator passado

## Redux Saga (Declarative) **Effects**
  - Providos pelo redux saga
  - Retornam objetos com instruções para o redux saga interpretar.
  - Não fazem nada por si só.
    - São simples ***POJOs***
  - É o middleware, redux-saga, o responsável por gerar os ***side effects***

### Effects

São objetos javascript simples (POJOs) que contém instruções a serem interpretadas pelo middleware.

- São providos pelo pacote `redux-saga/effects`

#### take
  - Pausa e espera por uma ***action***

#### put
  - ***despacha uma action*** para o restante da app
    - O resultado é como se chamasse a função `dispatch`
  - **Não pausa o código**
  - Pode ser usado para **passar dados para outra saga**, por exemplo uma que tenha `take` para a mesma action.

#### call
  - Chama a execução de um método
  - Será executado pelo redux saga
  - Forma recomendada de chamar execução de métodos / funções
  - Ajuda nos testes
    - **Deixa a saga mais testável**
    - Evita ter que mockar as dependências
      - `call` retorna um objeto simples de testar
    - O mesmo se aplica a `apply` e `fork`

#### apply
  - Mesmo que `call`,
    - invoca um método
    - mas **define o contexto de Execução**
    - caso precise fazer **bind** do ***this***
    - Outra opção seria usar `call` chamando a função com o **bind já realizado**.

#### fork
  - Funciona como `call`
    - invoca um método
    - Mas não pausa a execução de quem o chamou
      - **Não é possível obter yielded variables do fork**
    - Cria um child process que depende do parent (quem chamou ***fork***)
      - Se parent dispara um erro ou é cancelado, todos os processos ***forkados*** são cancelados
        - Os processos filhos podem usar blocos ***finally*** para tratar esses casos
    - **TIP**: Ao usar yield em um `Array.map` onde tudo que é retornado
    é de um tipo que pode ser yielded (<span style="color:red">**a ver ?**</span>), podemos colocar yield antes da chamada a `Array.map`

#### takeEvery
  - Funciona como `take`
    - Mas ***forka*** cada vez que a action especificada é despachada
  - Precisa do nome da action que deve "***pegar***" e qual a saga que irá ser forkada para tratar
  - Código não pausa
    - Forka e segue adiante
  - Acaba por servir para criar ***watchers*** que vão ser registrados pelo middleware e ficarão escutando as actions específicas, repassando-as para a saga também especificada no `takeEvery`

#### cancel & Cancelled
  - `cancel`
    - Pára um processo forkado
    - Somente pode ser executado em um `yield`
      - Após ser chamado, processo pára no próximo yield
    - O bloco `finally` é executado para tratar o cancelamento
    - Cancelar um processo cancela também todos os que foram forkados a partir dele
    - `cancelled` é um método que indica se o processo foi mesmo cancelado ou não
      - Um erro não tratado também pode parar um processo
      - Pode ser necessário distinguir as duas situações
      - deve ser chamado em um `yield`

#### takeLatest
  - Combina `fork`, `takeEvery` e `cancel`
  - Forka processos filhos cada vez que a ação esperada é despachada
    - Mas mantém apenas um desses processo executando
      - o último
  - Ao iniciar a execução de um processo, quando a próxima ação chegar esse
  processo em execução é cancelado e só então o referente à última action é forkado.

#### select
  - Retorna uma cópia do ***state atual*** da **store** a qual a saga está conectada
  - precisa do `yield`
  - permite passar selectors como parâmetro. Eles serão executados e o retorno sera devolvido à saga.

#### spawn
  - Quase igual ao `fork`
    - A diferença é que os **processos criados não são filhos do que chamou o spawn**
      - Eles não são cancelados se o processo que os criou for parado de alguma forma (cancelado ou por erro não tratado)

#### all
  - Combina varios `take` em um só
  - Quando todas as actions forem despachadas o código continua

## Channels

### What are Channels?

- Permite comunicação entre 2 sagas
- **3 tipos**
  - **Action** Channels
  - **Event** Channels
  - **Generic** Channels
- Actions comuns permitem essa comunicação
  - Mas channels tornam possível ***comunicação privada*** (?confirmar?) entre sagas

### Action Channels
  - ***Buffer***
  - Registram **todos os eventos** de um certo tipo (***action type*** ou ***event***)
  - `take` recebe um channel
    - Pega o mais antigo do channel e o remove
      - Não pega mais diretamente
  - Sagas que tratam o evento estão ocupadas
    - Subsequente eventos são perdidos
    - um action channel permite **estocar** os eventos, que passam a ser
    consumidos do buffer
      - sem perdas
      - sistema puxado, não mais empurrado
  - O ***buffer*** é **limitado**

### Generic Channels
  - Comunicação entre 2 sagas
    - Cria uma **linha de comunicação especial** entre as sagas
    - Sem necessidade de ***action types***
    - As actions enviadas por esse canal são tidas como de uso particular

### Event Channels

### Shipping Saga

### Tax Rate Saga

### Checkout Availability Saga

### Checkout Saga

## Testing Redux Saga Applications

## Conclusion
