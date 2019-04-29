---
title: Copiar dados do SAP Business Warehouse via Open Hub com o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do SAP BW (Business Warehouse) via Open Hub para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 03/08/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: c64842dc89c9519c738701558f510940f4cc148d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848855"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse via Open Hub com o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP BW (Business Warehouse) via Open Hub. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do SAP Business Warehouse via Open Hub para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP Business Warehouse Open Hub dá suporte a:

- SAP Business Warehouse **versão 7.01 ou superior (em uma recente SAP suporte pacote pilha liberadas após o ano de 2015)**.
- Cópia de dados por meio da tabela de local de destino do Open Hub que pode ser, por baixo, DSO, InfoCube, MultiProvider, DataSource etc.
- À cópia de dados usando a autenticação Básica.
- Conexão ao Servidor de Aplicativos.

## <a name="sap-bw-open-hub-integration"></a>Integração do SAP BW Open Hub 

O [Serviço do SAP BW Open Hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) é uma maneira eficiente de extrair dados do SAP BW. O diagrama a seguir mostra um dos fluxos típicos que os clientes têm em seu sistema SAP, caso em que os dados fluem do SAP ECC -> PSA -> DSO -> Cubo.

O SAP BW OHD (Destino de Open Hub) define o destino ao qual os dados do SAP são retransmitidos. Quaisquer objetos com suporte pelo processo de transferência de dados do SAP (DTP) podem ser usados como fontes de dados de hub aberto, por exemplo, o DSO, InfoCube, fonte de dados, etc. Tipo de Destino Open Hub – no qual os dados retransmitidos são armazenados – pode ser composto por tabelas de banco de dados (locais ou remotas) e arquivos simples. Esse conector do SAP BW Open Hub é compatível com copiar dados da tabela local do OHD no BW. Caso você esteja usando outros tipos, poderá conectar-se diretamente ao banco de dados ou sistema de arquivos usando outros conectores.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Fluxo de extração de delta

Conector de Hub aberto do AAD SAP BW oferece duas propriedades opcionais: `excludeLastRequest` e `baseRequestId` que pode ser usado para tratar da carga de delta do Open Hub. 

- **excludeLastRequestId**: Se você deseja excluir os registros da última solicitação. Valor padrão é true. 
- **baseRequestId**: A ID da solicitação do carregamento delta. Depois que ele for definido, somente os dados com ID de solicitação maior que o valor dessa propriedade serão recuperados. 

Em geral, a extração de InfoProviders do SAP para o Azure Data Factory (ADF) consiste em 2 etapas: 

1. **Processo de transferência de dados de BW da SAP (DTP)** essa etapa copia os dados de um InfoProvider de BW SAP em uma tabela de SAP BW Open Hub 

1. **Cópia de dados do ADF** nesta etapa, a tabela do Open Hub é lido pelo conector do ADF 

![Fluxo de extração de delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

A primeira etapa, é executada uma DTP. Cada execução cria uma nova ID de solicitação SAP. A ID da solicitação é armazenada na tabela Open Hub e, em seguida, é usada pelo conector do AAD para identificar o delta. As duas etapas executadas de forma assíncrona: o DTP é disparado pelo SAP e a cópia de dados do ADF é disparada por meio do ADF. 

Por padrão, ADF não está lendo o delta mais recente da tabela de Open Hub (a opção "última solicitação de exclusão" é true). Por meio deste, os dados no ADF não serão 100% atualizada com os dados na tabela Open Hub (o último delta está ausente). Em troca, esse procedimento garante que nenhuma linha se perder causado pela extração assíncrona. Ele funciona bem, mesmo quando o ADF está lendo a tabela do Open Hub, enquanto o DTP ainda é escrita na mesma tabela. 

Você geralmente armazena a ID de solicitação máxima de copiado na última execução pelo ADF em um armazenamento de dados de preparo (como BLOBs do Azure no acima diagrama). Portanto, a mesma solicitação não é lido uma segunda vez pelo ADF na execução subsequente. Enquanto isso, observe que os dados não são excluídos automaticamente da tabela de Hub aberto.

Delta adequada, lidar com isso não é permitido para que solicitações IDs de DTPs diferentes na mesma tabela Open Hub. Portanto, você não deve criar DTP mais de um para cada destino aberto de Hub (OHD). Quando a necessidade de extração completa e Delta de InfoProvider o mesmo, você deve criar dois OHDs para InfoProvider o mesmo. 

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do SAP Business Warehouse Open Hub, você precisa:

- Configurar um tempo de execução da integração auto-hospedada com versão 3.13 ou superior. Consulte o artigo [Self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Integration Runtime auto-hospedado) para obter detalhes.

- Baixar a versão de **64 bits do [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** do site da SAP e instalá-la no computador de IR auto-hospedado. Ao instalar, na janela de etapas de instalação opcionais, verifique se você selecionou a opção **Instalar Assemblies no GAC** conforme mostra a imagem a seguir. 

    ![Instalar o SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O usuário do SAP que está sendo usado no conector do Data Factory BW precisa ter as seguintes permissões: 

    - Autorização para RFC e SAP BW. 
    - Permissões para a Atividade de "Executar" do Objeto de Autorização "S_SDSAUTH".

- Criar tipo de Destino do SAP Open Hub como **Tabela de Banco de Dados** com opção "Chave Técnica" marcada.  Também é recomendável deixar a opção Excluir Dados da Tabela desmarcada, embora não seja necessário. Aproveite o DTP (execute ou integre diretamente na cadeia de processo existente) para descarregar dados do objeto de origem (como um cubo) que você escolheu para a tabela de destino do Open Hub.

## <a name="getting-started"></a>Introdução

> [!TIP]
>
> Para obter uma explicação de como usar o conector do SAP BW Open Hub, consulte [carregar dados do SAP BW (Business Warehouse) usando o Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir apresentam detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas do conector do SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do SAP BW Open Hub (Business Warehouse):

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **SapOpenHub** | Sim |
| Servidor | Nome do servidor no qual reside a instância do SAP BW. | Sim |
| systemNumber | Número de sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim |
| clientId | ID de Cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| Linguagem | Idioma que o sistema SAP usa. | Não (o valor padrão é **EN**)|
| userName | Nome do usuário que tem acesso ao servidor SAP. | Sim |
| Senha | Senha do usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Salesforce.

Para copiar dados de e para o SAP BW Open Hub, defina a propriedade type do conjunto de dados como **SapOpenHubTable**. Há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como **SapOpenHubTable**.  | Sim |
| openHubDestinationName | O nome de Destino do Open Hub do qual copiar dados. | Sim |
| excludeLastRequest | Se você deseja excluir os registros da última solicitação. | Não (o padrão é **true**) |
| baseRequestId | A ID da solicitação do carregamento delta. Depois que ele for definido, somente os dados com requestId **maior do que** o valor dessa propriedade serão recuperados.  | Não  |

>[!TIP]
>Se a tabela do Open Hub contém apenas os dados gerados por ID de solicitação única, por exemplo, você sempre realiza carga completa e substitui os dados existentes na tabela ou somente executa o DTP uma vez para teste; lembre-se de desmarcar a opção "excludeLastRequest" para copiar os dados.

**Exemplo:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub como origem

Para copiar dados do SAP BW Open Hub, defina o tipo de origem na atividade de cópia como **SapOpenHubSource**. Embora não haja nenhuma propriedade de tipo específico adicional necessária na seção de **origem** da atividade de cópia.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapeamento de tipo de dados para SAP BW Open Hub

Ao copiar dados do SAP BW Open Hub, os seguintes mapeamentos são usados de tipos de dados do SAP BW para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo SAP ABAP | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| C (Cadeia de caracteres) | Cadeia de caracteres |
| I (inteiro) | Int32 |
| F (Flutuante) | Double |
| D (Data) | Cadeia de caracteres |
| T (Hora) | Cadeia de caracteres |
| P (BCD Empacotado, Moeda, Decimal, Qtd) | Decimal |
| N (Numc) | Cadeia de caracteres |
| X (Binário e Bruto) | Cadeia de caracteres |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
