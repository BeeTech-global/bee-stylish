# Guia da Infra Matadora

Sabe aquela funcionalidade bala de prata que você cria, ou aquela vontade avassaladora de testar a nova versão da linguagem X que sempre empaca no "cara da infra" porque ele já está cheio de ambientes para criar e manter? Você pode pensar: "ah, mas agora eu uso Docker, posso subir um container rapidinho e mostrar pro tester na minha máquina". #shame #shame #shame

Conforme os projetos vão crescendo, eles precisam rodar numa estrutura própria, preferencialmente isolada de outras aplicações que possam interferir na funcionalidade desenvolvida.

## Falou, falou e não mostrou nada

Calma pequeno gafanhoto, deixa eu contar história primeiro...

Nós da [BeeTech](https://www.beetech.global) achamos que todo time deve ter a liberdade que achar necessária para criar um ambiente e explorar as idéias, então, junto ao nosso CI [Bitbucket Pipelines](#), implementamos também, alguns "custom build" bem úteis para todos, os mais importantes, e que vamos tratar nesse guia são os relacionados à [Heroku](https://www.heroku.com) - e se você não conhece a Heroku, tá esperando o quê pra dar uma olhada rapida e retornar pra cá?

Basicamente, um fluxo de desenvolvimento se baseia em:

- Selecionar a tarefa e a colocar como _"Em andamento"_
- Criar uma branch local baseada no nome da tarefa (ex.: bee-1234)
- [Comitar suas alterações](../commits/README.md)
- [Criar um Pull Request](../pull-requests/README.md)
- Publicar no ambiente de desenvolvimento e/ou homologação

Mas aí você pára e pensa: "mas e se eu quiser criar algo novo, testar uma biblioteca ou uma versão nova da linguagem? Não posso jogar em nenhum dos ambientes". Zatamênti meu caro, você não pode e nem deve!!!! E é aqui que começa: o *Guia da Infra Matadora*!


## O primeiro passo: habilitar o Pipelines

Como nosso controle de versão atual é o Bitbucket, resolvemos utilizar o Pipelines por alguns motivos: preço, zero instalação, facilidade de configuração e por utilizar fortemente o Docker, o que nos dá total liberdade na criação do ambiente de testes e garante total compatilidade com nossos ambientes. Poucos motivos, não?!?

Então claro, que o primeiro passo, é habilitar o Pipelines para o seu projeto, isso é fácil, basta ir nas configurações do projeto, e no menu lateral, selecionar Pipelines e habilitá-lo, simples assim. 

![Inicio](Seleção_001.png)

![Settings](Seleção_002.png)

![Pipelines Settings - OFF](Seleção_003.png)

![Pipelines Settings - ON](Seleção_004.png)

Após isso, você será questionado sobre a linguagem utilizada e apresentado a uma configuração padrão, que você pode alterar neste momento ou após, conforme sua necessidade. Como grande parte dos nossos projetos são em [Nodejs](https://www.nodejs.com), utilizamos uma configuração semelhante a mostrada abaixo:


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

Simples assim, e com isso, o desenvolvedor é totalmente capaz de configurar se próprio ambiente com poucos cliques e repassá-lo ao time de testes sem depender do *cara da infra* nem ninguém mais.

## Segundo passo: Doutrinando o time

"Com grandes poderes requerem grandes responsabilidaes", dizia o sábio Tio Joe, é verdade. Como você pode ver no exemplo acima, a configuração do Pipelines fica totalmente disponível para os desenvolvedores, que podem adicionar sua branch de trabalho para que faça uma entrega contínua, rode scripts e tudo mais que ele achar necessário.
Para utilizar os _custom builds_ e criar/atualizar o ambiente, basta que vá a lista de branches e no menu de ações, selecione *Run Pipeline for a branch* e selecionar a ação necessária.

![Branches - Custom Build](Seleção_005.png)
![Custom Build Modal](Seleção_006.png)

## Terceiro passo: Testando coisas no ambiente

- _Mas como vou testar uma versão diferente da linguagem?_
- _informar a versão no engines do package json basta você jovem padawan._

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

## Thats all folks

Sim, é esta a infra matadora, sem trabalho, sem estresse e linda de ver funcionando.

![Build bonito](Seleção_007.png)