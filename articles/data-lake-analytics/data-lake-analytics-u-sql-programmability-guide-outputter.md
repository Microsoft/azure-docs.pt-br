---
title: Guia de programação de saída definido pelo usuário do U-SQL para Azure Data Lake
description: Saiba mais sobre o UDO de saída definido pelo usuário do guia de programação do U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512503"
---
# <a name="use-user-defined-outputter"></a>Usar o emissor definido pelo usuário

## <a name="u-sql-udo-user-defined-outputter"></a>Udo do U-SQL: emissor definido pelo usuário
O outputter definido pelo usuário é outro UDO do U-SQL que permite estender uma funcionalidade interna do U-SQL. Assim como o extrator, há vários outputters internos.

* *Outputters.Text()*: grava dados em arquivos de texto delimitados de codificações diferentes.
* *Outputters.Csv()*: grava dados em arquivos CSV (valores separados por vírgula) de codificações diferentes.
* *Outputters.Tsv()*: grava dados em arquivos TSV (valores separados por tabulação) de codificações diferentes.

O outputter personalizado permite gravar dados em um formato definido personalizado. Isso pode ser útil para as seguintes tarefas:

* Gravando dados em arquivos semiestruturados ou não estruturados.
* Gravar dados em codificações sem suporte.
* Modificar dados de saída ou adicionar atributos personalizados.

## <a name="how-to-define-and-use-user-defined-outputter"></a>Como definir e usar o emissor definido pelo usuário
Para definir o outputter definido pelo usuário, precisamos criar a interface `IOutputter`.

A seguir temos a implementação da classe `IOutputter` base:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Todos os parâmetros de entrada para o outputter, como delimitadores de linha/coluna, codificação e afins precisam ser definidos no construtor da classe. A interface `IOutputter` também deve conter uma definição para a substituição `void Output`. O atributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` pode ser definido opcionalmente para o processamento de arquivos atômico. Para saber mais, consulte os detalhes a seguir.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` é chamado para cada linha de entrada. Retorna o conjunto de linhas `IUnstructuredWriter output`.
* A classe Constructor é usada para passar parâmetros ao outputter definido pelo usuário.
* `Close` é usado para, opcionalmente, substituir e liberar estado caro ou determinar quando a última linha foi gravada.

O atributo **SqlUserDefinedOutputter** indica que o tipo deve ser registrado como um outputter definido pelo usuário. Essa classe não pode ser herdada.

SqlUserDefinedOutputter é um atributo opcional para a definição de um outputter definido pelo usuário. Ele é usado para definir a propriedade AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **true** = indica que esse outputter exige arquivos de saída atômicos (JSON, XML, ...)
* **false** = indica que esse outputter pode lidar com arquivos divididos/distribuídos (CSV, SEQ, ...)

Os principais objetos de programação são **row** e **output**. O objeto **row** é usado para enumerar os dados de saída como interface `IRow`. **Saída** é usado para definir os dados de saída para o arquivo de destino.

Os dados de saída são acessados por meio da interface `IRow`. Os dados de saída são passados em uma linha por vez.

Os valores individuais são enumerados chamando o método Get da interface IRow:

```csharp
row.Get<string>("column_name")
```

Os nomes de coluna individuais podem ser determinados chamando `row.Schema`:

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Essa abordagem permite que criar um outputter flexível para qualquer esquema de metadados.

Os dados de saída são gravados em um arquivo usando `System.IO.StreamWriter`. O parâmetro de fluxo é definido como `output.BaseStream` como parte do `IUnstructuredWriter output`.

Observe que é importante liberar o buffer de dados para o arquivo depois de cada iteração de linha. Além disso, o objeto `StreamWriter` deve ser usado com o atributo Disposable habilitado (padrão) e a palavra-chave **using**:

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Caso contrário, chame o método Flush() explicitamente depois de cada iteração. Vamos mostrar isso no exemplo a seguir.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Definir cabeçalhos e rodapés para outputter definido pelo usuário
Para definir um cabeçalho, use o fluxo de execução única de iteração.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

O código no primeiro bloco `if (isHeaderRow)` é executado apenas uma vez.

Para o rodapé, use a referência à instância do objeto `System.IO.Stream` (`output.BaseStream`). Escreva o rodapé no método Close () da interface `IOutputter`.  (Para saber mais, veja a seção a seguir.)

Veja um exemplo de um outputter definido pelo usuário:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

E o script base U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Esse é um outputter HTML, que cria um arquivo HTML com dados da tabela.

### <a name="call-outputter-from-u-sql-base-script"></a>Chamando outputter do script base U-SQL
Para chamar um outputter personalizado do script base U-SQL, a nova instância do objeto outputter deve ser criada.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Para evitar a criação de uma instância do objeto no script base, podemos criar um wrapper de função, conforme mostrado em nosso exemplo anterior:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Nesse caso, a chamada original se parece com esta:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programação do U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)