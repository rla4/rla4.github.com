---
title: "Sobre Terminais"
date: 2020-11-20T10:07:18-03:00
---

Tenho uma confissão a fazer: nunca fui programadora de terminal. Se tivesse que elencar as cinco ferramentas que mais utilizei como desenvolvedora, um terminal certamente não estaria entre elas. Confesso também que isso já me causou um desconforto danado, como se usar mais comumente ferramentas visuais fizesse de mim uma programadora menos legítima.

Quando era mais nova e a síndrome de impostora batia, eu lia tudo que podia sobre bash, powershell, escrevia meus pequenos scripts pra fazer coisas bobas... mas nunca incorporei de fato o uso de terminais no meu dia-a-dia. Mas veja bem, nem acho que eu *precisaria* fazê-lo; como essa discussão recente salienta, usar ferramentas só porque são "cool" é bobeira. E se você, como eu, é mais produtivo ou prefere usar UIs gráficas, vai em frente ué.

<blockquote class="twitter-tweet tw-align-center"><p lang="en" dir="ltr">Real programmers use the tool that makes them happy and solves the problem. Choose productive happiness! <a href="https://t.co/q5mfsI3xyz">https://t.co/q5mfsI3xyz</a></p>&mdash; Scott Hanselman (@shanselman) <a href="https://twitter.com/shanselman/status/1225708945869684736?ref_src=twsrc%5Etfw">February 7, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Dito isto, mais por curiosidade do que por necessidade, resolvi ano passado que iria tentar mudar alguns hábitos e usar mais linha de comando pra tarefas corriqueiras. A motivação foi simples: tenho preguiça de ficar pegando no mouse, e estou cada vez mais tendo dificuldades de decorar tanto atalho de teclado pra interagir com minhas ferramentas. Ônus da idade. Mas além disso, tive também que fazer alguns projetos no trabalho que eram basicamente um conjunto de scripts PowerShell.

Por sorte (ou um excelente timing), a Microsoft tinha lançado há pouco tempo o novo Terminal do Windows. Eu usava cmder antes e não sinto nenhuma saudade. Essas são minhas configurações atuais e atalhos favoritos:

- Extensões pra Git: oh-my-posh, tema Paradox
- Atalhos:
 - Dividir a tela em painéis: `ctrl+shift+|` (criei um binding pra esse, ver meu settings.json abaixo)
 - Alternar entre painéis: `alt+<tecla direcional>`
 - Fechar painel: `ctrl+shift+w`

Uma coisa bacana é que você pode alterar seu `settings.json` em tempo real, sem precisar reinicializar o terminal para visualizar as mudanças. Dá pra botar até gif animado de background. Como sou mais old school, meu terminal é bem boringzinho. 

![powershell core](/img/terminal.png "powershell core")

Abaixo tem o gist público do meu arquivo de configurações atual:

<script src="https://gist.github.com/rla4/8018cfa4567cd6329ecf81e70c1388d4.js"></script>

