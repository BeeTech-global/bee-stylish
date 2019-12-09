# [Nomenclatura](#nomenclatura) e [estrutura de arquivos](#estrutura-de-arquivos )

## Nomenclatura

### Pastas
* Nomes em inglês
* Nomes devem ser coesos e auto explicativos
* Nomes no formato kebab-case, ex. Documentos do cliente: customer-documents


### Arquivos
* Nomes em inglês
* Nomes no formato kebab-case
* Nomes devem ser coesos e auto explicativos
* Classes devem ter o mesmo nome do arquivo, ex. Documentos do cliente *CustomerDocuments.js* sendo a classe:

```js
  class CustomerDocuments {
    // implementation
  }
```

* Os arquivos de testes devem referenciar o arquivo testado sempre que possível e ser acrescido de .test antes do .js, ex. Documentos do cliente *CustomerDocuments.js* -> *CustomerDocuments.test.js* ou, se não for classe, *customer-document.js* -> *customer-document.test.js*
* Os demais arquivos devem estar no formato de slug, ex. Documentos do cliente: customer-documents.js

### Migrations
o nome é um timestamp no formato YYYYMMDDHHmmss seguido da funcionalidade da migration ex: `20181206131505-add-customer-table.js`

### Funções
* As funções e métodos preferencialmente devem ter o seu nome formado pelo verbo e o sujeito da ação que a função executa, como por exemplo:
  * executeTask
  * findById

### Constantes e Enums
* As constantes e os valores primitivos em enums devem usar CAPS_SNAKE_CASE:

## Estrutura de Arquivos

As pastas/arquivos devem ser referentes a mesma funcionalidade/módulo e subirem de nível caso sejam necessário(a)s fora daquele contexto, exemplo:

dado o arquivo *src/api/customers/adapters/parse-country-code.js* que é utilizado na hora de tratar o endereço do customer, suponhamos que ele seja necessário também em outro contexto (*src/api/stores*), nesse caso, devemos colocá-lo em um nível acima de ambos (*src/common/adapters/parse-country-code.js*)

### Projetos de APIs

```
├── db/
|   ├── migrations/
|   ├── seeds/
├── deploy/
├── src/
|   ├── app/
|   |   ├── middleware/
|   ├── api/
|   |   ├── {version}/
|   |   |   ├── {context}/
|   |   |   |   |   ├── adapters/
|   |   |   |   |   ├── commons*/
|   |   |   |   |   ├── controllers/
|   |   |   |   |   ├── factories/
|   |   |   |   |   ├── repositories/
|   |   |   |   |   ├── routes/
|   |   |   |   |   ├── services/
|   ├── commons*/
|   |   ├── enums/
|   ├── core
|   ├── helpers
|   ├── router
├── test
|   ├── integration
|   ├── functional
|   └── unit
|   |   ├── api/
|   |   |   ├── {version}/

```
* **db/**: Pasta destinada a tudo relacionado ao banco de dados (migrations, seeds, diagramas e etc)
* **db/migrations/**: Pasta para os arquivos de migrations
* **db/seeds/**: Para para os arquivos de seeds
* **deploy/**: Pasta com arquivos utilizados no CI de cada ambiente
* **src/**: Abreviação de source, é onde estará todos arquivos/pastas referente à API
* **src/app/**: Pasta para arquivos relacionados a camada de HTTP 
* **src/app/middleware/**: Pasta para interceptores HTTP
* **src/api/**: Pasta onde ficarão todos contextos da API
* **src/api/{version}**: Pasta com a versão da API. Ex `src/api/v1/`
* **src/api/{version}/{context}/**: Pasta para o nome do contexto, podendo uma API ter um ou mais contextos, sempre que aplicável, deixar no plural. Ex: `src/api/v1/customers` e/ou `src/api/v1/transactions`
* **src/api/{version}/{context}/adapters/**: Pasta para os adapters referentes àquele contexto
* **src/api/{version}/{context}/controllers/**: Pasta para os controllers referentes àquele contexto
* **src/api/{version}/{context}/factories/**: Pasta para as factories, que são responsáveis por criarem instancias dos services, controllers e repositories com suas respectivas dependencias
* **src/api/{version}/{context}/repositories/**: Pasta para os repositories referentes àquele contexto
* **src/api/{version}/{context}/routes/**: Pasta para as rotas referentes àquele contexto
* **src/api/{version}/{context}/services/**: Pasta para os services referentes àquele contexto
* **src/core/**: Pasta para declaração das classes/interfaces base que serão herdadas/implementadas independentes de contexto
* **src/helper/**: Pasta dedicada aos helpers
* **src/\**/commons/**: Pasta para os commons, o ’**/’ significa que ele pode ser declarada em qualquer parte dentro do `src/` conforme necessidade
* **src/router/**: Pasta onde fica o gerenciador principal de rotas que irá ‘conversar’ com a camada de app
* **test/**: Pasta para os arquivos de teste
* **test/integration/**: Pasta para os testes de integração, a hierarquia deve ser separada por funcionalidade e/ou contexto/funcionalidade conforme necessidade
* **test/functional**: Pasta para os testes funcionais, a hierarquia deve ser separada por contextos
* **test/unit**: Pasta para os testes unitários, a hierarquia deve seguir o mesmo padrão da pasta src
