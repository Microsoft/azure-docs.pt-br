---
title: Copiar dados do SAP Business Warehouse via hub aberto
description: Saiba como copiar dados do SAP BW (Business Warehouse) via Open Hub para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2020
ms.openlocfilehash: ca8fad59e581ef3f5a3ebf585356564d539f0bbd
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430723"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse via Open Hub com o Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP BW (Business Warehouse) via Open Hub. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada sobre cada conector SAP, análise e diretrizes.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este SAP Business Warehouse via conector de Hub aberto tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do SAP Business Warehouse via Open Hub para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP Business Warehouse Open Hub dá suporte a:

- SAP Business Warehouse **versão 7, 1 ou superior (em uma pilha recente do pacote de suporte SAP lançada após o ano 2015)**. Não há suporte para SAP BW/4HANA por este conector.
- Copiar dados por meio da tabela local de destino de Hub aberto, que embaixo pode ser DSO, InfoCube, multifornecedor, DataSource, etc.
- À cópia de dados usando a autenticação Básica.
- Conectando-se a um servidor de aplicativos SAP ou servidor de mensagens SAP.
- Recuperando dados via RFC.

## <a name="sap-bw-open-hub-integration"></a>Integração do SAP BW Open Hub 

O [Serviço do SAP BW Open Hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) é uma maneira eficiente de extrair dados do SAP BW. O diagrama a seguir mostra um dos fluxos típicos que os clientes têm em seu sistema SAP, caso em que os dados fluem do SAP ECC -> PSA -> DSO -> Cubo.

O SAP BW OHD (Destino de Open Hub) define o destino ao qual os dados do SAP são retransmitidos. Todos os objetos com suporte do SAP Transferência de Dados Process (DTP) podem ser usados como fontes de dados de Hub aberto, por exemplo, DSO, InfoCube, DataSource, etc. Tipo de destino de Hub aberto-onde os dados retransmitidos são armazenados-podem ser tabelas de banco de dados (locais ou remotos) e arquivos simples. Esse conector do SAP BW Open Hub é compatível com copiar dados da tabela local do OHD no BW. Caso você esteja usando outros tipos, poderá conectar-se diretamente ao banco de dados ou sistema de arquivos usando outros conectores.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Fluxo de extração de Delta

O ADF SAP BW Open Hub Connector oferece duas propriedades opcionais: `excludeLastRequest` e `baseRequestId` que podem ser usadas para lidar com a carga Delta do Open Hub. 

- **excludeLastRequestId**: se os registros da última solicitação devem ser excluídos. O valor padrão é true. 
- **baseRequestId**: a ID da solicitação de carregamento Delta. Depois que ele for definido, somente os dados com requestId maior do que o valor dessa propriedade serão recuperados. 

Em geral, a extração do SAP InfoProviders para Azure Data Factory (ADF) consiste em duas etapas: 

1. **Processo de transferência de dados de SAP BW (DTP)** Esta etapa copia os dados de um SAP BW Infoprovider para uma SAP BW tabela de Hub aberta 

1. **Cópia de dados do ADF** Nesta etapa, a tabela de Hub aberta é lida pelo conector do ADF 

![Fluxo de extração de Delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Na primeira etapa, um DTP é executado. Cada execução cria uma nova ID de solicitação do SAP. A ID da solicitação é armazenada na tabela de Hub aberta e, em seguida, é usada pelo conector do ADF para identificar o Delta. As duas etapas são executadas de forma assíncrona: o DTP é disparado pelo SAP e a cópia de dados do ADF é disparada por meio do ADF. 

Por padrão, o ADF não está lendo o último Delta da tabela de Hub aberta (a opção "excluir última solicitação" é verdadeira). Aqui, os dados no ADF não são 100% atualizados com os dados na tabela de Hub aberta (o último Delta está ausente). Em retorno, esse procedimento garante que nenhuma linha seja perdida causada pela extração assíncrona. Ele funciona bem mesmo quando o ADF está lendo a tabela de Hub aberto enquanto o DTP ainda está gravando na mesma tabela. 

Normalmente, você armazena a ID de solicitação máxima copiada na última execução pelo ADF em um armazenamento de dados de preparo (como o blob do Azure no diagrama acima). Portanto, a mesma solicitação não é lida uma segunda vez pelo ADF na execução subsequente. Enquanto isso, observe que os dados não são excluídos automaticamente da tabela de Hub aberta.

Para uma manipulação de Delta adequada, não é permitido ter IDs de solicitação de DTPs diferentes na mesma tabela de Hub aberta. Portanto, você não deve criar mais de um DTP para cada destino de Hub aberto (OHD). Ao precisar de extração completa e Delta do mesmo Infoprovider, você deve criar dois OHDs para o mesmo Infoprovider. 

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do SAP Business Warehouse Open Hub, você precisa:

- Configurar um Integration Runtime auto-hospedado com versão 3.13 ou superior. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

- Baixar a versão de **64 bits do [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** do site da SAP e instalá-la no computador de IR auto-hospedado. Ao instalar, na janela de etapas de instalação opcionais, verifique se você selecionou a opção **Instalar Assemblies no GAC** conforme mostra a imagem a seguir. 

    ![Instalar o SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O usuário do SAP que está sendo usado no conector do Data Factory BW precisa ter as seguintes permissões: 

    - Autorização para RFC e SAP BW. 
    - Permissões para a Atividade de "Executar" do Objeto de Autorização "S_SDSAUTH".

- Criar tipo de Destino do SAP Open Hub como **Tabela de Banco de Dados** com opção "Chave Técnica" marcada.  Também é recomendável deixar a opção Excluir Dados da Tabela desmarcada, embora não seja necessário. Use o DTP (executar ou integrar diretamente na cadeia de processo existente) para esterrar dados do objeto de origem (como cubo) que você escolheu para a tabela de destino de Hub aberto.

## <a name="getting-started"></a>Introdução

> [!TIP]
>
> Para obter uma explicação sobre como usar SAP BW conector de Hub aberto, consulte [carregar dados do SAP Business Warehouse (BW) usando Azure data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir apresentam detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas do conector do SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do SAP BW Open Hub (Business Warehouse):

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **SapOpenHub** | Sim |
| Servidor | Nome do servidor no qual reside a instância do SAP BW. | Sim |
| systemNumber | Número de sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim |
| messageServer | O nome do host do servidor de mensagens SAP.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| messageServerService | O nome do serviço ou o número da porta do servidor de mensagens.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| systemId | A ID do sistema SAP em que a tabela está localizada.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| logon | O grupo de logon do sistema SAP.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| clientId | ID de Cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| Linguagem | Idioma que o sistema SAP usa. | Não (o valor padrão é **EN**)|
| userName | Nome do usuário que tem acesso ao servidor SAP. | Sim |
| password | Senha do usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de SAP BW Open Hub.

Para copiar dados de e para o SAP BW Open Hub, defina a propriedade type do conjunto de dados como **SapOpenHubTable**. Há suporte para as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **SapOpenHubTable**.  | Sim |
| openHubDestinationName | O nome de Destino do Open Hub do qual copiar dados. | Sim |

Se você estivesse Configurando `excludeLastRequest` e `baseRequestId` no DataSet, ele ainda tem suporte como está, enquanto você é sugerido para usar o novo modelo na origem da atividade no futuro.

**Exemplo:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub como origem

Para copiar dados de SAP BW Hub aberto, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **SapOpenHubSource**. | Sim |
| excludeLastRequest | Se você deseja excluir os registros da última solicitação. | Não (o padrão é **true**) |
| baseRequestId | A ID da solicitação do carregamento delta. Depois que ele for definido, somente os dados com requestId **maior do que** o valor dessa propriedade serão recuperados.  | Não |

>[!TIP]
>Se a tabela do Open Hub contém apenas os dados gerados por ID de solicitação única, por exemplo, você sempre realiza carga completa e substitui os dados existentes na tabela ou somente executa o DTP uma vez para teste; lembre-se de desmarcar a opção "excludeLastRequest" para copiar os dados.

Para acelerar o carregamento de dados, você pode definir [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) na atividade de cópia para carregar dados de SAP BW Hub aberto em paralelo. Por exemplo, se você definir `parallelCopies` como quatro, data factory executar simultaneamente quatro chamadas RFC, e cada chamada RFC recuperará uma parte dos dados da tabela SAP BW Open Hub particionada pela ID da solicitação DTP e ID do pacote. Isso se aplica quando o número de ID de solicitação DTP + ID de pacote exclusiva é maior que o valor de `parallelCopies` . Ao copiar dados para o armazenamento de dados baseado em arquivo, ele também é recriado para gravar em uma pasta como vários arquivos (apenas especifique o nome da pasta); nesse caso, o desempenho é melhor do que gravar em um único arquivo.

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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapeamento de tipo de dados para SAP BW Open Hub

Ao copiar dados do SAP BW Open Hub, os seguintes mapeamentos são usados de tipos de dados do SAP BW para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo SAP ABAP | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| C (Cadeia de caracteres) | String |
| I (inteiro) | Int32 |
| F (Flutuante) | Double |
| D (Data) | String |
| T (Hora) | String |
| P (BCD Empacotado, Moeda, Decimal, Qtd) | Decimal |
| N (Numc) | String |
| X (Binário e Bruto) | String |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

**Sintomas:** Se você estiver executando SAP BW no HANA e observar que apenas o subconjunto de dados é copiado usando a atividade de cópia do ADF (1 milhão linhas), a possível causa é que você habilita a opção "SAP HANA execução" no DTP, caso em que o ADF pode recuperar somente o primeiro lote de dados.

**Resolução:** Desabilite a opção "execução de SAP HANA" no DTP, reprocesse os dados e tente executar a atividade de cópia novamente.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
