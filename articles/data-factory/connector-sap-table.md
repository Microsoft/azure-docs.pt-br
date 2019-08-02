---
title: Copiar dados de uma tabela do SAP usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de uma tabela do SAP para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827762"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiar dados de uma tabela do SAP usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de uma tabela SAP. Para obter mais informações, consulte [visão geral da atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de uma tabela do SAP para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, consulte o [armazenamentos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de tabela SAP dá suporte a:

- Copiar dados de uma tabela SAP em:

  - Componente Central SAP ERP SAP ECC () versão 7.01 ou posterior (em uma recente SAP suporte pacote pilha liberada após 2015).
  - SAP BW (Business Warehouse SAP) versão 7.01 ou posterior.
  - SAP S/4HANA.
  - Outros produtos do SAP Business Suite versão 7.01 ou posterior.

- Copiar dados de uma tabela de transparentes SAP, uma tabela em pool, uma tabela clusterizada e um modo de exibição.
- Copiar dados usando a autenticação básica ou proteger as comunicações SNC (rede), se SNC está configurado.
- Conectando a um servidor de aplicativos SAP ou servidor de mensagens SAP.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector de tabela do SAP, você precisa:

- Configure um integration runtime auto-hospedado (versão 3,17 ou posterior). Para obter mais informações, consulte [criar e configurar um integration runtime auto-hospedado](create-self-hosted-integration-runtime.md).

- Download de 64 bits [conector do SAP para o Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) no site da SAP e instalá-lo no computador do tempo de execução de integração auto-hospedado. Durante a instalação, verifique se você selecionou o **instalar Assemblies ao GAC** opção a **etapas de configuração opcionais** janela.

  ![Instalar o conector do SAP para .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O usuário do SAP que está sendo usado no conector do SAP de fábrica de dados de tabela deve ter as seguintes permissões:

  - Autorização para usar destinos de chamada de função remota (RFC).
  - Permissões para a atividade de execução do objeto S_SDSAUTH autorização.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector de tabela do SAP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço do SAP BW Open Hub vinculado:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| `type` | O `type` propriedade deve ser definida como `SapTable`. | Sim |
| `server` | O nome do servidor no qual a instância do SAP está localizada.<br/>Use para se conectar a um servidor de aplicativos SAP. | Não |
| `systemNumber` | O número de sistema do sistema SAP.<br/>Use para se conectar a um servidor de aplicativos SAP.<br/>Valor permitido: Um número decimal de dois dígitos representado como uma cadeia de caracteres. | Não |
| `messageServer` | O nome de host do servidor de mensagens SAP.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `messageServerService` | O nome do serviço ou a porta número do servidor de mensagens.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `systemId` | A ID do sistema SAP em que a tabela está localizada.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `logonGroup` | O grupo de logon para o sistema SAP.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `clientId` | A ID do cliente no sistema SAP.<br/>Valor permitido: Um número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| `language` | O idioma que usa o sistema SAP.<br/>O valor padrão é `EN`.| Não |
| `userName` | O nome do usuário que tem acesso ao servidor SAP. | Sim |
| `password` | A senha do usuário. Marcar este campo com o `SecureString` tipo de armazená-lo com segurança no Data Factory, ou [fazer referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| `sncMode` | O indicador de ativação SNC para acessar o servidor SAP em que a tabela está localizada.<br/>Use se você quiser usar SNC para se conectar ao servidor SAP.<br/>Valores permitidos são `0` (desativado, o padrão) ou `1` (ativado). | Não |
| `sncMyName` | O nome do iniciador SNC para acessar o servidor SAP em que a tabela está localizada.<br/>Aplica-se quando `sncMode` está em. | Não |
| `sncPartnerName` | O communication nome do parceiro SNC para acessar o servidor SAP em que a tabela está localizada.<br/>Aplica-se quando `sncMode` está em. | Não |
| `sncLibraryPath` | Biblioteca do produto de segurança externas para acessar o servidor SAP onde está localizada a tabela.<br/>Aplica-se quando `sncMode` está em. | Não |
| `sncQop` | O nível de SNC qualidade de proteção a ser aplicado.<br/>Aplica-se quando `sncMode` está em. <br/>Valores permitidos são `1` (autenticação), `2` (integridade), `3` (privacidade), `8` (padrão), `9` (máximo). | Não |
| `connectVia` | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Um tempo de execução de integração auto-hospedado é necessário, conforme mencionado anteriormente na [pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: Conectar-se a um servidor de aplicativos SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Exemplo 2: Conectar a um servidor de mensagens SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Exemplo 3: Conectar-se usando SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades para definir conjuntos de dados, consulte [conjuntos de dados](concepts-datasets-linked-services.md). A seção a seguir fornece uma lista das propriedades com suporte pelo conjunto de dados de tabela SAP.

Para copiar dados de e para o serviço do SAP BW Open Hub vinculado, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| `type` | O `type` propriedade deve ser definida como `SapTableResource`. | Sim |
| `tableName` | O nome da tabela para copiar dados do SAP. | Sim |

### <a name="example"></a>Exemplo

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). A seção a seguir fornece uma lista das propriedades com suporte pela fonte de tabela de SAP.

### <a name="sap-table-as-a-source"></a>Tabela SAP como uma fonte

Para copiar dados de uma tabela do SAP, há suporte para as seguintes propriedades:

| Propriedade                         | DESCRIÇÃO                                                  | Obrigatório |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | O `type` propriedade deve ser definida como `SapTableSource`.         | Sim      |
| `rowCount`                         | O número de linhas a serem recuperadas.                              | Não       |
| `rfcTableFields`                   | Os campos (colunas) para copiar da tabela de SAP. Por exemplo, `column0, column1`. | Não       |
| `rfcTableOptions`                  | As opções para filtrar as linhas em uma tabela SAP. Por exemplo, `COLUMN0 EQ 'SOMEVALUE'`. Consulte também a tabela de operador de consulta SAP neste artigo. | Não       |
| `customRfcReadTableFunctionModule` | Um módulo personalizado de função do RFC, pode ser usado para ler dados de uma tabela SAP.<br>Você pode usar um módulo personalizado de função RFC para definir como os dados são recuperados do seu sistema SAP e retornados ao Data Factory. O módulo de função personalizada deve ter uma interface implementada (importar, exportar, tabelas) que é semelhante ao `/SAPDS/RFC_READ_TABLE2`, que é a interface padrão usada pelo Data Factory. | Não       |
| `partitionOption`                  | O mecanismo de partição para ler de uma tabela SAP. As opções com suporte incluem: <ul><li>`None`</li><li>`PartitionOnInt` (inteiro normal ou valores inteiros com zero preenchimento à esquerda, tal como `0000012345`)</li><li>`PartitionOnCalendarYear` (4 dígitos no formato "Aaaa")</li><li>`PartitionOnCalendarMonth` (6 dígitos no formato "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 dígitos no formato "AAAAMMDD")</li></ul> | Não       |
| `partitionColumnName`              | O nome da coluna usada para particionar os dados.                | Não       |
| `partitionUpperBound`              | O valor máximo da coluna especificada em `partitionColumnName` que será usado para continuar com o particionamento. | Não       |
| `partitionLowerBound`              | O valor mínimo da coluna especificada em `partitionColumnName` que será usado para continuar com o particionamento. | Não       |
| `maxPartitionsNumber`              | O número máximo de partições para dividir os dados.     | Não       |

>[!TIP]
>Se sua tabela SAP tem um grande volume de dados, como várias bilhões linhas, use `partitionOption` e `partitionSetting` para dividir os dados em partições menores. Nesse caso, os dados são lidos por partição, e cada partição de dados é recuperada do servidor SAP por meio de uma única chamada RFC.<br/>
<br/>
>Levando `partitionOption` como `partitionOnInt` por exemplo, o número de linhas em cada partição é calculado com esta fórmula: (total de linhas incluído entre `partitionUpperBound` e `partitionLowerBound`) /`maxPartitionsNumber`.<br/>
<br/>
>Para executar as partições em paralelo para acelerar a cópia, é altamente recomendável fazer `maxPartitionsNumber` um múltiplo do valor da `parallelCopies` propriedade. Para obter mais informações, consulte [cópia paralela](copy-activity-performance.md#parallel-copy).

No `rfcTableOptions`, você pode usar os seguintes operadores de consulta comuns do SAP para filtrar as linhas:

| Operador | DESCRIÇÃO |
| :------- | :------- |
| `EQ` | Igual a |
| `NE` | Não igual a |
| `LT` | Menor que |
| `LE` | Menor ou igual a |
| `GT` | Maior que |
| `GE` | Maior ou igual a |
| `LIKE` | Como em `LIKE 'Emma%'` |

### <a name="example"></a>Exemplo

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapeamentos de tipo de dados para uma tabela SAP

Quando você estiver copiando dados de uma tabela SAP, os seguintes mapeamentos são usados de tipos de dados de tabela do SAP para os tipos de dados provisórios do Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo SAP ABAP | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| `C` (String) | `String` |
| `I` (Inteiro) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Date) | `String` |
| `T` (Hora) | `String` |
| `P` (Compactado e BCD, moeda, Decimal, Qty) | `Decimal` |
| `N` (Numérico) | `String` |
| `X` (Binário e brutos) | `String` |

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
