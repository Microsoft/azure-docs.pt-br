---
title: Guia de programação do extrator do U-SQL definido pelo usuário para Azure Data Lake
description: Saiba mais sobre o guia de programação do UDO do U-SQL – extrator definido pelo usuário.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: ad7f6336753903533771033de21aec8262425a61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608010"
---
# <a name="use-user-defined-extractor"></a>Usar extrator definido pelo usuário

## <a name="u-sql-udo-user-defined-extractor"></a>UDO do U-SQL: extrator definido pelo usuário
O U-SQL permite importar dados externos usando uma instrução EXTRACT. Uma instrução EXTRACT pode usar extratores de UDO internos:  

* *Extractors.Text()*: fornece extração dos arquivos de texto delimitados de diferentes codificações.

* *Extractors.Csv()*: fornece extração de arquivos CSV (valores separados por vírgula) de diferentes codificações.

* *Extractors.Tsv()*: fornece extração de arquivos TSV (valores separados por tabulação) de diferentes codificações.

Pode ser útil desenvolver um extrator personalizado. Isso pode ser útil durante a importação de dados para executar uma das seguintes tarefas:

* Modificar dados de entrada ao dividir colunas e modificar os valores individuais. A funcionalidade PROCESSOR é melhor para combinar colunas.
* Analisar dados não estruturados, como páginas da Web e emails, ou semiestruturados, como XML/JSON.
* Analisar dados na codificação sem suporte.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Como definir e usar o extrator definido pelo usuário
Para definir o extrator definido pelo usuário, ou UDE, precisamos criar uma interface `IExtractor`. Todos os parâmetros de entrada para o extrator, como delimitadores de linha/coluna e codificação precisam ser definidos no construtor da classe. A interface `IExtractor` também deve conter uma definição para a substituição `IEnumerable<IRow>` da seguinte forma:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

O atributo **SqlUserDefinedExtractor** indica que o tipo deve ser registrado como um extrator definido pelo usuário. Essa classe não pode ser herdada.

SqlUserDefinedExtractor é um atributo opcional para a definição de UDE. Ele é usado para definir a propriedade AtomicFileProcessing do objeto UDE.

* bool     AtomicFileProcessing   

* **true** = indica que esse extrator exige arquivos de entrada atômicos (JSON, XML, ...)
* **false** = indica que esse extrator pode lidar com arquivos divididos/distribuídos (CSV, SEQ, ...)

Os principais objetos de programação do UDE são **input** e **output**. O objeto de entrada é usado para enumerar os dados de entrada como `IUnstructuredReader`. O objeto de saída é usado para definir os dados de saída como resultado da atividade extractor.

Os dados de entrada são acessados por meio de `System.IO.Stream` e `System.IO.StreamReader`.

Para enumeração de colunas de entrada, primeiramente dividimos o fluxo de entrada usando um delimitador de linha.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Em seguida, dividimos ainda mais a linha de entrada em partes de coluna.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Para definir os dados de saída, usamos o método `output.Set`.

É importante entender que o extrator personalizado só gera colunas e valores que são definidos com a saída. Defina a chamada do método.

```csharp
output.Set<string>(count, part);
```

A saída real do extrator é disparada chamando `yield return output.AsReadOnly();`.

Vejamos o exemplo do extrator:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

Nesse cenário de caso de uso, o extrator regenera o GUID para a coluna "guid" e converte os valores da coluna "usuário" em letras maiúsculas. Os extratores personalizados podem produzir resultados mais complicados ao analisar dados de entrada e manipulá-los.

Script base U-SQL que usa um extrator personalizado:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programação do U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)