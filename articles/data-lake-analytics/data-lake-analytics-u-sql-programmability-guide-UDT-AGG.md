---
title: Guia de programação de UDT e UDAGG do U-SQL para Azure Data Lake
description: Saiba mais sobre a programação de UDT e UDAGG do U-SQL no Azure Data Lake Analytics para permitir que você crie um bom script USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 10fcce9a667d9a08318f5adab804f482387052ff
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606650"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>Guia de programação do U-SQL-UDT e UDAGG



## <a name="use-user-defined-types-udt"></a>Tipos de CLR definidos pelo usuário: UDT
Os tipos definidos pelo usuário, ou UDT, são outro recurso de programação do U-SQL. O UDT do U-SQL atua como um tipo definido pelo usuário regular de C#. C# é uma linguagem fortemente tipada que permite o uso de tipos definidos pelo usuário personalizados e internos.

O U-SQL não pode implicitamente serializar ou desserializar UDTs arbitrários quando o UDT é passado entre os vértices em conjuntos de linhas. Isso significa que o usuário precisa fornecer um formatador explícito usando a interface IFormatter. Isso fornece ao U-SQL métodos de serialização e desserialização para o UDT.

> [!NOTE]
> Extratores internos e outputters de U-SQL atualmente não podem serializar ou desserializar dados de UDT de ou para arquivos, mesmo com o conjunto do IFormatter. Assim, quando você está gravando dados UDT em um arquivo com a instrução OUTPUT ou lendo com um extrator, precisa passá-lo como uma cadeia de caracteres ou matriz de bytes. Em seguida, deve chamar o código de serialização e desserialização (ou seja, o método ToString () do UDT) explicitamente. Por outro lado, extratores definidos pelo usuário e outputters podem ler e gravar UDTs.

Se tentarmos usam o UDT em EXTRATOR ou OUTPUTTER (fora do SELECT anterior), conforme mostrado aqui:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Recebemos o seguinte erro:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Para trabalhar com UDT em outptutter, também temos que serializá-lo para cadeia de caracteres com o método ToString() ou criar um outputter personalizado.

Atualmente, os UDTs não podem ser usados em GROUP BY. Se o UDT for usado em GROUP BY, o seguinte erro será gerado:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Para definir um UDT, temos que:

1. Adicione os seguintes namespaces:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Adicione `Microsoft.Analytics.Interfaces`, que é necessário para as interfaces UDT. Além disso, `System.IO` pode ser necessário para definir a interface IFormatter.

3. Defina o tipo definido usado com o atributo SqlUserDefinedType.

**SqlUserDefinedType** é usado para marcar uma definição de tipo em um assembly como um UDT (tipo definido pelo usuário) no U-SQL. As propriedades no atributo refletem as características físicas do UDT. Essa classe não pode ser herdada.

SqlUserDefinedType é um atributo necessário para a definição de UDT.

O construtor da classe:  

* SqlUserDefinedTypeAttribute(type formatter)

* Digite o parâmetro formatter: Required para definir um formatador UDT, mais especificamente, o tipo da interface `IFormatter` deve ser passado aqui.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* O UDT típico também requer a definição da interface IFormatter, conforme mostrado no exemplo abaixo:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

A `IFormatter` interface serializa e desserializa um grafo de objeto com o tipo de raiz de \<typeparamref name="T"> .

\<typeparam name="T">O tipo de raiz do grafo de objeto a serializar e desserializar.

* **Desserializar**: desserializa os dados no fluxo fornecido e reconstitui o grafo de objetos.

* **Serializar**: serializa um objeto ou grafo de objetos com determinada raiz para o fluxo fornecido.

Instância `MyType`: a instância do tipo.  
Gravador `IColumnWriter`/leitor `IColumnReader`: o fluxo da coluna subjacente.  
Contexto `ISerializationContext`: enumeração que define um conjunto de sinalizadores que especifica o contexto de origem ou destino para o fluxo durante a serialização.

* **Intermediário**: especifica que o contexto de origem ou de destino não é um armazenamento persistente.

* **Persistência**: especifica que o contexto de origem ou de destino é um armazenamento persistente.

Como um tipo C# regular, uma definição de UDT do U-SQL pode incluir substituições para operadores como +/==/!=. Ele também pode incluir métodos estáticos. Por exemplo, se formos usar esse UDT como um parâmetro para uma função agregada MIN do U-SQL, teremos que definir a substituição do operador <.

Anteriormente neste guia, demonstramos um exemplo da identificação do período fiscal na data específica no formato `Qn:Pn (Q1:P10)`. O exemplo a seguir mostra como definir um tipo personalizado para os valores do período fiscal.

Abaixo temos um exemplo de uma seção code-behind com interface IFormatter e UDT personalizado:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

O tipo definido inclui dois números: trimestre e mês. Os operadores `==/!=/>/<` e o método estático `ToString()` são definidos aqui.

Como mencionado anteriormente, o UDT pode ser usado nas expressões SELECT, mas não pode ser usado em OUTPUTTER/EXTRACTOR sem serialização personalizada. Ele precisa ser serializado como uma cadeia de caracteres com `ToString()` ou usado com um OUTPUTTER/EXTRACTOR personalizado.

Agora, vamos falar sobre o uso do UDT. Na seção code-behind, alteramos a nossa função GetFiscalPeriod para o seguinte:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Como você pode ver, ela retorna o valor do nosso tipo FiscalPeriod.

Aqui, fornecemos um exemplo de como usá-lo ainda mais no script base U-SQL. Este exemplo demonstra as diferentes formas de invocação do UDT no script U-SQL.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
        start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        fiscalquarter,
        fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Veja um exemplo de uma seção completa code-behind:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Use agregações definidas pelo usuário: UDAGG
As agregações definidas pelo usuário são funções relacionadas à agregação que não são enviadas para uso imediato com o U-SQL. O exemplo pode ser uma agregação para executar um cálculo matemático personalizado, concatenações de cadeia de caracteres ou manipulações com cadeias de caracteres, etc.

A definição de classe base da agregação definida pelo usuário é a seguinte:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** indica que o tipo deve ser registrado como uma agregação definida pelo usuário. Essa classe não pode ser herdada.

O atributo SqlUserDefinedType é **opcional** para a definição de UDAGG.


A classe base permite passar três parâmetros abstratos: dois como parâmetros de entrada e um como resultado. Os tipos de dados são variáveis e devem ser definidos durante a herança da classe.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** é invocado uma vez para cada grupo durante o cálculo. Ele fornece uma rotina de inicialização para cada grupo de agregação.  
* **Accumulate** é executado uma vez para cada valor. Ele fornece a funcionalidade principal para o algoritmo de agregação. Ele pode ser usado para agregar valores com vários tipos de dados que são definidos durante a herança da classe. Ele pode aceitar dois parâmetros de tipos de dados variáveis.
* **Terminate** é executado uma vez por grupo de agregação no fim do processamento para gerar o resultado para cada grupo.

Para declarar tipos de dados de entrada e saída corretos, use a seguinte definição de classe:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: primeiro parâmetro para accumulate
* T2: segundo parâmetro para acumular
* TResult: tipo de retorno de terminate

Por exemplo:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

ou

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Usar UDAGG no U-SQL
Para usar a UDAGG, primeiramente defina-a no code-behind ou faça referência a ela na DLL de programação existente, conforme discutido anteriormente.

Em seguida, use a seguinte sintaxe:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Veja um exemplo de UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

E o script base U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Nesse cenário de caso de uso, concatenamos os GUIDs de classe para usuários específicos.

## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programação do U-SQL-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
