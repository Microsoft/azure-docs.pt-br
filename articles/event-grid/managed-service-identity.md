---
title: Entrega de eventos, identidade de serviço gerenciada e link privado
description: Este artigo descreve como habilitar a identidade do serviço gerenciado para um tópico da grade de eventos do Azure. Use-o para encaminhar eventos para destinos com suporte.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625256"
---
# <a name="event-delivery-with-a-managed-identity"></a>Entrega de eventos com uma identidade gerenciada
Este artigo descreve como usar uma [identidade de serviço gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para um tópico do sistema de grade de eventos do Azure, um tópico personalizado ou um domínio. Use-o para encaminhar eventos para destinos com suporte, como filas e tópicos de Barramento de Serviço, hubs de eventos e contas de armazenamento.



## <a name="prerequisites"></a>Pré-requisitos
1. Atribua uma identidade atribuída pelo sistema a um tópico do sistema, um tópico personalizado ou um domínio. 
    - Para tópicos e domínios personalizados, consulte [habilitar identidade gerenciada para domínios e tópicos personalizados](enable-identity-custom-topics-domains.md). 
    - Para tópicos sobre o sistema, consulte [habilitar identidade gerenciada para tópicos do sistema](enable-identity-system-topics.md)
1. Adicione a identidade a uma função apropriada (por exemplo, remetente de dados do barramento de serviço) no destino (por exemplo, uma fila do barramento de serviço). Para obter etapas detalhadas, consulte [Adicionar identidade às funções do Azure em destinos](add-identity-roles.md)

    > [!NOTE]
    > Atualmente, não é possível entregar eventos usando pontos de [extremidade privados](../private-link/private-endpoint-overview.md). Para obter mais informações, consulte a seção [pontos de extremidade particulares](#private-endpoints) no final deste artigo. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Criar assinaturas de evento que usam uma identidade
Depois de ter um tópico ou tópico personalizado da grade de eventos ou do sistema ou de um domínio com uma identidade gerenciada pelo sistema e tiver adicionado a identidade à função apropriada no destino, você estará pronto para criar assinaturas que usam a identidade. 

### <a name="use-the-azure-portal"></a>Use o Portal do Azure
Ao criar uma assinatura de evento, você verá uma opção para habilitar o uso de uma identidade atribuída pelo sistema para um ponto de extremidade na seção **detalhes do ponto de extremidade** . 

![Habilitar identidade ao criar uma assinatura de evento para uma fila do barramento de serviço](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Você também pode habilitar o uso de uma identidade atribuída pelo sistema para ser usada para mensagens mortas na guia **recursos adicionais** . 

![Habilitar identidade atribuída ao sistema para mensagens mortas](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Usar a fila do barramento de serviço CLI do Azure 
Nesta seção, você aprenderá a usar o CLI do Azure para habilitar o uso de uma identidade atribuída pelo sistema para entregar eventos a uma fila do barramento de serviço. A identidade deve ser um membro da função **Remetente de dados do Barramento de Serviço do Azure**. Ela também deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento usada para mensagens mortas. 

#### <a name="define-variables"></a>Definir variáveis
Primeiro, especifique valores para as variáveis a seguir a serem usadas no comando da CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega 
Este comando de exemplo cria uma assinatura de evento para um tópico personalizado da grade de eventos com um tipo de ponto de extremidade definido como **fila do barramento de serviço**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega e mensagens mortas
Este comando de exemplo cria uma assinatura de evento para um tópico personalizado da grade de eventos com um tipo de ponto de extremidade definido como **fila do barramento de serviço**. Ele também especifica que a identidade gerenciada pelo sistema deve ser usada para mensagens mortas. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Usar os hubs de eventos de CLI do Azure 
Nesta seção, você aprenderá a usar o CLI do Azure para habilitar o uso de uma identidade atribuída pelo sistema para entregar eventos a um hub de eventos. A identidade deve ser um membro da função **Remetente de dados dos Hubs de Eventos do Azure**. Ela também deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento usada para mensagens mortas. 

#### <a name="define-variables"></a>Definir variáveis
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega 
Este comando de exemplo cria uma assinatura de evento para um tópico personalizado da grade de eventos com um tipo de ponto de extremidade definido como **hubs de eventos**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega + mensagens mortas 
Este comando de exemplo cria uma assinatura de evento para um tópico personalizado da grade de eventos com um tipo de ponto de extremidade definido como **hubs de eventos**. Ele também especifica que a identidade gerenciada pelo sistema deve ser usada para mensagens mortas. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Usar o CLI do Azure-fila de armazenamento do Azure 
Nesta seção, você aprenderá a usar o CLI do Azure para habilitar o uso de uma identidade atribuída pelo sistema para entregar eventos a uma fila de armazenamento do Azure. A identidade deve ser um membro da função de **remetente da mensagem de dados da fila de armazenamento** na conta de armazenamento. Ela também deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento usada para mensagens mortas.

#### <a name="define-variables"></a>Definir variáveis  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Criar uma assinatura de evento usando uma identidade gerenciada para entrega + mensagens mortas 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Pontos de extremidade privados
Atualmente, não é possível entregar eventos usando pontos de [extremidade privados](../private-link/private-endpoint-overview.md). Ou seja, não haverá suporte se você tiver requisitos estritos de isolamento de rede em que o tráfego de eventos entregues não deve sair do espaço IP privado. 

No entanto, se seus requisitos chamarem uma maneira segura de enviar eventos usando um canal criptografado e uma identidade conhecida do remetente (nesse caso, a grade de eventos) usando o espaço IP público, você poderá entregar eventos aos hubs de eventos, ao barramento de serviço ou ao serviço de armazenamento do Azure usando um tópico personalizado da grade de eventos do Azure ou um domínio com identidade gerenciada pelo sistema Em seguida, você pode usar um link privado configurado em Azure Functions ou seu webhook implantado em sua rede virtual para efetuar pull de eventos. Consulte o exemplo: [conectar-se a pontos de extremidade privados com Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Nessa configuração, o tráfego passa pelo IP público/Internet da grade de eventos para os hubs de eventos, barramento de serviço ou armazenamento do Azure, mas o canal pode ser criptografado e uma identidade gerenciada da grade de eventos é usada. Se você configurar seu Azure Functions ou webhook implantado em sua rede virtual para usar os hubs de eventos, o barramento de serviço ou o armazenamento do Azure por meio do link privado, essa seção do tráfego permanecerá, de forma evidente, no Azure.


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre identidades gerenciadas, confira [o que são identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).
