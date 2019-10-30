---
title: "Consultas SQL e Parênteses"
date: 2019-10-30T09:12:01-03:00
---

Esses dias precisei alterar uma constraint em uma de nossas tabelas. No caso, uma CHECK constraint que garantia que apenas os valores definidos no meu `enum` poderiam ser aceitos numa coluna que representava esse `enum` no banco. O script existente era mais ou menos assim (nomes das tabelas/colunas alterados pra facilitar):

```sql
Alter Table Posts
  Add Constraint CK_PostTypeId_RequiredFields Check (
    (PostTypeId = 1 And Foo Is Not Null)
    Or
    (PostTypeId = 2 And Bar Is Not Null)
    Or
    (PostTypeId = 3)
  )
```

A lógica da verificação efetuada pela constraint era mais ou menos essa:

- Se o tipo do post é = 1, a coluna Foo não pode ser nula
- Se o tipo do post é = 2, a coluna Bar não pode ser nula
- Se o tipo do post é = 3, tá tudo certo, nada a verificar
- Outros tipos de post não podem ser aceitos

*PS: É importante frisar que esse tipo de checagem tipicamente acontece na aplicação, não no banco. Porém, essa constraint que eu precisava alterar existe num domínio super crítico, e portanto decidimos implementar a redundância de verificações para garantir que, mesmo se tivéssemos uma injection ou algo do tipo, nenhum dado inválido seria persistido no banco de dados.*

A alteração que eu precisava fazer era adicionar um novo valor pro `enum PostType`, no caso, = 4. Mas antes de fazê-la, puxei diretamente do banco o script existente da constraint. Isso foi o que o SQL Server cuspiu pra mim:

```sql
Alter Table Posts
  Add Constraint CK_PostTypeId_RequiredFields Check (
    PostTypeId = 1 And Foo Is Not Null
    Or
	PostTypeId = 2 And Bar Is Not Null
    Or
    PostTypeId = 3
)
```

Meu coração gelou na hora. Cadê os parênteses? Puxei o script direto do banco de produção, e... mesma coisa. Minha cabeça de programadora acostumou-se a usar parênteses para indicar precedência em expressões lógicas, *mesmo quando não é necessário*, por uma questão de legibilidade. Como em SQL, assim como em C#, o operador AND (&&) tem precedência sobre o operador OR (||), os dois scripts acima se equivalem. O código abaixo ilustra mais ou menos o que quero dizer:

```csharp
var result = true || true && false;   // true 
    result = true || (true && false); // true
    result = (true || true) && false; // false
```

Ou seja: na terceira expressão, ao adicionar os parênteses, nós forçamos a avaliação do || primeiro, resultando na sub expressão `true && false`, que evidentemente avalia para `false`. Já as duas primeiras expressões são equivalentes: agrupar por parênteses não é necessário. Entretanto, eu *sempre* costumo adicionar os parênteses, acho que fica muito mais legível. Daí meu susto quando vi o código da constraint existente em produção: parecia um bug prestes a acontecer! Minha sugestão: na dúvida, bote os parênteses. Podem evitar erros futuros e facilitam a compreensão da expressão lógica. Tô falando por já ter [colocado um bug em produção](https://meta.stackoverflow.com/a/309967/411343) por causa disso :)