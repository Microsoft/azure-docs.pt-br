---
title: Guia de programação do processador do U-SQL definido pelo usuário para Azure Data Lake
description: Saiba mais sobre o guia de programação do UDO do U-SQL-processador definido pelo usuário.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512512"
---
# <a name="use-user-defined-processor"></a>Usar o processador definido pelo usuário

## <a name="u-sql-udo-user-defined-processor"></a>UDO U-SQL: processador definido pelo usuário
O processador definido pelo usuário, ou UDP, é um tipo de UDO do U-SQL que permite processar as linhas de entrada aplicando recursos de programação. O UDP permite combinar colunas, modificar valores e adicionar novas colunas, se necessário. Basicamente, ele ajuda a processar um conjunto de linhas para produzir elementos de dados necessários.

## <a name="how-to-define-and-use-user-defined-processor"></a>Como definir e usar o processador definido pelo usuário
Para definir um UDP, precisamos criar uma interface `IProcessor` com o atributo `SqlUserDefinedProcessor`, que é opcional para o UDP.

Essa interface deve conter a definição para a substituição do conjunto de linhas de interface `IRow`, conforme mostrado no exemplo abaixo:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** indica que o tipo deve ser registrado como um processador definido pelo usuário. Essa classe não pode ser herdada.

O atributo SqlUserDefinedProcessor é **opcional** para a definição de UDP.

Os principais objetos de programação são **input** e **output**. O objeto Input é usado para enumerar dados de entrada e saída e para definir dados de saída como resultado da atividade do processador.

Para enumeração de colunas de entrada, usamos o método `input.Get`.

```csharp
string column_name = input.Get<string>("column_name");
```

O parâmetro para o método `input.Get` é uma coluna passada como parte da cláusula `PRODUCE` da instrução `PROCESS` do script base U-SQL. Precisamos usar o tipo de dados correto aqui.

Para saída, use o método `output.Set`.

É importante observar que o produtor personalizado gera apenas colunas e valores definidos com a chamada de método `output.Set`.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

A saída real do processador é disparada chamando `return output.AsReadOnly();`.

Veja abaixo um exemplo de processador:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

Nesse cenário de caso de uso, o processador está gerando a nova coluna chamada "full_description" combinando as colunas existentes, nesse caso, "user" em letras maiúsculas e "des". Ele também regenera um GUID e retorna o original, bem como novos valores de GUID.

Como você pode ver no exemplo anterior, é possível chamar métodos C# durante a chamada de método `output.Set`.

Abaixo temos um exemplo de script base U-SQL que usa um processador personalizado:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programação do U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)