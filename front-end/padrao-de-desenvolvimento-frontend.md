# Padrão de desenvolvimento frontend

<p align="center" width="100%">
  <img width="50%" src="https://user-images.githubusercontent.com/2935122/115732148-1933a380-a35e-11eb-9f52-55631f5eeb0f.png">
</p>

No futuro, um novo framework deve nascer e ele pode ser o mais adequado de ser usado em algum projeto da Remessa.

Clean Architecture será útil no frontend para que regras de negócios fiquem desacopladas da camada de `presentation`.
Com isso, será muito mais simples adicionar uma nova _presentation_ independente da decisão do mercado, hive ou da Remessa.

## Estrutura de pasta

- Nomes em inglês
- Nomes devem ser coesos e auto explicativos
- Nomes no formato kebab-case, ex. Documentos do cliente: customer-documents

```
├── module/
|   ├── [module]/
|   |   ├── data/
|   |   |   ├── entities/
|   |   |   ├── usecases/
|   |   |   ├── adapters/
|   |   |   ├── factories/
|   |   |   ├── repositories/
|   |   |   ├── validations/
|   |   |   ├── constants/
|   |   |   ├── errors/
|   |   |   ├── translations/
|   |   ├── domain/
|   |   |   ├── [layer-with-interfaces-&-types-of-data-folders]
|   |   |   ├── models/
├── shared/
|   ├── infra/
|   |   ├── cache/
|   |   ├── http/
├── presentation/
|   ├── [framework-frontend]/
|   |   ├── public/
|   |   ├── src/
|   |   |   ├── pages/
|   |   |   ├── module/
|   |   |   |   ├── [module]/
|   |   |   |   |   ├── assets/
|   |   |   |   |   ├── components/
|   |   |   |   |   |   ├── atoms/
|   |   |   |   |   |   ├── molecules/
|   |   |   |   |   |   ├── organisms/
|   |   |   |   |   |   ├── template/
|   |   |   |   |   |   ├── pages/
|   |   |   ├── shared/
|   |   |   |   ├── assets/
|   |   |   |   ├── styles/
|   |   |   |   |   ├── global.ts
|   |   |   |   |   ├── theme.ts
|   |   |   |   ├── components/
|   |   |   |   |   ├── [atomic-design-folders]
```

### Entendendo a estrutura

`module` - Organizar e centralizar arquivos em comum.

`shared` - Arquivos que são de uso compartilhado entre os _modules_ e _presentation_.

`data` - Local onde ficam informações puras como no caso dos _models_ e _constants_, mas também toda a implementação de lógicas e regras organizadas entre `entities` e `usecases` em especial.

`domain` - _interfaces_ e _types_ que servem para definir contratos da camada `data`.

`entities` - Assim como na primeira imagem: regras de `business`, ou seja, cálculos e regras/lógicas de negócio.

`usecases` - Assim como na primeira imagem: regras de `application`, ou seja, arquivos com `entities` por exemplo para serem usados no
`presentation` e outros.

`adapters` - Conversão de dados que seja mais acessível e conveniente, para `entities` e `usecases`.

`factories` - Serve para unir, sem nenhum regra de negócio ou lógica, implementações em comum, que exporta elas de forma centralizada, para facilitar o que seria importação de múltiplos recursos, no _presentation_ ou outro.

`repositories` - Centralização de configurações para _requests_. Normalmente consome recursos de `infra/`.

`validation` - _Schemas_ de validação como os do [Yup](https://meet.google.com/qwm-gurh-ufj) - dê preferência ele - e lógicas em JS com validações.

`constants` - Constantes em _upper case_.

`errors` - Centralização de erros como aqueles usados no `throw`.

`translations` - Centralização dos JSON com traduções. Traduções genéricas como as de um _footer_, ficam em `shared/data/translations`.

`models` - _types_ de todos os tipos de dados e informações, podendo ser _input_ e/out _output_ das `entities`, `usecases`, `repositories`, formulários, _models_ de banco de dados em si.

`infra` - Recursos de máquina ou navegador como _Local Storage_, bibliotecas de terceiro como _axios_ e etc.

`presentation` - Contém _frameworks frontend_ por exemplo. Pode ter dois _presentation_ ou mais, como exemplo de `angularjs/` (projeto legado) e `react` (novo projeto como refatorações do legado).

`[framework-frontend]` - Deixamos ali a estrutura de pasta do _framework_ **NextJS**.

`components` - Estrutura de pastas do _Atomic Design_ para organizar melhor os componentes.

`pages` - Neste exemplo com **NextJS**, acabou por ter duas pasta _pages_. `src/pages` é do NextJS e não tem implementação de página. Apenas importa do `[module]/components/pages/`.

`shared/styles` - Estilos genéricos da aplicação como temas e CSS global.

`shared/components` - Componentes genéricos como _Layout_, _Header_ e _Footer_ organizados via _Atomic Design_.

## Arquivos

### Definições

- Nomes em inglês
- Nomes no formato title-case para componentes e kebab-case para demais
- Nomes devem ser coesos e auto explicativos
- Classes e Functional Component devem ter o mesmo nome do arquivo, ex. Documentos do cliente: _CustomerDocuments.ts_

## Conceitos

### Clean Architecture

Na camada `core` sendo `module` e `shared` contém todo o código mais importante de `business` e `application`. Está com uma estrutura escalável e organizado por _modules_ para permitir fácil organização por jornada do usuário e _Atomic Design_ para organização dos componentes.

#### Dependency rules

Existe algumas regras sobre dependencias entre as camadas.

<p align="center" width="100%">
  <img width="50%" src="https://user-images.githubusercontent.com/2935122/115903958-9896a500-a43a-11eb-8663-50b6798d15cd.png">
</p>

Outro exemplo visual:

<p align="center" width="100%">
  <img width="50%" src="https://user-images.githubusercontent.com/2935122/115903965-9af8ff00-a43a-11eb-9e68-8b8d31423b71.png">
</p>

É importante lembrar desses conceitos do Clean Arch e até mesmo princípios do SOLID ao criar e escrever um arquivo.

### Jornada do usuário

Exemplos básicos: _Authentication_, _Payments_ e _Receiving_.

## Definições gerais

### Patterns

<p align="center" width="100%">
  <img width="50%" src="https://user-images.githubusercontent.com/2935122/115890798-b3155200-a42b-11eb-87d7-1f96526a66d1.png">
</p>

Existe vários _patterns_ e talvez possamos recomendar [SOLID](https://medium.com/backticks-tildes/the-s-o-l-i-d-principles-in-pictures-b34ce2f1e898) em especial para camada `core`.

### Custom Hooks

<p align="center" width="100%">
  <img width="75%" src="https://user-images.githubusercontent.com/2935122/115892970-130cf800-a42e-11eb-80c5-64d866fc7ab7.png">
</p>

Podemos usar os Hooks para centralizar regras comuns dos `components` neles mesmo. Isso permite que não tenha todas as regras misturadas.

### Interfaces & Types

Use TypeScript.

## Referências

- [Explicação bacana sobre _Entities_, _Use Cases_ e _Adapters_](https://www.objective.com.br/insights/clean-architecture-com-mvvm/#:~:text=A%20Clean%20Architecture%20consiste%20em,Interface%20Adapters%E2%80%9D%20e%20assim%20sucessivamente.)
- [_Post_ com uma definição bem clara sobre o que vai em _Data_, _Domain_ e _Infra_, com exemplo em React](https://dev.to/joaosczip/clean-architecture-a-little-introduction-4ag6)
- [Excelente _post_ com várias explicações e exemplo](https://proandroiddev.com/clean-architecture-data-flow-dependency-rule-615ffdd79e29)
- [SOLID via robot images](https://medium.com/backticks-tildes/the-s-o-l-i-d-principles-in-pictures-b34ce2f1e898)
- [SOLID em ptbr](https://medium.com/desenvolvendo-com-paixao/o-que-%C3%A9-solid-o-guia-completo-para-voc%C3%AA-entender-os-5-princ%C3%ADpios-da-poo-2b937b3fc530)
