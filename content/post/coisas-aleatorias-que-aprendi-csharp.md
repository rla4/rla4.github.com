---
title: "Coisas Aleatórias Que Aprendi com C#"
date: 2018-11-21T20:41:23-02:00
---

Eu trabalho com C# há mais de dez anos. Já fiz certificação, trabalhei em provavelmente mais de 40 projetos, e já li e escrevi bastante código. Ainda assim, vez ou outra me deparo com curiosidades/features da linguagem que ou eu desconhecia ou esqueci como funcionavam, por serem mais "raras" na selva mesmo. Esse post reúne algumas dessas redescobertas, recentes ou não, pra ver se dessa vez eu não esqueço :)

- `byte` é o único tipo numérico cujo nome padrão é unsigned - isso é, representa apenas números positivos. Bytes assinados são representados pelo tipo `sbyte`
- O sufixo `e` em declaração literal de números de ponto flutuante significa 10^X. Pode-se declarar, por exemplo, `1e3` pra representar de forma mais enigmática o número 1000
- A única vantagem de decorar enums com `[FlagsAttribute]` é a representação desses enums quando chamamos `ToString()`. Nada é alterado na estrutura ou comportamento da enumeração em si
- `[CallerFilePath]` e `[CallerLineNumber]` são atributos muito úteis pra escrever ferramentas de monitoramento/debugging
- `is null` e `== null` são diferentes, e a primeira forma é preferível, evitando possíveis problemas causados por sobrecarga do operador de igualdade. [[ref]](https://twitter.com/rla4/status/1039909190712602625) de quando aprendi, e alguns links na thread
- A sintaxe de inicialização de objetos, quando usada em coleções readonly, é traduzida em chamadas ao método `.Add` (e não em chamadas ao setter). O termo correto seria *collection initializer*. Exemplo:

```csharp
    public List<Attachment> Attachments { get; } = new List<Attachment>();
    // ...
    var message = new SlackMessage()
    {
        Attachments = { attachment }
    };
```
no caso acima, `Attachments` é uma propriedade readonly (veja que o setter não foi definido). A sintaxe de inicialização não falha pois é traduzida em uma chamada ao método `Add`. Veja que isso significa que sucessivas inicializações com esta mesma sintaxe **não** "limpam" a coleção. [[ref]](https://stackoverflow.com/questions/5646285/c-sharp-object-initialization-of-read-only-collection-properties)