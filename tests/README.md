# Testes

* [Pattern TDD](#pattern-tdd)

---

* [Teste Unitário](#teste-unitário)
* [Teste de Integração](#teste-de-integração)
* [Teste Funcional](#teste-funcional-caixa-preta)
* [Teste de Integridade (performance, carga, stress](#teste-de-integridade-performance-carga-stress)
* [Teste de Sanidade (Smoke test, health check)](#teste-de-sanidade-smoke-test-health-check)
* [Teste E2E (aceitação)](#teste-e2e-aceitação)
* [Teste de Acessibilidade (a11y)](#teste-de-acessibilidade-a11y)
* [Teste de Regressão](#teste-de-regressão)

---

* [Pirâmide de testes](#pirâmide-de-testes)
  * [Ui](#ui)
  * [Service](#service)
  * [Unit](#unit)
---
* [Coverage](#coverage)
---
* [Test Double](#test-double)
  * [Fake](#fake)
  * [Spy](#spy)
  * [Stub](#stub)
  * [Mock](#mock)
---

## Pattern TDD

Teste Depois do Deploy e... Não pera...
TDD é o Desenvolvimento Orientado por testes (**T**est **D**riven **D**evelopment) ou seja, você não implementa NADA, absolutamente NADA antes do teste

Funciona da seguinte maneira, você cria o cenário de teste de unidade (ou teste unitário) da sua funcionalidade e roda ele, obviamente que ele vai quebrar, então você implementa o necessário para fazer ele passar, da maneira mais 'burra' possível, daí você parte para o próximo cenário da funcionalidade, lembrando que só pode ter um teste falhando por vez, um possível ciclo ficaria mais ou menos assim: Verde, Vermelho, Refatoração

* Escrevemos um Teste que inicialmente não passa (Vermelho)
* Implementamos essa nova funcionalidade do sistema
* Fazemos o Teste passar (Verde)
* Refatoramos o código da nova funcionalidade (Refatoração)
* Escrevemos o próximo Teste

Exemplo:

* Dados as entradas A e B, quero que a função me retorne A+B (soma hihihi)
  * Primeiro teste
    ```js
    test('Dado 1 e 3 como entrada, deve me retornar 4' ,() => {
      expect(sum(1, 3)).to.be(4);
    });
    ```
  * Roda o teste e deixa ele quebrar (Vermelho)
  * Resolve o problema da maneira mais simples possível
    ```js
    function sum() {
      return 4;
    }
    ```
  * Rodamos de novo o teste e ele passa (Verde)
  * Seria refatoração agora, mas nosso código está muito imaturo pra isso ainda
  * Próximo teste
    ```js
    test('Dado 1 e 3 como entrada, deve me retornar 4' ,() => {
      expect(sum(1, 3)).to.be(4);
    });

    test('Dado 3 e 6 como entrada, deve retornar 9' ,() => {
      expect(sum(3, 6)).to.be(9);
    });
    ```
  * Roda o teste e deixa ele quebrar (Vermelho)
  * Resolve o problema
    ```js
    function sum(a, b) {
      return a + b;
    }
    ```
  * Rodamos de novo o teste e ele passa (Verde)
  * Refatoração
    ```js
    function sum(a, b) {
      const result = a + b;

      return result;
    }
    ```
  * E recomeça o ciclo...

### Vantagens do TDD

* uma visão mais objetiva de problemas e oportunidades a serem atacados e o que fazer para alcançá-los;
* código limpo e bem escrito, resultado da simplicidade na hora de criá-lo e o tempo para refatorar;
* facilidade e segurança para corrigir bugs, já que você trabalha com o código fração por fração;
* modularidade e flexibilidade no seu código, proporcionados por essa quebra em pequenos objetivos;
* maior produtividade pelo foco na resolução de problemas;
* economia de tempo sem perder qualidade de desenvolvimento, com menos bugs para corrigir e menos retrabalho.

---

## Teste Unitário

Também conhecido como teste de unidade, tem como objetivo testar a menor parte de um sistema, em geral, um método. É independente de outros testes, valida somente uma funcionalidade e não deve ter dependência externas (internet, banco de dados, variáveis de ambiente entre outros)

Para garantir que seu teste unitário está seguindo o padrão correto, ele deve responder as seguintes questões

* O que está sendo testado
* O que o método deveria fazer
* Qual o seu atual retorno
* O que eu espero que retorne
* Alguma dependência externa pode alterar o resultado do teste

Alguns exemplo podem ser verificados no tópico sobre TDD acima

## Teste de Integração

Tendo todos os módulos previamente testados de maneira unitária, é hora de testar como eles se integram, no teste de integração é onde garantimos que todos módulos que funcionam nos testes unitários, quando combinados, seguem o fluxo de dados esperado, exemplo:

Quero testar o sanitize e a inserção da função `createCustomer`
```js
function sanitize(obj) {
  // implementation
}

function createCustomer(customer) {
  const toCreate = sanitize(customer);

  return repository.create(customer);
} 

test('should create customer', async () => {
  const customer = { name: 'André Coelho' };

  const result = await createCustomer(customer);
  expect(result.name).to.be('Andre Coelho');
  expect(result.id).to.be.a(Number);
});
```

## Teste Funcional (caixa-preta)

Muitas vezes confundido com o teste de integração, esse teste visa validar o comportamento externo do software, ou seja, testar a entrada e saída sem ter conhecimento de como são os módulos internos, no caso de uma API por exemplo, é o teste que chama o endpoint e avalia seu retorno, exemplo:

```js
const request = require('supertest');
const server = require('../src/app');

test('should insert customer', async () => {
  await request(server)
    .post('/v1/customers')
    .set('Authorization', 'Bearer token')
    .send(customer)
    .expect(201)
    .expect('x-request-id', /./)
    .expect('x-id', /./);
});
```

Para garantir o teste somente da API, vale criar um mock das requisições externas e caso necessário utilizar uma base de dados limpa, um docker por exemplo

## Teste de Integridade (performance, carga, stress)
Visa avaliar a disponibilidade/estabilidade de um sistema perante alguns cenários como:
* Carga - condições normais de uso
* Stress - condições extremas (picos de carga, excesso de requisições simultâneas entre * outros)
* Período - Período mínimo aceitável de funcionamento em uso

## Teste de Sanidade (Smoke test, health check) 

Verifica se o sistema se mantém ativo, no caso de sistemas web por exemplo, verifica se um endpoint responde o que deveria periodicamente

## Teste E2E (aceitação)

End to end, como o próprio nome sugere, verifica o sistema como um todo, já em conjunto com uma interface gráfica, é o teste que avalia a interface e o funcionamento das funcionalidades ignorando quais sistemas estão por de trás. Pode ser feito manualmente ou de forma automatizado, utilizando ferramentas como cucumber, puppeteer entre outras.

[Exemplo cumcumber com puppeteer](https://github.com/patheard/cucumber-puppeteer)

## Teste de Acessibilidade (a11y)

Subconjunto de testes de usabilidade, onde se leva em consideração pessoas com todas as habilidades e deficiências. O significado deste teste é verificar a capacidade de uso e acessibilidade. Acessibilidade visa atender pessoas de diferentes habilidades, tais como: deficiência visual, deficiência física, deficiência auditiva, comprometimento cognitivo, dificuldade de aprendizagem entre outros. 

A WCAG (Web Content Accessibility Guidelines) é um conjunto de regras que têm como objetivo garantir que o conteúdo na web seja acessível a todos os usuários. Ela se divide em quatro princípios:

1. **Perceptível**: As informações e os componentes da interface do usuário devem ser apresentados em formas que possam ser percebidas pelo usuário.
2. **Operável**: Os componentes de interface de usuário e a navegação devem ser operáveis, por exemplo: todas as funcionalidades da página estão disponíveis via teclado.
3. **Compreensível**: A informação e a operação da interface de usuário devem ser compreensíveis, por exemplo: a página possui indicador da linguagem no cabeçalho.
4. **Robusto**: O conteúdo deve ser robusto o suficiente para poder ser interpretado de forma confiável por uma ampla variedade de agentes de usuário, incluindo tecnologias assistivas.

Exemplo utilizando a ferramenta [axe](https://www.deque.com/axe/)

```js
import React from 'react';
import { renderToString } from 'react-dom/server';
import { axe, toHaveNoViolations } from 'jest-axe';
import Component from '/path';

expect.extend(toHaveNoViolations);

it('should have no a11y violations', async () => {
  const wrapper = renderToString(<Component />);
  const results = await axe(wrapper);
  expect(results).toHaveNoViolations;
});
```

Outras ferramentas podem ser conferidas nesse [post](https://medium.com/@mariaclarasantana/construindo-uma-web-melhor-ferramentas-e-testes-de-acessibilidade-20200f2a3739)

## Teste de Regressão

Reteste de um sistema ou componente para verificar se alguma modificação recente causou algum efeito indesejado, além de, certificar se o sistema ainda atende todos requisitos.
Pode ser feito manualmente ou ser automatizado

---

## Pirâmide de testes

![image Pirâmide de testes](pyramid.png)

A pirâmide de testes serve para nos mostrar o quanto de esforço devemos aplicar em cada tipo de teste na aplicação (os demais testes são feitos fora da aplicação), Então temos:

### UI

São os testes automatizados de interface, como E2E e o de acessibilidade, eles garantem que as premissas de interface sejam cumpridas, não sendo necessário testar toda interface, apenas os pontos críticos

### Service

São os testes de integração e funcionais, como eles testam cenários de maneira não isolada, sua quantidade também será menor

### Unit

São os testes unitários ou de unidade, seguindo o padrão TDD, consequentemente os testes unitário terão maior cobertura

---

## Coverage

Utilizamos o coverage (cobertura) para mensurarmos a porcentagem do código que está sendo executada naquela suíte de teste, ele não garante que todos cenários tenham sido testados, apenas que aquela porcentagem de ‘linhas’ tenham sido chamadas durante o teste

Pode ser usado como métrica para aceitação mínima de cobertura em um projeto e ter a validação a cada pull request com uma ferramenta de CI por exemplo

---

## Test Double

Testar código com ajax, network, timeouts, banco de dados e outras dependências que produzem efeitos colaterais é sempre complicado. Por exemplo, quando se usa ajax, ou qualquer outro tipo de networking, é necessário comunicar com um servidor que irá responder para a requisição; já com o banco de dados será necessário iniciar um serviço para tornar possível o teste da aplicação: limpar e criar tabelas para executar os testes e etc.

Quando as unidades que estão sendo testadas possuem dependências que produzem efeitos colaterais, como os exemplos acima, não temos garantia de que a unidade está sendo testada isoladamente. Isso abre espaço para que o teste quebre por motivos não vinculados a unidade em si, como por exemplo o serviço de banco não estar disponível ou uma API externa retornar uma resposta diferente da esperada no teste.
Há alguns anos atrás Gerard Meszaros publicou o livro XUnit Test Patterns: Refactoring Test Code e introduziu o termo Test Double (traduzido como "dublê de testes") que nomeia as diferentes maneiras de substituir dependências. A seguir vamos conhecer os mais comuns test doubles e quais são suas características, prós e contras.
Para facilitar a explicação será utilizado o mesmo exemplo para os diferentes tipos de test doubles, também será usada uma biblioteca de suporte chamada [Sinon.js](https://sinonjs.org/) que possibilita a utilização de stubs, mocks e spies.
O service abaixo é uma classe que recebe um repository como dependência no construtor. O método que iremos testar unitariamente dessa classe é o método “getAll”, ele retorna uma consulta do banco de dados com uma lista de usuários.

```js
const repository = {
  findAll() {}
}

class UsersService {
  constructor(repository) {
    this.repository = repository;
  }

  getAll() {
    return this.repository.findAll('users');
  }
}
```

### Fake

Durante o teste, é frequente a necessidade de substituir uma dependência para que ela retorne algo específico, independente de como for chamada, com quais parâmetros, quantas vezes, a resposta sempre deve ser a mesma. Nesse momento a melhor escolha são os Fakes. Fakes podem ser classes, objetos ou funções que possuem uma resposta fixa independente da maneira que forem chamadas.

O exemplo abaixo mostra como testar a classe `UsersService` usando um fake:

```js
describe('UsersService getAll()', () => {
  it('should return a list of users', () => {
    const expectedRepositoryDatabaseResponse = [{
      id: 1,
      name: 'John Doe',
      email: 'john@mail.com'
    }];

    const fakeRepositoryDatabase = {
      findAll() {
        return expectedRepositoryDatabaseResponse;
      }
    }
    const usersService = new UsersService(fakeRepositoryDatabase);
    const response = usersService.getAll();

    expect(response).to.be.eql(expectedRepositoryDatabaseResponse);
  });
});
```

Nesse caso de teste não é necessária nenhuma biblioteca de suporte, tudo é feito apenas criando um objeto fake para substituir a dependência do repositório. O método `findAll` passa a ter uma resposta fixa, que é uma lista com um usuário.
Para validar o teste é necessário verificar se a resposta do método `getAll` do service responde com uma lista igual a declarada no `expectedRepositoryDatabaseResponse`.

Vantagens:
* Simples de escrever
* Não necessita de bibliotecas de suporte
* Desacoplado da dependência original

Desvantagens:
* Não possibilita testar múltiplos casos
* Só é possível testar se a saída está como esperado, não é possível validar o comportamento interno da unidade

Quando usar *fakes*:
*Fakes* devem ser usados para testar dependências que não possuem muitos comportamentos ou somente para preenchimento de argumentos.

### Spy

Como vimos anteriormente os [fakes](#fake) permitem substituir uma dependência por algo customizado mas não possibilitam saber, por exemplo, quantas vezes uma função foi chamada, quais parâmetros ela recebeu e etc. Para isso existem os spies, como o próprio nome já diz, eles gravam informações sobre o comportamento do que está sendo "espionado".

No exemplo abaixo é adicionado um spy no método `findAll` do repository para verificar se ele está sendo chamado com os parâmetros corretos:

```js
describe('UsersService getAll()', () => {
  it('should findAll users from database with correct parameters', () => {
    const findAll = sinon.spy(repository, 'findAll');

    const usersService = new UsersService(Database);
    usersService.getAll();

    sinon.assert.calledWith(findAll, 'users');

    findAll.restore();
  });
});
```

Note que é adicionado um `spy` na função "findAll" do repository, dessa maneira o Sinon devolve uma referência a essa função e também adiciona alguns comportamentos a ela que possibilitam realizar checagens como `sinon.assert.calledWith(findAll, 'users')` onde é verificado se a função foi chamada com o parâmetro esperado.

Vantagens:
* Permite melhor assertividade no teste
* Permite verificar comportamentos internos
* Permite integração com dependências reais

Desvantagens:
* Não permitem alterar o comportamento de uma dependência
* Não é possível verificar múltiplos comportamentos ao mesmo tempo

Quando usar `spies`:

`Spies` podem ser usados sempre que for necessário ter assertividade de uma dependência real ou, como em nosso caso, em um `fake`. Para casos onde é necessário ter muitos comportamos é provável que [stubs](#stub) e [mocks](mock) venham melhor a calhar.

### Stub

*Fakes* e *spies* são simples e substituem uma dependência real com facilidade, como visto anteriormente, porém, quando é necessário representar mais de um cenário para a mesma dependência eles podem não dar conta. Para esse cenário entram na jogada os *Stubs*. *Stubs* são spies que conseguem mudar o comportamento dependendo da maneira em que forem chamados, veja o exemplo abaixo:

```js
describe('UsersService getAll()', () => {
  it('should return a list of users', () => {
    const expectedRepositoryDatabaseResponse = [{
      id: 1,
      name: 'John Doe',
      email: 'john@mail.com'
    }];

    const findAll = sinon.stub(repository, 'findAll');
    findAll.withArgs('users').returns(expectedRepositoryDatabaseResponse);

    const usersService = new UsersService(repository);
    const response = usersService.getAll();

    sinon.assert.calledWith(findAll, 'users');

    expect(response).to.be.eql(expectedRepositoryDatabaseResponse);

    findAll.restore();
  });
});
```

Quando usamos *stubs* podemos descrever o comportamento esperado, como nessa parte do código:

```js
findAll.withArgs('users').returns(expectedDatabaseResponse)
```

Quando a função `findAll` for chamada com o parâmetro `users`, retornará a resposta padrão.

Com *stubs* é possível ter vários comportamentos para a mesma função com base nos parâmetros que são passados, essa é uma das maiores diferenças entre *stubs* e *spies*.

Como dito anteriormente, *stubs* são *spies* que conseguem alterar o comportamento. É possível notar isso na asserção `sinon.assert.calledWith(findAll, 'users')` ela é a mesma asserção do *spy* anterior. Nesse teste são feitas duas asserções, apenas para mostrar a semelhança com *spies*, pois múltiplas asserções em um mesmo caso de teste é considerado uma má prática.

Vantagens:
* Comportamento isolado
* Diversos comportamentos para uma mesma função
* Bom para testar código assíncrono

Desvantagens:
* Assim como *spies* não é possível fazer múltiplas verificações de comportamento

Quando usar *stubs*:

Stubs são perfeitos para utilizar quando a unidade tem uma dependência complexa, que possui múltiplos comportamentos. Além de serem totalmente isolados os *stubs* também tem o comportamento de *spies* o que permite verificar os mais diferentes tipos de comportamento.

### Mock

*Mocks* e *stubs* são comumente confundidos pois ambos conseguem alterar comportamento e também armazenar informações. *Mocks* também podem ofuscar a necessidade de usar *stubs* pois eles podem fazer tudo que *stubs* fazem. O ponto de grande diferença entre *mocks* e *stubs* é sua responsabilidade: *stubs* tem a responsabilidade de se comportar de uma maneira que possibilita testar diversos caminhos do código, como por exemplo uma resposta de uma requisição http ou uma exceção; Já os *mocks* substituem uma dependência permitindo a verificação de múltiplos comportamentos ao mesmo tempo.

O exemplo a seguir mostra a classe `UsersService` sendo testada utilizando Mock:

```js
describe('UsersService getAll()', () => {
  it('should call repository with correct arguments', () => {
    const repositoryMock = sinon.mock(repository);
    repositoryMock.expects('findAll').once().withArgs('users');

    const usersService = new UsersService(repository);

    usersService.getAll();

    repositoryMock.verify();
    repositoryMock.restore();
  });
});
```

A primeira coisa a se notar no código é a maneira de fazer asserções com *Mocks*, elas são descritas nessa parte:

```js
repositoryMock.expects('findAll').once().withArgs('users')
```

Nela são feitas duas asserções, a primeira para verificar se o método `findAll` foi chamado uma vez e na segunda se ele foi chamado com o argumento `users`, após isso o código é executado e é chamada a função `verify()` do *Mock* que irá verificar se as expectativas foram atingidas.

Vantagens:
* Verificação interna de comportamento
* Diversos asserções ao mesmo tempo

Desvantagens:
* Diversas asserções ao mesmo tempo podem tornar o teste difícil de entender

[fonte](https://walde.co/2017/02/05/testes-e-javascript-diferenca-entre-fake-spy-stub-e-mock/)

----


É isso pessoal! 🙆
