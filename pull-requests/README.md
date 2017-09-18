# Guia do Pull Request Campeão

Se você não vive em Nárnia, `dev` saber a importância de um Pull Request bem estruturado e com aquela dose de carinho e empatia. 

Code Review é um dos processos que mais exigem atenção da equipe e, logo, temos que ser concisos e precisos em nossa comunicação.

Às vezes, pode ser trabalhoso. Afinal, temos que, além de analisar o código, verificar se as peças da estrutura se encaixam e respeitam a arquitetura vigente, se não há falhas de segurança e etc.

Também é uma maravilhosa ferramenta para ajudar a disseminar conhecimento e melhorar o código dos nossos coleguinhas.

Este guia tem como objetivo despertar aquele bom senso que vive dentro de _você_.

## Premissas

### Crie PRs pequenos

Toda vez que você cria um PR gigante, um Saci-Pererê tropeça em um Tamanduá-Bandeira. Evite ao _máximo_ fazer isso. Sério.

PRs gigantes diminuem a qualidade do Code Review, transformando-se numa tarefa deveras dispendiosa. 

##### "Ah, mais não achava que a tarefa iria ser gigante :("

Desta tarefa de complexidade maluca, é possível quebrar em pequenas partes. Pensar na feature como um todo pode pregar umas peças em nossas cabecinhas.

##### "Como eu evito esta zueira?"

Como sugestão, no primeiro sinal do PR gigante que se aproxima, crie uma branch principal para a sua feature. E, a partir dela, incremente a principal gradualmente:

```
* CARD-42-feature-bregumelo
|
|--* bregumelo-endpoint
|
|--* modal-bregumelo-component
|
|--* ...
```

##### "Poxa, mas no final de tudo vai ter aquele PR gigante para a branch de develop, não?" 

Sim, coleguinha! Mas como diferencial vocẽ terá todos os PRs anteriores devidamente avaliados. Por isso, evite reescrever o history da branch (CARD-42-feature-bregumelo). 

## Empatia

Há alguns fatores dos quais nos influenciam em escrever um código, digamos, "descortês":

* Desatenção
* Falta de conhecimento técnico
* O calor da batalha

Nenhum deles te dão a permissão para ser um babaca. Tenha empatia, mostre os pontos com erro e, o mais importante, apresente _soluções_.

## Largue o `Coder Ego`

É uma grande bobeira levar os comentários para o lado pessoal. Sério. Tão bobo que nem sei explicar isso direito, mas vou tentar.

Gosta de analogias? Aqui vai uma: Imagine que o projeto é uma casinha da hora que abriga a todos nós. 
Se você traz algo bem exótico para dentro de casa, perguntaremos o porquê disso e discutiremos sobre. 
Analisaremos se vale a pena mexer tanto nos móveis para acomodar este seu item, se todos ficarão confortáveis e por aí vai.

Note: Todo este processo envolve os residentes que estão deveras preocupados com os eventuais impactos. Nada pessoal.

Logo, largue o `Coder Ego` pois só há uma preocupação: A casa estar em ordem. 

~~_(ahhh muleque, nem pense entrar em casa com esse pé sujo aí)_~~

## Anatomia de um PR campeão

Vamos adentrar as entrefeufas de um PR que melhora a vida do seu amiguinho de labuta.

### Título

O segredo é ser conciso, como uma mensagem de commit:

* Se o PR for relativo a um Card, o título será o ID do card e, de preferência, acompanhado com uma mensagem indicativa.
  * Ex: `BEE-123: add remittance order CTA`

Senão tiver um card atrelado, utilize notação de mensagem de commit: `feat(remittance): add order CTA`.

### Descrição

Explique graciosamente o que o PR acrescenta.
 
Teve uma modificação de arquitetura? Usou algum pattern novo? Explique aos coleguinhas os motivos!

#### PRs de Front

Seja visual: Tire prints das alterações; grave GIFs dos fluxos. Isto ajuda (e muito) aos brothers identificarem possíveis erros de layout (aquele item desalinhado, cor equivocada) e, claro, direcionar qual parte do projeto você alterou.

#### Exemplos

<p align="center">
  <img src="https://cdn.rawgit.com/Beetech-global/bee-stylish/master/pull-requests/pr-1.gif" alt="">
</p>

<p align="center">
  <img src="https://cdn.rawgit.com/Beetech-global/bee-stylish/master/pull-requests/pr-2.jpg" alt="">
</p>

_(Repare que as mensagens de commit bem escritas ajudam um bocado e podem substituir a descrição na moralzinha)_

### Comentários

Esta é a ferramenta mais `zica das galáxias` para ajudar o amiguinho a se transformar numa besta implacável de geração de código.

Lembre-se: Empatia, bons argumentos e sem `Coder Ego`.

#### Comentando

Comente para elogiar, perguntar e, principalmente, *propor* melhorias. Sim, não reclame por reclamar, reclame sempre com uma proposta de ação/sugestão.

<p align="center">
  <img src="https://cdn.rawgit.com/Beetech-global/bee-stylish/master/pull-requests/comment-1.jpg" alt="">
</p>

Veja esta coisinha, só faltou o autor corroborar com uma sugestão. Coisa da qual o cara estranho em seguida o faz. Perceba, Ivair, o empenho em ajudar.

---

<p align="center">
  <img src="https://cdn.rawgit.com/Beetech-global/bee-stylish/master/pull-requests/comment-helpful.jpg" alt="">
</p>

Olha este _teamwork_, cara! Depois deste comentário eles fizeram um montão de código.

#### Respondendo

Adotamos um pequeno "framework" para rastrearmos os planos de ação de comentários.

Por exemplo:

<p align="center">
  <img src="https://cdn.rawgit.com/Beetech-global/bee-stylish/master/pull-requests/comment-commit.jpg" alt="">
</p>

O jovem não escreveu as mensagens de commit da maneira adequada, comprometeu-se em arrumar e criou uma `task`. Com as tasks, é muito mais eficiente saber se os pontos levantados foram endereçados.

---

Não deixe os comentários no vácuo. Se vossa senhoria não concorda com algum ponto levantado, responda a pessoa usando toda a sua argumentação _Aristotélica_ da ousadia. Traga referências para a conversa! _(sem falácias, plz)_

<p align="center">
  <img src="https://cdn.rawgit.com/Beetech-global/bee-stylish/master/pull-requests/comment-argument.jpg" alt="">
</p>

## Conclusão da alegria

Esperamos que este guia forneça a base para você fazer aquela balbúrdia do *BEM* no código dos seus coleguinhas.

Tenha empatia, mande o `Coder Ego` passar umas férias em Acapulco e esteja de peito aberto para aceitar e/ou dar pitaco no código alheio.

Afinal...

<p align="center">
  <img src="https://cdn.rawgit.com/Beetech-global/bee-stylish/master/pull-requests/so-vira-fera-quem-ve-pull-requests.jpg" alt="">
</p>

## Links

* [The (written) unwritten guide to pull requests](https://www.atlassian.com/blog/git/written-unwritten-guide-pull-requests) _vale a leitura_
* [Peek - Screen Recorder (Linux)](https://github.com/phw/peek)
* [ScreenToGif - Screen Recorder (Windows)](https://www.screentogif.com)
* [LICEcap - Screen Recorder (Windows/OSX)](https://www.cockos.com/licecap/)
