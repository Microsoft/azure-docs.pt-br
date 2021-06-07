---
title: Adicionar identidade gerenciada a uma função no destino da grade de eventos do Azure
description: Este artigo descreve como adicionar identidade gerenciada a funções do Azure em destinos como o barramento de serviço do Azure e hubs de eventos do Azure.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1bcef878c982122d80980dd7194fae2de6fc8762
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630319"
---
# <a name="add-an-identity-to-azure-roles-on-azure-event-grid-destinations"></a>Adicionar uma identidade às funções do Azure nos destinos da grade de eventos do Azure
Esta seção descreve como adicionar a identidade para o tópico do sistema, o tópico personalizado ou o domínio a uma função do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Atribua uma identidade gerenciada atribuída pelo sistema usando as instruções dos seguintes artigos:

- [Tópicos ou domínios personalizados](enable-identity-custom-topics-domains.md)
- [Tópicos do sistema](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Destinos com suporte e funções do Azure
Depois de habilitar a identidade para seu tópico ou domínio personalizado da grade de eventos, o Azure cria automaticamente uma identidade no Azure Active Directory. Adicione essa identidade às funções apropriadas do Azure para que o tópico ou o domínio personalizado possa encaminhar eventos para os destinos com suporte. Por exemplo, adicione a identidade à função de **remetente de dados dos hubs de eventos do Azure** para um namespace de hubs de eventos do Azure para que o tópico personalizado da grade de eventos possa encaminhar eventos para os hubs de eventos nesse namespace. 

Atualmente, a grade de eventos do Azure dá suporte a tópicos personalizados ou a domínios configurados com uma identidade gerenciada atribuída pelo sistema para encaminhar eventos para os seguintes destinos. Essa tabela também fornece as funções em que a identidade deve estar para que o tópico personalizado possa encaminhar os eventos.

| Destino | Função do Azure | 
| ----------- | --------- | 
| Filas e tópicos do Barramento de Serviço | [Remetente de dados do Barramento de Serviço do Azure](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Hubs de eventos do Azure | [Remetente de dados dos Hubs de Eventos do Azure](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Armazenamento de Blobs do Azure | [Colaborador de dados de blob de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Armazenamento de Filas do Azure |[Remetente da mensagem de dados da fila de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Use o Portal do Azure
Você pode usar a portal do Azure para atribuir o tópico personalizado ou a identidade do domínio a uma função apropriada para que o tópico ou domínio personalizado possa encaminhar eventos para o destino. 

O exemplo a seguir adiciona uma identidade gerenciada para um tópico personalizado da grade de eventos chamado **msitesttopic** à função de **remetente de dados do barramento de serviço do Azure** para um namespace do barramento de serviço que contém um recurso de fila ou tópico. Quando você adiciona à função no nível de namespace, o tópico personalizado da grade de eventos pode encaminhar eventos para todas as entidades no namespace. 

1. Vá para o **namespace do barramento de serviço** na [portal do Azure](https://portal.azure.com). 
1. Selecione **controle de acesso** no painel esquerdo. 
1. Selecione **Adicionar** na seção **Adicionar uma atribuição de função**. 
1. Na página **Adicionar uma atribuição de função**, siga os seguintes passos:
    1. Selecione a função. Neste caso, é **Remetente de dados do Barramento de Serviço do Azure**. 
    1. Selecione a **identidade** do seu tópico ou domínio personalizado da grade de eventos. 
    1. Selecione **salvar** para salvar a configuração.

Para adicionar uma identidade a outras funções mencionadas na tabela, as etapas são semelhantes. 

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure
O exemplo nesta seção mostra como usar o CLI do Azure para adicionar uma identidade a uma função do Azure. Os comandos de exemplo são para tópicos personalizados de grade de eventos. Os comandos para domínios de grade de eventos são semelhantes. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Obter a ID da entidade de segurança para a identidade do sistema do tópico personalizado 
Primeiro, obtenha a ID da entidade de segurança da identidade gerenciada pelo sistema do tópico personalizado e atribua a identidade às funções apropriadas.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Criar uma atribuição de função para hubs de eventos em vários escopos 
O exemplo de CLI a seguir mostra como adicionar a identidade de um tópico personalizado à função de **remetente de dados dos hubs de eventos do Azure** no nível do namespace ou no nível do hub de eventos. Se você criar a atribuição de função no nível de namespace, o tópico personalizado poderá encaminhar eventos para todos os hubs de eventos nesse namespace. Se você criar uma atribuição de função no nível do hub de eventos, o tópico personalizado poderá encaminhar eventos somente para esse Hub de eventos específico. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Criar uma atribuição de função para um tópico do barramento de serviço em vários escopos 
O exemplo de CLI a seguir mostra como adicionar uma identidade do tópico personalizado da grade de eventos à função de **remetente de dados do barramento de serviço do Azure** no nível do namespace ou no nível de tópico do barramento de serviço. Se você criar a atribuição de função no nível de namespace, o tópico da grade de eventos poderá encaminhar eventos para todas as entidades (filas ou tópicos do barramento de serviço) dentro desse namespace. Se você criar uma atribuição de função no nível de fila ou de tópico do barramento de serviço, o tópico personalizado da grade de eventos poderá encaminhar eventos somente para a fila ou tópico específico do barramento de serviço. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Próximas etapas
Agora que você atribuiu uma identidade atribuída pelo sistema ao tópico do sistema, ao tópico personalizado ou ao domínio e adicionou a identidade às funções apropriadas em destinos, consulte [eventos Devlier usando a identidade](managed-service-identity.md) na entrega de eventos aos destinos usando a identidade.


