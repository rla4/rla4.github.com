---
title: "Como fazemos onboarding"
date: 2021-07-13T19:35:36-03:00
tags: ["stack-overflow"]
---

Antes de começar a trabalhar na Stack Overflow, no carnaval de 2014, lembro que, para atenuar minha ansiedade, comecei a buscar conteúdo na internet sobre o ambiente de trabalho, quem seriam meus futuros colegas e o que esperar. Encontrei [esse artigo](https://jonhmchan.com/essays/2014/1/16/my-first-six-weeks-working-at-stack-overflow), escrito pelo Jon Chan (que, diga-se de passagem, ainda trabalha conosco e hoje é diretor de engenharia).

Embora o artigo já tenha envelhecido e alguma coisa tenha mudado, em geral o processo de onboarding na Stack ainda segue a mesma estrutura. No momento estou mentorando uma nova desenvolvedora - a 7a desde que comecei a trabalhar lá! [Esta interação no Twitter](https://twitter.com/rla4/status/1408930845373652997) me motivou a escrever um pouco mais sobre como nosso processo de onboarding funciona e as coisas que eu, pessoalmente, considero importantes ao receber um novo profissional.

## Um mentor dedicado por pessoa

Algumas semanas antes da chegada de uma nova desenvolvedora, um desenvolvedor sênior é designado para mentorá-la. Idealmente, o mentor deve estar há pelo menos 6 meses na empresa e fazer parte do mesmo time em que a nova contratada será alocada - mas não necessariamente. Como mentora, minhas responsabilidades são garantir que a nova integrante do time tenha sempre algo pra fazer/aprender, servir de ponto focal pra tirar dúvidas (tanto técnicas quanto organizacionais) e apresentá-la a outras pessoas-chave na empresa.

Também é minha responsabilidade fazer _sessões de onboarding_ para mostrar e discutir as diferentes partes do sistema (gosto de chamar de “tours”). Uma consequência direta muito positiva desse esquema de mentoria é que geralmente se estabelece uma relação de confiança entre mentor-mentorado que extrapola o tempo do onboarding. Por exemplo, mesmo anos depois de entrar na empresa, eu ainda recorria com frequência ao meu (ex-)mentor para trocar ideias e debater soluções.

Formalmente, o processo inteiro de onboarding tem seis semanas. Há um número grande de atividades pré-estabelecidas ao longo destas seis semanas, que descrevo mais detalhadamente abaixo. Mentores devem separar 50% do seu tempo na primeira semana exclusivamente para onboarding, mas esse tempo vai diminuindo à medida que o mentorado ganha mais autonomia.

Por fim, mentorar é uma atividade opcional e voluntária: eu gosto, e portanto, frequentemente me voluntario. Algumas empresas exigem (ou, pra ficar no eufemismo, "recomendam fortemente") que líderes técnicos e desenvolvedores com mais experiência sejam mentores; não é nosso caso.

## Onboarding: nosso template

Como citei anteriormente, nosso processo inteiro de onboarding está dividido em seis semanas. As primeiras quatro semanas são dedicadas a familiarizar a nova desenvolvedora com o time, com o projeto, as pessoas e a empresa. As duas últimas semanas devem ser utilizadas para o que chamamos de **projeto de graduação**. 

Caso a desenvolvedora não tenha experiência nenhuma com C#/.NET, as três primeiras semanas também envolvem o desenvolvimento de um projeto-piloto - uma aplicação web pequena, que deve ser desenvolvida em par com o mentor. Excepcionalmente, nesse caso, podemos estender o onboarding em uma ou duas semanas.

O processo inteiro de onboarding fica organizado num card do Trello: cada semana tem seu próprio checklist, com todas as atividades que devem ser cumpridas naquele período. Assim, fica fácil acompanhar o progresso e ajustar o ritmo se necessário.

## Primeira semana

A primeira semana é _intensa!_

Idealmente, queremos que a nova desenvolvedora coloque código em produção no _primeiro dia_ de trabalho. Para que isso seja possível, separamos algumas tarefas pequenas - coisa de uma ou duas horas - e pareamos durante todo o processo de montar o ambiente de desenvolvimento, resolver a tarefa e subir pra produção. Essa é também a oportunidade perfeita de demonstrar na prática nossos processos de build/testes/deploy e garantir que a nova desenvolvedora tem todos os seus acessos configurados corretamente.

Além de parear e resolver pequenos bugs em conjunto, procuro fazer pelo menos 1 “tour” por dia (tour do banco de dados, do processo de build/deploy, ferramentas de observabilidade, arquitetura, diferentes repositórios, e por aí vai). Sei que é sempre muita informação, então minha estratégia é repetir sutilmente e incansavelmente tudo que a minha mentorada precisa aprender.

Também gosto de usar a primeira semana de onboarding pra conhecer melhor meus mentorados, falar sobre nossos hobbies e amenidades. Acho que ajuda a estabelecer essa relação de confiança e também torna o processo menos cansativo e mais divertido. Afinal, são em torno de 3 horas por dia conversando!

## Semanas 2-4

A partir da segunda semana, começamos a cumprir uma agenda de reuniões de onboarding com dezenas de pessoas diferentes da empresa. Essas reuniões normalmente são de 3 tipos: 1:1 pra conhecer alguém, visão geral de outros times da empresa, e overview técnico de alguma área/projeto. Alguns exemplos:
- 1:1 com a CTO
- 1:1 com o VP de engenharia
- Visão geral de UX Research, com alguém de UX
- Visão geral do time de Ads, com alguém de Ads
- Observabilidade e SignalFX, com qualquer desenvolvedor do time
- SQL tuning e melhores práticas, com alguém do time de banco de dados

A grande maioria destas sessões dura 30 minutos e é responsabilidade do mentor agendá-las. O propósito maior aqui não é fazer a nova desenvolvedora memorizar tudo isso em tão pouco tempo, e sim apresentá-la ao maior número de pessoas que fará parte de seu dia-a-dia possível. Assim, ela sempre terá algum rosto conhecido quando precisar interagir com outros times e departamentos (e vice-versa!).

!["card no trello da 2a semana de onboarding, com as seguintes atividades: Introduction to Remote Beverage Bash group, Review diversity and inclusion wiki page, Schedule 30 min Public Platform Overview chat (jon), Scheduled 30 min Reach and Relevance Overview chat (Ben), Schedule 30 min Internal Dev overview chat, Review 60 min Theory of DevOps presentation, Schedule 30 min Design Overview chat (anyone from design), Schedule 30 min UX Research overview chat (anyone from UX), Schedule 30 min Intro to Stacks (Aaron), Schedule 45 min Infrastructure Tour (nick/tom), Code review check in with new hire (mentor)"](/img/onboarding.png "nosso template no trello: 2a semana de onboarding")
*nosso template no Trello, pra segunda semana de onboarding*

# Semanas 5-6: projeto de "graduação"

Finalmente, após quatro semanas intensas de correção de pequenos bugs, sessões de onboarding, projeto-piloto (pra quem não sabia nada de C#/.NET), chegou a hora de dedicar essas duas semanas finais ao projeto de graduação! Idealmente, mentor e mentorado já devem estar discutindo ideias pra esse projeto desde a semana 3.

O propósito aqui é escolher um projeto um pouco mais complexo do que as pequenas tarefas que até agora foram executadas pela nova desenvolvedora. O projeto pode tanto ser uma nova funcionalidade no produto quanto algo interno, como uma nova ferramenta pra ajudar o time ou melhorar nosso monitoramento. Por exemplo, um desenvolvedor escolheu como projeto de graduação reestruturar certas partes de nossa arquitetura, tornando-as mais testáveis.

Não necessariamente o projeto de graduação precisa ir pra produção ao final da sexta semana; muitos projetos inclusive nunca evoluem além disso, ficando apenas como provas de conceito ou experimentos divertidos. A funcionalidade de “snippets” no Stack Overflow, por exemplo, surgiu de uma ideia de projeto de graduação e acabou sendo publicada, mas essa não é uma regra ou obrigação.

## Pós-onboarding

Uma vez finalizada a sexta semana e apresentado o projeto de graduação pro time, parabéns, você está oficialmente _onboarded_ e pronto pra trabalhar em projetos mais robustos e pegar tarefas do nosso backlog. Acredito que uma boa experiência de onboarding é fundamental pra qualquer time, tanto pra quem está chegando quanto pra quem está mentorando. É também uma excelente oportunidade de aprender e revisitar seus processos e sistemas com um novo olhar.

<blockquote class="twitter-tweet tw-align-center"><p lang="pt" dir="ltr">o mais bacana é que a relação que se estabelece durante o onboarding meio que &quot;fica&quot;, sabe? meu mentor original já saiu da empresa, mas enquanto ele tava lá eu o tinha como referência (ainda tenho, na verdade).</p>&mdash; Roberta Arcoverde (@rla4) <a href="https://twitter.com/rla4/status/1408931325013377028?ref_src=twsrc%5Etfw">June 26, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>