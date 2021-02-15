---
title: Esquema e mapeamento de tipo de dados na atividade de cópia
description: Saiba mais sobre como a atividade de cópia no Azure Data Factory mapeia os esquemas e os tipos de dados dos dados de origem para coletar dados.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: 0aee6030e5608b5413864d6a32dc8442dd346f42
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392775"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Esquema e mapeamento de tipo de dados na atividade de cópia
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como a atividade de cópia de Azure Data Factory executa o mapeamento de esquema e o mapeamento de tipo de dados dos dados de origem para coletar dados.

## <a name="schema-mapping"></a>Mapeamento de esquema

### <a name="default-mapping"></a>Mapeamento padrão

Por padrão, a atividade de cópia mapeia os dados de origem para o coletor **por nomes de coluna** de maneira diferenciar maiúsculas de minúsculas. Se o coletor não existir, por exemplo, gravar em arquivo (s), os nomes de campo de origem serão persistidos como nomes de coletor. Esse mapeamento padrão dá suporte a esquemas flexíveis e descompasso de esquema da origem para o coletor a partir da execução até a execução. todos os dados retornados pelo armazenamento de dados de origem podem ser copiados para o coletor.

Se sua origem for um arquivo de texto sem linha de cabeçalho, o [mapeamento explícito](#explicit-mapping) será necessário, pois a origem não conterá nomes de coluna.

### <a name="explicit-mapping"></a>Mapeamento explícito

Você também pode especificar o mapeamento explícito para personalizar o mapeamento de coluna/campo da origem para o coletor com base em sua necessidade. Com o mapeamento explícito, você pode copiar apenas dados parciais de origem para o coletor ou mapear dados de origem para o coletor com nomes diferentes ou remodelar dados tabulares/hierárquicos. Atividade de cópia:

1. Lê os dados da origem e determina o esquema de origem.
2. Aplica o mapeamento definido.
3. Grava os dados no coletor.

Saiba mais sobre:

- [Origem de tabela para coletor de tabela](#tabular-source-to-tabular-sink)
- [Fonte hierárquica para coletor tabular](#hierarchical-source-to-tabular-sink)
- [Tabela/origem hierárquica para coletor hierárquico](#tabularhierarchical-source-to-hierarchical-sink)

Você pode configurar o mapeamento na interface do usuário de criação de Data Factory-> atividade de cópia-> guia mapeamento ou especificar programaticamente o mapeamento na Propriedade Copy-> `translator` . As propriedades a seguir têm suporte em `translator`  ->  `mappings` objetos de > de matriz-> `source` e `sink` , que aponta para a coluna/campo específico para mapear dados.

| Propriedade | Descrição                                                  | Obrigatório |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nome do campo/coluna de origem ou de coletor. Aplicar para fonte de tabela e coletor. | Sim      |
| ordinal  | Índice de coluna. Inicie de 1. <br>Aplica-se e é necessário ao usar texto delimitado sem linha de cabeçalho. | Não       |
| caminho     | Expressão de caminho JSON para cada campo a ser extraído ou mapeado. Aplicar para origem e coletor hierárquicos, por exemplo, Cosmos DB, MongoDB ou conectores REST.<br>Para campos sob o objeto raiz, o caminho JSON começa com root `$` ; para campos dentro da matriz escolhida pela `collectionReference` propriedade, o caminho JSON é iniciado a partir do elemento de matriz sem `$` . | Não       |
| type     | Data Factory tipo de dados provisório da coluna de origem ou do coletor. Em geral, você não precisa especificar ou alterar essa propriedade. Saiba mais sobre [mapeamento de tipo de dados](#data-type-mapping). | Não       |
| culture  | Cultura da coluna de origem ou do coletor. Aplicar quando o tipo for `Datetime` ou `Datetimeoffset` . O padrão é `en-us`.<br>Em geral, você não precisa especificar ou alterar essa propriedade. Saiba mais sobre [mapeamento de tipo de dados](#data-type-mapping). | Não       |
| format   | Cadeia de caracteres de formato a ser usada quando o tipo é `Datetime` ou `Datetimeoffset` . Consulte [Data personalizada e cadeias de caracteres de formato de hora](/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar a data e hora. Em geral, você não precisa especificar ou alterar essa propriedade. Saiba mais sobre [mapeamento de tipo de dados](#data-type-mapping). | Não       |

As propriedades a seguir têm suporte em `translator` além de `mappings` :

| Propriedade            | Descrição                                                  | Necessária |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Aplicar ao copiar dados de fonte hierárquica, por exemplo, Cosmos DB, MongoDB ou conectores REST.<br>Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. | Não       |

#### <a name="tabular-source-to-tabular-sink"></a>Origem de tabela para coletor de tabela

Por exemplo, para copiar dados do Salesforce para o banco de dados SQL do Azure e mapear explicitamente três colunas:

1. Na guia atividade de cópia – mapeamento de >, clique no botão **Importar esquemas** para importar os esquemas de origem e de coletor.

2. Mapeie os campos necessários e exclua/exclua o restante.

![Mapear tabular para tabular](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

O mesmo mapeamento pode ser configurado como o seguinte na carga da atividade de cópia (consulte `translator` ):

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Para copiar dados de arquivos de texto delimitados sem linha de cabeçalho, as colunas são representadas por ordinal em vez de nomes. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Fonte hierárquica para coletor tabular

Ao copiar dados da origem hierárquica para o coletor de tabela, a atividade de cópia dá suporte aos seguintes recursos:

- Extrair dados de objetos e matrizes.
- Cross aplicar vários objetos com o mesmo padrão de uma matriz, nesse caso, para converter um objeto JSON em vários registros em resultado de tabela.

Para uma transformação hierárquica e tabular mais avançada, você pode usar o [fluxo de dados](concepts-data-flow-overview.md). 

Por exemplo, se você tiver um documento do MongoDB de origem com o seguinte conteúdo:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
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
    "city": [ { "name": "Seattle" } ]
}
```

E você deseja copiá-lo em um arquivo de texto no formato a seguir com a linha de cabeçalho, mesclando os dados dentro da matriz *(order_pd e order_price)* e entre junções com as informações de raiz comuns *(número, data e cidade)*:

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Você pode definir esse mapeamento na interface do usuário de criação de Data Factory:

1. Na guia atividade de cópia – mapeamento de >, clique no botão **Importar esquemas** para importar os esquemas de origem e de coletor. Como Data Factory amostras dos principais objetos ao importar o esquema, se algum campo não aparecer, você poderá adicioná-lo à camada correta na hierarquia-focalizar um nome de campo existente e escolher Adicionar um nó, um objeto ou uma matriz.

2. Selecione a matriz da qual você deseja iterar e extrair dados. Ele será preenchido automaticamente como **referência de coleção**. Observação apenas uma única matriz tem suporte para essa operação.

3. Mapeie os campos necessários para o coletor. Data Factory determina automaticamente os caminhos JSON correspondentes para o lado hierárquico.

> [!NOTE]
> Para registros em que a matriz marcada como referência de coleção está vazia e a caixa de seleção está marcada, o registro inteiro é ignorado.

![Mapear hierárquica para tabular usando a interface do usuário](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

Você também pode alternar para o **Editor avançado**; nesse caso, você pode ver e editar os caminhos JSON dos campos diretamente. Se você optar por adicionar um novo mapeamento nessa exibição, especifique o caminho JSON.

![Mapear hierárquica para tabular usando o editor avançado](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

O mesmo mapeamento pode ser configurado como o seguinte na carga da atividade de cópia (consulte `translator` ):


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Tabela/origem hierárquica para coletor hierárquico

O fluxo de experiência do usuário é semelhante ao [coletor hierárquico de origem para tabular](#hierarchical-source-to-tabular-sink). 

Ao copiar dados da origem de tabela para o coletor hierárquico, não há suporte para gravar na matriz dentro do objeto.

Ao copiar dados da origem hierárquica para o coletor hierárquico, você também pode preservar a hierarquia inteira da camada, selecionando o objeto/matriz e mapeando para o coletor sem tocar nos campos internos.

Para uma transformação remodelação de dados mais avançada, você pode usar o [fluxo de dados](concepts-data-flow-overview.md). 

### <a name="parameterize-mapping"></a>Parametrizar mapeamento

Se você quiser criar um pipeline modelos para copiar um grande número de objetos dinamicamente, determine se você pode aproveitar o [mapeamento padrão](#default-mapping) ou se precisa definir o [mapeamento explícito](#explicit-mapping) para os respectivos objetos.

Se o mapeamento explícito for necessário, você poderá:

1. Defina um parâmetro com o tipo de objeto no nível do pipeline, por exemplo, `mapping` .

2. Parametrizar o mapeamento: na guia atividade de cópia – mapeamento de >, escolha Adicionar conteúdo dinâmico e selecione o parâmetro acima. A carga da atividade seria a seguinte:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Construa o valor a ser passado para o parâmetro Mapping. Deve ser o objeto inteiro de `translator` definição, consulte a seção exemplos no [mapeamento explícito](#explicit-mapping) . Por exemplo, para a origem de tabela para a cópia de coletor de tabela, o valor deve ser `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

A atividade de cópia executa tipos de origem no mapeamento de tipos de coletor com o seguinte fluxo: 

1. Converter de tipos de dados nativos de origem para Azure Data Factory tipos de dados provisórios.
2. Converta automaticamente o tipo de dados provisório conforme necessário para corresponder os tipos de coletor correspondentes, aplicáveis ao [mapeamento padrão](#default-mapping) e ao [mapeamento explícito](#explicit-mapping).
3. Converta de Azure Data Factory tipos de dados provisórios em tipos de dados nativos do coletor.

Atualmente, a atividade de cópia dá suporte aos seguintes tipos de dados provisórios: booliano, byte, matriz de bytes, DateTime, DatetimeOffset, Decimal, Double, GUID, Int16, Int32, Int64, SByte, Single, String, TimeSpan, UInt16, UInt32 e UInt64.

As conversões de tipo de dados a seguir têm suporte entre os tipos provisórios da origem para o coletor.

| Source\Sink | Boolean | Matriz de bytes | Decimal | Data/hora <small>(1)</small> | Ponto flutuante <small>(2)</small> | GUID | Inteiro <small>(3)</small> | String | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Boolean     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Matriz de bytes  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Data/Hora   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Ponto de flutuação | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Integer     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| String      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) data/hora inclui DateTime e DateTimeOffset.

(2) ponto flutuante inclui único e duplo.

(3) o inteiro inclui SByte, byte, Int16, UInt16, Int32, UInt32, Int64 e UInt64.

> [!NOTE]
> - Atualmente, essa conversão de tipo de dados tem suporte ao copiar entre dados tabulares. Não há suporte para fontes/coletores hierárquicos, o que significa que não há nenhuma conversão de tipo de dados definida pelo sistema entre os tipos intermediários de origem e coletor.
> - Esse recurso funciona com o modelo de conjunto de informações mais recente. Se você não vir essa opção na interface do usuário, tente criar um novo conjunto de um.

As propriedades a seguir têm suporte na atividade de cópia para conversão de tipo de dados (em `translator` seção para a criação programática):

| Propriedade                         | Descrição                                                  | Necessária |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | Habilite a nova experiência de conversão de tipo de dados. <br>O valor padrão é false devido à compatibilidade com versões anteriores.<br><br>Para novas atividades de cópia criadas por meio da interface do usuário de criação de Data Factory desde o final de junho de 2020, essa conversão de tipo de dados é habilitada por padrão para a melhor experiência, e você pode ver as seguintes configurações de conversão de tipo na guia atividade de cópia – mapeamento de > para cenários aplicáveis. <br>Para criar o pipeline programaticamente, você precisa definir explicitamente `typeConversion` a propriedade como true para habilitá-la.<br>Para as atividades de cópia existentes criadas antes desse recurso ser liberado, você não verá as opções de conversão de tipo na interface do usuário de criação de Data Factory para compatibilidade com versões anteriores. | Não       |
| typeConversionSettings           | Um grupo de configurações de conversão de tipo. Aplicar quando `typeConversion` estiver definido como `true` . As propriedades a seguir estão todas sob esse grupo. | Não       |
| *Sob `typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | Permitir truncamento de dados ao converter dados de origem para coletor com tipo diferente durante a cópia, por exemplo, de decimal para inteiro, de DatetimeOffset para DateTime. <br>O valor padrão é true. | Não       |
| treatBooleanAsNumber             | Trate os valores Boolianos como números, por exemplo, true como 1.<br>O valor padrão é false. | Não       |
| dateTimeFormat                   | Cadeia de formato ao converter entre datas sem deslocamento de fuso horário e cadeias de caracteres, por exemplo, `yyyy-MM-dd HH:mm:ss.fff` .  Consulte [cadeias de caracteres de formato de data e hora personalizadas](/dotnet/standard/base-types/custom-date-and-time-format-strings) para obter informações detalhadas. | Não       |
| dateTimeOffsetFormat             | Cadeia de caracteres de formato ao converter entre datas com deslocamento e cadeias de caracteres de fuso horário, por exemplo, `yyyy-MM-dd HH:mm:ss.fff zzz` .  Consulte [cadeias de caracteres de formato de data e hora personalizadas](/dotnet/standard/base-types/custom-date-and-time-format-strings) para obter informações detalhadas. | Não       |
| timeSpanFormat                   | Cadeia de formato ao converter entre períodos de tempo e cadeias de caracteres, por exemplo, `dd\.hh\:mm` . Consulte [cadeias de caracteres de formato de TimeSpan personalizadas](/dotnet/standard/base-types/custom-timespan-format-strings) para obter informações detalhadas. | Não       |
| culture                          | Informações de cultura a serem usadas ao converter tipos, por exemplo, `en-us` ou `fr-fr` . | Não       |

**Exemplo:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Modelos herdados

> [!NOTE]
> Os modelos a seguir para mapear colunas/campos de origem para o coletor ainda têm suporte como são para compatibilidade com versões anteriores. Sugerimos que você use o novo modelo mencionado no [mapeamento de esquema](#schema-mapping). A interface do usuário de criação do Data Factory mudou para gerar o novo modelo.

### <a name="alternative-column-mapping-legacy-model"></a>Alternativa-mapeamento de coluna (modelo herdado)

Você pode especificar a atividade de cópia-> `translator`  ->  `columnMappings` para mapear entre dados em formato tabular. Nesse caso, a seção "Structure" é necessária para os conjuntos de dados de entrada e saída. O mapeamento de coluna dá suporte ao **mapeamento de todas as colunas ou de um subconjunto de colunas na “structure” do conjunto de dados de origem para todas as colunas na “structure” do conjunto de dados do coletor**. Veja a seguir condições de erro que resultam em uma exceção:

- O resultado da consulta do armazenamento de dados de origem não tem um nome de coluna especificado na seção “structure” do conjunto de dados de entrada.
- O armazenamento de dados do coletor (se estiver com o esquema predefinido) não tem um nome de coluna especificado na seção “structure” do conjunto de dados de saída.
- Menos colunas ou mais colunas na seção "structure" do conjunto de dados do coletor do que o especificado no mapeamento.
- Mapeamento duplicado.

No exemplo a seguir, o conjunto de dados de entrada tem uma estrutura, e aponta para uma tabela em um Oracle Database local.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Neste exemplo, o conjunto de dados de saída tem uma estrutura e aponta para uma tabela em Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O JSON a seguir define uma atividade de cópia em um pipeline. As colunas da origem são mapeadas para as colunas no coletor usando a propriedade do **Tradutor**  ->  **ColumnMappings** .

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Se você está utilizando a sintaxe de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` para especificar o mapeamento de coluna, ainda há suporte no estado em que se encontra.

### <a name="alternative-schema-mapping-legacy-model"></a>Esquema alternativo – mapeamento (modelo herdado)

Você pode especificar a atividade de cópia-> `translator`  ->  `schemaMapping` para mapear entre dados em formato hierárquico e dados em formato de tabela, por exemplo, copiar do MongoDB/REST para o arquivo de texto e copiar do Oracle para a API do Azure Cosmos DB para MongoDB. As propriedades a seguir têm suporte na seção `translator` da atividade de cópia:

| Propriedade            | Descrição                                                  | Obrigatório |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | A propriedade Type do tradutor de atividade de cópia deve ser definida como: **TabularTranslator** | Sim      |
| schemaMapping       | Uma coleção de pares chave-valor, que representa a relação de mapeamento **do lado da origem para o lado do coletor**.<br/>- **Chave:** representa a origem. Para **fonte de tabela**, especifique o nome da coluna conforme definido na estrutura do conjunto de dados; para **fonte hierárquica**, especifique a expressão de caminho JSON para cada campo a ser extraído e mapeado.<br>- **Valor:** representa o coletor. Para o **coletor de tabela**, especifique o nome da coluna conforme definido na estrutura do conjunto de dados; para **coletor hierárquico**, especifique a expressão de caminho JSON para cada campo a ser extraído e mapeado. <br>No caso de dados hierárquicos, para campos em objeto raiz, o caminho JSON começa com root $; para campos dentro da matriz escolhida pela `collectionReference` propriedade, o caminho JSON é iniciado a partir do elemento da matriz. | Sim      |
| collectionReference | Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. Essa propriedade só terá suporte quando os dados hierárquicos forem a origem. | Não       |

**Exemplo: copiar do MongoDB para a Oracle:**

Por exemplo, se você tiver o documento do MongoDB com o seguinte conteúdo:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
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
    "city": [ { "name": "Seattle" } ]
}
```

e você deseja copiá-lo para uma tabela do Azure SQL no formato a seguir, ao nivelar os dados de dentro da matriz *(order_pd e order_price)* e fazer uma união cruzada com as informações de raiz comuns *(número, data e cidade)*:

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Configure a regra de mapeamento de esquema como o seguinte exemplo JSON de atividade de cópia:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
