---
title: Copiar dados do Dynamics CRM e 365 usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados do Dynamics CRM e 365 para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jingwang
ms.openlocfilehash: 74a4fca2ff5a0f913b5cb195f42482339e6200cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>Copiar dados do Dynamics 365/Dynamics CRM usando o Azure Data Factory
Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Dynamics 365/Dynamics CRM. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados do Dynamics 365/Dynamics CRM para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte, que funcionam como fontes/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Dynamics dá suporte às versões e tipos de autenticação do Dynamics abaixo:

| Versões do Dynamics | Tipos de autenticação | Exemplos de serviço vinculado |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM online | Office365 | [Dynamics Online + Autenticação do Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local com IFD <br> Dynamics CRM 2016 local com IFD <br> Dynamics CRM 2015 local com IFD | IFD | [Dynamics local com IFD + Autenticação do IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD é abreviação de implantação para a Internet.*

> [!NOTE]
> Para usar o conector do Dynamics, armazene sua senha no Azure Key Vault e permita que a atividade de cópia do ADF faça pull desse local ao realizar a cópia de dados. Veja como configurar na seção [propriedades do serviço vinculado](#linked-service-properties).

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Dynamics.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Dynamics:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Dynamics**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"Online"** para o Dynamics Online. | Sim |
| organizationName | O nome da organização da instância do Dynamics. | Não, deve especificar quando há mais de uma instância do Dynamics associada ao usuário. |
| authenticationType | O tipo de autenticação para se conectar ao servidor do Dynamics. Especifique **"Office365"** para o Dynamics Online. | Sim |
| Nome de Usuário | Especifica o nome de usuário para se conectar ao Dynamics. | Sim |
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Você precisa colocar a senha no Azure Key Vault e configurar a senha como um "AzureKeyVaultSecret". Para obter mais informações, consulte o artigo [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo: Dynamics online usando a autenticação do Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM local com IFD

*Propriedades adicionais, comparando com o Dynamics online, são "hostName" e "port".*

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Dynamics**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"OnPremisesWithIfd"** para o Dynamics local com IFD.| Sim |
| **hostName** | O nome do host do servidor do Dynamics local. | Sim |
| **port** | A porta do servidor do Dynamics local. | Não, o padrão é 443 |
| organizationName | O nome da organização da instância do Dynamics. | Sim |
| authenticationType | O tipo de autenticação para se conectar ao servidor do Dynamics. Especifique **"Ifd"** para o Dynamics local com IFD. | Sim |
| Nome de Usuário | Especifica o nome de usuário para se conectar ao Dynamics. | Sim |
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Observe que você precisa colocar a senha no Azure Key Vault e configurar a senha como um "AzureKeyVaultSecret". Para obter mais informações, consulte o artigo [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |

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
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Dynamics.

Para copiar dados do Dynamics, defina a propriedade type do conjunto de dados como **DynamicsEntity**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **DynamicsEntity** |Sim |
| entityName | O nome lógico da entidade a ser recuperada. | Não (se "query" na fonte da atividade for especificada) |

> [!IMPORTANT]
> **A seção "estrutura" no conjunto de dados é necessária para o Dynamics**, a qual define o tipo de dados e o nome de coluna para os dados do Dynamics nos quais você deseja copiar. Saiba mais em [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure) e [Mapeamento de tipo de dados para o Dynamics](#data-type-mapping-for-dynamics).

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
        "typePoperties": {
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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do Dynamics.

### <a name="dynamics-as-source"></a>Dynamics como fonte

Para copiar dados do Dynamics, defina o tipo de fonte na atividade de cópia como **DynamicsSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **DynamicsSource**  | Sim |
| query  | FetchXML é uma linguagem de consulta proprietária que é usada no Microsoft Dynamics (online e local). Veja o exemplo abaixo e saiba mais em [Criar consultas com FetchXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Não (se "entityName" no conjunto de dados for especificada)  |

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

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento de tipo de dados para Dynamics

Ao copiar dados do Dynamics, os seguintes mapeamentos são usados de tipos de dados do Dynamics para tipos de dados intermediários do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

Configure o tipo de dados do ADF correspondente na estrutura do conjunto de dados com base em sua fonte de tipo de dados do Dynamics, usando a tabela de mapeamento abaixo:

| Tipo de dados do Dynamics | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| AttributeTypeCode.BigInt | long |
| AttributeTypeCode.Boolean | Booliano |
| AttributeType.Customer | Guid |
| AttributeType.DateTime | Datetime |
| AttributeType.Decimal | Decimal |
| AttributeType.Double | Duplo |
| AttributeType.EntityName | Cadeia de caracteres |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | Guid |
| AttributeType.ManagedProperty | Booliano |
| AttributeType.Memo | Cadeia de caracteres |
| AttributeType.Money | Decimal |
| AttributeType.Owner | Guid |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | Guid |
| AttributeType.String | Cadeia de caracteres |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> Não há suporte para os tipo de dados do Dynamics AttributeType.CalendarRules e AttributeType.PartyList.


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).