# clean-code-javascript

## Índice
  1. [Introdução](#introdução)
  2. [Variáveis](#variáveis)
  3. [Funções](#funções)
  4. [Objetos e Estruturas de Dados](#objects-and-data-structures)
  5. [Classes](#classes)
  6. [Testes](#testing)
  7. [Concorrência](#concurrency)
  8. [Tratamento de Erros](#error-handling)
  9. [Formatação](#formatting)
  10. [Comentários](#comments)

## Introdução
![Imagem humorística da estimativa de qualidade do software baseado na contagem de quantos palavrões você gritou enquanto lia o código.](http://www.osnews.com/images/comics/wtfm.jpg)

Princípios da Engenharia de Software, do livro de Robert C. Martin
[*Código Limpo*](https://www.amazon.com.br/C%C3%B3digo-Limpo-Habilidades-Pr%C3%A1ticas-Software/dp/8576082675),
adaptados para JavaScript. Isto não é um guia de estilos. É um guia para se produzir código legível, reutilizável e refatorável em JavaScript.

Nem todo principio demonstrado deve ser seguido rigorosamente, e ainda menos são os que possuem consenso universal. São orientações e nada mais, entretanto, foram usadas em código durante muitos anos de experiencia coletiva pelos autores de *Código limpo*.

Nosso oficio de engenharia de software tem pouco mais de 50 anos, e ainda estamos aprendendo muito. Quando a arquitetura de software for tão velha quando a própria arquitetura, talvez então tenhamos regras mais rígidas para seguir. Por enquanto, deixe que estas orientações sirvam como critério para se avaliar a qualidade de código JavaScript que tanto você e o seu time produzirem.

Mais uma coisa: aprender isto não irá lhe transformar imediatamente em um desenvolvedor de software melhor, e trabalhar com eles por muitos anos não quer dizer que você não cometerá erros. Toda porcão de código começa com um rascunho, como argila molhada sendo moldada em sua forma final. Finalmente, talhamos as imperfeições quando revisamos com nossos colegas. Não se bata pelos primeiros rascunhos que ainda precisam de melhorias. Ao invés, bata em seu código.

## **Variáveis**
### Use nomes de variáveis que tenham significado e sejam pronunciáveis

**Ruim:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Bom**:
```javascript
const yearMonthDay = moment().format('YYYY/MM/DD');
```
**[⬆ voltar ao topo](#Índice)**

### Use o mesmo vocabulário para o mesmo tipo de variável

**Ruim:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Bom**:
```javascript
getUser();
```
**[⬆ voltar ao topo](#table-of-contents)**

### Use nomes pesquisáveis
Nós leremos mais código do que jamais seremos capazes de escreveremos. É importante que o código que escrevemos seja legível e pesquisável. *Não* dando nomes em variáveis que sejam significativos para entender nosso programa, machucamos nossos leitores. Torne seus nomes pesquisáveis. Ferramentas como [buddy.js](https://github.com/danielstjules/buddy.js) e [ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md) podem ajudar a identificar constantes sem nome.

**Ruim:**
```javascript
// Para que diabos serve 86400?
setTimeout(() => {
  this.blastOff()
}, 86400);

```

**Bom**:
```javascript
// Declare-as como `const` global em letras maiúsculas.
const SECONDS_IN_A_DAY = 86400;

setTimeout(() => {
  this.blastOff()
}, SECONDS_IN_A_DAY);

```
**[⬆ voltar ao topo](#table-of-contents)**

### Use variáveis explicativas
**Ruim:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityStateRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityState(address.match(cityStateRegex)[1], address.match(cityStateRegex)[2]);
```

**Bom**:
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityStateRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, state] = address.match(cityStateRegex);
saveCityState(city, state);
```
**[⬆ voltar ao topo](#table-of-contents)**

### Evite Mapeamento Mental
Explicito é melhor que implícito.

**Ruim:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Espera, para ques serve o `l` mesmo?
  dispatch(l);
});
```

**Bom**:
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```
**[⬆ voltar ao topo](#table-of-contents)**

### Não adicione contextos desnecessários
Se o nome de sua classe/objeto já lhe diz alguma coisa, não as repitas nos nomes de suas variáveis.

**Ruim:**
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Bom**:
```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Use argumentos padrões ao invés de curto circuitar ou condicionais

**Ruim:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  ...
}

```

**Bom**:
```javascript
function createMicrobrewery(breweryName = 'Hipster Brew Co.') {
  ...
}

```
**[⬆ voltar ao topo](#table-of-contents)**

## **Funções**
### Argumentos de funções (idealmente 2 ou menos)
Limitar a quantidade de parâmetros de uma função é incrivelmente importante porque torna mais fácil testá-la. Ter mais que três leva a uma explosão combinatória onde você tem que testar muitos casos diferentes com cada argumento separadamente.

Nenhum argumento é o caso ideal. Um ou dois argumentos é bom, e três deve ser evitado. Qualquer coisa a mais que isso deve ser consolidado. Geralmente, se você tem mais que dois argumentos então sua função esta tentando fazer muitas coisas. Nos casos em que não esta, na maioria das vezes um objeto é suficiente como argumento.

Já que JavaScript nos permite criar objetos instantaneamente, sem ter que escrever muita coisa, você pode usar um objeto se você se pegar precisando usar muitos argumentos.

**Ruim:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Bom**:
```javascript
const menuConfig = {
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
};

function createMenu(config) {
  // ...
}

```
**[⬆ voltar ao topo](#table-of-contents)**


### Funções devem fazer uma coisa
Essa é de longe a regra mais importante em engenharia de software. Quando funções fazem mais que uma coisa, elas se tornam difíceis de serem compostas, testadas e raciocinadas. Quando você pode isolar uma função para realizar apenas uma ação, elas podem ser refatoradas facilmente e seu código ficará muito mais limpo. Se você não levar mais nada desse guia além disso, você já estará na frente de muitos desenvolvedores.

**Ruim:**
```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Bom**:
```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Nomes de funções devem dizer o que elas fazem

**Ruim:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// É difícil dizer pelo nome da função o que é adicionado
addToDate(date, 1);
```

**Bom**:
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```
**[⬆ voltar ao topo](#table-of-contents)**

### Funções devem ter apenas um nível de abstração
Quando você tem mais de um nível de abstração sua função provavelmente esta fazendo coisas demais. Dividir suas funções leva a reutilização e teste mais fáceis.

**Ruim:**
```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Bom**:
```javascript
function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Remova código duplicado
Absolutamente nunca, nunca, em qualquer circunstância, tenha código duplicado. Não existe motivo para fazer isto e é possivelmente o pior pecado que você pode cometer como um desenvolvedor profissional. Código duplicado quer dizer que existe mais de um lugar onde você deverá alterar algo se precisar mudar alguma lógica. JavaScript possui tipagem fraca, então torna-se fácil ter funções genéricas. Aproveite-se disso! Ferramentas como [jsinspect](https://github.com/danielstjules/jsinspect) podem lhe ajudar a encontrar código duplicado elegível para refatoração.

**Ruim:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary: expectedSalary,
      experience: experience,
      githubLink: githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary: expectedSalary,
      experience: experience,
      portfolio: portfolio
    };

    render(data);
  });
}
```

**Bom**:
```javascript
function showList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    const data = {
      expectedSalary: expectedSalary,
      experience: experience,
      portfolio: portfolio
    };

    render(data);
  });
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Defina (set) objetos padrões com Object.assign

**Ruim:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable === undefined ? config.cancellable : true;

}

createMenu(menuConfig);
```

**Bom**:
```javascript
const menuConfig = {
  title: 'Order',
  // Usuário não incluiu a chave 'body'
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // configuração agora é: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```
**[⬆ voltar ao topo](#table-of-contents)**


### Não use flags como parâmetros de funções
Flags falam para o seu usuário que sua função faz mais de uma coisa. Funções devem fazer apenas uma coisa. Divida suas funções se elas estao seguindo caminhos de código diferentes baseadas em um valor boleano.

**Ruim:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Bom**:
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Evite Efeitos Colaterais
Uma função produz um efeito colateral se ela faz alguma coisa que não seja receber um valor de entrada e retornar outro(s) valor(es). Um efeito colateral pode ser escrever em um arquivo, modificar uma variável global, ou acidentalmente transferir todo seu dinheiro para um estranho.

Agora, você precisa de efeitos colaterais ocasionalmente no seu programa. Como no exemplo anterior, você pode precisar escrever em um arquivo. O que você quer fazer é centralizar aonde está fazendo isto. Não tenha diversas funções e classes que escrevam para uma arquivo em particular. Tenha um serviço que faça isso. Um e apenas um.

O ponto principal é evitar armadilhas como compartilhar o estado entre objetos sem nenhuma estrutura, usando tipos de dados mutáveis que podem ser escritos por qualquer coisa, e não centralizando onde seu efeito colateral acontece. Se você conseguir fazer isto, você sera muito mais feliz que a grande maioria dos outros programadores.

**Ruim:**
```javascript
// Variável global referenciada pela função seguinte
// Se tivéssemos outra função que usa esse nome, então seria um vetor (array) e poderia quebrar seu código
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Bom**:
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```
**[⬆ voltar ao topo](#table-of-contents)**

### Não escreva em funções globais
Poluir globais é uma pratica ruim em JavaScript porque você pode causar conflito com outra biblioteca e o usuário da sua API não faria a menor ideia até que ele tivesse um exceção sendo levantada em produção. Vamos pensar em um exemplo: e se você quisesse estender o método nativo Array do JavaScript para ter um método `diff` que poderia mostrar a diferença entre dois vetores? Você poderia escrever sua nova função em `Array.prototype`, mas poderia colidir com outra biblioteca que tentou fazer a mesma coisa. E se esta outra biblioteca estava apenas usando `diff` para achar a diferença entre o primeiro e último elemento de um vetor? É por isso que seria muito melhor usar as classes padrões do ES2015/ES6 e apenas estender o `Array` global.

**Ruim:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const values = [];
  const hash = {};

  for (const i of comparisonArray) {
    hash[i] = true;
  }

  for (const i of this) {
    if (!hash[i]) {
      values.push(i);
    }
  }

  return values;
};
```

**Bom:**
```javascript
class SuperArray extends Array {
  constructor(...args) {
    super(...args);
  }

  diff(comparisonArray) {
    const values = [];
    const hash = {};

    for (const i of comparisonArray) {
      hash[i] = true;
    }

    for (const i of this) {
      if (!hash[i]) {
        values.push(i);
      }
    }

    return values;
  }
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Favoreça programação funcional sobre programação imperativa
JavaScript não é uma linguagem funcional da mesma forma que Haskell é, mas tem um toque de funcional em si. Linguagens funcionais são mais limpas e fáceis de se testar. Favoreça esse tipo de programação quando puder.

**Ruim:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Bom**:
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, 0);
```
**[⬆ volta ao topo](#table-of-contents)**

### Encapsule condicionais

**Ruim:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Bom**:
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Evite negações de condicionais

**Ruim:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Bom**:
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Evite condicionais
Esta parece ser uma tarefa impossível. Da primeira vez que as pessoas escutam isso, a maioria diz, “como eu supostamente faria alguma coisa sem usar `if`? ” A resposta é que você pode usar polimorfismo para realizar a mesma tarefa em diversos casos. A segunda questão é geralmente, “bom, isso é ótimo, mas porque eu deveria fazer isso?” A resposta é um conceito de código limpo aprendido previamente: uma função deve fazer apenas uma coisa. Quando você tem classes e funções que tem declarações `if`, você esta dizendo para seu usuário que sua função faz mais de uma coisa. Relembre-se, apenas uma coisa.

**Ruim:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Bom**:
```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Evite checagem de tipos (parte 1)
JavaScript não possui tipos, o que significa que suas funções podem receber qualquer tipo de argumento. Algumas vezes esta liberdade pode te morder, e se torna tentador fazer checagem de tipos em suas funções. Existem muitas formas de evitar ter que fazer isso. A primeira coisa a se considerar são APIs consistentes.

**Ruim:**
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.peddle(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Bom**:
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Evite checagem de tipos (parte 2)
Se você estiver trabalhando com valores primitivos básicos como strings, inteiros e vetores, e você não pode usar polimorfismo mas ainda sente a necessidade de checar o tipo, você deveria considerar usar TypeScript. É uma excelente alternativa para o JavaScript normal, já que fornece uma tipagem estática sobre a sintaxe  padrão do JavaScript. O problema com checagem manual em JavaScript é que para se fazer bem feito requer tanta verborragia extra que a falsa “tipagem-segura” que você consegue não compensa pela perca de legibilidade. Mantenha seu JavaScript limpo, escreve bons testes, e tenha boas revisões de código. Ou, de outra forma, faça tudo isso mas com TypeScript (que, como eu falei, é uma ótima alternativa!).

**Ruim:**
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Bom**:
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Não otimize demais
Navegadores modernos fazem muitas otimizações por debaixo dos panos em tempo de execução. Muitas vezes, se você estiver otimizando esta apenas perdendo o seu tempo. [Existem bons recursos](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers) para se verificar onde falta otimização. Foque nesses por enquanto, até que	eles sejam concertados caso seja possível.

**Ruim:**
```javascript

// Em navegadores antigos, cada iteração de `list.length` não cacheada seria custosa
// devido a recomputação de `list.length`. Em navegadores modernos, isto é otimizado.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Bom**:
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Remova código morto
Código morto é tão ruim quanto código duplicado. Não existe nenhum motivo para deixá-lo em seu código. Se ele não estiver sendo chamado, livre-se dele. Ele ainda estará a salvo no seu histórico de versionamento se ainda precisar dele.

**Ruim:**
```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**Bom**:
```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```
**[⬆ voltar ao topo](#table-of-contents)**

## **Objetos e Estruturas de Dados**
### Use getters e setters
JavaScript não possui interfaces ou tipos então é muito difícil forçar esse padrão, porque nós não temos palavras-chave como `public` e `private`. Usar getters e setters para acessar dados nos objetos é bem melhor que simplesmente procurar por uma propriedade em um objeto. "Por que?" você deve perguntar. Bem, aqui vai uma lista desorganizada de motivos:

* Quando você quer fazer mais além de pegar (get) a propriedade de um objeto, você não tem que procurar e mudar todos os acessores do eu código.
* Torna mais fácil fazer validação quando estiver dando um `set`.
* Encapsula a representação interna
* Mais fácil de adicionar logs e tratamento de erros quando dando get and set.
* Herdando esta classe, você pode sobrescrever as funcionalidades padrões.
* Você pode usar lazy loading nas propriedades de seu objeto, digamos por exemplo pegando ele de um servidor.


**Ruim:**
```javascript
class BankAccount {
  constructor() {
    this.balance = 1000;
  }
}

const bankAccount = new BankAccount();

// Buy shoes...
bankAccount.balance -= 100;
```

**Bom**:
```javascript
class BankAccount {
  constructor(balance = 1000) {
    this._balance = balance;
  }

  // Não precisa ter como prefixo `get` ou `set` para ser um getter/setter
  set balance(amount) {
    if (verifyIfAmountCanBeSetted(amount)) {
      this._balance = amount;
    }
  }

  get balance() {
    return this._balance;
  }

  verifyIfAmountCanBeSetted(val) {
    // ...
  }
}

const bankAccount = new BankAccount();

// Comprar sapatos...
bankAccount.balance -= shoesPrice;

// Get o balanço
let balance = bankAccount.balance;

```
**[⬆ voltar ao topo](#table-of-contents)**


### Faça objetos terem membros privados
Isto pode ser alcançado através de closures (para ES5 e além).

**Ruim:**
```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Bom**:
```javascript
const Employee = function (name) {
  this.getName = function getName() {
    return name;
  };
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```
**[⬆ voltar ao topo](#table-of-contents)**


## **Classes**
### Principio da Responsabilidade Única (SRP)
Como dito em Código Limpo, "Nunca deveria haver mais de um motivo para uma classe ter que mudar". É tentador empacotar uma classe em excesso com muitas funcionalidades, como quando você pode levar apenas uma mala em seu voo. O problema com isso é que sua classe não sserá conceitualmente coesa e lhe dará diversos motivos para mudá-la. Minimizar o número de vezes que você precisa mudar uma classe é importante. É importante porque se muitas funcionalidades estão em uma classe e você mudar uma porção dela, pode ser difícil entender como isto afetará outras módulos que dependem dela no seu código.

**Ruim:**
```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Bom**:
```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```
**[⬆ voltar ao topo](#table-of-contents)**

### Princípio do Aberto/Fechado (OCP)
Como foi dito por Bertrand Meyer, "entidades de software (classes, módulos, funções, etc.) devem se manter abertas para extensões, mas fechadas para modificações." Mas o que isso significa? Esse principio basicamente diz que você deve permitir que usuários estendam as funcionalidades do seu módulo sem ter que abrir o arquivo de código fonte `.js` e manipulá-lo manualmente.

**Ruim:**
```javascript
class AjaxRequester {
  constructor() {
    // E se nós quiséssemos outro método HTTP, como DELETE? Nós teríamos que
    // abrir esse arquivo e modificar o this para colocá-lo manualmente.
    this.HTTP_METHODS = ['POST', 'PUT', 'GET'];
  }

  get(url) {
    // ...
  }

}
```

**Bom**:
```javascript
class AjaxRequester {
  constructor() {
    this.HTTP_METHODS = ['POST', 'PUT', 'GET'];
  }

  get(url) {
    // ...
  }

  addHTTPMethod(method) {
    this.HTTP_METHODS.push(method);
  }
}
```
**[⬆ voltar ao topo](#table-of-contents)**


### Princípio de Substituição de Liskov (LSP)
Esse é um termo assustador para um conceito extremamente simples. É formalmente definido como “Se S é um subtipo de T, então objetos do tipo T podem ser substituídos por objetos com o tipo S (i.e., objetos do tipo S podem substituir objetos do tipo T) sem alterar nenhuma das propriedades desejáveis de um programa (corretude, performance em tarefas, etc.).” Esta é uma definição ainda mais assustadora.

A melhor explicação para este conceito é se você tiver uma classe pai e uma classe filha, então a classe base e a classe filha pode ser usadas indistintamente sem ter resultados incorretos. Isso ainda pode ser confuso, então vamos dar uma olhada no exemplo clássico do Quadrado-Retângulo (Square-Rectangle). Matematicamente, um quadrado é um retângulo, mas se você modelá-lo usando uma relação “isto-é” através de herança, você rapidamente terá problemas.

**Ruim:**
```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  constructor() {
    super();
  }

  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // RUIM: Retornará 25 para o Quadrado. Deveria ser 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Bom**:
```javascript
class Shape {
  constructor() {}

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor() {
    super();
    this.width = 0;
    this.height = 0;
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor() {
    super();
    this.length = 0;
  }

  setLength(length) {
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
    switch (shape.constructor.name) {
      case 'Square':
        shape.setLength(5);
        break;
      case 'Rectangle':
        shape.setWidth(4);
        shape.setHeight(5);
    }

    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(), new Rectangle(), new Square()];
renderLargeShapes(shapes);
```
**[⬆ voltar ao topo](#table-of-contents)**

### Princípio da Segregação de Interface (ISP)
JavaScript não possui interfaces então esse principio não se aplica estritamente como os outros. Entretanto, é importante e relevante até mesmo com a falta de um sistema de tipos em JavaScript.

ISP diz que "Clientes não deve ser forcados a depender de interfaces que eles não usam." Interfaces são contratos implícitos em JavaScript devido a sua tipagem pato (duck typing).

Um bom exemplo para se observar que demonstra esse principio em JavaScript é de classes que requerem objetos de configurações grandes. Não pedir para clientes definirem grandes quantidades de opções é benéfico, porque na maioria das vezes eles não precisarão de todas as configurações. Torná-las opcionais ajuda a prevenir uma “interferência gorda”.

**Ruim:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} //  Na maioria das vezes, não precisamos animar enquanto atravessamos (traversing).
  // ...
});

```

**Bom**:
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```
**[⬆ voltar ao topo](#table-of-contents)**

### Princípio da Inversão de Dependência  (DIP)
Este principio nos diz duas coisas essenciais:
1. Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações.
2. Abstrações não devem depender de detalhes. Detalhes devem depender de abstrações.

Isso pode ser difícil de entender a principio, mas se você já trabalhou com Angular.js, você já viu uma implementação deste principio na forma de injeção de dependência (DI). Apesar de não serem conceitos idênticos, DIP não deixa módulos de alto nível saber os detalhes de seus módulos de baixo nível, assim como configurá-los. Isso pode ser alcançado através de DI. Um grande beneficio é que reduz o acoplamento entre os módulos. Acoplamento é um padrão de desenvolvimento muito ruim porque torna seu código mais difícil de ser refatorado.

Como dito anteriormente, JavaScript não possui interfaces, então as abstrações que são necessárias são contratos implícitos. Que quer dizer que, os métodos e as classes que um objeto/classe expõe para outros objeto/classe. No exemplo abaixo, o contrato implícito é que qualquer modulo de Request  para `InventoryTracker` terá um método `requestItems`:

**Ruim:**
```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // Ruim: Nós criamos uma dependência numa implementação de request especifica.
    // Nós deveriamos apenas ter requestItems dependendo de um método de request: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Bom**:
```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// Construindo nossas dependências externamente e injetando-as, podemos facilmente
// substituir nosso modulo de request por um novo mais chique que usa WebSockets
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```
**[⬆ voltar ao topo](#table-of-contents)**

### Prefira classes do ES2015/ES6 ao invés de funções simples do ES5
É muito difícil conseguir que herança de classe, construtores, e definições de métodos sejam legíveis para classes de ES5 clássicas. Se você precisa de herança (e esteja ciente que você talvez não precise), então prefira classes. Entretanto, prefira funções pequenas ao invés de classes até que você precise de objetos maiores e mais complexos.

**Ruim:**
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Bom:**
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```
**[⬆ voltar ao topo](#table-of-contents)**


### Use encadeamento de métodos
Indo contra um conselho de Código Limpo, aqui é a ocasião onde teremos que divergir.
Foi argumentado que o encadeamento de métodos não é claro e que viola a [Lei de Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter).
Talvez seja verdade, mas esse padrão é muito útil em JavaScript e você o vera em muitas bibliotecas como jQuery e Lodash. Ele permite que seu código seja expressivo e menos verboso. Por esse motivo, eu digo, use encadeamento de métodos e dê uma olhada em como o seu código ficará mais limpo. Em suas funções de classes, apenas retorne `this` no final de cada função, e você poderá encadear mais métodos de classe nele.

**Ruim:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

**Bom**:
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
    // NOTA: Retorne this para encadear
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTA: Retorne this para encadear
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTA: Retorne this para encadear
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTA: Retorne this para encadear
    return this;
  }
}

const car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```
**[⬆ voltar ao topo](#table-of-contents)**

### Prefira composição ao invés de herança
Como dito famosamente em  [*Padrão de projeto*](https://pt.wikipedia.org/wiki/Padr%C3%A3o_de_projeto_de_software) pela Guangue dos Quatro, você deve preferir composição sobre herança onde você puder. Existem muitas boas razoes para usar herança e muitas boas razoes para se usar composição. O ponto principal para essa máxima é que se sua mente for instintivamente para a herança, tente pensar se composição poderia modelar melhor o seu problema. Em alguns casos pode.

Você deve estar pensando então, "quando eu deveria usar herança?" Isso depende especificamente do seu problema, mas essa é uma lista decente de quando herança faz mais sentido que composição:

1. Sua herança representa uma relação de "isto-é" e não uma relação de "isto-tem" (Animal→Human vs. User->UserDetails)
2. Você pode reutilizar código de classes de base (Humanos podem se mover como todos os animais).
3. Você quer fazer mudanças globais para classes derivadas mudando apenas a classe base. (Mudar o custo calórico para todos os animais quando se movem).

**Ruim:**
```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Ruim porque Employees (Empregados) "tem" dados de impostos. EmployeeTaxData não é um tipo de Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Bom**:
```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;

  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```
**[⬆ voltar ao topo](#table-of-contents)**

## **Testing**
Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](http://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There's [plenty of good JS test frameworks]
(http://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Bad:**
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    date.shouldEqual('1/31/2015');

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Good**:
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    date.shouldEqual('1/31/2015');
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```
**[⬆ back to top](#table-of-contents)**

## **Concurrency**
### Use Promises, not callbacks
Callbacks aren't clean, and they cause excessive amounts of nesting. With ES2015/ES6,
Promises are a built-in global type. Use them!

**Bad:**
```javascript
require('request').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    require('fs').writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});

```

**Good**:
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```
**[⬆ back to top](#table-of-contents)**

### Async/Await are even cleaner than Promises
Promises are a very clean alternative to callbacks, but ES2017/ES8 brings async and await
which offer an even cleaner solution. All you need is a function that is prefixed
in an `async` keyword, and then you can write your logic imperatively without
a `then` chain of functions. Use this if you can take advantage of ES2017/ES8 features
today!

**Bad:**
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```

**Good**:
```javascript
async function getCleanCodeArticle() {
  try {
    const request = await require('request-promise');
    const response = await request.get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    const fileHandle = await require('fs-promise');

    await fileHandle.writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```
**[⬆ back to top](#table-of-contents)**


## **Error Handling**
Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping function execution on the current stack, killing the
process (in Node), and notifying you in the console with a stack trace.

### Don't ignore caught errors
Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error to the console (`console.log`)
isn't much better as often times it can get lost in a sea of things printed
to the console. If you wrap any bit of code in a `try/catch` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Bad:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Don't ignore rejected promises
For the same reason you shouldn't ignore caught errors
from `try/catch`.

**Bad:**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  console.log(error);
});
```

**Good:**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
});
```

**[⬆ back to top](#table-of-contents)**


## **Formatting**
Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are [tons of tools](http://standardjs.com/rules.html) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization
JavaScript is untyped, so capitalization tells you a lot about your variables,
functions, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**Bad:**
```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good**:
```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```
**[⬆ back to top](#table-of-contents)**


### Function callers and callees should be close
If a function calls another, keep those functions vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**Bad:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupMananger() {
    return db.lookup(this.employee, 'manager');
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(user);
review.perfReview();
```

**Good**:
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupMananger() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ back to top](#table-of-contents)**

## **Comments**
### Only comment things that have business logic complexity.
Comments are an apology, not a requirement. Good code *mostly* documents itself.

**Bad:**
```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good**:
```javascript

function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}

```
**[⬆ back to top](#table-of-contents)**

### Don't leave commented out code in your codebase
Version control exists for a reason. Leave old code in your history.

**Bad:**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good**:
```javascript
doStuff();
```
**[⬆ back to top](#table-of-contents)**

### Don't have journal comments
Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Bad:**
```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good**:
```javascript
function combine(a, b) {
  return a + b;
}
```
**[⬆ back to top](#table-of-contents)**

### Avoid positional markers
They usually just add noise. Let the functions and variable names along with the
proper indentation and formatting give the visual structure to your code.

**Bad:**
```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good**:
```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

const actions = function() {
  // ...
};
```
**[⬆ back to top](#table-of-contents)**

Atualizar a partir deste commit: https://github.com/ryanmcdermott/clean-code-javascript/commit/05089ba5de6a50190eaadd14276225d06863a8a0