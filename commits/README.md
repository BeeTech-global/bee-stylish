# Commits styleguide



###

Um dos motivadores para a criação desse styleguide é

* A geração de changelog
* A navegação simplificada pelo histórico de commits 

### Formato do commit

```
<tipo>(escopo): assunto

<corpo>

<rodapé>
```

### Exemplo da messagem de commit

```
fix(listagem): adiciona serviço da api

Adiciona o get aos serviços da api para garantir que a listagem, seja carregada de forma dinâmica e atualizada pela api 

Fixes #2310
```

### Título do commit <tipo>(escopo): assunto


Para a escrita do assunto deve se seguir alguns padrões que estão abaixo:

* A primeira linha em hipotese nenhuma pode ter mais de 70 linhas;
* Separe o título do assunto com uma linha em branco;
* Não se deve terminar um título com ponto;
* Use do imperativo para fazer a escrita do assunto

**\<tipos> permitidos**

* feat (Uma nova funcionalidade para o usuário)

* fix (Bug fix para o usuário)

* docs (Mudança na documentação)

* style (formatação, falta de ponto e virgula)

* refactor (refatoração de código em produção)

* test (adicionar testes, refatorar testes)


* chore (Atualização de tarefas)

### Descrição do commit <corpo>

A descrição deve conter o `o que` e o `por que` daquela commit feito ao invês de conter o `como`