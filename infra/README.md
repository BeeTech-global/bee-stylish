# Guia da Infra Matadora

Sabe aquela funcionalidade bacanuda que você cria, ou aquela vontade destruidora de testar aquela funcionalidade da versão mais atual da linguagem tal, mas que sempre empaca no _cara da infra_ porque ele já está cheio de tarefas _mais importantes que a sua_? 

Você pode pensar: 

```
- ah, mas agora eu uso Docker, posso subir um container rapidinho e mostrar pro tester na minha máquina.
```

![Shame on you!!!](shame-on-you.jpg)

Não meu caro, o que o _cara da infra_ te diria? Que isso precisa rodar em um ambiente separado, correto? Mas, então porquê não automatizar isso? Porque você mesmo não sobe seu ambiente para testar suas idéias e de quebra, cria um host para repassar para o time de testes ou para aquele seu gerente que só acredita vendo.

## Falou, falou e não mostrou nada

Calma pequeno gafanhoto, deixa eu contar história primeiro...

Nós da [BeeTech](https://www.beetech.global) achamos que todo time deve ter a liberdade de explorar idéias e consumir novas tecnologias, então, junto ao nosso CI [Bitbucket Pipelines](https://bitbucket.org/product/features/pipelines), implementamos também, alguns "[custom builds](https://confluence.atlassian.com/bitbucket/run-pipelines-manually-861242583.html)" bem úteis para todos e que vamos tratar nesse guia.

Para termos essa flexibilidade na infra, sem prejudicar ou onerar ninguém, optamos por utilizar a [Heroku](https://www.heroku.com) - se você não conhece a Heroku, taí a desculpa perfeita pra aprender - como [PaaS](https://pt.wikipedia.org/wiki/Plataforma_como_servi%C3%A7o).

Basicamente, um fluxo de desenvolvimento se baseia em:

- Mover a tarefa para _"Em andamento"_
- Criar uma branch local baseada no nome da tarefa (ex.: bee-1234)
- [Comitar suas alterações](../commits/README.md)
- [Criar um Pull Request](../pull-requests/README.md)
- Publicar no ambiente correto (desenvolvimento, teste, produção e etc)

Mas aí você para e pensa: "mas e se eu quiser criar algo novo, testar uma biblioteca ou uma versão nova da linguagem? Não posso jogar em nenhum dos ambientes". Zatamênti meu caro, você não pode e nem deve!!!! E é aqui que começa: o **GUIA DA INFRA MATADORA**!

![Que rufem os tambores](rafur-os-tombares.gif)

## O primeiro passo: habilitar o Pipelines

Como nosso controle de versão atual é o [Bitbucket](https://www.bitbucket.org), resolvemos utilizar o Pipelines por alguns motivos: 

- preço
- zero instalação
- facilidade de configuração 
- utilizar fortemente o Docker

Por utilizar Docker, já ganhamos máxima liberdade na criação do ambiente de testes e garante total compatilidade entre os ambientes, já que podemos utilizar o mesmo container em todos.

Então claro, que o primeiro passo, é habilitar o Pipelines para o seu projeto, isso é fácil, basta ir nas configurações do projeto, e no menu lateral, selecionar Pipelines e habilitá-lo, simples assim. 

![Inicio](Seleção_001.png)

![Settings](Seleção_002.png)

![Pipelines Settings - OFF](Seleção_003.png)

![Pipelines Settings - ON](Seleção_004.png)

Após isso, você será questionado sobre a linguagem utilizada e apresentado a uma configuração padrão, que você pode alterar neste momento ou após, conforme sua necessidade. Isso criará um arquivo *bitbucket-pipelines.yml* na raiz do seu projeto. 

Como grande parte dos nossos projetos são em [Nodejs](https://nodejs.org), utilizamos uma configuração semelhante a mostrada abaixo:


```
image: egenius/node

clone:
  depth: full

pipelines:
  default:
    - step:
        caches:
          - node
        script:
          - echo -e "registry=https://registry.npmjs.org/\n//registry.npmjs.org/:_authToken=$NPM_TOKEN" > ~/.npmrc
          - npm install --progress=false
          - npm test

  branches:
    master:
      - step:
          caches:
            - node
          script:
            - echo -e "registry=https://registry.npmjs.org/\n//registry.npmjs.org/:_authToken=$NPM_TOKEN" > ~/.npmrc
            - npm install --progress=false
            - npm test
			- heroku config:set WEB_MEMORY=1024 NPM_CONFIG_PRODUCTION=true NPM_TOKEN=$NPM_TOKEN NODE_ENV=production
            - git push -f https://heroku:$HEROKU_API_KEY@git.heroku.com/$BITBUCKET_REPO_SLUG-$BITBUCKET_BRANCH.git $BITBUCKET_BRANCH:refs/heads/master

    release:
      - step:
          caches:
            - node
          script:
            - echo -e "registry=https://registry.npmjs.org/\n//registry.npmjs.org/:_authToken=$NPM_TOKEN" > ~/.npmrc
            - npm install --progress=false
            - npm test
			- heroku config:set WEB_MEMORY=1024 NPM_CONFIG_PRODUCTION=true NPM_TOKEN=$NPM_TOKEN NODE_ENV=release
            - git push -f https://heroku:$HEROKU_API_KEY@git.heroku.com/$BITBUCKET_REPO_SLUG-$BITBUCKET_BRANCH.git $BITBUCKET_BRANCH:refs/heads/master

    develop:
      - step:
          caches:
            - node
          script:
            - echo -e "registry=https://registry.npmjs.org/\n//registry.npmjs.org/:_authToken=$NPM_TOKEN" > ~/.npmrc
            - npm install --progress=false
            - npm test
			- heroku config:set WEB_MEMORY=1024 NPM_CONFIG_PRODUCTION=false NPM_TOKEN=$NPM_TOKEN NODE_ENV=development
            - git push -f https://heroku:$HEROKU_API_KEY@git.heroku.com/$BITBUCKET_REPO_SLUG-$BITBUCKET_BRANCH.git $BITBUCKET_BRANCH:refs/heads/master

  custom:
    publish-heroku:
      - step:
          caches:
            - node
          script:
            - echo -e "registry=https://registry.npmjs.org/\n//registry.npmjs.org/:_authToken=$NPM_TOKEN" > ~/.npmrc
            - npm install --progress=false
            - npm run test
            - git push -f https://heroku:$HEROKU_API_KEY@git.heroku.com/$BITBUCKET_REPO_SLUG-$BITBUCKET_BRANCH.git $BITBUCKET_BRANCH:refs/heads/master

    create-heroku:
      - step:
          caches:
            - node
          script:
            - echo -e  "registry=https://registry.npmjs.org/\n//registry.npmjs.org/:_authToken=$NPM_TOKEN" > ~/.npmrc
            - npm install --progress=false
            - npm run test
            - heroku apps:create $BITBUCKET_REPO_SLUG-$BITBUCKET_BRANCH
            - heroku config:set WEB_MEMORY=1024 NPM_CONFIG_PRODUCTION=false NPM_TOKEN=$NPM_TOKEN NODE_ENV=development 
            - git push -f https://heroku:$HEROKU_API_KEY@git.heroku.com/$BITBUCKET_REPO_SLUG-$BITBUCKET_BRANCH.git $BITBUCKET_BRANCH:refs/heads/master

    delete-heroku:
      - step:
          script:
            - echo -e  "machine api.heroku.com" > ~/.netrc
            - echo -e  "  login $HEROKU_EMAIL" >>  ~/.netrc
            - echo -e  "  password $HEROKU_PASSWORD" >>  ~/.netrc
            - echo -e  "machine git.heroku.com" >>  ~/.netrc
            - echo -e  "  login $HEROKU_EMAIL" >>  ~/.netrc
            - echo -e  "  password $HEROKU_PASSWORD" >>  ~/.netrc
            - heroku apps:destroy $BITBUCKET_REPO_SLUG-$BITBUCKET_BRANCH -c $BITBUCKET_REPO_SLUG-$BITBUCKET_BRANCH
```

Nesta configuração, temos:

- testes rodando para todas as branches
- branches *master*, *release* e *develop* atualizam seus respectivos ambientes automaticamente caso os testes passem
- temos três *custom builds*:
	- create-heroku: responsável pela criação do ambiente na heroku, com algumas variáveis de ambientes e seguindo o modelo <nome-do-projeto>-<nome-da-branch>.herokuapp.com
	- publish-heroku: responsável pela atualização de algum ambiente previamente criado pelo custom build anterior
	- delete-heroku: remove o ambiente da heroku

A variável $NPM_TOKEN você pode criar nas configurações do Pipelines, indo em **Environment Variables**. As outras são geradas automagicamente pelo próprio Pipelines.

Simples assim, e com isso, o desenvolvedor é totalmente capaz de configurar se próprio ambiente com poucos cliques e repassá-lo ao time de testes sem depender do *cara da infra* ou daquele amigão que manja de servidores.

## Segundo passo: Doutrinando o time

"Com grandes poderes requerem grandes responsabilidaes", dizia o sábio [Tio Ben](https://pt.wikipedia.org/wiki/Ben_Parker), é verdade. Como você pode ver no exemplo acima, a configuração do Pipelines fica totalmente disponível para os desenvolvedores, que podem adicionar sua branch de trabalho para que faça uma entrega contínua, rode scripts e tudo mais que ele achar necessário.
Para utilizar os _custom builds_ e criar/atualizar o ambiente, basta que vá a lista de branches e no menu de ações, selecione **Run Pipeline for a branch** e selecionar a ação necessária.

![Branches - Custom Build](Seleção_005.png)
![Custom Build Modal](Seleção_006.png)

## Terceiro passo: Testando coisas no ambiente

``` 
- Mas como vou testar uma versão diferente da linguagem?
- Informar a versão no engines do package json basta você jovem padawan.
```

![Yoda](yoda.jpg)

```
{
  "name": "pipelines-test",
  "version": "0.0.1",
  "description": "Teste do Bitbucket Pipelines",
  "main": "index.js",
  "repository": "git@bitbucket.org:beetech_egenius/pipelines-test.git",
  "scripts": {
    "start": "nodemon index.js",
    "start:develop": "NODE_ENV=develop node index.js",
    "start:production": "NODE_ENV=production node index.js",
    "start:release": "NODE_ENV=release node index.js",
    "test": "NODE_ENV=test mocha",
    "heroku-postbuild": "mkdir logs && npm run migrations:heroku"
  },
  "pre-commit": [
    "test"
  ],
  "keywords": [
    "pipelines",
    "infra",
    "heroku",
    "test"
  ],
  "author": "BeeTech",
  "license": "MIT",
  "private": true,
  "dependencies": {
    "express": "^4.13.3"
  },
  "devDependencies": {
    "chai": "*",
    "eslint": "3.7.1",
    "eslint-config-airbnb-base": "^8.0.0",
    "eslint-plugin-import": "1.16.0",
    "extend": "^3.0.1",
    "istanbul": "^0.4.5",
    "mocha": "*",
    "nock": "^9.0.4",
    "nodemon": "^1.11.0",
    "pre-commit": "1.1.3",
    "sinon": "^2.3.2",
    "supertest": "^1.1.0"
  },
  "engines": {
    "node": "^6.9.2",
    "npm": "^5.3.0"
  }
}
```

Entendeu? Quer testar uma versão nova do Node? Só informar ali em *engines* a versão que você quer e pronto!
Usa PHP? Não tem problema, informa no *composer.json*, Python? Vai no *requirements.txt* e por aí vai, é mais simples do que parece ;)

Caso precise rodar alguma tarefa a antes ou depois do build, mas apenas na Heroku, temos a opção de criar em **scripts** os gatilhoes pré-configurados da Heroku: pre-build ou post-build. Na [documentação](https://devcenter.heroku.com/) deles você encontra mais opções.

![Build bonito](Seleção_007.png)

## Thats all folks

Como podemos ver, com isso, todos do time ganham total liberdade de aplicar suas idéias, testar novas funcionalidades ou mesmo trabalhar tranquilamento em uma correção, sabendo que seu trabalho não irá afetar ninguém que esteja utilizando o mesmo ambiente. Tudo fica perfeitamente isolado e funcionando perfeitamente.

Sim, é esta a infra matadora, sem trabalho, sem estresse e linda de ver funcionando.

![Done](Expendables_2_Chuck_Norris.jpg)