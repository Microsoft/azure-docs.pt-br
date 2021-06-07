---
title: Copiar dados no Dynamics (Common Data Service)
description: Saiba como copiar dados do Microsoft Dynamics CRM ou do Microsoft Dynamics 365 (Common Data Service/Microsoft dataverso) para armazenamentos de dados de coletor com suporte ou de armazenamentos de dados de origem com suporte para o Dynamics CRM ou o Dynamics 365 usando uma atividade de cópia em um pipeline de data factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: f2db75fdcd4519b5ba0869bf4ef89c8323435539
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565972"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-servicemicrosoft-dataverse-or-dynamics-crm-by-using-azure-data-factory"></a>Copiar dados de e para o Dynamics 365 (Common Data Service/Microsoft dataverso) ou o Dynamics CRM usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar uma atividade de cópia no Azure Data Factory para copiar dados de e para o Microsoft Dynamics 365 e o Microsoft Dynamics CRM. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral de uma atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com a [origem com suporte e a matriz de coletor](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Dynamics 365 (Common Data Service/Microsoft dataverso) ou do Dynamics CRM para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para Dynamics 365 (Common Data Service) ou Dynamics CRM. Para obter uma lista de armazenamentos de dados com suporte de uma atividade de cópia como fontes e coletores, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Este conector do Dynamics dá suporte ao Dynamics das versões 7 a 9 para online e local. Mais especificamente:

- A versão 7 é mapeada para o Dynamics CRM 2015.
- A versão 8 é mapeada para o Dynamics CRM 2016 e para a versão anterior do Dynamics 365.
- A versão 9 é mapeada para a versão mais recente do Dynamics 365.

Consulte a tabela a seguir de tipos de autenticação e configurações com suporte para versões e produtos do Dynamics.

| Versões do Dynamics | Tipos de autenticação | Exemplos de serviço vinculado |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 online <br/><br/> Dynamics CRM online | Entidade de serviço do Azure Active Directory (Azure AD) <br/><br/> Office 365 | [Dynamics online e serviço Azure AD-entidade de segurança ou autenticação do Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local com a IFD (implantação voltada para a Internet) <br/><br/> Dynamics CRM 2016 local com IFD <br/><br/> Dynamics CRM 2015 local com IFD | IFD | [Dynamics local com a IFD e a autenticação IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Para o Dynamics 365 especificamente, os seguintes tipos de aplicativos são compatíveis:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Esse conector não dá suporte a outros tipos de aplicativos, como finanças, operações e talento.

>[!TIP]
>Para copiar dados de finanças e operações do Dynamics 365, você pode usar o [conector do Dynamics AX](connector-dynamics-ax.md).

Esse conector do Dynamics é criado sobre as [Ferramentas do Dynamics xrm](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector com a autenticação de entidade de serviço do Azure AD, você deve configurar a autenticação de servidor para servidor (S2S) no Common Data Service ou Dynamics. Consulte [Este artigo](/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) para obter as etapas detalhadas.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Dynamics.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como "Dynamics", "DynamicsCrm" ou "CommonDataServiceForApps". | Yes |
| deploymentType | O tipo de implantação da instância do Dynamics. O valor deve ser "online" para o Dynamics online. | Yes |
| serviceUri | A URL de serviço da instância do Dynamics, a mesma que você acessa no navegador. Um exemplo é "https:// \<organization-name> . CRM [x]. Dynamics. com". | Sim |
| authenticationType | O tipo de autenticação para se conectar a um servidor do Dynamics. Os valores válidos são "AADServicePrincipal" e "Office365". | Sim |
| servicePrincipalId | A ID do cliente do aplicativo do Azure AD. | Sim quando a autenticação for "AADServicePrincipal" |
| servicePrincipalCredentialType | O tipo de credencial a ser usado para autenticação de entidade de serviço. Os valores válidos são "ServicePrincipalKey" e "ServicePrincipalCert". | Sim quando a autenticação for "AADServicePrincipal" |
| servicePrincipalCredential | A credencial de entidade de serviço. <br/><br/>Quando você usa "ServicePrincipalKey" como o tipo de credencial, `servicePrincipalCredential` pode ser uma cadeia de caracteres que Azure data Factory criptografa na implantação do serviço vinculado. Ou pode ser uma referência a um segredo no Azure Key Vault. <br/><br/>Quando você usa "ServicePrincipalCert" como a credencial, `servicePrincipalCredential` deve ser uma referência a um certificado no Azure Key Vault. | Sim quando a autenticação for "AADServicePrincipal" |
| Nome de Usuário | O nome de usuário para se conectar ao Dynamics. | Sim quando a autenticação for "Office365" |
| password | A senha da conta de usuário que você especificou como o nome de usuários. Marque este campo com "SecureString" para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim quando a autenticação for "Office365" |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se nenhum valor for especificado, a propriedade usará o tempo de execução de integração do Azure padrão. | No |

>[!NOTE]
>O conector do Dynamics usou anteriormente a propriedade **OrganizationName** opcional para identificar sua instância do Dynamics CRM ou do Dynamics 365 online. Embora essa propriedade ainda funcione, sugerimos que você especifique a nova propriedade **ServiceUri** em vez disso, para obter um melhor desempenho para a descoberta de instância.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Exemplo: Dynamics online usando o serviço do Azure AD-entidade de segurança e autenticação de chave

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Exemplo: Dynamics online usando serviço do Azure AD-entidade de segurança e autenticação de certificado

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Exemplo: Dynamics online usando a autenticação do Office 365

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
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

Propriedades adicionais que se comparam ao Dynamics online são **hostname** e **Port**.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como "Dynamics", "DynamicsCrm" ou "CommonDataServiceForApps". | Sim. |
| deploymentType | O tipo de implantação da instância do Dynamics. O valor deve ser "OnPremisesWithIfd" para o Dynamics local com IFD.| Sim. |
| hostName | O nome do host do servidor do Dynamics local. | Sim. |
| porta | O nome da porta do servidor do Dynamics local. | Não. O valor padrão é 443. |
| organizationName | O nome da organização da instância do Dynamics. | Sim. |
| authenticationType | O tipo de autenticação para se conectar ao servidor do Dynamics. Especifique "Ifd" para o Dynamics local com IFD. | Sim. |
| Nome de Usuário | O nome de usuário para se conectar ao Dynamics. | Sim. |
| password | A senha para a conta de usuário especificada para o nome do usuário. Você pode marcar esse campo com "SecureString" para armazená-lo com segurança em Data Factory. Ou você pode armazenar uma senha em Key Vault e deixar a atividade de cópia efetuar pull a partir daí quando ela faz a cópia de dados. Saiba mais sobre [Armazenar credenciais no Cofre de Chaves](store-credentials-in-key-vault.md). | Sim. |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se nenhum valor for especificado, a propriedade usará o tempo de execução de integração do Azure padrão. | No |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Exemplo: Dynamics local com IFD usando a autenticação de IFD

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

Para copiar dados de e para o Dynamics, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como "DynamicsEntity", "DynamicsCrmEntity" ou "CommonDataServiceForAppsEntity". |Yes |
| entityName | O nome lógico da entidade a ser recuperada. | Não para origem se a origem da atividade for especificada como "consulta" e sim para o coletor |

#### <a name="example"></a>Exemplo

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
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

Para copiar dados do Dynamics, a seção **origem** da atividade de cópia dá suporte às seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como "Dynamics Source", "DynamicsCrmSource" ou "CommonDataServiceForAppsSource". | Sim |
| Consulta | FetchXML é uma linguagem de consulta proprietária que é usada no Dynamics online e no local. Veja o exemplo a seguir. Para saber mais, consulte [criar consultas com FetchXML](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8)). | Não se `entityName` o conjunto de DataSet for especificado |

>[!NOTE]
>A coluna PK sempre será copiada, mesmo que ela não esteja contida na projeção da coluna que você configurar na consulta FetchXML.

> [!IMPORTANT]
>- Quando você copia dados do Dynamics, o mapeamento de coluna explícito do Dynamics para o coletor é opcional. Mas é altamente recomendável o mapeamento para garantir um resultado de cópia determinística.
>- Quando Data Factory importa um esquema na interface do usuário de criação, ele infere o esquema. Ele faz isso por meio da amostragem das principais linhas do resultado da consulta do Dynamics para inicializar a lista de colunas de origem. Nesse caso, as colunas sem valores nas linhas superiores são omitidas. O mesmo comportamento se aplica a execuções de cópia se não houver mapeamento explícito. Você pode examinar e adicionar mais colunas ao mapeamento, que são respeitadas durante o tempo de execução da cópia.

#### <a name="example"></a>Exemplo

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

Para copiar dados para o Dynamics, a seção de **coletor** de atividade de cópia dá suporte às seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como "DynamicsSink", "DynamicsCrmSink" ou "CommonDataServiceForAppsSink". | Sim. |
| writeBehavior | O comportamento da operação de gravação. O valor deve ser "Upsert". | Yes |
| alternateKeyName | O nome da chave alternativa definido em sua entidade para fazer um Upsert. | Não. |
| writeBatchSize | A contagem de linhas de dados gravados no Dynamics em cada lote. | Não. O valor padrão é 10. |
| ignoreNullValues | Se é para ignorar valores nulos de dados de entrada que não sejam campos de chave durante uma operação de gravação.<br/><br/>Os valores válidos são **true** e **false**:<ul><li>**True**: deixa os dados no objeto de destino inalterados quando você faz uma operação de Upsert ou atualização. Insira um valor padrão definido quando você faz uma operação insert.</li><li>**False**: atualizar os dados no objeto de destino para um valor nulo quando você fizer uma operação de Upsert ou atualização. Insira um valor nulo ao fazer uma operação de inserção.</li></ul> | Não. O valor padrão é **false**. |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| No |

>[!NOTE]
>O valor padrão para o coletor **writeBatchSize** e a atividade de cópia **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** para o coletor do Dynamics é 10. Portanto, os registros 100 são enviados simultaneamente por padrão ao Dynamics.

Para o Dynamics 365 online, há um limite de [duas chamadas de lote simultâneas por organização](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations). Se esse limite for excedido, uma exceção de "servidor ocupado" será lançada antes que a primeira solicitação seja executada. Mantenha **writeBatchSize** em 10 ou menos para evitar essa limitação de chamadas simultâneas.

A combinação ideal de **writeBatchSize** e **parallelCopies** depende do esquema da sua entidade. Os elementos de esquema incluem o número de colunas, o tamanho da linha e o número de plug-ins, fluxos de trabalho ou atividades de fluxo de trabalho conectados a essas chamadas. A configuração padrão de **writeBatchSize** (10) &times; **parallelCopies** (10) é a recomendação de acordo com o serviço do Dynamics. Esse valor funciona para a maioria das entidades do Dynamics, embora possa não fornecer o melhor desempenho. Você pode ajustar o desempenho ajustando a combinação em suas configurações de atividade de cópia.

#### <a name="example"></a>Exemplo

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

## <a name="retrieving-data-from-views"></a>Recuperando dados de exibições

Para recuperar dados de exibições do Dynamics, você precisa obter a consulta salva da exibição e usar a consulta para obter os dados.

Há duas entidades que armazenam diferentes tipos de exibição: "consulta salva" armazena a exibição do sistema e a "consulta do usuário" armazena a exibição do usuário. Para obter as informações dos modos de exibição, consulte a seguinte consulta FetchXML e substitua "TARGETENTITY" por `savedquery` ou `userquery` . Cada tipo de entidade tem mais atributos disponíveis que você pode adicionar à consulta com base em sua necessidade. Saiba mais sobre entidade [savedquery](/dynamics365/customer-engagement/web-api/savedquery) e [entidade userquery](/dynamics365/customer-engagement/web-api/userquery).

```xml
<fetch top="5000" >
  <entity name="<TARGETENTITY>">
    <attribute name="name" />
    <attribute name="fetchxml" />
    <attribute name="returnedtypecode" />
    <attribute name="querytype" />
  </entity>
</fetch>
```

Você também pode adicionar filtros para filtrar as exibições. Por exemplo, adicione o filtro a seguir para obter uma exibição denominada "minhas contas ativas" na entidade de conta.

```xml
<filter type="and" >
    <condition attribute="returnedtypecode" operator="eq" value="1" />
    <condition attribute="name" operator="eq" value="My Active Accounts" />
</filter>
```

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento de tipo de dados para Dynamics

Quando você copia dados do Dynamics, a tabela a seguir mostra mapeamentos de tipos de dados do Dynamics para Data Factory tipos de dados provisórios. Para saber como uma atividade de cópia é mapeada para um esquema de origem e um tipo de dados é mapeado para um coletor, consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

Configure o tipo de dados Data Factory correspondente em uma estrutura de conjunto de dados com base em seu tipo de fonte Dynamics de origem usando a seguinte tabela de mapeamento:

| Tipo de dados do Dynamics | Tipo de dados provisório do Data Factory | Tem suporte como origem | Tem suporte como coletor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓ (Consulte as [diretrizes](#writing-data-to-a-lookup-field)) |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ (Consulte as [diretrizes](#writing-data-to-a-lookup-field)) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓ (Consulte as [diretrizes](#writing-data-to-a-lookup-field)) |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Não há suporte para os tipos de dados do Dynamics **attributeType. CalendarRules**, **attributeType. MultiSelectPicklist** e **attributeType. partylist** .

## <a name="writing-data-to-a-lookup-field"></a>Gravando dados em um campo de pesquisa

Para gravar dados em um campo de pesquisa com vários destinos, como cliente e proprietário, siga este guia e exemplo:

1. Faça com que sua fonte contenha o valor do campo e o nome da entidade de destino correspondente.
   - Se todos os registros forem mapeados para a mesma entidade de destino, certifique-se de uma das seguintes condições:
      - Os dados de origem têm uma coluna que armazena o nome da entidade de destino.
      - Você adicionou uma coluna adicional na origem da atividade de cópia para definir a entidade de destino.
   - Se registros diferentes forem mapeados para entidades de destino diferentes, verifique se os dados de origem têm uma coluna que armazena o nome da entidade de destino correspondente.

1. Mapeie as colunas valor e referência de entidade da origem para o coletor. A coluna entidade-referência deve ser mapeada para uma coluna virtual com o padrão de nomenclatura especial `{lookup_field_name}@EntityReference` . Na verdade, a coluna não existe no Dynamics. Ele é usado para indicar que essa coluna é a coluna de metadados do campo de pesquisa de várias destinos fornecido.

Por exemplo, suponha que a fonte tenha estas duas colunas:

- **Customerfield** coluna do tipo **GUID**, que é o valor da chave primária da entidade de destino no Dynamics.
- Coluna de **destino** do tipo **cadeia de caracteres**, que é o nome lógico da entidade de destino.

Além disso, suponha que você deseja copiar esses dados para o campo de entidade de dinâmica do coletor **customerfield** do tipo **Customer**.

Em mapeamento de coluna de atividade de cópia, mapeie as duas colunas da seguinte maneira:

- **Customerfield** para **customerfield**. Esse mapeamento é o mapeamento de campo normal.
- **Destino** para **customerfield \@ EntityReference**. A coluna Sink é uma coluna virtual que representa a referência de entidade. Insira esses nomes de campo em um mapeamento, pois eles não aparecerão Importando esquemas.

![Pesquisa de Dynamics – mapeamento de coluna de campo](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Se todos os seus registros de origem mapearem para a mesma entidade de destino e os dados de origem não contiverem o nome da entidade de destino, aqui está um atalho: na origem da atividade de cópia, adicione uma coluna adicional. Nomeie a nova coluna usando o padrão `{lookup_field_name}@EntityReference` , defina o valor para o nome da entidade de destino e, em seguida, continue com o mapeamento de coluna como de costume. Se os nomes de coluna de origem e de coletor forem idênticos, você também poderá ignorar o mapeamento de coluna explícito, pois a atividade de cópia, por padrão, mapeia as colunas por nome.

![Pesquisa de Dynamics-campo adicionando uma coluna de referência de entidade](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para obter detalhes sobre as propriedades, consulte [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamento de dados, a atividade de cópia no Data Factory dá suporte a fontes e coletores, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).