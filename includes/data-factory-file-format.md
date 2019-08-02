---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 89d5483347f93cd3b57a02ced19b1e8b099a5ab0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172593"
---
## <a name="specifying-formats"></a>Especificando formatos
O Azure Data Factory dá suporte aos tipos de formato a seguir:

* [Formato de texto](#specifying-textformat)
* [Formato JSON](#specifying-jsonformat)
* [Formato Avro](#specifying-avroformat)
* [Formato ORC](#specifying-orcformat)
* [Formato de Parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Especificando TextFormat
Se você quiser analisar os arquivos de texto ou gravar os dados no formato de texto, defina a propriedade `format` `type` como **TextFormat**. Você também pode especificar as seguintes propriedades **opcionais** na seção `format`. Veja a seção [Exemplo de TextFormat](#textformat-example) sobre a configuração.

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| columnDelimiter |O caractere usado para separar as colunas em um arquivo. Você pode considerar o uso de um caractere raro não imprimível que provavelmente não existe nos dados: por exemplo, especifique "\u0001" que representa o Início do Título (SOH). |Somente um caractere é permitido. O valor **padrão** é **vírgula (‘,’)** . <br/><br/>Para usar um caractere Unicode, consulte [Caracteres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obter o código correspondente. |Não |
| rowDelimiter |O caractere usado para separar as linhas em um arquivo. |Somente um caractere é permitido. O valor **padrão** é um dos seguintes valores na leitura: **["\r\n", "\r" e "\n"]** e **"\r\n"** na gravação. |Não |
| escapeChar |O caractere especial usado como escape do delimitador de coluna no conteúdo do arquivo de entrada. <br/><br/>Não é possível especificar ambos escapeChar e quoteChar para uma tabela. |Somente um caractere é permitido. Nenhum valor padrão. <br/><br/>Exemplo: se você tem a vírgula (', ') como o delimitador de coluna, mas deseja ter o caractere de vírgula no texto (exemplo: "Olá, mundo"), você pode definir '$' como o caractere de escape e usar a cadeia de caracteres "Olá$, mundo" na fonte. |Não |
| quoteChar |O caractere usado para citar um valor de cadeia de caracteres. Os delimitadores de linha e coluna dentro dos caracteres de aspas seriam tratados como parte do valor de cadeia de caracteres. Essa propriedade é aplicável a ambos os conjuntos de dados de entrada e de saída.<br/><br/>Não é possível especificar ambos escapeChar e quoteChar para uma tabela. |Somente um caractere é permitido. Nenhum valor padrão. <br/><br/>Por exemplo, se tiver a vírgula (,) como o delimitador de coluna, mas quiser ter o caractere de vírgula no texto (exemplo: <Hello, world>), você poderá definir " (aspas duplas) como o caractere de citação e usar a cadeia de caracteres "Hello, world" na origem. |Não |
| nullValue |Um ou mais caracteres usados para representar um valor nulo. |Um ou mais caracteres. Os valores **padrão** são **"\N" e "NULL"** na leitura e **"\N"** na gravação. |Não |
| encodingName |Especifique o nome de codificação. |Um nomes de codificação válido. Consulte [Propriedade Encoding.EncodingName](/dotnet/api/system.text.encoding). Por exemplo: windows-1250 ou shift_jis. O valor **padrão** é **UTF-8**. |Não |
| firstRowAsHeader |Especifica se a primeira linha será considerada como cabeçalho. Para um conjunto de dados de entrada, o Data Factory lê a primeira linha como cabeçalho. Para um conjunto de dados de saída, o Data Factory lê a primeira linha como cabeçalho. <br/><br/>Veja [Cenários para usar o `firstRowAsHeader` e o `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. |True<br/>**False (padrão)** |Não |
| skipLineCount |Indica o número de linhas a serem ignoradas ao ler dados de arquivos de entrada. Se skipLineCount e firstRowAsHeader forem especificados, as linhas serão ignoradas pela primeira vez e, em seguida, as informações de cabeçalho serão lidas do arquivo de entrada. <br/><br/>Veja [Cenários para usar o `firstRowAsHeader` e o `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. |Número inteiro |Não |
| treatEmptyAsNull |Especifica se uma cadeia de caracteres nula ou vazia será ou não tratada como um valor nulo ao ler dados de um arquivo de entrada. |**True (padrão)**<br/>Falso |Não |

#### <a name="textformat-example"></a>Exemplo de TextFormat
O exemplo a seguir mostra algumas das propriedades de formato para TextFormat.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Para usar um `escapeChar` em vez de `quoteChar`, substitua a linha com `quoteChar` por este escapeChar:

```json
"escapeChar": "$",
```

#### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Cenários de uso de firstRowAsHeader e skipLineCount
* Você está copiando de uma fonte que não é de arquivo para um arquivo de texto e quer adicionar uma linha de cabeçalho que contém os metadados de esquema (por exemplo: esquema SQL). Especifique `firstRowAsHeader` como true no conjunto de dados de saída para esse cenário.
* Você está copiando de um arquivo de texto contendo uma linha de cabeçalho para um coletor que não é em arquivo e gostaria de remover essa linha. Especifique `firstRowAsHeader` como true no conjunto de dados de entrada.
* Você está copiando de um arquivo de texto e deseja ignorar algumas linhas no início que não são informações de dados nem de cabeçalho. Especifique `skipLineCount` para indicar o número de linhas a serem ignoradas. Se o restante do arquivo contiver uma linha de cabeçalho, você também poderá especificar `firstRowAsHeader`. Se `skipLineCount` e `firstRowAsHeader` forem especificados, as linhas serão ignoradas pela primeira vez e, em seguida, as informações de cabeçalho serão lidas do arquivo de entrada

### <a name="specifying-jsonformat"></a>Especificando JsonFormat
Para **importar/exportar arquivos JSON como estão de/para o Azure Cosmos DB**, veja a seção [Importar/exportar documentos JSON](../articles/data-factory/v1/data-factory-azure-documentdb-connector.md#importexport-json-documents) no conector do Azure Cosmos DB com detalhes.

Se você quiser analisar os arquivos de JSON ou gravar os dados no formato JSON, defina a propriedade `format` `type` como **JsonFormat**. Você também pode especificar as seguintes propriedades **opcionais** na seção `format`. Veja a seção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| filePattern |Indique o padrão de dados armazenados em cada arquivo JSON. Os valores permitidos são: **setOfObjects** e **arrayOfObjects**. O valor **padrão** é **setOfObjects**. Veja a seção [Padrões de arquivo JSON](#json-file-patterns) para obter detalhes sobre esses padrões. |Não |
| jsonNodeReference | Se você quiser fazer uma iteração e extrair dados de objetos dentro de um campo de matriz com o mesmo padrão, especifique o caminho JSON da matriz. Esta propriedade só terá suporte na cópia de dados de arquivos JSON. | Não |
| jsonPathDefinition | Especifique a expressão de caminho JSON para cada mapeamento de coluna com um nome de coluna personalizado (iniciar com letra minúscula). Esta propriedade só terá suporte na cópia de dados de arquivos JSON, e você pode extrair dados de objeto ou de matriz. <br/><br/> Para os campos sob o objeto root, comece com root $; para os campos dentro da matriz escolhidos pela propriedade `jsonNodeReference`, comece do elemento de matriz. Veja a seção [Exemplo de JsonFormat](#jsonformat-example) sobre como configurar. | Não |
| encodingName |Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, confira: Propriedade [Encoding.EncodingName](/dotnet/api/system.text.encoding). Por exemplo: windows-1250 ou shift_jis. O valor **padrão** é: **UTF-8**. |Não |
| nestingSeparator |Caractere que é usado para separar os níveis de aninhamento. O valor padrão é '.' (ponto). |Não |

#### <a name="json-file-patterns"></a>Padrões de arquivo JSON

A atividade de cópia pode analisar os padrões dos arquivos JSON abaixo:

- **Tipo I: setOfObjects**

    Cada arquivo contém um único objeto ou vários objetos concatenados/delimitados por linhas. Quando essa opção é escolhida em um conjunto de dados de saída, a atividade de cópia produz um único arquivo JSON com cada objeto por linha (delimitados por linha).

    * **Exemplo de JSON de objeto único**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exemplo de JSON delimitado por linha**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exemplo de JSON concatenado**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Tipo II: arrayOfObjects**

    Cada arquivo contém uma matriz de objetos.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

#### <a name="jsonformat-example"></a>Exemplo de JsonFormat

**Caso 1: Copiar dados de arquivos JSON**

Confira abaixo dois tipos de exemplos ao copiar dados de arquivos JSON e os pontos genéricos para observar:

**Exemplo 1: extrair dados de objeto e de matriz**

Neste exemplo, você espera que um objeto JSON de raiz seja mapeado para um único registro no resultado tabular. Se você tiver um arquivo JSON com o seguinte conteúdo:  

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```
e quiser copiá-lo para uma tabela SQL do Azure no formato a seguir, ao extrair dados dos objetos e da matriz:

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | Computador | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 13/01/2017 11:24:37 |

O conjunto de dados de entrada com o tipo **JsonFormat** é definido da seguinte maneira: (definição parcial com apenas as partes relevantes). Mais especificamente:

- A seção `structure` define os nomes de coluna personalizada e o tipo de dados correspondente ao converter em dados tabulares. Esta seção é **opcional**, a menos que você tenha de fazer o mapeamento de colunas. Veja a seção Especificar a definição de estrutura para conjuntos de dados retangulares para obter mais detalhes.
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Para copiar dados da matriz, você pode usar **array[x].property** para extrair o valor da propriedade do objeto xth ou usar **array[*].property** para localizar o valor de qualquer objeto que contém essa propriedade.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}      
        }
    }
}
```

**Exemplo 2: cruzar aplicar vários objetos com o mesmo padrão da matriz**

Neste exemplo, você espera transformar um objeto JSON de raiz em vários registros no resultado tabular. Se você tiver um arquivo JSON com o seguinte conteúdo:  

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```
e você deseja copiá-lo para uma tabela do Azure SQL no formato a seguir, ao nivelar os dados de dentro da matriz e fazer uma união cruzada com as informações comuns de root:

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

O conjunto de dados de entrada com o tipo **JsonFormat** é definido da seguinte maneira: (definição parcial com apenas as partes relevantes). Mais especificamente:

- A seção `structure` define os nomes de coluna personalizada e o tipo de dados correspondente ao converter em dados tabulares. Esta seção é **opcional**, a menos que você tenha de fazer o mapeamento de colunas. Veja a seção Especificar a definição de estrutura para conjuntos de dados retangulares para obter mais detalhes.
- `jsonNodeReference` indica iterar e extrair dados dos objetos com o mesmo padrão em linhas da ordem da **matriz**.
- `jsonPathDefinition` especifica o caminho JSON para cada coluna que indica de onde extrair os dados. Neste exemplo, "ordernumber", "orderdate" e "city" estão sob o objeto root com caminho JSON começando com"$.", enquanto "order_pd" e "order_price" são definidos com caminho derivado do elemento de matriz sem "$.".

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}         
        }
    }
}
```

**Observe os seguintes pontos:**

* Se `structure` e `jsonPathDefinition` não forem definidos no conjunto de dados do Data Factory, a atividade de cópia detectará o esquema do primeiro objeto e mesclará todo o objeto.
* Se a entrada JSON tiver uma matriz por padrão, a atividade de cópia converterá o valor da matriz inteira em uma cadeia de caracteres. Você pode optar por extrair dados dele usando o `jsonNodeReference` e/ou o `jsonPathDefinition` ou ignorá-lo ao não especificá-lo no `jsonPathDefinition`.
* Se houver nomes duplicados no mesmo nível, a atividade de cópia selecionará o último entre eles.
* Os nomes de propriedade diferenciam maiúsculas de minúsculas. Duas propriedades com o mesmo nome, mas com maiúsculas e minúsculas diferentes são tratadas como duas propriedades separadas.

**Caso 2: Gravar dados no arquivo JSON file**

Se você tiver a tabela a seguir no Banco de Dados SQL:

| id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | Davi |
| 2 | 20170120 | 3500 | Pedro |
| 3 | 20170121 | 4000 | Jason |

e, para cada registro, você espera gravar em um objeto JSON neste formato:
```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

O conjunto de dados de saída com o tipo **JsonFormat** é definido da seguinte maneira: (definição parcial com apenas as partes relevantes). Mais especificamente, a seção `structure` define os nomes de propriedade personalizada no arquivo de destino, `nestingSeparator` (o padrão é ".") será usado para identificar a camada de aninhamento do nome. Esta seção é **opcional**, a menos que você queira alterar o nome da propriedade em comparação ao nome da coluna de origem ou aninhar algumas das propriedades.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

### <a name="specifying-avroformat"></a>Especificando AvroFormat
Se você quiser analisar os arquivos Avro ou gravar os dados no formato Avro, defina a propriedade `format` `type` como **AvroFormat**. Não será necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

```json
"format":
{
    "type": "AvroFormat",
}
```

Para usar o formato Avro em uma tabela de Hive, confira [Tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Observe os seguintes pontos:  

* Não há suporte para [Tipos de dados complexos](http://avro.apache.org/docs/current/spec.html#schema_complex) (registros, enumerações, matrizes, mapas, uniões e fixo).

### <a name="specifying-orcformat"></a>Especificando OrcFormat
Se você quiser analisar os arquivos ORC ou gravar os dados no formato ORC, defina a propriedade `format` `type` como **OrcFormat**. Não será necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Se você não estiver copiando arquivos ORC **como são** entre repositórios de dados locais e na nuvem, você precisará instalar o JRE 8 (Java Runtime Environment) no computador do gateway. Um gateway de 64 bits exige JRE de 64 bits, enquanto um gateway de 32 bits exige JRE de 32 bits. Você pode encontrar as duas versões [aqui](https://go.microsoft.com/fwlink/?LinkId=808605). Escolha aquela que for apropriada.
>
>

Observe os seguintes pontos:

* Não há suporte para tipos de dados complexos (STRUCT, MAP, LIST e UNION)
* O arquivo ORC tem três [opções relacionadas à compactação](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB e SNAPPY. O Data Factory dá suporte à leitura de dados de arquivo ORC em qualquer um dos formatos compactados acima. Ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao gravar um arquivo ORC, o Data Factory escolhe ZLIB, que é o padrão para ORC. Não há nenhuma opção para substituir esse comportamento neste momento.

### <a name="specifying-parquetformat"></a>Especificando ParquetFormat
Se você quiser analisar os arquivos Parquet ou gravar os dados no formato Parquet, defina a propriedade `format` `type` como **ParquetFormat**. Não será necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Se você não estiver copiando arquivos Parquet **no estado em que se encontram** entre armazenamentos de dados locais e na nuvem, você precisará instalar o JRE 8 (Java Runtime Environment) no computador do gateway. Um gateway de 64 bits exige JRE de 64 bits, enquanto um gateway de 32 bits exige JRE de 32 bits. Você pode encontrar as duas versões [aqui](https://go.microsoft.com/fwlink/?LinkId=808605). Escolha aquela que for apropriada.
>
>

Observe os seguintes pontos:

* Não há suporte para tipos de dados complexos (MAP, LIST)
* O arquivo Parquet tem as seguintes opções relacionadas à compactação: NONE, SNAPPY, GZIP e LZO. O Data Factory dá suporte à leitura de dados de arquivo ORC em qualquer um dos formatos compactados acima. Ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao gravar um arquivo Parquet, o Data Factory escolhe SNAPPY, que é o padrão para o formato Parquet. Não há nenhuma opção para substituir esse comportamento neste momento.
