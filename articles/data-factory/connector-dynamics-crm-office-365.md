---
title: Copiar dados de e para Dynamics CRM ou Dynamics 365 (Common Data Service) usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Microsoft Dynamics CRM ou Microsoft Dynamics 365 (Common Data Service) para armazenamentos de dados de coletor com suporte ou de armazenamentos de dados de fonte com suporte para Dynamics CRM ou Dynamics 365 usando uma atividade de cópia em um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: jingwang
ms.openlocfilehash: 3f7bf3ce8c01e82fa69b3b041b573b4b31a719d2
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514095"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiar dados de e para Dynamics 365 (Common Data Service) ou Dynamics CRM usando o Azure Data Factory

Este artigo estrutura como usar atividade de cópia no Azure Data Factory para copiar dados de e para Microsoft Dynamics 365 ou Microsoft Dynamics CRM. Ele amplia o artigo [visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Dynamics 365 (Common Data Service) ou Dynamics CRM para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para Dynamics 365 (Common Data Service) ou Dynamics CRM. Para obter uma lista de repositórios de dados com suporte como fontes ou coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Este conector do Dynamics dá suporte à versão do Dynamics 7.x 9.x para ambos online ou local. Mais especificamente,

- Versão 7.x mapeia para o Dynamics CRM 2015
- Versão 8.x mapeia para o Dynamics CRM 2016 e a versão inicial do Dynamics 365
- Versão 9. x mapas para a versão mais recente do Dynamics 365

Consulte a tabela a seguir nos tipos de autenticação com suporte e configurações para as respectivas versões/produtos do Dynamics. (IFD é abreviação de implantação para a Internet.)

| Versões do Dynamics | Tipos de autenticação | Exemplos de serviço vinculado |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + Autenticação do Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local com IFD <br> Dynamics CRM 2016 local com IFD <br> Dynamics CRM 2015 local com IFD | IFD | [Dynamics local com IFD + Autenticação do IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Para o Dynamics 365 especificamente, os seguintes tipos de aplicativos são compatíveis:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Outros tipos de aplicação, por exemplo Finanças e Operações, Talentos, etc. não são suportados por este conector.

Este conector do Dynamics baseia-se na parte superior da [as ferramentas do XRM Dynamics](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Para copiar dados de **Finanças e Operações do Dynamics 365**, você pode usar o [Conector do Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Dynamics.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **Dynamics**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"Online"** para o Dynamics online. | Sim |
| serviceUri | A URL de serviço da instância do Dynamics, por exemplo, `https://adfdynamics.crm.dynamics.com`. | Sim |
| authenticationType | O tipo de autenticação para se conectar a um servidor do Dynamics. Especifique **"Office365"** para o Dynamics online. | Sim |
| username | Especifique o nome de usuário para se conectar ao Dynamics. | Sim |
| password | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para a fonte, Sim para o coletor se o serviço vinculado à fonte não possuir um integration runtime |

>[!NOTE]
>O conector do Dynamics costumava usar a propriedade "organizationName" opcional para identificar a instância do Dynamics CRM/365 Online. Embora continue funcionando, sugerimos que você especifique a nova propriedade "serviceUri" para obter melhor desempenho para a descoberta da instância.

**Exemplo: Dynamics online usando a autenticação do Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM local com IFD

*Propriedades adicionais que comparam com o Dynamics online são "hostName" e "port".*

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **Dynamics**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"OnPremisesWithIfd"** para o Dynamics local com IFD.| Sim |
| hostName | O nome do host do servidor do Dynamics local. | Sim |
| port | O nome da porta do servidor do Dynamics local. | Não, o padrão é 443 |
| organizationName | O nome da organização da instância do Dynamics. | Sim |
| authenticationType | O tipo de autenticação para se conectar ao servidor do Dynamics. Especifique **"Ifd"** para o Dynamics local com IFD. | Sim |
| username | Especifique o nome de usuário para se conectar ao Dynamics. | Sim |
| password | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Você pode optar por marcar este campo como uma SecureString para armazená-la com segurança no ADF ou armazenar a senha no Azure Key Vault e permitir o pull de atividade de cópia a partir daí, ao executar a cópia de dados - Saiba mais de [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para fonte, Sim para o coletor |

**Exemplo: Dynamics local com IFD usando a autenticação de IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Dynamics.

Para copiar dados de e para Dynamics, defina o tipo da propriedade do conjunto de dados como **DynamicsEntity**. Há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como **DynamicsEntity**. |Sim |
| entityName | O nome lógico da entidade a ser recuperada. | Não para fonte (se "query" na fonte da atividade for especificada), Sim para coletor |

> [!IMPORTANT]
>- Quando você copia dados do Dynamics, a seção "estrutura" é opcional mas altamente recommanded no conjunto de dados Dynamics para garantir um resultado determinística de cópia. Ela define o tipo de dados e o nome de coluna para dados do Dynamics que você deseja copiar. Saiba mais em [Estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure-or-schema) e [Mapeamento de tipo de dados para o Dynamics](#data-type-mapping-for-dynamics).
>- Durante a importação de esquema na criação da interface do usuário, o ADF infere o esquema fazendo a amostragem das primeiras linhas do resultado da consulta do Dynamics para inicializar a construção da estrutura, caso em que as colunas sem valores são omitidas. O mesmo comportamento se aplica para copiar as execuções se não houver nenhuma definição de estrutura explícita. Você poderá examinar o esquema/estrutura de conjunto de dados do Dynamics e adicionar mais colunas a ele conforme necessário, as quais serão respeitadas durante o tempo de execução de cópia.
>- Ao copiar dados para o Dynamics, a seção "estrutura" é opcional no conjunto de dados do Dynamics. Quais colunas copiar são determinadas pelo esquema de dados de origem. Se a fonte for um arquivo CSV sem cabeçalho, no conjunto de dados de entrada, especifique "structure" com o nome da coluna e tipo de dados. Eles são mapeados para os campos no arquivo CSV um por um em ordem.

**Exemplo:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e tipos de coletor do Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics como um tipo de fonte

Para copiar dados do Dynamics, defina o tipo de fonte na atividade de cópia como **DynamicsSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O tipo da propriedade da fonte da atividade de cópia deve ser definido como **DynamicsSource**. | Sim |
| query | FetchXML é uma linguagem de consulta proprietária que é usada no Dynamics (online e local). Veja os exemplos a seguir. Para obter mais informações, consulte [criar consultas com FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Não (se "entityName" no conjunto de dados for especificada) |

>[!NOTE]
>A coluna PK sempre será copiada, mesmo que ela não esteja contida na projeção da coluna que você configurar na consulta FetchXML.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Exemplo de consulta FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics como um tipo de coletor

Para copiar dados do Dynamics, defina o tipo de coletor na atividade de cópia como **DynamicsSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O tipo de propriedade do coletor da atividade de cópia deve ser definida como **DynamicsSink**. | Sim |
| writeBehavior | O comportamento da operação de gravação.<br/>O valor permitido é **"Upsert"** . | Sim |
| writeBatchSize | A contagem de linhas de dados gravados no Dynamics em cada lote. | Não (o padrão é 10) |
| ignoreNullValues | Indica se deve ignorar valores nulos de dados de entrada (exceto campos de chave) durante uma operação de gravação.<br/>Os valores permitidos são **True** e **False**.<br>- **True**: deixa os dados no objeto de destino inalterados quando você faz uma operação upsert/update. Insira um valor padrão definido quando você faz uma operação insert.<br/>- **False**: atualiza os dados no objeto de destino como NULL quando você faz uma operação upsert/update. Insira um valor NULL quando você faz uma operação insert. | Não (padrão é falso) |

>[!NOTE]
>O valor padrão de “**writeBatchSize**” do coletor e a atividade de cópia “ **[parallelCopies](copy-activity-performance.md#parallel-copy)** ” para o coletor Dynamics são ambos 10. Portanto, 100 registros são enviados ao Dynamics simultaneamente.

Para o Dynamics 365 online, há um limite de [2 chamadas simultâneas de lote por organização](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Se esse limite for excedido, uma falha de "Servidor ocupado" será lançada antes que a primeira solicitação seja executada. Manter "writeBatchSize" menor ou igual a 10 evitaria essa limitação de chamadas simultâneas.

A combinação ideal de "**writeBatchSize**"e"**parallelCopies**" depende do esquema da entidade, por exemplo, número de colunas, o tamanho de linha, o número de plug-ins/fluxos de trabalho/atividades de fluxo de trabalho vinculado para essas chamadas, etc. A configuração padrão de 10 writeBatchSize * 10 parallelCopies é a recomendação de acordo com o serviço do Dynamics, o que pode funcionar para a maioria das entidades do Dynamics embora talvez não seja um melhor desempenho. Você pode ajustar o desempenho ajustando a combinação em suas configurações de atividade de cópia.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento de tipo de dados para Dynamics

Ao copiar dados do Dynamics, os seguintes mapeamentos são usados de tipos de dados do Dynamics para tipos de dados intermediários do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

Configure o tipo de dados do Data Factory correspondente em uma estrutura do conjunto de dados com base em sua fonte de tipo de dados do Dynamics, usando a tabela de mapeamento a seguir.

| Tipo de dados do Dynamics | Tipo de dados provisório do Data Factory | Tem suporte como origem | Tem suporte como coletor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Cadeia de caracteres | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (com o único destino associado) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | Cadeia de caracteres | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Cadeia de caracteres | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Não há suporte para os tipos de dados do Dynamics Calendarrules, AttributeType.MultiSelectPicklist e PartyList.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
