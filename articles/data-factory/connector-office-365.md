---
title: Copiar dados do Office 365 usando Azure Data Factory
description: Saiba como copiar dados do Office 365 em armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: a7df69e7c5701074b40d6fa8340d8a0e247f00de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392996"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copiar dados do Office 365 para o Azure usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Azure Data Factory integra- [se com o Microsoft Graph data Connect](/graph/data-connect-concept-overview), permitindo que você coloque os dados organizacionais avançados em seu locatário do Office 365 no Azure de uma maneira escalonável e crie aplicativos de análise e extraia informações com base nesses valiosos ativos de dados. A integração com o Privileged Access Management fornece controle de acesso protegido para os dados importantes coletados no Office 365.  Consulte [este link](/graph/data-connect-concept-overview) para obter uma visão geral sobre Microsoft Graph data Connect e consulte [este link](/graph/data-connect-policies#licensing) para obter informações de licenciamento.

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Office 365. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte
O conector do AAD Office 365 e o Microsoft Graph data Connect permite a ingestão em escala de diferentes tipos de conjuntos de dados de caixas de correio habilitadas para email do Exchange, incluindo contatos do catálogo de endereços, eventos de calendário, mensagens de email, informações do usuário, configurações da caixa de correio e assim por diante.  Consulte [aqui](/graph/data-connect-datasets) para ver a lista completa de conjuntos de valores disponíveis.

Por enquanto, em uma única atividade de cópia, você só pode **copiar dados do Office 365 para o [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md) no formato JSON** (tipo setOfObjects). Se você quiser carregar o Office 365 em outros tipos de armazenamentos de dados ou em outros formatos, poderá encadear a primeira atividade de cópia com uma atividade de cópia subsequente para carregar mais dados em qualquer um dos [repositórios de destino do ADF com suporte](copy-activity-overview.md#supported-data-stores-and-formats) (consulte a coluna "com suporte como coletor"na tabela "Armazenamentos de dados e formatos com suporte").

>[!IMPORTANT]
>- A assinatura do Azure que contém o data factory e o armazenamento de dados do coletor deve estar no mesmo locatário do Azure Active Directory (Azure AD) que o locatário do Office 365.
>- Certifique-se de que a região do Azure Integration Runtime usada para atividade de cópia, bem como o destino estejam na mesma região da caixa de correio dos usuários locatários do Office 365. Clique [aqui](concepts-integration-runtime.md#integration-runtime-location) para entender como a localização do Azure IR é determinada. Consulte [esta tabela](/graph/data-connect-datasets#regions) para obter a lista de regiões com suporte do Office e regiões do Azure correspondentes.
>- A autenticação de entidade de serviço é o único mecanismo de autenticação com suporte para armazenamento de BLOBs do Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2 como armazenamentos de destino.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados do Office 365 no Azure, você precisa concluir as seguintes etapas de pré-requisito:

- O administrador do seu locatário do Office 365 deve concluir ações de integração, conforme descrito [aqui](/graph/data-connect-get-started).
- Criar e configurar um aplicativo Web do Azure AD no Azure Active Directory.  Para obter instruções, consulte [Criar um aplicativo do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
- Anote os seguintes valores, que serão usados para definir o serviço vinculado para o Office 365:
    - ID do locatário. Para obter instruções, consulte [Obter ID de locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    - ID do aplicativo e Chave do aplicativo.  Para obter instruções, confira [Obter ID do aplicativo e chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
- Adicione a identidade do usuário que fará a solicitação de acesso de dados como proprietário do aplicativo Web do Azure AD (no aplicativo Web do Azure AD > Configurações > Proprietários > Adicionar proprietário). 
    - A identidade do usuário deve estar na organização do Office 365 da qual você está obtendo dados e não deve ser um usuário Convidado.

## <a name="approving-new-data-access-requests"></a>Aprovação de novas solicitações de acesso a dados

Se esta for a primeira vez que você está solicitando dados para esse contexto (uma combinação de qual tabela de dados está sendo acessada, em qual conta de destino os dados estão sendo carregados e qual identidade de usuário está fazendo a solicitação de acesso a dados), você verá o status da atividade de cópia como "em andamento" e somente quando clicar no [link "detalhes" em ações](copy-activity-overview.md#monitoring) , você verá o status como "RequestingConsent".  Um membro do grupo de aprovadores de acesso a dados precisa aprovar a solicitação no Privileged Access Management antes que seja possível prosseguir com a extração de dados.

Confira [aqui](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) como o aprovador pode aprovar a solicitação de acesso a dados e confira [aqui](/graph/data-connect-pam) para obter uma explicação sobre a integração total com o Privileged Access Management, incluindo como configurar o grupo de aprovador de acesso a dados.

## <a name="policy-validation"></a>Validação de política

Se o ADF for criado como parte de um aplicativo gerenciado e as atribuições de políticas do Azure forem feitas em recursos dentro do grupo de recursos de gerenciamento, para cada atividade de cópia executada, o ADF verificará se as atribuições de política foram aplicadas. Confira [aqui](/graph/data-connect-policies#policies) uma lista de políticas com suporte.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para obter uma explicação de como usar o conector do Office 365, confira o artigo [Carregar dados do Office 365](load-office-365-data.md).

É possível criar um pipeline com a atividade de cópia usando uma das seguintes ferramentas ou SDKs. Selecione um link para acessar um tutorial com instruções passo a passo para criar um pipeline com uma atividade de cópia. 

- [Azure portal](quickstart-create-data-factory-portal.md)
- [SDK .NET](quickstart-create-data-factory-dot-net.md)
- [SDK do Python](quickstart-create-data-factory-python.md)
- [PowerShell do Azure](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Modelo de Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do Office 365.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Office 365:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como: **Office365** | Sim |
| office365TenantId | ID de locatário do Azure ao qual a conta do Office 365 pertence. | Sim |
| servicePrincipalTenantId | Especifique as informações de locatário sob as quais o aplicativo Web do Azure AD reside. | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. | Sim |
| connectVia | O Integration Runtime a ser usado para se conectar ao armazenamento de dados.  Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não |

>[!NOTE]
> A diferença entre **office365TenantId** e **servicePrincipalTenantId** e o valor correspondente a ser fornecido:
>- Se você for um desenvolvedor empresarial desenvolvendo um aplicativo com base nos dados do Office 365 para uso de sua organização, forneça a mesma ID de locatário para as duas propriedades, que é a ID de locatário do AAD da sua organização.
>- Se você for um desenvolvedor de ISV desenvolvendo um aplicativo para seus clientes, office365TenantId será a ID de locatário do AAD do seu cliente (instalador do aplicativo) e servicePrincipalTenantId será a ID de locatário do AAD da sua empresa.

**Exemplo:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte do conjunto de dados do Office 365.

Para copiar dados do Office 365, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo do conjunto de dados deve ser definida como: **Office365Table** | Sim |
| tableName | Nome do conjunto de dados para extrair do Office 365. Confira [aqui](/graph/data-connect-datasets#datasets) a lista de conjuntos de dados do Office 365 disponíveis para extração. | Sim |

Se você estivesse Configurando `dateFilterColumn` , `startTime` , `endTime` e `userScopeFilterUri` no conjunto de um, ele ainda terá suporte como está, enquanto você é sugerido para usar o novo modelo na origem da atividade no futuro.

**Exemplo**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte da fonte Office 365.

### <a name="office-365-as-source"></a>Office 365 como fonte

Para copiar dados do Office 365, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Office365Source** | Sim |
| allowedGroups | Predicado de seleção de grupo.  Use essa propriedade para selecionar até 10 grupos de usuários para os quais os dados serão recuperados.  Se nenhum grupo for especificado, os dados serão retornados para toda a organização. | Não |
| userScopeFilterUri | Quando `allowedGroups` a propriedade não é especificada, você pode usar uma expressão de predicado que é aplicada em todo o locatário para filtrar as linhas específicas a serem extraídas do Office 365. O formato do predicado deve corresponder ao formato de consulta de APIs de Microsoft Graph, por exemplo, `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` . | Não |
| dateFilterColumn | Nome da coluna de filtro DateTime. Use essa propriedade para limitar o intervalo de tempo para o qual os dados do Office 365 são extraídos. | Sim se o conjunto de informações tiver uma ou mais colunas DateTime. Consulte [aqui](/graph/data-connect-filtering#filtering) para obter a lista de conjuntos de valores que exigem este filtro de data e hora. |
| startTime | Inicie o valor DateTime para filtrar. | Sim se `dateFilterColumn` for especificado |
| endTime | Valor DateTime final para filtrar. | Sim se `dateFilterColumn` for especificado |
| outputColumns | Matriz das colunas a serem copiadas para o coletor. | Não |

**Exemplo:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).