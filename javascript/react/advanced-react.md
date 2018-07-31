# Advanced React

## Working with an Asynchronous API

### Dealing with an asynchronous API on the Client

* Usar **axios** library
  * Lembrar que axios retorna o conteúdo dentro de outro objeto (***data***).
* Fetch data em ```componentDidMount```
    * Usar ```async``` - ```await```, pois a chamada à **api** é _assíncrona_.
    * ```setState``` para renderizar o componente com os novos dados.
    * **ATENÇÃO**: ```componentDidMount``` **não executa no server side**
* Cuidado ao renderizar o componente
    * Ele será renderizado antes de ```componentDidMount```
    * Alguns valores podem não estar inicializados corretamente
    * Solução:
      * Criar um estado padrão ou renderizar outra coisa

### Dealing with an asynchronous API on the Server

* Usar **axios** library
* Fornecer o **estado inicial** (_initialState_)
  * Fornecer os dados de estado inicial ao componente a ser renderizado para que ele possa definir o seu _initalState_ a partir das ***props*** fornecidas.
* Lembrar que no dom renderer é necessário fornecer também o ***initialState***  
* Deixar o componente ```App``` ***server-side friendly***
  * Ajudar o **initialData** para receber o valor via **props**
  * Obter os dados (ex.: via axios) e fornecer ao componente via props
* Mas e o ***client - side***?
  * Precisamos fornecer o **initialData** também via **props**
* Agora temos um problema:
  1. A aplicação é renderizada inicialmente no server
  1. Chega até o browser e é a vez do client - side operar.
      * O initialData é vazio
      * Tentará substituir o conteúdo vindo do server renderizando o componente com o **estado vazio** 
  1. Lembrando que ```componentDidMount``` tenta obter os dados via axios, levando a uma terceira renderização.
* E agora?

### Delivering the Inital Data

* Precisamos **sincronizar a renderização** server-side com a client-side.
  * Obter os ***dados reais*** no client-side e **não passar o initialData vazio**.
  * Como obter esses dados?
  * No *server render*, além de passar para o template a string do html, podemos também **injetar os dados** no objeto ```window```.
    * Lembrando que precisamos ***stringificar*** (```JSON.stringify```) os dados no template (**não existe DOM no server-side**).

### Reading state from the state manager

* Tranformar **DataApi** em **StateApi**.
  * Não vamos mais obter os dados pedaço por pedaço
  * Vamos solicitar ao StateApi qual o **estado atual**.
* No ***client - site***, utilizar também StateApi para obter os dados.
  * Criar **StateApi** com os dados que estão no objeto ```window```
* No componente ```App``` o estado agora será o conteúdo que está no **store** que será obtido via ```getState()```
* Não preciso calcular o state toda vida que chamar ```getState()```. Posso armazenar o valor no contrutor e só retorná-lo.
* ```lookupAuthor(authorId)``` não precisa ser criado no componente. Possu deixá-lo no StateApi, afinal os dados estão lá.
* **ATENÇÃO**: Lembrar dos bindings.
  * em **StateApi** posso usar arrow functions nos métodos que utilizam ```this```, evitando fazer binding no construtor.
* Agora no lugar de passar ```lookupAuthors``` componentes abaixo, posso passar a **store** em ```App```
* Agora temos que o ***estado da aplicação*** é gerenciado em **StateApi**
* Falta somente **consertar os erros nos testes**.

## The Context API and Higher Order Components

### Type-checking with PropTypes

* Use ```PropTypes``` para descobrir ***erros de tipagem*** mais facilmente
* **prop-types** package

### React's Context API

* Define o **Context** object
  * `getChildContext()`
    * O **retorno** dessa função será o context object
* Define o **Context** Type
  * O **tipo do Context object** precisa ser definido
* **Todo componente** que for utilizar o objeto do Context precisa **declarar o Context type* a ser recebido
* `context` fica disponpivel como **segundo argumento** a ser recebido pelo componente
```javascript
const MyComponent = (props, context) => {...};
```

### _Shallow rendering_ with **Enzyme**

* **react-test-renderer** renderiza uma árvore inteira de componentes
  * Isso dificulta testar os componentes em unidades independentes
    * Principalmente se existe objetos globais (ex. context) envolvidos
* Uma melhor opção é utilizar o recurso de _shallow rendering_
  * Somente o componente imediato é renderizado (sem seus children)
  * Os testes unitários ficam mais simples
* Para isso, vamos utilizar a lib **Enzyme**, do **airbnb** no lugar da lib **react-test-renderer**
* No **react 16**, deve adicionar a lib **enzyme-adapter-react-16**
  * é necessário adicionar um arquivo de setup dos testes e adicionar um adapter ([enzyme docs com mais detalhes](http://airbnb.io/enzyme/docs/installation/react-16.html))
    1. Crie o arquivo jest.config.json na raíz do projeto
    2. Adicione o seguinte conteúdo:
    ```json
    {
      "setupFiles": [
        "<rootDir>/path/to/tests_dir/setupTests.js"
      ]
    }
    ```
    3. Crie o arquivo setupTests.js no caminho acima e adicione o seguinte conteúdo:
    ```javascript
    import { configure } from 'enzyme';
    import Adapter from 'enzyme-adapter-react-16';

    configure({ adapter: new Adapter() });
    ```
### The Context API and Higher Order Components : Presentational Components and Container Components

* Article usa context.
  * Para testá-lo, precisamos de um fake para o context
  * Uma nova estratégia:
    * Dividir Article em 2 componentes: **Container** e **Presentation**
    * **Container** é responsável por **extrair store do context**.
    * **Presentation** é responsável pela parte visual
  * **Container** é quem deve definir *contextTypes* e passar store para o **Presentation** component via *props*

### The Context API and Higher Order Components : Higher Order Components

* O Container não seŕa mais definido manualmente.
* Será gerado por uma **Higher Order Function**, criando o que se chama **Higher Order Component**, que é o Container *'wrappeado'* e com a store injetada nas props
* Esse **Higher Order Component** pode ser criado como uma `function` ou `class`. Normalmente é preferível uma class porque é comum o **Higher Order Component** lidar com eventos de ***lifecycle***

## Subscribing to State

### Using the setState Function

* Criar um search bar
  * usar `<input type="search" />`
    * Muda o comportamento do input
  * Fazer ***debounce*** do `onChange`


## Performance Optimization

### Performance Optimization : Perf Addons: Avoiding Wasteful Rerenders with PureComponent

> Perf Addons não funciona no React 16

* Function components **sempre** re-renderizam
* Para controlar isso teremos que usar pure components, por exemplo
* Ou usar um *Higher Order Component* para cuidar das otimizações de renderização, como no ***react-redux***
* Evite function components (a não ser utilizando um HoC)
  * Prefira ***Pure Component***
* 