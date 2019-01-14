---
title: "Debugando um Script de Build"
date: 2019-01-11T16:42:30-02:00
draft: true
tags: ["code", "msbuild"]
---

Hoje reportei [minha primeira issue no GitHub do .NET SDK](https://github.com/dotnet/sdk/issues/2813), depois de horas investigando o que parecia ser um erro impossível. Esse post vai detalhar meu processo desde encontrar o bug, debugar, testar, investigar logs, aceitar que o problema não era no meu código, criar um script que o reproduz e finalmente reportar a issue devidamente.

## Contexto

Temos na empresa um projeto chamado `dev-local-setup`, utilizado para criar um ambiente local de desenvolvimento do zero. Como tudo que fazemos, é um projetinho bastante simples e objetivo: um conjunto de scripts Powershell, responsáveis por instalar ferramentas, baixar repositórios, configurar bases de dados, variáveis de ambiente, rodar migrations, instalar serviços - enfim, tudo que podemos automatizar para facilitar o onboarding de novos desenvolvedores ou preparar um novo ambiente de desenvolvimento em máquinas novas.

Essa semana precisei criar um novo script no `dev-local-setup` para instalar um serviço Windows, chamado Chestnut. O que esse serviço faz é irrelevante para o entendimento deste post, mas talvez eu fale mais dele no futuro. O importante é saber que se trata de um serviço Windows desenvolvido internamente.

Para instalar o Chestnut, meu script deveria executar os seguintes passos:

1. Verificar se o serviço já estava instalado; caso afirmativo, desinstalar
2. Clonar o repositório do Chestnut (ou atualizá-lo caso já exista na máquina)
3. Buildar o projeto `Chestnut.WindowsService.csproj`
4. Alterar alguns arquivos de configuração
5. Instalar o serviço
6. Iniciar o serviço

Apesar da minha pouca experiência com Powershell, tudo parecia estar correndo bem. Até que cheguei no passo 3.

## Build FAILED

Costumo sempre escrever meus programas fazendo modificações incrementais e bem pequenas, talvez um hábito antigo influenciado pelos áureos tempos de TDD. Nesse caso, a menor linha de código que funcionaria para executar o build seria `dotnet build $solution`. Funcionou, então comecei a adicionar as configurações que gostaria de usar: primeiro adicionei `-c Release` para gerar os artefatos no modo Release (e não Debug); depois, para facilitar a instalação do serviço numa pasta já existente, definida na variável `$outputPath`, passei `-o $outputPath`; e, por fim, ao invés de buildar a solução inteira, passei o projeto específico em que estava interessada... e aí que as coisas ficaram interessantes.

```cmd
dotnet build "$solutionFolder\Chestnut.WindowsService.csproj" -o $outputPath -c Release

CSC : error CS5001: Program does not contain a static 'Main' method suitable for an entry point [C:\chestnut\Chestnut.WindowsService.csproj]

Build FAILED.
```

Opa! Isso não tinha acontecido antes. Aqui começou minha jornada de uma tarde inteira em busca do problema.

## Comparando Scripts

A primeira coisa que tentei fazer foi reverter o artefato do build: voltei a passar a solução inteira, ao invés do projeto específico. FAIL.

Então lembrei que o Chestnut tinha seu próprio script de build! Primeiro tentei executá-lo pra ver se estava funcionando: sucesso! Agora era só começar a comparar os dois scripts e ver onde estava a diferença. Veja se você acha os 7 erros:

Esse funciona:
```powershell
$proj = "ChestnutDS.WindowsService\ChestnutDS.WindowsService.csproj"
dotnet build $proj /p:Configuration=Release /p:OutputPath=$buildPath
```
Esse não:
```powershell
dotnet build "$solutionFolder\Chestnut.WindowsService.csproj" -o $outputPath -c Release
```

Em tempo: a sintaxe pra passagem de parâmetros *não* faz diferença. Eu estava diante de dois scripts idênticos, um funcionava e o outro não.

Passei a desconfiar do ambiente: talvez os meus scripts estivessem usando versões diferentes do SDK. Para testar, incluí em ambos a seguinte linha de código:

```powershell
dotnet --version
```
Infelizmente, ao executá-los, vi que geravam o mesmo resultado: 2.2.101. Hora de dar uma olhada nos logs de build.

## MSBuild.log FTW

Alterei novamente os scripts para gerar logs das execuções de build em arquivo. Queria um nível detalhado de informações, mas não necessariamente uma enciclopédia, então configurei `verbosity` para `normal` e adicionei o switch `flp` para mandar o log prum arquivo. Por algum motivo a [documentação oficial do comando dotnet build](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-build?tabs=netcore2x) é bem rasa, e só achei como fazer isso num post do [Stack Overflow](https://stackoverflow.com/a/48561727/411343) 🤷‍♀️. O comando final ficou assim:

```powershell
dotnet build "$solutionFolder\Chestnut.WindowsService.csproj" -o $outputPath -c Release /flp:v=n
```

Re-executei os dois scripts e comecei a comparar, linha a linha, os resultados dos arquivos `msbuild.log`. Até que essa linha chamou minha atenção:

```
/analyzer:C:\packages\microsoft.codeanalysis.analyzers\1.1.0\analyzers\dotnet\cs\Microsoft.CodeAnalysis.CSharp.Analyzers.dll ChestnutDSService.cs Program.cs 
(...)
Build succeeded.
```
```
/analyzer:C:\packages\microsoft.codeanalysis.analyzers\1.1.0\analyzers\dotnet\cs\Microsoft.CodeAnalysis.CSharp.Analyzers.dll obj\Release\net462\ChestnutDS.WindowsService.AssemblyInfo.cs 
(...)
Build FAILED.
```

Viu a diferença? Aparentemente o build do meu script não estava incluindo os arquivos `.cs` por algum motivo. Agora a mensagem de erro até passou a fazer sentido - como `Program.cs` não estava sendo incluído no analisador, nenhum método `Main` era de fato encontrado. A dúvida era: por quê? A essa altura investigar apenas os arquivos de texto não estava sendo muito produtivo, então parti pro próximo nível de troubleshooting: [MSBuild Structured Log Viewer](http://msbuildlog.com/).

## Logs binários

Eu já tinha usado o MSBuild Log Viewer há muito tempo, pra tentar otimizar o tempo de compilação de um projeto. Dessa vez, pedi a ajuda dos meus colegas Nick e Dean, desenvolvedores do `dev-local-setup`, pra tentarmos juntos decifrar o que estava acontecendo.

Para gerar o arquivo de log binário, podemos passar a flag `bl` no comando de build. No nosso caso, o comando inteiro ficou assim:

```powershell
dotnet build "$solutionFolder\Chestnut.WindowsService.csproj" -o $outputPath -c Release -bl:output.binlog
```
Visualizando o arquivo gerado no Log Viewer, confirmamos que o código fonte não estava de fato sendo incluído no build do projeto como deveria.

{{< figure src="/img/msbuild_01.png" caption="MSBuild Log Viewer" alt="screenshot do MSBuild Log Viewer, mostrando que a coleção de arquivos de código fonte Sources está vazia" >}}

Foi então que, num estalo, Dean resolveu retestar os scripts sem passar `outputPath` como parâmetro.

Funcionou.

## Output Path bugado

Sabíamos que algo estava errado com o ouput path que estávamos usando. Pragmaticamente, eu poderia nesse momento ter alterado meu script para gerar os artefatos na pasta padrão `/bin`, seguir a implementação e não mais me preocupar com as causas do erro. Entretanto, como ensinava Feynman, para realmente aprender algo novo, devemos perseguir os por quês incansavelmente. Continuamos então tentando identificar qual era a particularidade que estava quebrando um script mas parecia não afetar o outro.

Nesse momento, minha hipótese era que ao executar meus scripts como parte de um projeto maior - `dev-local-setup` - em algum momento variáveis de ambiente estariam alterando algum sutil, porém importante, parâmetro de execução. Comecei a tentar isolar meu script o máximo possível, mas antes mesmo que eu pudesse testar essa hipótese (que teria, agora sei, sido falseada), Nick teve outro estalo: quais output paths estavam efetivamente sendo usados nos scripts?

```
# Script que funcionava
$outPath = "$PSScriptRoot\artifacts"
```
```
# Script que dava pau
$outPath = "$env:CODEROOT\Chestnut"
```

Hm. O script que funcionava tinha como alvo uma pasta chamada "artifacts"; o script que dava erro estava jogando a saída de build para uma pasta chamada "Chestnut". O projeto, por sua vez, estava localizado numa pasta chamada "ChestnutDS". Alteramos novamente o script que estava dando erro, mudando a pasta destino de "Chestnut" para "WTF"...

Funcionou!

Comecei a pensar que talvez meu script estivesse criando a pasta destino com algum problema de permissão, mas antes que eu perdesse tempo verificando, veio o insight do Nick: e se o problema estiver relacionado não às configurações de pasta destino, mas sim ao nome da pasta? E se for um bug no SDK que só se manifesta quando a pasta destino é uma substring - ou prefixo - da pasta onde está o código fonte?

## Reproduzindo o erro

Graças aos novos comandos do SDK, que permitem que novos projetos sejam criados em linha de comando (finalmente!), era bem fácil agora testar nossa hipótese sem precisar nem abrir o Visual Studio. O passo a passo para reproduzir o erro parecia ser:

1. Criar uma pasta X qualquer (no nosso exemplo, "TestFullPath")
2. Criar nessa pasta um projeto executável, pois o erro era relacionado à ausência do método `Main` (Windows Service ou console app serviriam)
3. Executar `dotnet build` no projeto, passando como output path uma pasta cujo nome seja prefixo da pasta X (no nosso exemplo, chamamos de "Test")

Criamos o seguinte script que teoricamente deveria reproduzir nosso erro lindamente:

```cmd
mkdir C:\Temp2 && cd C:\Temp2
mkdir TestFullPath && cd TestFullPath 
dotnet new console && cd ..
dotnet build C:\Temp2\TestFullPath\TestFullPath.csproj -o C:\Temp2\Test
```

E... não falhou. 🤬 Nessa hora eu já estava prestes a arrancar os cabelos quando, duvidando de nós mesmos, executamos o script novamente.
Falhou! Hm, parece que o erro só aparece quando a *pasta destino não está mais vazia*. Beleza, basta chamar o build duas vezes. 😅

Já tínhamos um código que reproduzia o problema! Agora era hora de criar a issue no GitHub e avisar o time do SDK. Mas antes, Nick levantou a hipótese do erro já ter sido corrigido na versão 3.x (estávamos rodando a versão 2.2), então testamos isso também. Resultado: sucesso, bug já havia sido (aparentemente) corrigido no SDK 3.x, mas ainda assim valia a pena levantá-lo para a versão estável do 2.2, visto que esta também está em plena manutenção. Felizmente, é relativamente fácil forçar o uso de versões anteriores do SDK num script: basta criar um arquivo `global.json` especificando a versão desejada. Colocamos esse passo no nosso script, que finalmente, ficou assim:

```cmd
mkdir C:\Temp2 && cd C:\Temp2
@echo {"sdk":{"version":"2.2.100"}} > global.json
mkdir TestFullPath && cd TestFullPath && dotnet new console && cd ..
dotnet build C:\Temp2\TestFullPath\TestFullPath.csproj -o C:\Temp2\Test
dotnet build C:\Temp2\TestFullPath\TestFullPath.csproj -o C:\Temp2\Test
```

## Issue reportada, surpresas e lições aprendidas

Essa foi a issue que finalmente criamos no GitHub do SDK: https://github.com/dotnet/sdk/issues/2813

Nick comentou comigo que iria taggear o team lead lá do MSBuild, e tive a grata surpresa de ver quem era: Livar, contemporâneo meu no CIn/UFPE, com quem tirava sempre minhas dúvidas de Engenharia de Software! Sempre soube que Recife era um ovo, mas nunca imaginava que iria encontrar um conterrâneo nas esquinas do GitHub.

Esse post ficou deliberadamente longo. Minha ideia não era ensinar Powershell, dar dicas de ferramentas de debug ou documentar tecnicamente as tecnologias e ferramentas aqui presentes. Queria tentar descrever, quase que em tempo real, tudo que aconteceu entre o momento em que nos deparamos com um problema imprevisto até encontrarmos sua solução. Nesse caso, excepcionalmente, a solução foi contornar uma situação que fugia ao nosso controle - um bug na ferramenta que utilizávamos. Mas, de modo geral, o ciclo de problemas-hipóteses-testes-soluções é meu modus operandi, e penso que não estou sozinha.

Todo mundo que trabalha com desenvolvimento de software, imagino, passa cotidianamente por situações similares à que descrevi aqui: horas e horas tentando desvendar o que parece ser um bug impossível, ciclos de tentativa e erro intermináveis, hipóteses que parecem promissoras por alguns minutos e logo são descartadas, e a determinação criativa de que precisamos para formar novas hipóteses. Por acaso, esse bug foi interessante o suficiente (e longo o suficiente) para gerar um post; espero ter gerado um relato com o qual todo mundo que escreve software possa se identificar.

{{< tweet 1083740680088969216 >}}